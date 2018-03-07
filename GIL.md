# GIL

> ## The Python Global Interpreter Lock or GIL,in simple words, is a mutex \(or a lock\) that allows only one thread to hold the control of the Python interpreter.

```py
>>> import sys
>>> a = []
>>> sys.getrefcount(a)
```

In the above example,the reference count for the empty list object \[\] was 3.The list object was referenced by** a,b** and the arguement passed to **sys.getrefcount\(\).**

Back to the GIL：

##### The problem was that this reference count variable needed protection from race conditions where two threads increase or decrease its value simultaneously.If this happens,it can cause either leaked memory that is never released or,even worse,incorrectly release the memory while a reference to that object still exists.This can cause crashes or other "werid" bugs in your Python programs.

**This reference count variable can be kept safe by adding**_**locks**_**to all data structures that are shared across threads so that they are not modified inconsistently.But adding a lock to each object or groups of objects means multiple locks will exist which can cause another problem—Deadlocks \(deadlocks can only happen if there is more than one lock\). Another side effect would be decreased performance caused by the repeated acquisition and release of locks.The GIL is a single lock on the interpreter itself which adds a rule that execution of any Python bytecode requires acquiring the interpreter lock. This prevents deadlocks \(as there is only one lock\) and doesn’t introduce much performance overhead. But it effectively makes any CPU-bound Python program single-threaded.**

**The GIL, although used by interpreters for other languages like Ruby, is not the only solution to this problem. Some languages avoid the requirement of a GIL for thread-safe memory management by using approaches other than reference counting, such as garbage collection.**

**On the other hand, this means that those languages often have to compensate for the loss of single threaded performance benefits of a GIL by adding other performance boosting features like JIT compilers.**

## Why was the GIL chosen as the solution? {#why-was-the-gil-chosen-as-the-solution}

> Python has been around since the days when operating systems did not have a concept of threads. Python was designed to be easy-to-use in order to make development quicker and more and more developers started using it.
>
> A lot of extensions were being written for the existing C libraries whose features were needed in Python. To prevent inconsistent changes, these C extensions required a thread-safe memory management which the GIL provided.
>
> The GIL is simple to implement and was easily added to Python. It provides a performance increase to single-threaded programs as only one lock needs to be managed.
>
> C libraries that were not thread-safe became easier to integrate. And these C extensions became one of the reasons why Python was readily adopted by different communities.
>
> As you can see, the GIL was a pragmatic solution to a difficult problem that the CPython developers faced early on in Python’s life.

## The impact on multi-threaded Python programs {#the-impact-on-multi-threaded-python-programs}

When you look at a typical Python program—or any computer program for that matter—there’s a difference between those that are CPU-bound in their performance and those that are I/O-bound.

CPU-bound programs are those that are pushing the CPU to its limit. This includes programs that do mathematical computations like matrix multiplications, searching, image processing, etc.

I/O-bound programs are the ones that spend time waiting for Input/Output which can come from a user, file, database, network, etc. I/O-bound programs sometimes have to wait for a significant amount of time till they get what they need from the source due to the fact that the source may need to do its own processing before the input/output is ready, for example, a user thinking about what to enter into an input prompt or a database query running in its own process.

Let’s have a look at a simple CPU-bound program that performs a countdown:





