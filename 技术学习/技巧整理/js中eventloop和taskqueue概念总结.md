[[Event loop microtasks and macrotasks]]

## [Summary](https://javascript.info/event-loop#summary)

A more detailed event loop algorithm (though still simplified compared to the [specification](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)):

1.  Dequeue and run the oldest task from the _macrotask_ queue (e.g. “script”).
2.  Execute all _microtasks_:
    -   While the microtask queue is not empty:
        -   Dequeue and run the oldest microtask.
3.  Render changes if any.
4.  If the macrotask queue is empty, wait till a macrotask appears.
5.  Go to step 1.

To schedule a new _macrotask_:

-   Use zero delayed `setTimeout(f)`.

That may be used to split a big calculation-heavy task into pieces, for the browser to be able to react to user events and show progress between them.

Also, used in event handlers to schedule an action after the event is fully handled (bubbling done).

To schedule a new _microtask_

-   Use `queueMicrotask(f)`.
-   Also promise handlers go through the microtask queue.

There’s no UI or network event handling between microtasks: they run immediately one after another.

So one may want to `queueMicrotask` to execute a function asynchronously, but within the environment state.
![[eventLoop-full.svg]]![[eventLoop.svg]]

macrotask: 
	setTimeout
	UI事件
microtask：
	promises
	queueMicrotask(func)

event loop中执行一项工作完成后，后继：
	microtasks，**批量**全部执行完毕
	render；确保所有microtask执行的环境一致，ui，io
	macrotask中的下一项task，取出，执行
	render
	继续eventloop