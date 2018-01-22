## list.sort方法和内置函数sorted



> **list.sort方法会就地排序列表， 也就是说不会把原列表复制一份。 这也是这个方法的返回值是None的原因， 提醒你本方法不会新建一个列表。 在这种情况下返回None其实是Python的一个惯例： 如果一个函数或者方法对对象进行的是就地改动， 那它就应该返回None， 好让调用者知道传入的参数发生了变动， 而且并未产生新的对象。 例如，random.shuffle函数也遵守了这个惯例。**

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

































