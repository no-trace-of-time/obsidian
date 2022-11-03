![](https://www.educative.io/api/edpresso/shot/5741998548975616/image/5038563033874432)

Git is undoubtedly a popular version control system. When you adopt the best practices in Git, it turns into a potent and flexible tool. In this shot, you will learn what _Git mirroring_ is and why you need it. I will also share a step-by-step guide for _Git mirroring_.

Before we delve into more details, let’s see why you need Git mirroring.

## Why Git mirroring

You can start a project privately and decide to open-source it on GitHub later on.

### How can you do that without losing commit history?

Let’s try another example where you want to upgrade your repository server or try and move your Git repo between two servers without losing its history, branches, etc.

### What will you do?

`Git mirroring` can help you answer these questions.

## What is Git mirroring

**Git mirroring** is when a mirror copies the refsanything pointing to a commit & the remote-tracking branches. It’s supposed to be a functionally identical copy that is interchangeable with the original.

### Syntax

```
git clone --mirror old_repo_url destination_repo
```

In simple words, it will do the following:

-   All the refs will be available in target as-is.
-   You will get all the tags.
-   You will get all the local branches.
-   All the remote-tracking branches will be available in the target.

## How to use Git mirror

Its a four-step process – I will explain the step-by-step procedure of how you can do that.

### Step 1

The first step is straightforward. You have to mirror the Git repository to your local machine. You can use this command:

```
git clone --mirror <source_repo_url>
```

So, the above Git command will create a directory with your repository name.

### Step 2

Now, you can create a new repository on your **destination server**. You can use any name on the destination server, but it helps to use the same name (for simplicity).

### Step 3

It is time to set the _new server URL_ in your Git repo. You can run the command from inside your git repository folder:

```
git remote set-url --push origin <destination_repo_url>
```

### Step 4

Time to push your repository:

```
git push --mirror
```

By running the above Git command, you are pushing the complete Git repository with all its Commit history and Branches.

## Conclusion

With the Git Mirror option, you can save your valuable commit history with other important refs and remote-tracking branches.

The days are gone when developers would manually copy the code from one server to another.

I hope this simple shot helps you.