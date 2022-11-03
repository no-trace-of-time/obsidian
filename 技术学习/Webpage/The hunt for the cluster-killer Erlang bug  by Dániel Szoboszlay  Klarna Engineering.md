![](https://miro.medium.com/max/1400/1*6FP00bFxFlkHK1K5jgjwQA.jpeg)

What does it take to debug a really mysterious crash? Pulling together all your tools and relentlessly testing all your theories.

At the core of Klarna’s business there is a system written in Erlang. It is called Kred. When Klarna was young, Kred was the one and only service, taking care of all the business processes. It was designed to be robust, tolerant of all possible faults, because Kred going down would have meant Klarna going down. This use case is the sweet spot for Erlang, so Kred thrived.

Kred is no longer the only service in Klarna, but we don’t let its stability erode. Yet, on one March afternoon a 20 minute outage of a single Kafka node brought down the entire Kred cluster for hours. How could this happen?

## Kred and Kafka at Klarna

Let’s start with the 10,000 foot view: what is Kred and how does it rely on Kafka? I will try to be brief, but we have to cover some basics so everything else will make sense later.

To this very day Kred is a large monolith that has a lot of different responsibilities. There is one leader node in the Kred cluster, where all database transactions happen. The database of Kred is Mnesia, which is a popular choice for Erlang systems, since it is shipped together with the language runtime as part of Erlang/OTP. Unlike Postgres or MySQL, Mnesia is not a client-server system with a separate DB server, but it is running embedded in the application, just like SQLite.

Besides the leader there are a number of follower nodes in the cluster. They have their own copy of the database and can handle many requests on their own as read replicas. More importantly for us, they also do a lot of Kafka stream processing. The leader publishes events on a Kafka topic as it executes transactions. These events are in a compact, internal format, so they are cheap to generate. The followers consume these events, perform some CPU intensive transformations on them and publish the results to a large number of other Kafka topics where other Klarna systems can pick them up.

Finally, Kred also uses Kafka for exporting some monitoring data from the system. Besides conventional log messages and metrics, Kred also generates a report about the busiest processes within the Erlang VM every two seconds (process id, CPU and memory usage, message queue length, current function and such). This data has tremendous value when investigating problems, but does not fit well into the data model of our run-of-the-mill monitoring tools, such as Graphite or Splunk, so we use a custom pipeline for handling it. The first step in this pipeline is a Kafka topic, where every Kred node publishes these monitoring events.

This architecture has a few implications on Kred:

-   The leader can become quite busy, as it single-handedly executes every single transaction. To mitigate this, we try to off-load any task that doesn’t require a transaction to ordinary follower nodes.
-   Kred needs a huge amount of RAM. We want to keep as much of the data as possible in memory, to make querying it fast. Kred is currently running on `r5n.24xlarge` instances with 768 GB of memory, but not so long ago we had on-prem physical machines packed with 2 TB. Those were the days!
-   Kred nodes need to be treated a bit like pets, not cattle. It is a shame, and we are working on it, but we are not quite there yet. Although everything is provisioned with scripts, and the nodes are in fact recreated from time to time, this operation is just… _slooow_. Since all nodes have a complete copy of the entire database, spinning up a new one means copying tens of TB-s worth of EBS volumes. Even just restarting an existing one leads to waiting several minutes for reading data into memory, followed by syncing database updates from the leader since the node went off-line. Depending on how long ago that was, syncing can take anywhere between a few minutes and several hours. This means in case of an incident, we prefer to investigate what’s happening on the nodes and fixing the issues in place, instead of just deploying a fresh copy of the service.

At the end of the day, Kafka is not a critical dependency of Kred. An outage could lead to events being delayed, but they won’t get permanently lost. All communication through Kafka is designed to be asynchronous and tolerate such delays.

## The Kafka incident

But that’s enough background! Let’s see the incident. The inexplicable, the shameful, the one thing that shouldn’t have ever happened: a humble dependency killing our mighty Kred.

The chain of sad events started with a routine maintenance on the Kafka cluster going South. One of the Kafka nodes was terminated `kill -9` style, and Kafka failed to quickly elect a new leader for the partitions it handled. This affected <5% of the partitions, but caused smaller or bigger issues in almost every service at Klarna.

Immediately as the Kafka node went down memory usage started to grow on all Kred nodes. This is how the memory usage looked like, reported by both the OS and the Erlang VM:

![Memory reported by the OS starts growing at the beginning of the Kafka outage at a steady rate. At the same time the Erlang VM stops reporting memory metrics. 20 minutes later there is a sudden memory spike reported by the OS, and the node runs out of memory. Kred is killed by the OOM killer, then gets restarted at which point both OS and Erlang memory metrics return to normal values.](https://miro.medium.com/max/914/1*AJq7bc2YB9uIBsrSrhYkbA.png)

Memory growth on the leader node during the Kafka outage

At first look the memory growth seemed reasonable: `brod` (the Kafka client library used in Kred) will keep unsent messages in memory while Kafka is unavailable. But we were worried that if the incident goes on for too long, we may run out of memory on the hosts. `brod` wouldn’t keep buffering messages indefinitely, it gives up waiting for Kafka after hitting a limit. But how high is that limit? Do we have enough memory to fit this buffer limit times the number of affected partitions? We were not sure.

So we started doing what we always do during incidents: keeping a close eye on metrics and shutting down misbehaving parts of Kred before they would cause bigger trouble. And that’s when things became very-very strange:

-   We lost all metrics reported by Kred itself. The OS (`collectd` in particular) kept reporting basic CPU and memory usage from the hosts, but our own, much more detailed metrics that would provide Erlang process and application level insight into what Kred is doing went silent immediately as the incident began.
-   We could not connect with a shell to the Erlang VM. This meant we could not tweak the application, inspect its state with manual commands or stop/restart parts of it.
-   At the same time, logs kept flowing out of Kred, and it looked like the system kept serving requests as usual. Although the number of DB transactions was declining steadily, it was not clear at the time whether that was due to increasing memory pressure, something blocking transactions within Kred, or simply our clients being hit by the same Kafka outage and thus not sending so many requests to begin with.

19 minutes after the start of the incident came a sudden turn of events. Up to that point logs were flooded with `brod` complaining about not being able to connect to the failed Kafka node, or the Kafka cluster not having a leader for the partitions previously assigned to said node. These messages just stopped. Kafka started to work again, and the number of DB transactions skyrocketed. For the next minute, the Kred leader seemed to be super busy, but healthy. Well, almost healthy, since we still didn’t have any metrics or shell access.

This state lasted for a single minute however. After that came a huge memory spike and the out of memory killer terminated the leader node. As intended in this case, one of the followers promptly took over as the new leader, however this node was unable to execute a single DB transaction. We don’t have many log messages from this period that could shed some light on what happened, except for a handful of transactions that had a timeout on them. For some reason, these transactions all timed out while executing the same function:`code_server:call/1`.

It soon became clear that our only option for restoring the Kred cluster to a working state was to kill the remaining nodes and restart them in a clean state. The Kafka cluster was healthy again, and we had to be careful not to overload the cluster before it reached its full capacity, but this was routine work and nothing unusual happened. The incident was over, but it left behind a lot of question marks. What did actually go wrong? Could it happen again? Under what conditions?

## Huge crash logs

It was quite obvious that the Kafka outage triggered the incident. However, it was not clear exactly how: this was definitely not the first time Kafka suffered some transient issues, yet we had never before lost the leader node, nor had we seen a newly elected leader being paralysed after a takeover like this time. So there had to be some other contributing factors too.

The first question when trying to investigate an issue like this is whether you can reliably reproduce the problem in a test environment. This is not a trivial task due to the large number of unknown factors:

-   How did Kafka behave during the outage? Were the nodes simply inaccessible or did it maybe reply in an unusual way to status queries?
-   Kred is always busy in production, does it matter what it was doing exactly? Maybe there was a traffic spike on some particular interface? Or could an unusually demanding one-time job run at that specific time? There are some rarely used jobs, such as generating big reports or batch-updating thousands of purchases that finance or customer support executes on Kred maybe once a month at unpredictable times.
-   It could also happen that the job was demanding not on Kred’s side, but on Kafka. What if we happened to generate some GB-sized messages and that was the trigger?
-   Or maybe using the full-size production DB was an important factor?
-   Could it be that it is impossible to reproduce this particular problem on a developer’s laptop with 32 GB RAM, because you would run out of memory way before triggering the bug?

We have a test environment where we could restore the actual production DB from a backup on identical EC2 instances to what we use in production, and we had tools to send at least the most common types of requests to Kred. The Kafka team soon confirmed that their node had been simply down, inaccessible from the network, and they had no knowledge of the cluster reporting some unusual state. We could simulate the Kafka outage by simply cutting traffic to the Kafka nodes using the firewall:

Cutting traffic to the Kafka nodes using the firewall

It quickly turned out that the simulated Kafka outage did not result in the continuous growth of memory usage we experienced in production, yet we faced the same strange phenomena, like losing metrics and not being able to connect with a shell to the Erlang VM. What was going on here?

The first ideas we had were all related to memory growth caused by `brod`. In particular the huge crash messages produced by the `brod_producer` process. `brod_producer` is a `gen_server` that is managing messages produced on a Kafka partition. It buffers the messages in its state until Kafka acknowledges them and retries sending them in case of an error. But if it fails to send the messages a given number of times (for example in case of a prolonged Kafka outage), it gives up and crashes. And whenever a `gen_server` crashes, its state is logged in a crash message. The problem is, in case of this server the state also includes all of those buffered messages too. If we have 1 GB worth of messages in the buffer, the log message will contain that 1 GB of binary data pretty printed. But how hard could it be to pretty print that much data? Well, in case of Erlang, it can be _very_ demanding.

First of all, binary data is pretty printed as comma separated decimal numbers. For example the 4 byte hexadecimal value `0xdeadbeef` becomes `<<222,173,190,239>>`. Even without the `<<>>` wrapping, each byte is represented on average on 2.57 digits, or 3.57 characters if we count the comma too. But if the data is stored as a string in Erlang, it will be encoded as a list of Unicode code points, requiring 2 words of storage per list element. So our initial 1 GB binary data pretty printed as a string will take about 1 GB × 3.57 characters/byte × 2 words/character × 8 bytes/word = 57.12 GB memory. This is quite concerning, even though the system libraries are _probably_ smarter than to store the entire formatted error message as a string in memory.

We also very quickly found [this bug report](https://github.com/kafka4beam/brod/issues/443) on `brod` complaining about Erlang running out of memory when `brod_producer` exits. This find confirmed our suspicion about huge crash logs and we implemented two fixes:

1.  Applying the proposed fix for the `brod` ticket on our version (at that time the fix wasn’t yet merged to `master`).
2.  Limiting the depth of pretty printing the `gen_server` states in crash logs. It’s a pretty common strategy, and means that instead of pretty printing an entire 1 GB binary blob the logger would only print a prefix of it: `<<222,173,190,...>>`. (The reason we didn’t use a depth limit originally is that it’s just too easy to set the limit too low and get useless crash logs that only contain meaningless headers and cut off the parts that would reveal the actual root cause: `{"result": "error", "message": "The request failed beca...` — thank you, depth limit, this was indeed useful to know!)

In theory, either 1. or 2. should be enough to mitigate the out of memory issue. But it turned out that only fix 1. could solve the issue on Kred, implementing fix 2. had no effect at all. What’s going on here?

## Scheduler lock-ups

In fact, the huge crash logs theory failed to explain a lot of the strange symptoms we experienced during the incident. Starting from the loss of metrics to the new leader being unable to execute any transactions. On the other hand, all of these things can be explained by a _scheduler lock-up_.

Erlang achieves concurrency via its schedulers. For each CPU core the Erlang VM starts one scheduler thread. A scheduler thread in turn handles a bunch of Erlang processes. The scheduler is preemptive: it takes away the CPU from an Erlang process after executing a given number of operations (called _reductions_ in Erlang lingo). This preemptiveness makes the VM responsive even when there are many times more Erlang processes than CPU cores (schedulers). In fact, the entire VM is designed around the assumption that Erlang processes won’t keep running for too long. For example, inspecting a process with `erlang:process_info/2` is only possible when that process is not scheduled to run, but that’s fine: `process_info` won’t block the caller for long because the inspected process is guaranteed to be scheduled out in a couple of milliseconds worst case. Similarly, inspecting the VM-s global state with functions like `erlang:system_info/1` typically requires all schedulers to stop doing any work (so you can get a consistent view of the system), but that’s also fine, since those schedulers would preempt the currently executing process soon anyway.

Keeping Erlang processes preemptible and thus the system responsive is easy on the level of interpreting Erlang byte-code (or running the JIT-ted code in OTP 24). The real challenge is implementing all the lowest-level operations of the VM as C functions with responsiveness in mind: if the input is too large, instead of processing it in one go, the function must be able to stop executing after some bounded time and return some state suitable for continuing its execution at a later time, when the Erlang process gets a slice of the CPU again. This is of course only a concern for operations taking variable sized input — but as it turns out, most operations are like that. Even simple arithmetic, such as `+` and `—` can run indefinitely long, as integers in Erlang are not bounded to a fixed number of bits, they can grow arbitrarily large. The developers of the BEAM VM made an exceptionally good job keeping all the low level operations responsive, but there may be some edge cases that they missed. A more likely scenario is that the author of an Erlang library decided to implement some performance critical portion of the code as a C function too (called a Natively Implemented Function, or NIF for short), but wasn’t as careful as the VM developers.

So in theory a bug in the VM or a NIF may cause a scheduler to lock-up for a long time. When this happens, it doesn’t only affect the process that was currently executing on the scheduler. None of the other processes assigned to this scheduler will be able to progress either. In fact, the lock-up can start spreading across the VM like cancer:

-   First, the processes assigned to the scheduler get blocked.
-   Then other processes trying to call `erlang:system_info/1` or `erlang:process_info/2` on a blocked process get blocked, most likely locking up their scheduler too.
-   Furthermore, all processes that wait for a response from a locked up process (or for a Mnesia lock held by a locked up transaction process) get blocked. This will at least not lock up their scheduler, but would still prevent the system from making any progress.
-   Finally, there’s a process in most Erlang systems that acts as a liveness probe. It has to periodically send heartbeat messages to a watchdog called `heart`. If the lock-up would hit the liveness probe, it’s just a matter of time for `heart` to declare the Erlang node unresponsive and restart it. That’s the usual destiny of nodes with really long running scheduler lock-ups.

So how would a scheduler lock-up explain the strange phenomena during the incident?

## Losing the metrics

This is the most obvious one: there are many metrics based on VM-level statistics or `process_info` data collected from each and every process in the system. Any scheduler’s lock-up would instantly freeze the collection of these metrics, and (due to some unlucky design decisions in our code) the reporting of all the other metrics too. Except for `collectd` of course, that runs independently of the Erlang VM.

## Memory growth

The slow build-up of memory and gradual decline of number of transactions can be also caused by the lock-up hitting a transaction process holding some frequently needed lock. From that point on, more and more transactions will also hang waiting for this lock, but consuming some resources on the node.

## Opening a new shell fails

This isn’t really obvious at all: how could a scheduler lock-up prevent opening a remote shell towards a node? Well, an Erlang shell greets you with some VM-level statistics on the number of schedulers and such:

Greeting text of an Erlang shell

Could it be that printing this greeting line needs a lock across all schedulers? I don’t know, but we can test it with a little help from the debugger! We need to use `gdb` in non-stop mode, allowing all threads to continue except the single one we want to debug:

Using gdb in non-stop mode

Now the debugger is attached, and all the threads are running. The Erlang shell shall remain responsive. Let’s see what threads we have!

Threads in an Erlang VM

The first thing to note is that the Erlang VM uses quite a large number of threads! Luckily, they all have nice names, so we can figure out what do they do. My laptop has a 4 cores/8 threads CPU, so Erlang starts 8 schedulers for running Erlang code. It also starts 8 dirty CPU schedulers and 10 dirty I/O schedulers — these are for running NIF-s that are not preemptible and would become either CPU or I/O limited.

Let’s pick one of the scheduler threads and interrupt it with `gdb`! Don’t pick the first one though, the shell process is most probably running on that scheduler and we want it to stay responsive.

Interrupting a scheduler thread with gdb

You can type something into the Erlang shell now and see that it still works. But what happens if we would like to start a new remote shell?

Starting a remote shell fails when a scheduler hangs

Well, it looks like the greeting line can be printed without issues… However that’s all that works, the shell itself failed to start for some other reason. This test is all we need to confirm that a scheduler lock-up could cause the problem with remote shells we experienced.

## New leader is paralysed

After the original leader died, its newly elected replacement was in service for several minutes, yet it failed to execute a single DB transaction. Can a scheduler lock-up explain this strange behaviour?

The smoking gun in this mystery is that all the transactions that timed out were blocked in a `code_server:call/1`, which suggests they were probably trying to load a new module, but this operation (which is normally very fast) just hang indefinitely. Trying to load a new module makes sense: in Kred, modules are loaded only when they are needed the first time. Since the leader has some exclusive tasks (including actually executing transactions), it has more modules loaded than a follower. The node that took over had to load these modules too. But can a scheduler lock-up block module loads too?

Well, I’m not familiar with the internals of loading a module on the Erlang VM, but it’s quite intuitive that this is an operation that can only be performed when no processes are scheduled to run. Loading a module is a complex task that cannot be performed in a single, atomic step. Yet, the VM needs to perform it atomically: Erlang processes cannot find a module in a half-loaded state, because there is no such state defined in the language.

Let’s try to validate this intuition with the debugger! This time it is not enough to interrupt an idle scheduler thread, we need to catch the thread in the state where it’s executing a process. So let’s write an Erlang function that will keep spinning on the scheduler of our choice:

Erlang function that will keep scheduler 6 spinning

The `scheduler` is a little known and undocumented process flag. It lets you bind a process to a specific scheduler, and comes very handy for us. So what happens now if we interrupt the `6_scheduler` thread from the debugger a second time?

Interrupting a busy scheduler thread with gdb

First of all, the thread was interrupted in `process_main`, which means it is indeed busy. Last time it was caught in the middle of a `syscall`, which typically means the scheduler was idle and waiting for something interesting to happen. Second, any attempt to load a new module from the Erlang shell (such as `c:l(crypto)`) will block. Theory confirmed!

## Hunting for a scheduler lock-up

Let’s summarise what we know at this point!

-   The node run out of memory and crashed when the `brod_producer` process gave up communicating with Kafka, and although memory use grew constantly over time, the killer spike came at the end. This suggests the memory was allocated when logging the crash report.
-   All the other strange behaviours can be explained with a scheduler lock-up trashing the responsiveness of the VM, which is at the centre of its design. This lock-up went on for tens of minutes.

So what exactly was this operation that is part of logging a crash report and can lock up a scheduler for tens of minutes and eventually culminate in the allocation of 200–300 GB of memory at least?

The first thing we tried was just staring at the code (both in `brod` and the Erlang/OTP `stdlib` too) trying to find any operation that is implemented in C code and could potentially receive an unusually large argument. Some years ago list operators such as `++` and `--` were the usual suspects to blame, plus pretty printing large terms seemed likely to contain such code. But nothing came up: no list operators, and pretty printing large lists, tuples and maps is nowadays implemented 100% in Erlang, without calling any built-ins.

OK, next attempt: back to the debugger! Cut the network towards Kafka, and after a short while stop all traffic going to the node. So hopefully there will be only one scheduler thread busy, the one hit by the scheduler lock-up. And indeed there was one interrupted in the middle of a built-in function of the VM:

Backtrace of a locked up scheduler

The top of the stack points to [this function](https://github.com/erlang/otp/blob/ea2b0b819fb0c6c703c6699ce45b5a4c7c4f70b4/erts/emulator/beam/copy.c#L133-L146) in the VM’s source code. The next item confirms that the built-in is the message sending operator, `!`. Sending a message is necessary, because the crash report is not printed by the actual crashing process. That one simply sends over data (such as its state) to a logger process that will format the message. Sending messages is typically very fast in Erlang, and looks like everybody forgot to make the `!` operator preemptive. Or at least this `size_object_x` function, which seems to calculate how much memory to allocate on the recipient process’ heap for the message (processes in Erlang all have their separate heaps, which makes Garbage Collection (or GC for short) very cheap, but means message passing will involve copying memory).

What was also very unusual is that the `sum` variable in the `size_object_x` function, which holds the number of memory words counted, was already standing above 90 GigaWords, which in case of a 64 bit VM means 720+ GB of memory. It is clear that trying to allocate 720 GB for the message on the recipient’s side would crash the node, but how was the sender holding such a huge term to send in the first place?

This question seemed difficult to answer using the debugger, because one would have to understand what Erlang terms are on the crashing process’ heap from looking at their raw binary form. Inspecting terms would be much more convenient from an Erlang shell. So would it be possible to reproduce the issue but stop right before the process would crash?

## A minimal example

Let’s try to reproduce the strange state that fits into memory but cannot be sent in a message using `brod`'s own test environment! The [readme](https://github.com/kafka4beam/brod#readme) file of the [brod project](https://github.com/kafka4beam/brod) gives simple instructions on how to start a Kafka cluster in Docker containers and how to produce some messages on a topic.

Producing a message using brod’s test environment

We only had to modify slightly the producer’s configuration so it won’t crash too early when Kafka is unavailable. Executing the snippet will print the message from our `AckCb` function: `Produced to partition 0 at base-offset 0`.

Let’s cut traffic towards Kafka and produce 30 more of these messages! These will be buffered by the `brod_producer` process. And even though the size of these 30 messages is negligible (the entire Erlang VM uses ~22 MB), attempting to send the producer process’ state to the shell process (via the `sys:get_state/1` function) would cause a scheduler lock up for several minutes and end in a memory allocation error:

Sending the producer’s state with 30 messages in the buffer crashes the VM

So the <22 MB state of this process blew up to 2.7 TB somehow. Let’s see how the state looks like if we only buffer 10 messages:

State of the brod\_producer with 10 messages in the buffer

It doesn’t seem to be extraordinary large. The message buffer itself (the `#buf{}` record) stores 9 `#req{}` request records in the `buffer` field, and the 10th in the `onwire` field. Nothing unexpected here either. However,`brod` uses a lot of anonymous functions (or _funs_ for short), and those funs themselves can contain so called _free variable_s, values that were captured from the context where the fun closure was created. We can inspect the funs with `erlang:fun_info/1`:

A callback fun created by brod

The free variables are listed under `env`, and they don’t seem right: why does the fun contain the entire `#state{}` record of the producer process? Let’s see how this fun is created! The information also contains the `name` of the anonymous fun: `-handle_info/2-fun-0-`, which tells us this is the first fun defined within the `brod_producer:handle_info/2` function:

The handle\_info clause that defines the #req.cb fun

The fun refers to `Pid`, `CallRef` , `AckCb` and `State#state.partition` from its defining context. So even though it only needs the partition number from the `State` record, it accidentally captures the entire `State` as a free variable!

And how does this explain the memory expansion? To understand this, you need to know how Erlang actually stores terms in memory.

## Memory layout of Erlang terms

The Beam Book from Erik Stenman contains an [excellent chapter](https://blog.stenmans.org/theBeamBook/#CH-TypeSystem) about Erlang’s type system and the representation of types in memory. I really encourage you to read it, but the _tl;dr_ version is that Erlang term’s are either _immediates_, meaning they fit into one machine word, or _boxed_, if they need more storage space. Typical immediates are atoms, small integers and the empty list. Typical boxed terms are for example tuples: a tuple needs one word for a header that identifies the memory area as a tuple and tells the size of it; plus one word per tuple element. If a tuple element is an immediate, the immediate’s value is written directly into the tuple’s memory area. Otherwise the element is a boxed term, and the tuple will only contain a pointer to the boxed term’s header word in memory.

The below diagram shows the in-memory representation of the example term `T = {foo, 42, {1, []}, false}`, where each memory word is labelled as _H_ for header, _I_ for immediate or _B_ for boxed:

![](https://miro.medium.com/max/738/1*muDQe0l6mbGSN1qUlsnT1Q.png)

The term `{foo, 42, {1, []}, false} laid out in memory`

Erlang terms are immutable, so an expression like `setelement(4, T, true)` would not modify the existing `T` tuple, but would create a copy of it (dark green marks the newly created term on the heap):

![](https://miro.medium.com/max/1396/1*FK_4PHuCQIOzfGBnNl5nWg.png)

“Updating” a term creates a new copy

Please note that the newly created 4-tuple still references the same`{1, []}` tuple as the original. `setelement/3` only performed a shallow copy of the topmost tuple. By the way, the garbage collector may eventually have to copy these three boxed terms to new memory addresses, but it will preserve this property: these two 4-tuples will always point to the same `{1, []}` tuple on the heap.

For reference, this is how the state of the producer process looks like in the beginning, before buffering any messages:

![](https://miro.medium.com/max/1400/1*pWsMEZS6FzLTkPzdotea2A.png)

The initial state of the producer

You can see how boxed terms form a Directed Acyclic Graph (DAG). It is usually not a tree, because some sub-terms, such as the `<<"test-topic">>` binary are referenced from multiple places. However, this representation is a bit too detailed for our discussion. We are only interested in the `#state{}` and `#buf{}` records, plus the `#req{}` requests that will be added to the `buffer` and `onwire` lists within the `#buf{}`. So the initial state, with zero buffered messages will be simplified to this:

![](https://miro.medium.com/max/266/1*7f-wSzf-IxU_M2m14QGxtQ.png)

Simplified view of the initial state

Calling `brod:produce_cb/6` the first time will add a `#req{}` to the `onwire` list (dark green marks the newly created terms, dashed lines connect elements of a list):

![](https://miro.medium.com/max/526/1*y_RyfDtNrtykFSYSLx1AFw.png)

S1: the state after producing 1 message

Since `R1` accidentally refers to `S0` instead of only the `#state.partition` field’s value (an immediate integer), the old version of state is still kept around and cannot be garbage collected. But the problem only gets worse as more messages are produced and buffered:

![](https://miro.medium.com/max/560/1*7swxm3wI5k-O0gbbMubmIQ.png)

S2: the state after producing 2 messages

The second request didn’t go to the `onwire` list but stays in the `buffer`: the producer hasn’t tried to send it to Kafka yet (it’s still waiting for a response to `R1` ). But `S2` and `S1` share the same `onwire` list (this term hasn’t changed), so `S0` is now reachable via two distinct paths from `S2`:

-   `S2 → B2 → R1 → S0`
-   `S2 → B2 → R2 → S1 → B1 → R1 → S0`

Adding the third produce request creates even more paths:

![](https://miro.medium.com/max/630/1*vm0t05zyBedc4QIdqCs1Zg.png)

S3: the state after producing 3 messages

The third request goes to the `buffer` as well. It will be the head of a list that has the `buffer` from `B2` as its tail. At the end we have 4 different paths to `S0` and two to `S1` from the top of the graph. Without the accidental references to the previous versions of the state the graph could be much simpler:

![](https://miro.medium.com/max/838/1*0SjaprP6JQXkvw4AU-dKwA.png)

The desired state after producing 3 messages

## The end of the investigation

OK, having a lot of extra edges and references to previous versions of the state is not helping the garbage collector, but overall the memory growth is still minimal, isn’t it? We only add one new `#state{}`, `#buf{}` and `#req{}` records plus one more list cell to the heap in every step. How could this bug lead to `size_object_x` locking up a scheduler for tens of minutes?

The problem is that unlike the garbage collector, when message passing needs to copy a term it will not recognise the same boxed term appearing in the graph multiple times. If you refer to the same boxed term twice, the receiver of the message will get two copies of it (unless you configure and compile the VM with the `--enable-sharing-preserving` option). This means that while the heap of the original process will only grow linearly, the message size will grow exponentially with the number of buffered produce requests! Remember how there were two distinct paths from `S2` to `S0`, but four paths from `S3` to `S0`? Every such path means a new copy of `S0` in the message.

And this concludes the investigation: a simple mistake in the `brod_producer` code caused buffered produce requests to contain references to the previous version of the process’ `#state{}` record. This meant that while Kafka was down, buffering messages had a tiny memory overhead in the producer process. But if Kafka was down for too long, and the producer gave up waiting, this little inefficiency was turned into an exponential growth issue. First, `size_object_x` caused a tens of minutes long scheduler lock-up while it was doing a depth-first traversal of every path in the DAG. Then, if the node survived the lock-up (remember, `heart` may declare a locked up node unresponsive and restart it), it will fail to allocate enough memory for the message.

There were only two conditions for this bug to trigger during a Kafka outage:

-   The outage shall last long enough for the`brod_producer` to give up.
-   The outage shall affect a busy partition, where the node attempts to publish a few dozen messages at least during the outage.

This time we were unlucky to have them both: the Kafka outage lasted for almost 20 minutes, and it hit the partition that all Kred nodes use for exporting monitoring data. This partition is both busy (monitoring data is collected every 2 seconds) and is used by all nodes in the cluster. That’s why all nodes in the cluster were affected. If the Kafka outage would have hit a different partition, that would have only affected those nodes that were publishing on that partition.

This investigation lead to two bug reports:

-   This [bug report and PR](https://github.com/kafka4beam/brod/pull/452) to fix `brod_producer` by simply extracting the `#state.partition` field before the fun is created. The fix was quickly accepted, which means users of `brod` are no longer in risk from this problem during Kafka outages.
-   This [issue](https://github.com/erlang/otp/issues/4773) to the Erlang/OTP project that points out 3 problems that could be improved in the language/VM to avoid this class of problems:

1.  The message sending operator is not preemptive.
2.  The compiler is not smart enough to perform the extraction of sub-terms at the time the fun is created. It adds the large super-term as a free variable instead.
3.  Message sending duplicates sub-terms referenced multiple times. (I learnt that this is a deliberate decision for performance reasons, but can be controlled with the `--enable-sharing-preserving` configuration option when compiling Erlang/OTP from source.)

But is there anything else we could learn from the hunt for this bug? For me it taught that when speculating about the root cause of a problem it is important to always do a sanity check: can the proposed explanation really explain all the experienced phenomena? In this case we initially focused on the memory growth, and even found a [mitigation](https://github.com/kafka4beam/brod/pull/444) that prevented the entire problem — but only by accident. Memory growth was not able to explain the rest of the issues, such as losing metrics, new remote shells crashing or more importantly the paralysis of the newly elected leader. On the other hand we could demonstrate that scheduler lock-ups indeed cause all of these problems. So the final root cause we identified is a better candidate, because it explains scheduler lock-ups, and scheduler lock-ups in turn explain all the problems we experienced. It is very unlikely there were more hidden problems lurking behind this incident.

Another important lesson is that although Erlang is a very high-level language, and most of the time we, Erlang developers, can debug our problems using high level tools provided by the platform, there are times when it is extremely valuable to have at least some level of understanding of the lower layers of your tech stack too. I’m a very beginner user of `gdb` and I’m not particularly familiar with the C source code of the Erlang VM either. But firing up `gdb` and using a few simple commands found on StackOverflow was enough to crack this case. And, of course, some knowledge about how terms are laid out as a DAG and what that implies to copying.

So I really encourage everyone to learn about the internals of their chosen platform. It _may_ be useful one day, but it will be _definitely_ fun! While I got your attention, maybe watch my [Code BEAM talk](https://codesync.global/speaker/daniel-szoboszlay313/#402useless-performance-optimisations-on-the-beam-for-fun-and-fun) next about the assembly language of Erlang, and whether we can beat the compiler by hand-written code: _Useless performance optimisations on the BEAM for fun and… fun?_