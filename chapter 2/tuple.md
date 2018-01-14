# 1.元组不仅是可变的列表

> **有些python入门教程把元组称为"不可变列表"，然而这并没有完全概括元组的特点，除了用作不可变的列表，它还可以用于没有字段名的记录，我们先来看看元组作为记录的公用。**

## 1.1元组和记录

> **元组其实是对数据的记录；元组中的每个元素都存在了记录中一个字段的数据，外加这个字段的位置，正是这个位置信息给数据赋予了意义。**
>
> **如果只把元组理解为不可变的列表，那其他的信息---它所含有的元素的总数和他们的位置----似乎就变得可有可无。但是如果把元组看成一些字段的集合，那么数量和位置信息就变得非常重要了。**

```py
>>> lax_coordinates = (33.9425, -118.408056) ➊
>>> city, year, pop, chg, area = ('Tokyo', 2003, 32450, 0.66, 8014) ➋
>>> traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ➌
... ('ESP', 'XDA205856')]
>>> for passport in sorted(traveler_ids): ➍
... print('%s/%s' % passport) ➎
...
BRA/CE342567
ESP/XDA205856
USA/31195855
>>> for country, _ in traveler_ids: ➏
... print(country)
...
USA
BRA
ESP
```

1. **洛杉矶国际机场的经纬度。**
2. **东京市的一些信息： 市名、 年份、 人口（单位： 百万） 、 人口变化和面积。**

3. **一个元组列表， 元组的形式为\(country\_code,passport\_number\)。**

4. **在迭代的过程中，passport变量被绑定到每个元组上。**

5. **%格式运算符能被匹配到对应的元组元素上。**

6. **for循环可以分别提取元组里的元素， 也叫作拆包（unpacking） 。 因为元组中第二个元素对我们没有什么用， 所以它赋值给“\_”占位符。**

## 1.2元组拆包

> 我们把元组\('Tokyo', 2003, 32450, 0.66, 8014\)里的元素分别赋值给变量city、year、pop、chg和area， 而这所有的赋值我们只用一行声明就写完了。 同样， 在后面一行中， 一个%运算符就把passport元组里的元素对应到了print函数的格式字符串空档中。 这两个都是对元组拆包的应用。

```py
>>> lax_coordinates = (33.9425, -118.408056)
>>> latitude, longitude = lax_coordinates # 元组拆包
>>> latitude
33.9425
>>> longitude
-118.408056
```

> 另外一个很优雅的写法当属不使用中间变量交换两个变量的值：
>
> ```py
> >>> b, a = a, b
> ```
>
> 还可以用\*运算符把一个可迭代对象拆开作为函数的参数：
>
> ```py
> >>> divmod(20, 8)
> (2, 4)
> >>> t = (20, 8)
> >>> divmod(*t)
> (2, 4)
> >>> quotient, remainder = divmod(*t)
> >>> quotient, remainder
> (2, 4)
> ```
>
> 下面是另一个例子， 这里元组拆包的用法则是让一个函数可以用元组的形式返回多个值， 然后调用函数的代码就能轻松地接受这些返回值。 比如os.path.split\(\)函数就会返回以路径和最后一个文件名组成的元组\(path, last\_part\):
>
> ```py
> >>> import os
> >>> _, filename = os.path.split('/home/luciano/.ssh/idrsa.pub')
> >>> filename
> 'idrsa.pub'
> ```
>
> 除此之外， 在元组拆包中使用\*也可以帮助我们把注意力集中在元组的部分元素上。用\*来处理剩下的元素在Python中， 函数用**\*args**来获取不确定数量的参数算是一种经典写法了。于是**Python 3**里， 这个概念被扩展到了平行赋值中：
>
> ```py
> >>> a, b, *rest = range(5)
> >>> a, b, rest
> (0, 1, [2, 3, 4])
> >>> a, b, *rest = range(3)
> >>> a, b, rest
> (0, 1, [2])
> >>> a, b, *rest = range(2)
> >>> a, b, rest
> (0, 1, [])
> >>> a, *body, c, d = range(5)
> >>> a, body, c, d
> (0, [1, 2], 3, 4)
> >>> *head, b, c, d = range(5)
> >>> head, b, c, d
> ([0, 1], 2, 3, 4)
> ```
>
> 另外元组拆包还有个强大的功能， 那就是可以应用在嵌套结构中。

### 2.1.1嵌套元组拆包

> 接受表达式的元组可以是嵌套式的， 例如\(a, b, \(c, d\)\)。 只要这个接受元组的嵌套结构符合表达式本身的嵌套结构，Python就可以作出正确的对应。 示例如下就是对嵌套元组拆包的应用:用嵌套元组来获取经度
>
> ```py
> metro_areas = [
> ('Tokyo','JP',36.933,(35.689722,139.691667)), # ➊
> ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
> ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
> ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
> ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
> ] p
> rint('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))
> fmt = '{:15} | {:9.4f} | {:9.4f}'
> for name, cc, pop, (latitude, longitude) in metro_areas: # ➋
> if longitude <= 0: # ➌
> print(fmt.format(name, latitude, longitude))
> ```

1. 每个元组内有四个元素，其中最后一个元素是一对坐标
2. 我们把输入坐标的最后一个元素拆包到由变量构成的元组里面，这样就获取到了坐标。

|  | lats | long |
| :---: | :--- | :--- |
| mexico city | 19.4333 | -99.1333 |
| New York | 40.8086 | -74.0204 |
| sao Paul | -23.5478 | -46.6358 |

* 元组已经设计得很好用了， 但作为记录来用的话， 还是少了一个功能：我们时常会需要给记录中的字段命名。namedtuple函数的出现帮我们解决了这个问题。

### 2.1.2 具名元组

* collections.namedtuple是一个工厂函数， 它可以用来构建一个带字段名的元组和一个有名字的类——这个带名字的类对调试程序有很大帮助。

  > 用namedtuple构建的类的实例所消耗的内存跟元组是一样的， 因为字段名都被存在对应的类里面。 这个实例跟普通的对象实例比起来也要小一些， 因为Python不会用\_\_dict\_\_来存放这些实例的属性。
  >
  > ```py
  > >>> from collections import namedtuple
  > >>> City = namedtuple('City', 'name country population coordinates') ➊
  > >>> tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667)) ➋
  > >>> tokyo
  > City(name='Tokyo', country='JP', population=36.933, coordinates=(35.689722,
  > 139.691667))
  > >>> tokyo.population ➌
  > 36.933
  > >>> tokyo.coordinates
  > (35.689722, 139.691667)
  > >>> tokyo[1]
  > 'JP
  > ```

* 除了从普通元组那里继承来的属性之外， 具名元组还有一些自己专有的属性。 示例2-10中就展示了几个最有用的：**\_fields**类属性、 类方法**\_make**\(**iterable**\)和实例方法**\_asdict**\(\)。

  ```py
  >>> City._fields ➊
  ('name', 'country', 'population', 'coordinates')
  >>> LatLong = namedtuple('LatLong', 'lat long')
  >>> delhi_data = ('Delhi NCR', 'IN', 21.935, LatLong(28.613889, 77.208889))
  >>> delhi = City._make(delhi_data) ➋
  >>> delhi._asdict() ➌
  OrderedDict([('name', 'Delhi NCR'), ('country', 'IN'), ('population',
  21.935), ('coordinates', LatLong(lat=28.613889, long=77.208889))])
  >>> for key, value in delhi._asdict().items():
  print(key + ':', value)
  name: Delhi NCR
  country: IN
  population: 21.935
  coordinates: LatLong(lat=28.613889, long=77.208889)
  >>>
  ```

* \_fields属性是一个包含这个类所有字段名称的元组。

* 用\_make\(\)通过接受一个可迭代对象来生成这个类的一个实例， 它的作用跟City\(\*delhi\_data\)是一样的。

* \_asdict\(\)把具名元组以collections.OrderedDict的形式返回， 我们可以利用它来把元组里的信息友好地呈现出来。

### 2.1.3元组和列表的区别

> 可以清楚地看到， 除了跟增减元素相关的方法之外， 元组持列表的其他所有方法。 还有一个例外， 元组没有\_\_reversed\_\_方法， 但是这个方法只是个优化而已，reversed\(my\_tuple\)这个用法在没有\_\_reversed\_\_的情况下也是合法的。



