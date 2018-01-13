# python 数据模型

> **数据模型其实是对python框架的描述，它规范了这门语言自身构建模块的接口，这些模块包括但是不限于序列，迭代器，函数和类、和上下文管理器。**
>
> **不管在哪种框架下写程序， 都会花费大量时间去实现那些会被框架本身调用的方法，Python也不例外。Python解释器碰到特殊的句法时， 会使用特殊方法去激活一些基本的对象操作， 这些特殊方法的名字以两个下划线开头， 以两个下划线结尾（例如\_\_getitem\_\_） 。 比如obj\[key\]的背后就是\_\_getitem\_\_方法， 为了能求得my\_collection\[key\]的值， 解释器实际上会调用my\_collection.\_\_getitem\_\_\(key\)。**

```py
import collections
Card = collections.namedtuple('Card', ['rank', 'suit'])
class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]
    def __len__(self):
        return len(self._cards)
    def __getitem__(self, position):
        return self._cards[position]

```

* 自python2.6开始，namedtuple就加入到python库里面，用以构建只有少量属性但是没有方法的对象，比如数据库条目，利用nametuple，我们可以轻松得到一个纸牌的对象。

```py
>>> beer_card = Card('7', 'diamonds')
>>> beer_card
Card(rank='7', suit='diamonds')
```



