# 声明

笔记记录于https://www.liaoxuefeng.com/wiki/1016959663602400 ，仅供学习，侵删！

Python官网：https://www.python.org/

# 一、数据类型和变量

## 整数

Python可以处理任意大小的整数，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：`1`，`100`，`-8080`，`0`，等等。

十六进制用`0x`前缀和0-9，a-f表示，例如：`0xff00`，`0xa5b4c3d2`，等等。

Python允许在数字中间以`_`分隔，因此，写成`10_000_000_000`和`10000000000`是完全一样的。十六进制数也可以写成`0xa1b2_c3d4`。

## 浮点数

小数，可用科学计数法表示，如 0.000012 可以写成`1.2e-5`

##  字符串

以单引号`'`或双引号`"`括起来的任意文本

转义字符`\`可以转义很多字符，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`

如果字符串里面有很多字符都需要转义，就需要加很多`\`，为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义。（raw string）

r' ' , b' ', u' ', f' ' 的含义

- u'该字符串以 Unicode格式进行编码'
- b'该字符串是bytes 类型'
- 以`f`开头的字符串，称之为`f-string`，它和普通字符串不同之处在于，字符串如果包含`{xxx}`，就会以对应的变量替换

如果字符串内部有很多换行，用`\n`写在一行里不好阅读，为了简化，Python允许用`'''...'''`的格式表示多行内容

## 布尔值

一个布尔值只有`True`、`False`两种值，要么是`True`，要么是`False`

布尔值可以用`and（与）`、`or（或）`和`not（非）`运算。

## 空值

空值是Python里一个特殊的值，用`None`表示。`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。

## 变量

变量名必须是大小写英文、数字和`_`的组合，且不能用数字开头。

## 常量

常量就是不能变的变量，比如常用的数学常数π就是一个常量。在Python中，通常用全部大写的变量名表示常量：

```Python
PI = 3.14159265359
```

`/`除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数：

```bash
>>> 9 / 3
3.0
```

还有一种除法是`//`，称为地板除，两个整数的除法仍然是整数：

```bash
>>> 10 // 3
3
```

整数的地板除`//`永远是整数，即使除不尽。

# 二、Python的字符串

## ord() 和 chr()函数

对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符。

```python
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'
```

## bytes类型

Python的字符串类型是`str`，在内存中以Unicode表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`。

Python对`bytes`类型的数据用带`b`前缀的单引号或双引号表示：

```bash
x = b'ABC'
```

`bytes`的每个字符都只占用一个字节。

以Unicode表示的`str`通过`encode()`方法可以编码为指定的`bytes`，例如：

```python
>>> 'ABC'.encode('ascii')
b'ABC'
>>> '中文'.encode('utf-8')
b'\xe4\xb8\xad\xe6\x96\x87'
>>> '中文'.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
```

含有中文的`str`无法用`ASCII`编码，因为中文编码的范围超过了`ASCII`编码的范围，Python会报错。

在`bytes`中，无法显示为ASCII字符的字节，用`\x##`显示。

反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是`bytes`。要把`bytes`变为`str`，就需要用`decode()`方法：

```python
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

如果`bytes`中包含无法解码的字节，`decode()`方法会报错：

```python
>>> b'\xe4\xb8\xad\xff'.decode('utf-8')
Traceback (most recent call last):
  ...
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
```

如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节：

```python
>>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
'中'
```

由于Python源代码也是一个文本文件，所以，当你的源代码中包含中文的时候，在保存源代码时，就需要务必指定保存为UTF-8编码。当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行：

```bash
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```

第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

##  格式化

- 方式一   类似C语言格式

```python
>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
'Hi, Michael, you have $1000000.'
```

  输出%需要用%%转义

- 方式二    format()

```python
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
```

- 方式三    f-string

```python
>>> r = 2.5
>>> s = 3.14 * r ** 2
>>> print(f'The area of a circle with radius {r} is {s:.2f}')
The area of a circle with radius 2.5 is 19.62
```

# 三、条件判断与循环
## if 语句

```python
age = 20
if age >= 6:
    print('teenager')
elif age >= 18:
    print('adult')
else:
    print('kid')
```

`if`判断条件还可以简写，与C语言类似，Java中不可简写：

```python
if x:
    print('True')
```

只要`x`是非零数值、非空字符串、非空list等，就判断为`True`，否则为`False`。

### Python中的"三目运算符"

```python
a = True if 1 < 2 else False
```

## 循环

- for in 循环

```python
# 计算1-100的和
sum = 0
for x in range(101):
    sum = sum + x
print(sum)
```

- while循环

```python
# 计算1-100的和
sum = 0
n = 1
while n < 101:
    sum = sum + n
    n = n + 1
print(sum)
```

在循环中，`break`语句可以提前退出循环。

`continue`语句结束本次循环，直接开始下一次循环。

# 四、list、tuple、dict、set
## list

列表，Python内置的一种数据类型。

list是一种有序的集合，可以随时添加和删除其中的元素, 索引是从`0`开始。

```python
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
```

list里面的元素的数据类型也可以不同，也可以是另一个list，比如：

```python
>>> L = ['Apple', 123, True, ['asp','php'], 'java']
```

如果要取最后一个元素，除了计算索引位置外，还可以用`-1`做索引，直接获取最后一个元素：

```python
>>> classmates[-1]
'Tracy'
```

### 添加元素 append（）

```python
classmates.append('Adam')
classmates.insert(1, 'Jack')
```

### 删除元素 pop()

```python
>>> classmates.pop()
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
```

删除指定位置的元素，用`pop(i)`方法，其中`i`是索引位置。

### 修改元素

要把某个元素替换成别的元素，可以直接赋值给对应的索引位置：

```python
>>> classmates[1] = 'Sarah'
>>> classmates
['Michael', 'Sarah', 'Tracy']
```

## tuple

元组，一旦初始化就不能修改

```python
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

只有1个元素的tuple定义时必须加一个逗号`,`来消除歧义，避免解释为小括号()：

```python
>>> t = (1,)
>>> t
(1,)
```

tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向`'a'`，就不能改成指向`'b'`，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！



## dict          

字典，dict全称dictionary。在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。通过key计算位置的算法称为哈希算法（Hash）

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

如果key不存在，dict就会报错：

```python
>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'
```

要避免key不存在的错误，有两种办法，一是通过`in`判断key是否存在：

```python
>>> 'Thomas' in d
False
```

二是通过dict提供的`get()`方法，如果key不存在，可以返回`None`，或者自己指定的value：

```python
>>> d.get('Thomas')
>>> d.get('Thomas', -1)
-1
```

要删除一个key，用`pop(key)`方法，对应的value也会从dict中删除：

```python
>>> d.pop('Bob')
75
>>> d
{'Michael': 95, 'Tracy': 85}
```

请务必注意，dict内部存放的顺序和key放入的顺序是没有关系的。

## set

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

要创建一个set，需要提供一个list作为输入集合：

```python
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}
```

注意，传入的参数`[1, 2, 3]`是一个list，而显示的`{1, 2, 3}`只是告诉你这个set内部有1，2，3这3个元素，显示的顺序也不表示set是有序的。

重复元素在set中自动被过滤：

```python
>>> s = set([1, 1, 2, 2, 3, 3])
>>> s
{1, 2, 3}
```

通过`add(key)`方法可以添加元素到set中，可以重复添加，但不会有效果：

```python
>>> s.add(4)
>>> s
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

通过`remove(key)`方法可以删除元素：

```python
>>> s.remove(4)
>>> s
{1, 2, 3}
```

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：

```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```



## 关于不可变对象

str是不变对象，而list是可变对象。

对于可变对象，比如list，对list进行操作，list内部的内容是会变化的，比如：

```python
>>> a = ['c', 'b', 'a']
>>> a.sort()
>>> a
['a', 'b', 'c']
```

而对于不可变对象，比如str，对str进行操作呢：

```python
>>> a = 'abc'
>>> a.replace('a', 'A')
'Abc'
>>> a
'abc'
```

虽然字符串有个`replace()`方法，也确实变出了`'Abc'`，但变量`a`最后仍是`'abc'`，应该怎么理解呢？

我们先把代码改成下面这样：

```python
>>> a = 'abc'
>>> b = a.replace('a', 'A')
>>> b
'Abc'
>>> a
'abc'
```

要始终牢记的是，`a`是变量，而`'abc'`才是字符串对象！有些时候，我们经常说，对象`a`的内容是`'abc'`，但其实是指，`a`本身是一个变量，它指向的对象的内容才是`'abc'`：

```ascii
┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
```

当我们调用`a.replace('a', 'A')`时，实际上调用方法`replace`是作用在字符串对象`'abc'`上的，而这个方法虽然名字叫`replace`，但却没有改变字符串`'abc'`的内容。相反，`replace`方法创建了一个新字符串`'Abc'`并返回，如果我们用变量`b`指向该新字符串，就容易理解了，变量`a`仍指向原有的字符串`'abc'`，但变量`b`却指向新字符串`'Abc'`了：

```ascii
┌───┐                  ┌───────┐
│ a │─────────────────>│ 'abc' │
└───┘                  └───────┘
┌───┐                  ┌───────┐
│ b │─────────────────>│ 'Abc' │
└───┘                  └───────┘
```

所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

# 五、函数


## 内置函数

要调用一个函数，需要知道函数的名称和参数，比如求绝对值的函数`abs`，只有一个参数。

对于内置函数，可以直接从Python的官方网站查看文档：http://docs.python.org/3/library/functions.html#abs

也可以在交互式命令行通过`help(abs)`查看`abs`函数的帮助信息。

- max()

  `max`函数可以接收任意多个参数，并返回最大的那个：

```python
>>> max(1, 2)
2
>>> max(2, 3, 1, -5)
3
```

- hex()
  把一个整数转换成十六进制表示的字符串

- len()
  返回对象（字符、列表、元组等）长度或项目个数

- 类型转换

  int() ,  float(),  str(),  bool() 等

函数名其实就是指向一个函数对象的引用，完全可以把函数名赋给一个变量，相当于给这个函数起了一个“别名”：

```python
>>> a = abs # 变量a指向abs函数
>>> a(-1) # 所以也可以通过a调用abs函数
1
```



## 定义函数

定义一个函数要使用`def`关键字

```python
# 自定义求绝对值函数
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
# 函数执行完毕也没有return语句时，自动 return None
```

定义一个空函数，使用`pass`关键字，作为一个占位符，该函数不包含任何功能

```python
def nop():
    pass
```

函数的多个返回值 ( 实际上是返回了一个tuple )

```python
def getPoint():
    return 1, 2
```

## 函数的参数

- 设置默认参数

```python
def power(x, n=2):
    return x**n
# **运算符计算x的n次方
```

默认参数有个最大的坑，演示如下：

先定义一个函数，传入一个list，添加一个`END`再返回：

```
def add_end(L=[]):
    L.append('END')
    return L
```

当你正常调用时，结果似乎不错：

```
>>> add_end([1, 2, 3])
[1, 2, 3, 'END']
>>> add_end(['x', 'y', 'z'])
['x', 'y', 'z', 'END']
```

当你使用默认参数调用时，一开始结果也是对的：

```
>>> add_end()
['END']
```

但是，再次调用`add_end()`时，结果就不对了：

```
>>> add_end()
['END', 'END']
>>> add_end()
['END', 'END', 'END']
```

很多初学者很疑惑，默认参数是`[]`，但是函数似乎每次都“记住了”上次添加了`'END'`后的list。

原因解释如下：

Python函数在定义的时候，默认参数`L`的值就被计算出来了，即`[]`，因为默认参数`L`也是一个变量，它指向对象`[]`，每次调用该函数，如果改变了`L`的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的`[]`了。

 **定义默认参数要牢记一点：默认参数必须指向不变对象！**

要修改上面的例子，我们可以用`None`这个不变对象来实现：

```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

现在，无论调用多少次，都不会有问题：

```
>>> add_end()
['END']
>>> add_end()
['END']
```

- 可变参数

**可变参数允许传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。在参数前面加一个`*`号**

```python
# 参数numbers接收到的是一个tuple
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```

**Python允许在list或tuple前面加一个`*`号，把list或tuple的元素变成可变参数传进去：**

```python
>>> nums = [1, 2, 3]
>>> calc(*nums)
14
```

- 关键字参数

关键字参数允许传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict，使用方法：在函数参数前加`**`符号。

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```

与可变参数类似：

```python
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
```

- 命名关键字参数 ( 限制关键字参数的名字 )

  命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。

```python
def person(name, age, *, city, job):
    print(name, age, city, job)
```

调用方式如下：

```python
>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```

如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：

```python
def person(name, age, *args, city, job):
    print(name, age, args, city, job)
```

# 六、高级特性
## 切片（Slice）

取一个list或tuple的部分元素。

```python
# [i : j : s]  s表示步进，缺省为1，所以a[i:j:1]相当于a[i:j]

# 当s<0时，i缺省时，默认为-1. j缺省时，默认为-len(a)-1
# a[::-1]相当于 a[-1:-len(a)-1:-1]

>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

>>> L[0:3]     # 从索引0开始取，[0, 3), 0可省略，即 L[:3]
['Michael', 'Sarah', 'Tracy']

>>> L[:]   # 原列表
['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

>>> L[0:]
['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

>>> L[::-1]    # 逆序
['Jack', 'Bob', 'Tracy', 'Sarah', 'Michael']
```

tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple：

```python
>>> (0, 1, 2, 3, 4, 5)[:3]
(0, 1, 2)
```

字符串也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```python
>>> 'ABCDEFG'[::-1]
'GFEDCBA'
```

## 迭代

如果给定一个list或tuple，可以通过`for`循环来遍历这个list或tuple，这种遍历称为迭代（Iteration）。

**只要是可迭代对象，无论有无下标，都可以迭代**，比如dict就可以迭代：

```python
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> for key in d:
...     print(key)
...
a
c
b
```

因为dict的存储不是按照list的方式顺序排列，所以，迭代出的结果顺序很可能不一样。

默认情况下，dict迭代的是key。如果要迭代value，可以用`for value in d.values()`，如果要同时迭代key和value，可以用`for k, v in d.items()`。

**如何判断一个对象是可迭代对象呢？**方法是通过collections模块的Iterable类型判断：

```python
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

Python内置的`enumerate`函数可以把一个list变成索引-元素对，这样就可以在`for`循环中同时迭代索引和元素本身：

```python
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

## 列表生成式

List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。

```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

for循环后面还可以加上if判断：

```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

还可以使用两层循环，可以生成全排列：

```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

在一个列表生成式中，`for`前面的`if ... else`是表达式，而`for`后面的`if`是过滤条件，不能带`else`。

```python
>>> [x if x % 2 == 0 else -x for x in range(1, 11)]
[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]
```

## 生成器（可迭代对象）

在Python中，一边循环一边计算的机制，称为生成器：generator。

创建生成器：

- 将列表生成式的`[]`改成`()`， 使用next()函数获得generator的下一个返回值

```python
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
```

- yield关键字 (如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator)

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

generator和函数的执行流程不一样。函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用`next()`的时候执行，***遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行***。

如果想要拿到generator的return 语句返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中。

## yield关键字的理解

简单理解：**yield就是 return 返回一个值，并且记住这个返回的位置，下次迭代就从这个位置后开始（下次迭代的时候可用`send()`传入参数）。**

首先，如果你还没有对yield有个初步的认识，那么你先把yield看做“return”，这个是直观的，它首先是个return，普通的return是什么意思，就是在程序中返回某个值，返回之后程序就不再往下运行了。看做return之后再把它看做一个是生成器（generator）的一部分（带yield的函数才是真正的迭代器），好了，如果你对这些不明白的话，那先把yield看做return,然后直接看下面的程序，你就会明白yield的全部意思了：
```python
def foo():
    print("starting...")
    while True:
        res = yield 4
        print("res:",res)
g = foo()
print(next(g))
print("*"*20)
print(next(g))
```
就这么简单的几行代码就让你明白什么是yield，代码的输出这个：
```python
starting...
4
********************
res: None
4
```
我直接解释代码运行顺序，相当于代码单步调试：

1.程序开始执行以后，因为foo函数中有yield关键字，所以foo函数并不会真的执行，而是先得到一个生成器g(相当于一个对象)

2.直到调用next方法，foo函数正式开始执行，先执行foo函数中的print方法，然后进入while循环

3.程序遇到yield关键字，然后把yield想想成return,return了一个4之后，程序停止，并没有执行赋值给res操作，此时next(g)语句执行完成，所以输出的前两行（第一个是while上面的print的结果,第二个是return出的结果）是执行print(next(g))的结果，

4.程序执行`print("*"*20)，输出20个*`

5.又开始执行下面的print(next(g)),这个时候和上面那个差不多，不过不同的是，这个时候是从刚才那个next程序停止的地方开始执行的，也就是要执行res的赋值操作，这时候要注意，这个时候赋值操作的右边是没有值的（因为刚才那个是return出去了，并没有给赋值操作的左边传参数），所以这个时候res赋值是None,所以接着下面的输出就是res:None,

6.程序会继续在while里执行，又一次碰到yield,这个时候同样return 出4，然后程序停止，print函数输出的4就是这次return出的4.


到这里你可能就明白yield和return的关系和区别了，带yield的函数是一个生成器，而不是一个函数了，这个生成器有一个函数就是next函数，next就相当于“下一步”生成哪个数，这一次的next开始的地方是接着上一次的next停止的地方执行的，所以调用next的时候，生成器并不会从foo函数的开始执行，只是接着上一步停止的地方开始，然后遇到yield后，return出要生成的数，此步就结束。

```python
def foo():
    print("starting...")
    while True:
        res = yield 4
        print("res:",res)
g = foo()
print(next(g))
print("*"*20)
print(g.send(7))
```
再看一个这个生成器的send函数的例子，这个例子就把上面那个例子的最后一行换掉了，输出结果：
```python
starting...
4

********************
res: 7
4
```
先大致说一下send函数的概念：此时你应该注意到上面那个的紫色的字，还有上面那个res的值为什么是None，这个变成了7，到底为什么，这是因为，send是发送一个参数给res的，因为上面讲到，return的时候，并没有把4赋值给res，下次执行的时候只好继续执行赋值操作，只好赋值为None了，而如果用send的话，开始执行的时候，先接着上一次（return 4之后）执行，先把7赋值给了res,然后执行next的作用，遇见下一回的yield，return出结果后结束。

5.程序执行g.send(7)，程序会从yield关键字那一行继续向下运行，send会把7这个值赋值给res变量

6.由于send方法中包含next()方法，所以程序会继续向下运行执行print方法，然后再次进入while循环

7.程序执行再次遇到yield关键字，yield会返回后面的值后，程序再次暂停，直到再次调用next方法或send方法。

## 迭代器

可以被`next()`函数调用并不断返回下一个值的对象称为迭代器：`Iterator`。

生成器都是`Iterator`对象，但`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`。

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

Python的`for`循环本质上就是通过不断调用`next()`函数实现的。

# 七、函数式编程
函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数。

## 高阶函数

### map

`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

```python
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)    #结果r是一个Iterator，Iterator是惰性序列，因此通过list()函数让它把整个序列都计算出来并返回一个list。
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

### reduce

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算。

```python
# 实现sum()函数的功能，序列求和
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```

### filter

`filter()`函数用于过滤序列。

```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

### sorted

`sorted()`函数实现排序。

```python
# 对list进行排序
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
```

`sorted()`还可以接收一个`key`函数来实现自定义的排序，例如按绝对值大小排序：

```python
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。对比原始的list和经过`key=abs`处理过的list：

```python
list = [36, 5, -12, 9, -21]
keys = [36, 5,  12, 9,  21]
```

然后`sorted()`函数按照keys进行排序，并按照对应关系返回list相应的元素：

```ascii
keys排序结果 => [5, 9,  12,  21, 36]
                |  |    |    |   |
最终结果     => [5, 9, -12, -21, 36]
```

要进行反向排序，不必改动key函数，可以传入第三个参数reverse=True

```python
>>> L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
>>> def by_score(t):
	return t[1]

>>> sorted(L, key=by_score)
[('Bart', 66), ('Bob', 75), ('Lisa', 88), ('Adam', 92)]
>>> sorted(L, key=by_score, reverse=True)
[('Adam', 92), ('Lisa', 88), ('Bob', 75), ('Bart', 66)]
```

## 返回函数

高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
```

当我们调用`lazy_sum()`时，返回的并不是求和结果，而是求和函数：

```python
>>> f = lazy_sum(1, 3, 5, 7, 9)
>>> f
<function lazy_sum.<locals>.sum at 0x101c6ed90>
```

调用函数`f`时，才真正计算求和的结果：

```python
>>> f()
25
```

在函数`lazy_sum`中又定义了函数`sum`，并且，内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量，当`lazy_sum`返回函数`sum`时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力。

当调用`lazy_sum()`时，每次调用都会返回一个新的函数，即使传入相同的参数：

```python
>>> f1 = lazy_sum(1, 3, 5, 7, 9)
>>> f2 = lazy_sum(1, 3, 5, 7, 9)
>>> f1==f2
False
```

`f1()`和`f2()`的调用结果互不影响。

返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

## 匿名函数

关键字`lambda`表示匿名函数。

匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。

```python
>>> f = lambda x: x * x
>>> f
<function <lambda> at 0x101c6ef28>
>>> f(5)
25
```

## 装饰器

函数也是一个对象，而且函数对象可以被赋值给变量，所以，通过变量也能调用该函数。函数对象有一个`__name__`属性，可以拿到函数的名字.

```python
>>> def now():
	print("2020-12-12")

>>> now.__name__
'now'
```

假设我们要增强`now()`函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改`now()`函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。

本质上，decorator就是一个返回函数的高阶函数。

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

观察上面的`log`，因为它是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处。

```python
@log   # 相当于执行了语句：now = log(now)
def now():
	print("2020-12-12")

>>> now()
call now():
2020-12-12
```

如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

这个3层嵌套的decorator用法如下：

```python
@log('execute')  #　now = log('execute')(now)
def now():
    print('2015-3-25')
```

首先执行`log('execute')`，返回的是`decorator`函数，再调用返回的函数，参数是`now`函数，返回值最终是`wrapper`函数。

查看now函数的__name__函数名

```python
>>> now.__name__
'wrapper'
```

更改函数名为now。 `@functools.wraps`

```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

## 偏函数

Python的`functools`模块提供了很多有用的功能，如偏函数（Partial function）。

`int()`函数可以把字符串转换为整数，当仅传入字符串时，`int()`函数默认按十进制转换：

```python
>>> int('12345')
12345
```

但`int()`函数还提供额外的`base`参数，默认值为`10`。如果传入`base`参数，就可以做N进制的转换：

```python
>>> int('12345', base=8)
5349
>>> int('12345', 16)
74565
```

假设要转换大量的二进制字符串，每次都传入`int(x, base=2)`非常麻烦，于是，我们想到，可以定义一个`int2()`的函数，默认把`base=2`传进去：

```python
def int2(x, base=2):
    return int(x, base)
```

`functools.partial`就是帮助我们创建一个偏函数的，不需要我们自己定义`int2()`，可以直接使用下面的代码创建一个新的函数`int2`：

```
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
>>> int2('1010101')
85
```

所以，简单总结`functools.partial`的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。

# 八、模块
## 自定义模块

- 编写

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

'MyMath : a math module'  # 模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；

__author__ = 'foryatto'  # 作者

def add(a, b):
    return a + b

if __name__ == '__main__':
    print('正在使用自定义math模块')
```

- 使用

上述编写过程中自定义了一个MyMath.py文件，单独运行MyMath.py将会看见如下运行结果：

```python
> python3 MyMath.py
正在使用自定义math模块
```

新建test.py使用MyMath模块。

```python
import MyMath as M

print(M.add(1,2))
```

- 作用域

  类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如上面的`__author__`，`__name__`就是特殊变量，`hello`模块定义的文档注释也可以用特殊变量`__doc__`访问，我们自己的变量一般不要用这种变量名；

  通过`_`前缀定义的变量名就是非公开的（private），**不应该**被直接引用(`Python并没有一种方法可以完全限制访问private函数或变量`)，比如`_abc`，`__abc`等；

## 安装第三方模块

使用包管理工具`pip`。

```bash
pip install xxx(模块名)
```

使用时，`import` 模块

## 模块搜索路径

默认情况下，Python解释器会搜索当前目录、所有已安装的内置模块和第三方模块，搜索路径存放在`sys`模块的`path`变量中：

```python
>>> import sys
>>> sys.path
['', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python36.zip', '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6', ..., '/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages']
```

如果我们要添加自己的搜索目录，有两种方法：

一是直接修改`sys.path`，添加要搜索的目录：

```
>>> import sys
>>> sys.path.append('/Users/michael/my_py_scripts')
```

这种方法是在运行时修改，运行结束后失效。

第二种方法是设置环境变量`PYTHONPATH`，该环境变量的内容会被自动添加到模块搜索路径中。设置方式与设置Path环境变量类似。注意只需要添加自己的搜索路径。

# 九、面向对象编程
## 类和实例

定义类使用`class`关键字。

```python
class Student(object):
    pass
```

该类继承自object类，可省略，所有类最终都继承自object。

创建实例

```python
>>> xiaoMing = Student()
>>> xiaoMing.name = "xiaoMing"  #可以自由地给一个实例变量绑定属性，这点与静态语言不同
>>> xiaoMing
<__main__.Student object at 0x0000023A0B2703C8>
>>> xiaoMing.name
'xiaoMing'
```

在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去。通过定义一个特殊的`__init__`方法，在创建实例的时候，就把`name`，`score`等属性绑上去。

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

'''
__init__方法的第一个参数永远是self，表示创建的实例本身，因此，在__init__方法内部，就可以把各种属性绑定到self，因为self就指向创建的实例本身。

有了__init__方法，在创建实例的时候，就不能传入空的参数了，必须传入与__init__方法匹配的参数，但self不需要传，Python解释器自己会把实例变量传进去
'''

>>> xiaoMing = Student("XiaoMing", 90)
```

​      编写类方法

```python
class Student(object):

    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))
        
'''
要定义一个方法，除了第一个参数是self外，其他和普通函数一样。要调用一个方法，只需要在实例变量上直接调用，除了self不用传递，其他参数正常传入
'''
```

## 访问限制

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问。

在Python中，变量名类似`__xxx__`的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用`__name__`、`__score__`这样的变量名。

有些时候，你会看到以一个下划线开头的实例变量名，比如`_name`，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问`__name`是因为Python解释器对外把`__name`变量改成了`_Student__name`，所以，仍然可以通过`_Student__name`来访问`__name`变量：

```
>>> bart._Student__name
'Bart Simpson'
```

但是强烈建议你不要这么干，因为不同版本的Python解释器可能会把`__name`改成不同的变量名。

总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉。

## 继承和多态

继承可以把父类的所有功能都直接拿过来，子类只需要新增自己特有的方法，也可以把父类不适合的方法覆盖重写。

## 获取对象信息

### 使用type()

type()函数返回对应的Class类型。

```python
import types
>>> type(123)==int
True
>>> type('abc')==type('123')
True

>>> def myFunction():
...     pass
...
>>> type(myFunction)==types.FunctionType
True
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True
```

### 使用isinstance()

能用`type()`判断的基本类型也可以用`isinstance()`判断，isinstance还可以判断类的继承关系。

如果存在以下继承关系：

```python
object -> Animal -> Dog -> Husky
```

```python
>>> a = Animal()
>>> d = Dog()
>>> h = Husky()

>>> isinstance(h, Husky)
True
>>> isinstance(h, Animal)
True
```

并且还可以判断一个变量是否是某些类型中的一种，比如下面的代码就可以判断是否是list或者tuple：

```python
>>> isinstance([1, 2, 3], (list, tuple))
True
>>> isinstance((1, 2, 3), (list, tuple))
True
```


### 使用dir()

获得一个对象的所有属性和方法，它返回一个包含字符串的list。

```python
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
>>> len('ABC')
3
>>> 'ABC'.__len__()
3

'''
在len()函数内部，它自动去调用该对象的__len__()方法
'''
```

## 实例属性和类属性

由于Python是动态语言，**根据类创建的实例可以任意绑定属性（通过实例变量，或者通过`self`变量）。**

创建一个类属性：

```python
class Student(object):
    name = 'Student'
```

相同名称的实例属性将屏蔽掉类属性。

```python
>>> class Student(object):
...     name = 'Student'
...
>>> s = Student() # 创建实例s
>>> print(s.name) # 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
Student
>>> print(Student.name) # 打印类的name属性
Student
>>> s.name = 'Michael' # 给实例绑定name属性
>>> print(s.name) # 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
Michael
>>> print(Student.name) # 但是类属性并未消失，用Student.name仍然可以访问
Student
>>> del s.name # 如果删除实例的name属性
>>> print(s.name) # 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
Student
```

# 十、面向对象高级编程

## 使用`__slot__`

Python作为动态语言，可给实例绑定一个方法：

```
>>> def set_age(self, age): # 定义一个函数作为实例方法
...     self.age = age
...
>>> from types import MethodType
>>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
>>> s.set_age(25) # 调用实例方法
>>> s.age # 测试结果
25
```

为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的`__slots__`变量，来限制该class实例能添加的属性。

```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

`__slots__`定义的属性仅对当前类实例起作用，对子类无效，除非在子类中也定义`__slots__`，这样，子类实例允许定义的属性就是自身的`__slots__`加上父类的`__slots__`。

## 使用@property

Python内置的`@property`装饰器负责把一个方法变成属性调用。

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
        
'''
把一个getter方法变成属性，只需要加上@property就可以了，
此时，@property本身又创建了另一个装饰器@score.setter，
负责把一个setter方法变成属性赋值。
'''

'''
只定义getter方法，不定义setter方法就是一个只读属性
'''

>>> s = Student()
>>> s.score = 60 # OK，实际转化为s.set_score(60)
>>> s.score # OK，实际转化为s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!

```

## 多重继承

通过多重继承，一个子类就可以同时获得多个父类的所有功能。

- MixIn

  MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。

  Python自带的很多库也使用了MixIn。举个例子，Python自带了`TCPServer`和`UDPServer`这两类网络服务，而要同时服务多个用户就必须使用多进程或多线程模型，这两种模型由`ForkingMixIn`和`ThreadingMixIn`提供。通过组合，我们就可以创造出合适的服务来。

  比如，编写一个多进程模式的TCP服务，定义如下：

  ```python
  class MyTCPServer(TCPServer, ForkingMixIn):
      pass
  ```

## 定制类

Python的class中还有许多有特殊用途的函数，可以帮助我们定制类。

- `__str__()`  

	定义打印出的的类实例内容

- `__iter__()`

	该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到`StopIteration`错误时退出循环。

```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1 # 初始化两个计数器a，b

    def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

    def __next__(self):
        self.a, self.b = self.b, self.a + self.b # 计算下一个值
        if self.a > 100000: # 退出循环的条件
            raise StopIteration()
        return self.a # 返回下一个值
```

```python
>>> for n in Fib():
...     print(n)
...
1
1
2
3
5
...
46368
75025
```

- `__getitem__()`

  与`__iter__()`相比，`__getitem__()`可以像list那样按照下标取出元素。

```python
class Fib(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a
```

```python
>>> f = Fib()
>>> f[0]
1
>>> f[1]
1
>>> f[2]
2
```

- `__getattr__()`

  当调用不存在的属性时，比如`score`，Python解释器会试图调用`__getattr__(self, 'score')`来尝试获得属性。

```python
class Student(object):

    def __init__(self):
        self.name = 'Michael'

    def __getattr__(self, attr):
        if attr=='score':
            return 99		# 也可返回函数
```

- `__call__()`

  任何类，只需要定义一个`__call__()`方法，就可以直接对实例进行调用。

```python
class Student(object):
    def __init__(self, name):
        self.name = name

    def __call__(self):
        print('My name is %s.' % self.name)
```

```python
>>> s = Student('Michael')
>>> s() # self参数不要传入
My name is Michael.

'''
通过callable()函数，可以判断一个对象是否是“可调用”对象。
'''
```

## 枚举类

`Enum`可以把一组相关常量定义在一个class中，且class不可变，而且成员可以直接比较。

## 元类

`type()`函数既可以返回一个对象的类型，又可以创建出新的类型。

```python
>>> def fn(self, name='world'): # 先定义函数
...     print('Hello, %s.' % name)
...
>>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
>>> h = Hello()
>>> h.hello()
Hello, world.
>>> print(type(Hello))
<class 'type'>
>>> print(type(h))
<class '__main__.Hello'>
```

`type()`函数依次传入3个参数：

1. class的名称；
2. 继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；
3. class的方法名称与函数绑定，这里我们把函数`fn`绑定到方法名`hello`上。

### metaclass

先定义metaclass，就可以创建类，最后创建实例。

元类的理解和应用：[〖Python进阶〗从0到1编写ORM 详细实现](https://blog.csdn.net/qq_45271256/article/details/106952496)

metaclass是Python中非常具有魔术性的对象，它可以改变类创建时的行为。

# 十一、异常与调试

## 错误处理 (try...except)

```python
try:
    print('try...')
    r = 10 / int('2')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
else:
    print('no error!')
finally:
    print('finally...')
print('END')
```

Python的错误其实也是class，所有的错误类型都继承自`BaseException`，所以在使用`except`时需要注意的是，它不但捕获该类型的错误，还可捕获子类。

如果错误没有被捕获，它就会一直往上抛，最后被Python解释器捕获。

- 记录错误

  如果不捕获错误，自然可以让Python解释器来打印出错误堆栈，但程序也被结束了。既然我们能捕获错误，就可以把错误堆栈打印出来，然后分析错误原因，同时，让程序继续执行下去。

  Python内置的`logging`模块可以非常容易地记录错误信息。

```python
import logging

def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)

main()
print('END') #　同样是出错，但程序打印完错误信息后会继续执行，并正常退出
```

- 抛出错误 （使用`raise` 关键字，错误也是一个class，所以也可以自定义错误类）

```python
def foo(s):
    n = int(s)
    if n==0:
        raise ValueError('invalid value: %s' % s)
    return 10 / n

def bar():
    try:
        foo('0')
    except ValueError as e:
        print('ValueError!')

bar()
```

# 十二、IO编程

## 文件读写

- open()函数，r,  w

```python
# 如果文件不存在，open()函数就会抛出一个IOError的错误
#调用read()方法可以一次读取文件的全部内容
#文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源

'''
open('test.txt', 'r', encoding='gbk') 
encoding指定编码
# rb表示打开二进制文件，w表示写文件(如果被写入文件存在则会覆盖原内容)，a表示追加模式写入
'''

try:
    f = open('test.txt', 'r')  
    print(f.read())
finally:
    if f:
        f.close()
        
'''
但是每次都这么写实在太繁琐，所以，Python引入了with语句来自动帮我们调用close()方法：
'''

with open('test.txt', 'r') as f:
    print(f.read())
    
'''
其他方法
read(size) 每次最多读取size个字节的内容
readline() 每次读取一行内容
readlines() 一次读取所有内容并按行返回list
write() 写入数据
'''
```

像`open()`函数返回的这种有个`read()`方法的对象，在Python中统称为file-like Object。除了file外，还可以是内存的字节流，网络流，自定义流等等。file-like Object不要求从特定类继承，只要写个`read()`方法就行。

`StringIO`就是在内存中创建的file-like Object，常用作临时缓冲。

## with的用法

**with是从Python2.5引入的一个新的语法，它是一种上下文管理协议，目的在于从流程图中把 try,except 和finally 关键字和**

**资源分配释放相关代码统统去掉，简化try….except….finlally的处理流程。**

**with通过`__enter__`方法初始化，然后在`__exit__`中做善后以及处理异常。**

**所以使用with处理的对象必须有`__enter__()`和`__exit__()`这两个方法。**

**其中`__enter__()`方法在语句体（with语句包裹起来的代码块）执行之前进入运行，`__exit__()`方法在语句体执行完毕退出后运行。**

**with 语句适用于对资源进行访问的场合，确保不管使用过程中是否发生异常都会执行必要的“清理”操作，释放资源，比如文件使用后自动关闭、线程中锁的自动获取和释放等。**

**With语句的基本语法格式:**

```python
with expression [as target]:
	with_body
```

**参数说明：**

expression：是一个需要执行的表达式；

target：是一个变量或者元组，存储的是expression表达式执行返回的结果，可选参数。

with工作原理代码示例：

```python
class Sample:
    def __enter__(self):
        print "in __enter__"
        return "Foo"
    def __exit__(self, exc_type, exc_val, exc_tb):
        print "in __exit__"
def get_sample():
    return Sample()
with get_sample() as sample:
    print "Sample: ", sample
```

代码的运行结果如下：

```python
in __enter__
Sample:  Foo
in __exit__123
```

可以看到，整个运行过程如下：
（１）`__enter__()`方法被执行；
（２）`__enter__()`方法的返回值，在这个例子中是”Foo”，赋值给变量sample；
（３）执行代码块，打印sample变量的值为”Foo”；
（４）`__exit__()`方法被调用；

【注】`__exit__()`方法中有３个参数， exc_type, exc_val, exc_tb，这些参数在异常处理中相当有用。
exc_type：　错误的类型
exc_val：　错误类型对应的值
exc_tb：　代码中错误发生的位置

示例代码：

```python
class Sample():
    def __enter__(self):
        print('in enter')
        return self
    def __exit__(self, exc_type, exc_val, exc_tb):
        print "type: ", exc_type
        print "val: ", exc_val
        print "tb: ", exc_tb
    def do_something(self):
        bar = 1 / 0
        return bar + 10
with Sample() as sample:
    sample.do_something()
```

程序输出结果：

```python
in enter
Traceback (most recent call last):
type:  <type 'exceptions.ZeroDivisionError'>
val:  integer division or modulo by zero
  File "/home/user/Code/with_test.py", line 36, in <module>
tb:  <traceback object at 0x7f9e13fc6050>
    sample.do_something()
  File "/home/user/Code/with_test.py", line 32, in do_something
    bar = 1 / 0
ZeroDivisionError: integer division or modulo by zero

Process finished with exit code 1
```

## StringIO和BytesIO

StringIO就是在内存中读写str。

```python
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!

>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```

BytesIO实现了在内存中读写bytes。

```python
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'

>>> f = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')
>>> f.read()
b'\xe4\xb8\xad\xe6\x96\x87'
```

## 操作文件和目录

Python内置的`os`模块可以直接调用操作系统提供的接口函数。`os`模块封装了操作系统的目录和文件操作，要注意这些函数有的在`os`模块中，有的在`os.path`模块中。

```python
>>> import os
>>> os.name # 操作系统类型
'posix'
# 如果是posix，说明系统是Linux、Unix或Mac OS X，如果是nt，就是Windows系统。
# os模块的某些函数是跟操作系统相关的

# 在操作系统中定义的环境变量，全部保存在os.environ这个变量中,要获取某个环境变量的值，可以调用os.environ.get('key')
```

## 序列化

把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，在其他语言中也被称之为serialization，marshalling，flattening等等。

Python提供了`pickle`模块来实现序列化。

`pickle.dumps()`方法把任意对象序列化成一个`bytes`，然后，就可以把这个`bytes`写入文件。或者用另一个方法`pickle.dump()`直接把对象序列化后写入一个file-like Object

```python
>>> import pickle
>>> d = dict(name='Bob', age=20, score=88)
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x14X\x05\x00\x00\x00scoreq\x02KXX\x04\x00\x00\x00nameq\x03X\x03\x00\x00\x00Bobq\x04u.'
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()
```

`pickle.loads()`方法反序列化出对象，也可以直接用`pickle.load()`方法从一个`file-like Object`中直接反序列化出对象。

```python
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': 'Bob'}
```

Pickle的缺点: 只能用于Python，不同版本的Python可能存在兼容性问题。

### JSON

Python内置的`json`模块提供了非常完善的Python对象到JSON格式的转换。

`dumps()`方法返回一个`str`，内容就是标准的JSON。类似的，`dump()`方法可以直接把JSON写入一个`file-like Object`。

要把JSON反序列化为Python对象，用`loads()`或者对应的`load()`方法，前者把JSON的字符串反序列化，后者从`file-like Object`中读取字符串并反序列化。

```python
>>> import json
>>> d = dict(name='Bob', age=20, score=88)
>>> json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'

>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> json.loads(json_str)
{'age': 20, 'score': 88, 'name': 'Bob'}
```

默认情况下，`dumps()`方法不知道如何将一个`类实例`变为一个JSON的`{}`对象

```python
print(json.dumps(s, default=lambda obj: obj.__dict__)) # 把任意class的实例变为dict
```

同理，如果要把JSON反序列化为一个`类对象实例`，`loads()`方法首先转换出一个`dict`对象，然后`object_hook`函数负责把`dict`转换为`Student`实例。

```python
def dict2student(d):
    return Student(d['name'], d['age'], d['score'])  # Student是一个自定义类

>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> print(json.loads(json_str, object_hook=dict2student))
<__main__.Student object at 0x10cd3c190>
```

# 十三、进程和线程

对于操作系统来说，一个任务就是一个进程（Process），比如打开一个浏览器就是启动一个浏览器进程，打开一个记事本就启动了一个记事本进程，打开两个记事本就启动了两个记事本进程，打开一个Word就启动了一个Word进程。

有些进程还不止同时干一件事，比如Word，它可以同时进行打字、拼写检查、打印等事情。在一个进程内部，要同时干多件事，就需要同时运行多个“子任务”，我们把进程内的这些“子任务”称为线程（Thread）。

线程是最小的执行单元，而进程由至少一个线程组成。如何调度进程和线程，完全由操作系统决定，程序自己不能决定什么时候执行，执行多长时间。

## 多进程

Unix/Linux操作系统提供了一个`fork()`系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是`fork()`调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。

子进程永远返回`0`，而父进程返回子进程的ID。这样做的理由是，一个父进程可以fork出很多子进程，所以，父进程要记下每个子进程的ID，而子进程只需要调用`getppid()`就可以拿到父进程的ID。

Python的`os`模块封装了常见的系统调用，其中就包括`fork`，可以在Python程序中轻松创建子进程。

```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
```

```python
Process (876) start...
I (876) just created a child process (877).
I am child process (877) and my parent is 876.
```

### multiprocessing

因为Windows没有`fork`调用，`multiprocessing`模块就是跨平台版本的多进程模块。

```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()	# join()方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。
    print('Child process end.')
```

```python
Parent process 928.
Child process will start.
Run child process test (929)...
Process end.
```

### Pool

如果要启动大量的子进程，可以用进程池的方式批量创建子进程：

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()
    p.join()
    print('All subprocesses done.')
```

对`Pool`对象调用`join()`方法会等待所有子进程执行完毕，调用`join()`之前必须先调用`close()`，调用`close()`之后就不能继续添加新的`Process`了。

请注意输出的结果，task `0`，`1`，`2`，`3`是立刻执行的，而task `4`要等待前面某个task完成后才执行，这是因为`Pool`的默认大小在我的电脑上是4，因此，最多同时执行4个进程。这是`Pool`有意设计的限制，并不是操作系统的限制。如果改成：

```python
p = Pool(5)
```

就可以同时跑5个进程。

### subprocess

`subprocess`模块可以让我们非常方便地启动一个子进程，然后控制其输入和输出。

```python
>>> import subprocess
>>> r = subprocess.call(['nslookup', 'www.python.org'])
```

如果子进程还需要输入，则可以通过`communicate()`方法输入。

```python
import subprocess
print('$ nslookup')
p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
print(output.decode('utf-8'))
print('Exit code:', p.returncode)
```

Python的`multiprocessing`模块包装了底层的机制，提供了`Queue`、`Pipes`等多种方式来交换数据，实现进程间的通信。

```python
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    print('Process to write: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    print('Process to read: %s' % os.getpid())
    while True:
        value = q.get(True)
        print('Get %s from queue.' % value)

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()
    # 启动子进程pr，读取:
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    pr.terminate()
```

## 多线程

启动一个线程就是把一个函数传入并创建`Thread`实例，然后调用`start()`开始执行：

```python
import time, threading

# 新线程执行的代码:
def loop():
    print('thread %s is running...' % threading.current_thread().name)
    n = 0
    while n < 5:
        n = n + 1
        print('thread %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
    print('thread %s ended.' % threading.current_thread().name)

print('thread %s is running...' % threading.current_thread().name)
t = threading.Thread(target=loop, name='LoopThread') # 如果不起名字Python就自动给线程命名为Thread-1，Thread-2……
t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)

运行结果:
thread MainThread is running...
thread LoopThread is running...
thread LoopThread >>> 1
thread LoopThread >>> 2
thread LoopThread >>> 3
thread LoopThread >>> 4
thread LoopThread >>> 5
thread LoopThread ended.
thread MainThread ended.
```

### Lock

多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了，需要加锁（涉及操作系统的知识），坏处是可能造成死锁，阻止了多线程并发执行。

Python中创建一个锁，`threading.Lock()`。

```python
balance = 0
lock = threading.Lock()

def run_thread(n):
    for i in range(100000):
        # 先要获取锁:
        lock.acquire()
        try:
            # 当多个线程同时执行lock.acquire()时，只有一个线程能成功地获取锁，然后继续执行代码，其他线程就继续等待直到获得锁为止。
            change_it(n)
        finally:
            # 改完了一定要释放锁:
            lock.release()
```

### ThreadLocal

一个`ThreadLocal`变量虽然是全局变量，但每个线程都只能读写自己线程的独立副本，互不干扰。`ThreadLocal`解决了参数在一个线程中各个函数之间互相传递的问题。（线程自己的全局变量）

```python
import threading
    
# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
    # 获取当前线程关联的student:
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):
    # 绑定ThreadLocal的student:
    local_school.student = name
    process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()
```

## 分布式进程

在Thread和Process中，应当优选Process，因为Process更稳定，而且，Process可以分布到多台机器上，而Thread最多只能分布到同一台机器的多个CPU上。

Python的`multiprocessing`模块不但支持多进程，其中`managers`子模块还支持把多进程分布到多台机器上。一个服务进程可以作为调度者，将任务分布到其他多个进程中，依靠网络通信。

```python
# task_master.py

import random, time, queue
from multiprocessing.managers import BaseManager

# 发送任务的队列:
task_queue = queue.Queue()
# 接收结果的队列:
result_queue = queue.Queue()

# 从BaseManager继承的QueueManager:
class QueueManager(BaseManager):
    pass

# 把两个Queue都注册到网络上, callable参数关联了Queue对象:
QueueManager.register('get_task_queue', callable=lambda: task_queue)
QueueManager.register('get_result_queue', callable=lambda: result_queue)
# 绑定端口5000, 设置验证码'abc':
manager = QueueManager(address=('', 5000), authkey=b'abc')
# 启动Queue:
manager.start()
# 获得通过网络访问的Queue对象:
task = manager.get_task_queue()
result = manager.get_result_queue()
# 放几个任务进去:
for i in range(10):
    n = random.randint(0, 10000)
    print('Put task %d...' % n)
    task.put(n)
# 从result队列读取结果:
print('Try get results...')
for i in range(10):
    r = result.get(timeout=10)
    print('Result: %s' % r)
# 关闭:
manager.shutdown()
print('master exit.')
```

```python
# task_worker.py

import time, sys, queue
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 连接到服务器，也就是运行task_master.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与task_master.py设置的完全一致:
m = QueueManager(address=(server_addr, 5000), authkey=b'abc')
# 从网络连接:
m.connect()
# 获取Queue的对象:
task = m.get_task_queue()
result = m.get_result_queue()
# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n*n)
        time.sleep(1)
        result.put(r)
    except Queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.')
```

# 十四、正则表达式

`\d`可以匹配一个数字

`\w`可以匹配一个字母或数字

`.`可以匹配任意字符（单个），用`*`表示任意个字符（>=0），用`+`表示至少一个字符(>=1)，用`?`表示0个或1个字符，用`{n}`表示n个字符，用`{n,m}`表示n-m个字符

要做更精确地匹配，可以用`[]`表示范围，如`[0-9a-zA-Z\_]`可以匹配一个数字、字母或者下划线（单个）

`A|B`可以匹配A或B，所以`(P|p)ython`可以匹配`'Python'`或者`'python'`。

`^`表示行的开头，`^\d`表示必须以数字开头。

`$`表示行的结束，`\d$`表示必须以数字结束。

## re模块

- match()

  `match()`方法判断是否匹配，如果匹配成功，返回一个`Match`对象，否则返回`None`。

```python
>>> import re
>>> re.match(r'^\d{3}\-\d{3,8}$', '010-12345')
<_sre.SRE_Match object; span=(0, 9), match='010-12345'>
>>> re.match(r'^\d{3}\-\d{3,8}$', '010 12345')
>>>
```

- 切分字符串 split() 

```python
>>> re.split(r'\s+', 'a b   c') #　存在多个空格也可以正常分割，str的split(' ')则只能分割一个字符串
['a', 'b', 'c']
```

- 分组
用`()`表示的就是要提取的分组（Group）

```python
>>> m = re.match(r'^(\d{3})-(\d{3,8})$', '010-12345')
>>> m
<_sre.SRE_Match object; span=(0, 9), match='010-12345'>
>>> m.group(0)　　＃ 正则表达式中用()定义了组，则可以用group()方法提取出子串
'010-12345'
>>> m.group(1)
'010'
>>> m.group(2)
'12345'

#　group(0)永远是原始字符串，group(1)、group(2)……表示第1、2、……个子串。
```

### 贪婪匹配

最后需要特别指出的是，正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符。举例如下，匹配出数字后面的`0`：

```python
>>> re.match(r'^(\d+)(0*)$', '102300').groups()
('102300', '')
```

由于`\d+`采用贪婪匹配，直接把后面的`0`全部匹配了，结果`0*`只能匹配空字符串了。

必须让`\d+`采用非贪婪匹配（也就是尽可能少匹配），才能把后面的`0`匹配出来，加个`?`就可以让`\d+`采用非贪婪匹配：

```python
>>> re.match(r'^(\d+?)(0*)$', '102300').groups()
('1023', '00')
```

### 编译

如果一个正则表达式要重复使用几千次，出于效率的考虑，我们可以预编译该正则表达式，接下来重复使用时就不需要编译这个步骤了，直接匹配：

```python
>>> import re
# 编译:
>>> re_telephone = re.compile(r'^(\d{3})-(\d{3,8})$')
# 使用：
>>> re_telephone.match('010-12345').groups()
('010', '12345')
>>> re_telephone.match('010-8086').groups()
('010', '8086')
```

# 十五、异步IO

当代码需要执行一个耗时的IO操作时，它只发出IO指令，并不等待IO结果，然后就去执行其他代码了。一段时间后，当IO返回结果时，再通知CPU进行处理。

异步IO模型需要一个消息循环，在消息循环中，主线程不断地重复“读取消息-处理消息”这一过程：

```python
loop = get_event_loop()
while True:
    event = loop.get_event()
    process_event(event)
```

## 协程

协程(Coroutine)，执行过程中，在子程序内部可中断，然后转而执行别的子程序，在适当的时候再返回来接着执行。类似多线程，但其实是**由一个线程执行**。

Python对协程的支持是通过generator实现的。

在generator中，我们不但可以通过`for`循环来迭代，还可以不断调用`next()`函数获取由`yield`语句返回的下一个值。

但是Python的`yield`不但可以返回一个值，它还可以接收调用者发出的参数。

来看例子：

传统的生产者-消费者模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。

如果改用协程，生产者生产消息后，直接通过`yield`跳转到消费者开始执行，待消费者执行完毕后，切换回生产者继续生产，效率极高：

```python
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None) 
    '''
    在一个生成器函数未启动之前，是不能传递值进去。也就是说在使用c.send(n)之前，必须先使用c.send(None)或者next(c)来返回生成器的第一个值。
    c.send(None)，yield第一次中断，返回''，然后可向yield发送数据，yield接收数据之后，赋值给n
    '''
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

c = consumer()
produce(c)
```

执行结果：

```
[PRODUCER] Producing 1...
[CONSUMER] Consuming 1...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 2...
[CONSUMER] Consuming 2...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 3...
[CONSUMER] Consuming 3...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 4...
[CONSUMER] Consuming 4...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 5...
[CONSUMER] Consuming 5...
[PRODUCER] Consumer return: 200 OK
```

注意到`consumer`函数是一个`generator`，把一个`consumer`传入`produce`后：

1. 首先调用`c.send(None)`启动生成器；
2. 然后，一旦生产了东西，通过`c.send(n)`切换到`consumer`执行；
3. `consumer`通过`yield`拿到消息，处理，又通过`yield`把结果传回；
4. `produce`拿到`consumer`处理的结果，继续生产下一条消息；
5. `produce`决定不生产了，通过`c.close()`关闭`consumer`，整个过程结束。

整个流程无锁，由一个线程执行，`produce`和`consumer`协作完成任务，所以称为“协程”，而非线程的抢占式多任务。

最后套用Donald Knuth的一句话总结协程的特点：

“子程序就是协程的一种特例。”

## asyncio

`asyncio`是Python 3.4版本引入的标准库，内置了对异步IO的支持。

`asyncio`的编程模型就是一个消息循环。我们从`asyncio`模块中直接获取一个`EventLoop`的引用，然后把需要执行的协程扔到`EventLoop`中执行，就实现了异步IO。

用`asyncio`实现`Hello world`代码如下：

```python
import asyncio

@asyncio.coroutine
def hello():
    print("Hello world!")
    # 异步调用asyncio.sleep(1):
    r = yield from asyncio.sleep(1)
    print("Hello again!")

# 获取EventLoop:
loop = asyncio.get_event_loop()
# 执行coroutine
loop.run_until_complete(hello())
loop.close()
```

`@asyncio.coroutine`把一个generator标记为coroutine类型，然后，我们就把这个`coroutine`扔到`EventLoop`中执行。

`hello()`会首先打印出`Hello world!`，然后，`yield from`语法可以让我们方便地调用另一个`generator`。由于`asyncio.sleep()`也是一个`coroutine`，所以线程不会等待`asyncio.sleep()`，而是直接中断并执行下一个消息循环。当`asyncio.sleep()`返回时，线程就可以从`yield from`拿到返回值（此处是`None`），然后接着执行下一行语句。

把`asyncio.sleep(1)`看成是一个耗时1秒的IO操作，在此期间，主线程并未等待，而是去执行`EventLoop`中其他可以执行的`coroutine`了，因此可以实现并发执行。

```python
import threading
import asyncio

@asyncio.coroutine
def hello():
    print('Hello world! (%s)' % threading.currentThread())
    yield from asyncio.sleep(1)
    print('Hello again! (%s)' % threading.currentThread())

loop = asyncio.get_event_loop()
tasks = [hello(), hello()]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```

异步操作需要在`coroutine`中通过`yield from`完成；

多个`coroutine`可以封装成一组Task然后并发执行。

## async/await

从Python 3.5开始引入了新的语法`async`和`await`，可以让coroutine的代码更简洁易读。

`async`和`await`是针对coroutine的新语法，要使用新的语法，只需要做两步简单的替换：

1. 把`@asyncio.coroutine`替换为`async`；
2. 把`yield from`替换为`await`。

```python
@asyncio.coroutine
def hello():
    print("Hello world!")
    r = yield from asyncio.sleep(1)
    print("Hello again!")
```

用新语法重新编写如下：

```python
async def hello():
    print("Hello world!")
    r = await asyncio.sleep(1)
    print("Hello again!")
```

剩下的代码保持不变。

## aiohttp

`asyncio`可以实现单线程并发IO操作。如果仅用在客户端，发挥的威力不大。如果把`asyncio`用在服务器端，例如Web服务器，由于HTTP连接就是IO操作，因此可以用单线程+`coroutine`实现多用户的高并发支持。

`asyncio`实现了TCP、UDP、SSL等协议，`aiohttp`则是基于`asyncio`实现的HTTP框架。

```python
pip install aiohttp
```

aiohttp官方文档：https://docs.aiohttp.org/en/stable/