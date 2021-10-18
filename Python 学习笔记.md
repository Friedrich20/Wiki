## Python简介

Python是解释型语言，代码在执行时会被一行一行地翻译成CPU能理解的机器码

## Python安装

### 命令行模式与Python交互模式相互切换

命令行模式 -> Python交互模式：python / python3
Python交互模式 -> 命令行模式：exit()

### 直接运行.py文件方法

在`.py`文件行首添加注释
```#!/usr/bin/env python3```
并给予文件执行权限
``` chmod a+x hello.py```

##  Python解释器

### CPython（官方默认解释器）

当我们从Python官方网站下载并安装好Python 3.x后，我们就直接获得了一个官方版本的解释器：CPython。这个解释器是用C语言开发的，所以叫CPython。在命令行下运行python就是启动CPython解释器。

CPython是使用最广的Python解释器。教程的所有代码也都在CPython下执行。

### IPython

IPython是基于CPython之上的一个交互式解释器，也就是说，IPython只是在交互方式上有所增强，但是执行Python代码的功能和CPython是完全一样的。好比很多国产浏览器虽然外观不同，但内核其实都是调用了IE。

CPython用>>>作为提示符，而IPython用In [序号]:作为提示符。

### PyPy
PyPy是另一个Python解释器，它的目标是执行速度。PyPy采用JIT技术，对Python代码进行动态编译（注意不是解释），所以可以显著提高Python代码的执行速度。

绝大部分Python代码都可以在PyPy下运行，但是PyPy和CPython有一些是不同的，这就导致相同的Python代码在两种解释器下执行可能会有不同的结果。如果你的代码要放到PyPy下执行，就需要了解PyPy和CPython的不同点。

### Jython
Jython是运行在Java平台上的Python解释器，可以直接把Python代码编译成Java字节码执行。

### IronPython

IronPython和Jython类似，只不过IronPython是运行在微软.Net平台上的Python解释器，可以直接把Python代码编译成.Net的字节码。

## Python基础

### 缩进
4个空格，便于复制黏贴
在同一个py文件里空格和Tab的缩进方式不能混用

### 字符编码
1个字节byte = 8个比特bit
一个字节能表示的最大整数为255（11111111）

ASCII码是1个字节
Unicode码通常是2个字节
UTF-8是可变长编码，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。适用于传输和保存。

### 编码解码
编码encode: str -> byte
解码decode: byte -> str

###  字符串格式化

1. 占位符 %
`print('%d + %d = %d' % (1, 2, 3))`
%d - 整数
%f - 浮点数，如%.2f可保留两位小数
%s - 字符串
%x - 十六进制整数
%% - %
2. format
     `print('{0} + {1} = {2}'.format(1, 2, 3))`
3. f-string
     `print(f'{a} + {b} = {c}')`

### list, tuple, dict和set

- list: list = [1, 2, 3]

  list.append(4)

  list.insert(3, )

  list.pop(4)

  list[0] = 9

  把list变成索引-元素对`for i, value in enumerate(['A', 'B', 'C']):`
  有序，可变

- tuple: t = (1, 2, 3)  

  有序，不可变

- dict: d = {1: 'bob', 2: 'tracy'}

  无序，key必须为不可变对象例如整数字符串

  用空间换时间

  如果要迭代value，可以用 `for value in d.values()` ，如果要同时迭代key和value，可以用 `for k, v in d.items() `

- set: s = {1, 2, ,3}
无序，去重

### 条件判断 if
```python
# x非零非空
if x:
    print(''True'')
```

### 循环 for和while
```python
for n in range(3):
    print(n)
    
while n > 1:
    print(n)
```

## 函数
### 参数
- 位置参数 `def func(x):`
- 默认参数 `def func(x = 1):`
- **可变参数** `def func(*args): # args为list或tuple`
- **关键字参数** `def func(**kw): # kw为dict`
- 命名关键字参数 `def func(name, age, *, city) # city在传入时必须指明 e.g. city = 'Shanghai'`

### 递归
尾递归是指在return中没有表达式

```python
# 汉诺塔算法
def move(n, a, b, c):
        if n == 1:
            # 将最大盘，从A柱移动到C柱
            print(a, '-->', c)
        else:
            # 最大盘移动前：将n-1个盘，从A柱借助C柱移动到B柱
            move(n-1, a, c, b)
            # 将最大盘，从A柱移动到C柱
            print(a, '-->', c)
            # 最大盘移动后：将n-1个盘，从B柱借助A柱移动到C柱
            move(n-1, b, a, c)
```

### 切片
列表倒序 `L[::-1]`

### 列表生成式
`[d for d in os.listdir('.')] # os.listdir可以列出文件和目录`

### 生成器 Generator
如何定义生成器
 - 把列表生成式最外层的`[]`改为`()`，e.g. `(x * x for x in range(10))`
 - 把函数中的`print(x)`改为`yield x`，函数则变成生成器

```python
# 斐波那契数列 Fibonacci
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

如何调用生成器
1. 首先生成一个generator对象 `o = fib(3)`
2. 获得下一个返回值 `next(o)`
3. 通过循环来获取值

```python
# 杨辉三角
def triangle(n):
    L = [1]
    while True:
        yield L
        L = [1] + [L[n]+L[n+1] for n in range(len(L)-1)] + [1]
```

### 迭代器

凡是可作用于`for`循环的对象都是`Iterable`类型；
凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列；
集合数据类型如`list`、`dict`、`str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象。
Python的`for`循环本质上就是通过不断调用`next()`函数实现的，例如：

```python
for x in [1, 2, 3, 4, 5]:
    pass
```

实际上完全等价于：

```python
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

### 匿名函数 Lambda

```python
def is_odd(n):
    return n % 2 == 1

L = list(filter(is_odd, range(1, 20)))
```

等价于

```python
L = list(filter(lambda x: x % 2 == 1, range(1, 20)))
```

### 装饰器 Decorator

1. 定义

   ```python
   def log(func):
       def wrapper(*args, **kw):
           print('call %s():' % func.__name__)
           return func(*args, **kw)
       return wrapper
   ```

2. 使用

   ```python
   @log
   def now():
       print('2020.2.11')
   ```

## 模块

`test`包目录下的`__init__.py`模块，其模块名就是包名`test.py`

### 模块标准文件模板

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```

第1行注释可以让这个文件直接在Unix/Linux/Mac上运行

第2行注释表示.py文件本身使用标准UTF-8编码

第4行表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释

第6行表示作者

第19行直接运行该模块则会执行，导入则不会执行

### 作用域

`xxx`为公开(public)变量

`__xxx__`为特殊变量

`_xxx`为私有(private)变量，可从外部访问但最好不要

`__xxx`为私有(private)变量，不可从外部访问（仍能通过`_Classname__xxx`访问）

## 面向对象编程

### 封装

```python
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))
        
    def get_score(self):
        return self._score
    
    def set_score(self, score):
        self._score = score
```

使数据不能从外部直接访问，只能通过get/set等方法

### 继承和多态

Python支持多继承，e.g. `class ArtStudent(Student, LearnMixIn):`， 前者是继承链，后者是给该类增加的功能

```python
class ArtStudent(Student):
    def print_score(self):
        print('Art Student %s: %s' % (self.__name, self.__score))
```

### 获取对象信息

`type()`可以判断对象的基本数据类型

`isinstance()`可以判断对象的继承关系

`dir()`获取一个对象的所有属性和方法

`getattr(obj, 'x')`得到obj.x

`setattr(obj, 'x', 1)`赋值obj.x = 1

`hasattr(obj, 'x')`返回一个布尔值判断obj是否有属性x

### @property 修饰器

定义

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
```

使用

```python
s = Student()
s.score = 60 # OK，实际转化为s.set_score(60)
s.score # OK，实际转化为s.get_score()
```

## 错误、调试和测试

### 错误处理

#### try...except

```python
try:
    foo()
except ValueError as e:
    print('ValueError')
except UnicodeError as e:
    print('UnicodeError')
```

#### raise

```python
raise ValueError('invalid value: %s' % s)
```

### 调试

#### assert 断言

```python
assert n != 0, 'n is zero!'
```

若满足条件`n != 0`则继续进行，否则抛出异常`AssertionError: n is zero!`

#### logging

```python
`debug`，`info`，`warning`，`error`
```

### 单元测试

```python
import unittest

from mydict import Dict

class TestDict(unittest.TestCase):
    # setUp()和tearDown()在每个测试方法调用前后都分别会被执行
    def setUp(self):
        print('setUp...')

    def tearDown(self):
        print('tearDown...')
        
    def test_init(self):
        d = Dict(a=1, b='test')
        self.assertEqual(d.a, 1)
        self.assertEqual(d.b, 'test')
        self.assertTrue(isinstance(d, dict))

    def test_key(self):
        d = Dict()
        d['key'] = 'value'
        self.assertEqual(d.key, 'value')

    def test_attr(self):
        d = Dict()
        d.key = 'value'
        self.assertTrue('key' in d)
        self.assertEqual(d['key'], 'value')

    def test_keyerror(self):
        d = Dict()
        with self.assertRaises(KeyError):
            value = d['empty']

    def test_attrerror(self):
        d = Dict()
        with self.assertRaises(AttributeError):
            value = d.empty

if __name__ == '__main__':
    unittest.main()
```

### 文档测试

```python
class Dict(dict):
    '''
    Simple dict but also support access as x.y style.

    >>> d1 = Dict()
    >>> d1['x'] = 100
    >>> d1.x
    100
    >>> d1.y = 200
    >>> d1['y']
    200
    >>> d2 = Dict(a=1, b=2, c='3')
    >>> d2.c
    '3'
    >>> d2['empty']
    Traceback (most recent call last):
        ...
    KeyError: 'empty'
    >>> d2.empty
    Traceback (most recent call last):
        ...
    AttributeError: 'Dict' object has no attribute 'empty'
    '''
    def __init__(self, **kw):
        super(Dict, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

if __name__=='__main__':
    import doctest
    doctest.testmod()
```

## Input/Output

#### 读文件

```python
f = open('/Users/michael/test.txt', 'r')
f.read()
f.close()
```

文件对象会占用操作系统的紫苑，所以使用后必须关闭，或使用`with`语句自动调用`close()`方法：

```python
with open('/path/to/file', 'r') as f:
    print(f.read())
```

#### 写文件

```python
f = open('/Users/michael/test.txt', 'w')
f.write('Hello, world!')
f.close()
```

```python
with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```

#### 操作文件和目录

```python
# 查看当前目录的绝对路径:
os.path.abspath('.')
'/Users/michael'
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
os.rmdir('/Users/michael/testdir')
# 拆分路径，后一部分是最后级别的目录或文件名
os.path.split('/Users/michael/testdir/file.txt')
('/Users/michael/testdir', 'file.txt')
# 拆分路径，后一部分是文件扩展名
os.path.splitext('/path/to/file.txt')
('/path/to/file', '.txt')
```

#### 序列化

把变量从内存中变成可存储或传输的过程称为序列化，plckling/serialization/marshalling/flattening;

```python
import json
d = dict(name='Bob', age=20, score=88)
json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'
```

把变量内容从序列化的对象重新读到内存里称为反序列化

```python
json_str = '{"age": 20, "score": 88, "name": "Bob"}'
json.loads(json_str)
{'age': 20, 'score': 88, 'name': 'Bob'}
```