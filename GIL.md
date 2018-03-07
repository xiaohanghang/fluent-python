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

```py
# single_threaded.py
import time
from threading import Thread

COUNT = 50000000

def countdown(n):
    while n>0:
        n -= 1

start = time.time()
countdown(COUNT)
end = time.time()

print('Time taken in seconds -', end - start)
```

```
$ python single_threaded.py
Time taken in seconds - 6.20024037361145
```

As you can see, both versions take almost same amount of time to finish. In the multi-threaded version the GIL prevented the CPU-bound threads from executing in parellel.

The GIL does not have much impact on the performance of I/O-bound multi-threaded programs as the lock is shared between threads while they are waiting for I/O.

But a program whose threads are entirely CPU-bound, e.g., a program that processes an image in parts using threads, would not only become single threaded due to the lock but will also see an increase in execution time, as seen in the above example, in comparison to a scenario where it was written to be entirely single-threaded.

This increase is the result of acquire and release overheads added by the lock.







