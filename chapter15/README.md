# 上下文管理器和else模块

> ###### **with语句会设置一个临时的上下文， 交给上下文管理器对象控制， 并且负责清理上下文。 这么做能避免错误并减少样板代码， 因此API更安全， 而且更易于使用。 除了自动关闭文件之外，with块还有很多用途。**
>
> ###### **for仅当for循环运行完毕时（即for循环没有被break语句中止）才运行else块。while仅当while循环因为条件为假值而退出时（即while循环没有被break语句中止） 才运行else块。try仅当try块中没有异常抛出时才运行else块。**在所有情况下， 如果异常或者return、break或continue语句导致控制权跳到了复合语句的主块之外，else子句也会被跳过。
>
> #### 上下文管理器对象存在的目的是管理with语句，就像迭代器的存在是为了管理for循环语句一样。 with语句的目的是为了简化try/finally模块。保证一段代码执行后执行某项操作。即便那段代码由于异常、return语句或者sys.exit（）调用而终止。也会执行的特定的操作。
>
> 1. #### 上下文管理器协议包含\_\__enter和\_\_exit \_\_两个方法。with语句开始时候会在上下文管理器对象上调用_\_\__enter\_\_方法。_with语句运行结束后， 会在上下文管理器对象上调用\_\_exit\_\_方法， 以此扮演finally子句的角色。
> 2. ### \#

## contextlib模块中的实用工具

> * **closing如果对象提供了close\(\)方法， 但没有实现\_\_enter\_\_/\_\_exit\_协议， 那么可以使用这个函数构建上下文管理器。**
>
> * **suppress构建临时忽略指定异常的上下文管理器**
>
> * **@contextmanager这个装饰器把简单的生成器函数变成上下文管理器， 这样就不用创建类去实现管理器协议了。**
>
> * **ContextDecorator这是个基类， 用于定义基于类的上下文管理器。 这种上下文管理器也能用于装饰函数， 在受管理的上下文中运行整个函数。**
>
> * **ExitStack这个上下文管理器能进入多个上下文管理器。with块结束时，ExitStack按照后进先出的顺序调用栈中各个上下文管理器的\_\_exit\_\_方法。 如果事先不知道with块要进入多少个上下文管理器， 可以使用这个类。 例如， 同时打开任意一个文件列表中的所有文件。显然， 在这些实用工具中， 使用最广泛的是@contextmanager装饰器， 因此要格外留心。 这个装饰器也有迷惑人的一面， 因为它与迭代无关， 却要使用yield语句。 由此可以引出协程， 这是下一章的题。**

## 使用@comtextmanager

@comtextmanager装饰器能减少上下文管理器的样板代码量，因为不用编写一个完整的类，定义一个_enter和_exit方法，而是只需要有一个yield语句的生成器，生成想让_enter 方法返回的值就行。_

> 在使用@contextmanager装饰的生成器，yield语句的作用是把函数的定义体分成两部分：yied语句前面的所有代码在with块开始的时候执行\(即解释器调用enter方法的时候执行\)yiedy语句后面的代码在with块结束时候\(即调用\_\__enter _\_\_方法执行\).
>
> 下面举个例子：
>
> ```py
> import contextlib
> @contextlib.contextmanager 
> def looking_glass():
> import sys
> original_write = sys.stdout.write 
> def reverse_write(text): 
> original_write(text[::-1])
> sys.stdout.write = reverse_write 
> yield 'JABBERWOCKY' 
> sys.stdout.write = original_write 
>
>
>
> #测试
> >>> from mirror_gen import looking_glass
> >>> with looking_glass() as what: ➊
> ... print('Alice, Kitty and Snowdrop')
> ... print(what)
> ...
> pordwonS dna yttiK ,ecilA
> YKCOWREBBAJ
> >>> what
> 'JABBERWOCKY
> ```





