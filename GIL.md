# GIL

> ## The Python Global Interpreter Lock or GIL,in simple words, is a mutex \(or a lock\) that allows only one thread to hold the control of the Python interpreter.

```py
>>> import sys
>>> a = []
>>> sys.getrefcount(a)
```

In the above example,the reference count for the empty list object \[\] was 3.The list object was referenced by** a,b** and the arguement passed to **sys.getrefcount\(\).**

Back to the GIL：

The problem was that this reference count variable needed protection from race conditions where two threads increase or decrease its value simultaneously.If this happens,it can cause either leaked memory that is never released or,even worse,incorrectly release the memory while a reference to that object still exists.This can cause crashes or other "werid" bugs in your Python programs.







