# 协程

> yield item 这行代码会产生一个值，提供给next\(\)的调用方，此外，还会做出让步，暂停执行生成器，让调用方继续工作，直到需要使用另一个值的时候再调用next\(\)。调用方会从生成器中拉取值。此外，还会做出让步，暂停执行生成器，让调用方继续工作，直到需要使用另一个值的时候再调用next（）。调用方会从生成器中拉取值。
>
> 从句法上看， 协程与生成器类似， 都是定义体中包含 yield 关键字的函数。 可是， 在协程中， yield 通常出现在表达式的右边（例如， datum = yield） ， 可以产出值， 也可以不产出——如果 yield关键字后面没有表达式， 那么生成器产出 None。 协程可能会从调用方接收数据， 不过调用方把数据提供给协程使用的是 .send\(datum\) 方法， 而不是 next\(...\) 函数。 通常， 调用方会把值推送给协程。

* 生成器作为协协程使用时的行为和状态
* 使用装饰器自动预激协程
* 调用方如何使用生成器对象的.close\(\)和.throw\(\)方法控制协程。
* 携程终止时如何返回值
* yield from新句法的用途和语义
* 使用案例---使用携程管理仿真系统中的并发活动

# 生成器如何进化为协程

协程的底层架构在“PEP 342—Coroutines via EnhancedGenerators”（[https://www.python.org/dev/peps/pep-0342/）](https://www.python.org/dev/peps/pep-0342/）) 中定义， 并在  
Python 2.5（2006 年） 实现了。 自此之后， yield 关键字可以在表达式中使用， 而且生成器 API 中增加了 .send\(value\) 方法。 生成器的调用  
方可以使用 .send\(...\) 方法发送数据， 发送的数据会成为生成器函数中 yield 表达式的值。 因此， 生成器可以作为协程使用。 协程是指一  
个过程， 这个过程与调用方协作， 产出由调用方提供的值。

# 用作协程的生成器的基本行为

```py
>>> def simple_coroutine(): # ➊
...     print('-> coroutine started')
...     x = yield # ➋
...     print('-> coroutine received:', x)
...
>>> my_coro = simple_coroutine()
>>>  my_coro # ➌
<generator object simple_coroutine at 0x100c2be10>
>>> next(my_coro) # ➍
-> coroutine started
>>> my_coro.send(42) # ➎
-> coroutine received: 42
Traceback (most recent call last): # ➏
...
StopIteration
```

1. 协程使用生成器函数的定义：定义体中有yield关键字
2. yield在表达式中的使用：如果协程只需要从客户那里接收数据，那么产出的值是none-----这个值是隐喻指定的，因为yield关键字右边没有表达式
3. 与创建生成器的方式一样，调用函数得到生成器对象
4. 首先要调用next（）函数，因为生成器还没有启动，没有在yield语句处暂停，所以一开始无法发送数据，
5. 调用这个方法后， 协程定义体中的 yield 表达式会计算出 42； 现在， 协程会恢复， 一直运行到下一个 yield 表达式， 或者终止。    
6. 这里， 控制权流动到协程定义体的末尾， 导致生成器像往常一样抛出 StopIteration 异常。

> 因为 send 方法的参数会成为暂停的 yield 表达式的值， 所以， 仅当协程处于暂停状态时才能调用 send 方法， 例如 my\_coro.send\(42\)。 不
>
> 过， 如果协程还没激活（即， 状态是 'GEN\_CREATED'） ， 情况就不同了。 因此， 始终要调用 next\(my\_coro\) 激活协程——也可以调用
>
> my\_coro.send\(None\)， 效果一样。
>
> 最先调用 next\(my\_coro\) 函数这一步通常称为“预激”（prime） 协程（即， 让协程向前执行到第一个 yield 表达式， 准备好作为活跃的协  
> 程使用） 。

```py
>>> def simple_coro2(a):
...     print('-> Started: a =', a)
...     b = yield a
...     print('-> Received: b =', b)
...     c = yield a + b
...     print('-> Received: c =', c)
...
>>> my_coro2 = simple_coro2(14)
>>> from inspect import getgeneratorstate
>>> getgeneratorstate(my_coro2) ➊
'GEN_CREATED'
>>> next(my_coro2) ➋
-> Started: a = 14
14
>>> getgeneratorstate(my_coro2) ➌
'GEN_SUSPENDED'
>>> my_coro2.send(28) ➍
-> Received: b = 28
42
>>> my_coro2.send(99) ➎
-> Received: c = 99
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
StopIteration
>>> getgeneratorstate(my_coro2) ➏
'GEN_CLOSED
```



