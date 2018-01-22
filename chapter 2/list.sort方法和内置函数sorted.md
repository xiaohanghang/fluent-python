## list.sort方法和内置函数sorted

> **list.sort方法会就地排序列表， 也就是说不会把原列表复制一份。 这也是这个方法的返回值是None的原因， 提醒你本方法不会新建一个列表。 在这种情况下返回None其实是Python的一个惯例： 如果一个函数或者方法对对象进行的是就地改动， 那它就应该返回None， 好让调用者知道传入的参数发生了变动， 而且并未产生新的对象。 例如，random.shuffle函数也遵守了这个惯例。**
>
> **list.sort相反的是内置函数sorted， 它会新建一个列表作为返回值。 这个方法可以接受任何形式的可迭代对象作为参数， 甚至包括不可变序列或生成器 。 而不管sorted接受的是怎样的参数， 它最后都会返回一个列表。**

## 1.1用bisect来管理已经排序的序列

bisect模块包含两个主要的函数，bisect和insort，两个函数都利用二分查找来在有序序列查找或者插入元素。

> 在haystack（干草垛） 里搜索needle（针） 的位置， 该位置满足的条件是， 把needle插入这个位置之后，haystack还能保持升序。 也就是在说这个函数返回的位置前面的值， 都小于或等于needle的值。 其中haystack必须是一个有序的序列。 你可以先用bisect\(haystack, needle\)查找位置index， 再用haystack.insert\(index, needle\)来插入新值。 但你也可用insort来一步到位， 并且后者的速度更快一些。

```py
import bisect
import sys
HAYSTACK = [1, 4, 5, 6, 8, 12, 15, 20, 21, 23, 23, 26, 29, 30]
NEEDLES = [0, 1, 2, 5, 8, 10, 22, 23, 29, 30, 31]
ROW_FMT = '{0:2d} @ {1:2d} {2}{0:<2d}'
def demo(bisect_fn):
    for needle in reversed(NEEDLES):
        position = bisect_fn(HAYSTACK, needle) ➊
        offset = position * ' |' ➋
         print(ROW_FMT.format(needle, position, offset)) ➌
if __name__ == '__main__':
    if sys.argv[-1] == 'left': ➍
        bisect_fn = bisect.bisect_left
    else:
        bisect_fn = bisect.bisect
    print('DEMO:', bisect_fn.__name__) ➎
    print('haystack ->', ' '.join('%2d' % n for n in HAYSTACK))
    demo(bisect_fn)
```

![](/assets/GQ~%`PPX_%E%29RF702Z$W{56.png)

```py
>>> def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
... i = bisect.bisect(breakpoints, score)
... return grades[i]
...
>>> [grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]
['F', 'A', 'C', 'C', 'B', 'A', 'A']
```

## 1.2用bisect.insort插入新元素

排序很耗时， 因此在得到一个有序序列之后， 我们最好能够保持它的有序。bisect.insort就是为了这个而存在的。insort\(seq, item\)把变量item插入到序列seq中， 并能保持seq的升序顺序。

```
import bisect
import random
SIZE=7

random.seed(1729)
my_list = []
for i in range(SIZE):
    new_item = random.randrange(SIZE*2)
    bisect.insort(my_list, new_item)
    print('%2d ->' % new_item, my_list)
    
```

![](/assets/4@XKR2SY%29O70RM@T1%29BNK~7.png)

## 1.3当列表不是首选

> **虽然列表既灵活又简单， 但面对各类需求时， 我们可能会有更好的选择。 比如， 要存放1000万个浮点数的话， 数组（array） 的效率要高得多， 因为数组在背后存的并不是floa对象， 而是数字的机器翻译， 也就是字节表述。 这一点就跟C语言中的数组一样。 再比如说， 如果需要频繁对序列做先进先出的操作，deque（双端队列） 的速度应该会更快。**

> **如果在你的代码里， 包含操作（比如检查一个元素是否出现在一个集合中） 的频率很高， 用set（集合） 会更合适。set专为检查元素是否存在做过优化。 但是它并不是序列， 因为set是无序的。 第3章会详细讨论它。**

### 1.3.1数组

* **如果我们需要一个只包含数字的列表， 那么array.array比list更高效。 数组支持所有跟可变序列有关的操作， 包括pop、.insert和.extend。 另外， 数组还提供从文件读取和存入文件的更快的方法， 如.frombytes和.tofile.**

* **Python数组跟C语言数组一样精简。 创建数组需要一个类型码， 这个类型码用来表示在底层的C语言应该存放怎样的数据类型。 比如b类型码代表的是有符号的字符（signed char） ， 因此array\('b'\)创建出的数组就只能存放一个字节大小的整数， 范围从-128到127， 这样在序列很大的时候， 我们能节省很多空间。 而且Python不会允许你在数组里存放除指定类型之外的数据。**

  > 一个浮点型数组的创建、 存入文件和从文件读取的过程

  ```py
  >>> from array import array ➊
  >>> from random import random
  >>> floats = array('d', (random() for i in range(10**7))) ➋
  >>> floats[-1] ➌
  0.07802343889111107
  >>> fp = open('floats.bin', 'wb')
  >>> floats.tofile(fp) ➍
  >>> fp.close()
  >>> floats2 = array('d') ➎
  >>> fp = open('floats.bin', 'rb')
  >>> floats2.fromfile(fp, 10**7) ➏
  >>> fp.close()
  >>> floats2[-1] ➐
  0.07802343889111107
  >>> floats2 == floats ➑
  True
  ```

## 1.4内存视图

> **memoryview是一个内置类， 它能让用户在不复制内容的情况下操作同一个数组的不同切片。memoryview的概念受到了NumPy的启发（参见2.9.3节） 。Travis Oliphant是NumPy的主要作者， 他在回答“ Whenshould a memoryview be used?”（http://stackoverflow.com/questions/4845418/when-should-amemoryview-be-used/） 这个问题时是这样说的：**
>
> **内存视图其实是泛化和去数学化的NumPy数组。 它让你在不需要复制内容的前提下， 在数据结构之间共享内存。 其中数据结构可以是任何形式， 比如PIL图片、SQLite数据库和NumPy的数组， 等等。 这个功能在处理大型数据集合的时候非常重要。**

memoryview.cast的概念跟数组模块类似， 能用不同的方式读写同一块内存数据， 而且内容字节不会随意移动。 这听上去又跟C语言中类型

转换的概念差不多。memoryview.cast会把同一块内存里的内容打包成一个全新的memoryview对象给你。

  




