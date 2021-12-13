#### 1.3 我终于把Python中下划线的含义弄清楚了

> *Python变量和方法名称中的下划线和下划线分别*是*什么意思？*

· 单引号下划线： `_var`

· 单尾划线： `var_`

· 双领先下划线： `__var`

· 领先和落后双下划线： `__var__`

· 单下划线： `_`

---

##### 1. 单引号下划线： **`_var`**

当涉及变量和方法名称时，单个下划线前缀仅具有约定的含义。这是对程序员的提示，它意味着Python同意它的含义，但不影响程序的行为。

下划线前缀是向其他程序员的***提示***，即以单个下划线开头的变量或方法供内部使用。此约定在PEP 8中定义。

这不是Python强制执行的。Python在Java的“私有”变量和“公共”变量之间没有明显的区别。就像有人贴了一个很小的下划线警告标志，上面写着：

*“嘿，这并不是要真正成为此类的公共接口的一部分。最好不要管它。”*

看下面的例子：

```python
class Test:
    def __init__(self):
        self.foo = 11
        self._bar = 23
```

如果你实例化这个类并试图访问它的构造函数中定义的foo和_bar属性，会发生什么?让我们来看看:

```
>>> t = Test()
>>> t.foo
11
>>> t._bar
23
```

> 看到_bar中的前一个下划线并没有阻止我们“进入”类并访问该变量的值。
>
> 这是因为Python中的单个下划线前缀仅仅是一种约定
>
> 但是，前导下划线确实会影响名称从模块导入的方式。假设在一个名为my_module的模块中有以下代码:

```Python
# This is my_module.py:
def external_func():
    return 23

def _internal_func():
    return 42
```

现在，如果使用通配符导入来从模块中导入所有名称，Python将不会导入带有前导下划线的名称(除非模块定义了覆盖此行为的_all__列表):

```javascript
>>> from my_module import *
>>> external_func()
23
>>> _internal_func()
NameError: "name '_internal_func' is not defined"
```

顺便说一下，应该避免通配符导入，因为它们使名称空间中出现的名称变得不清楚。

与通配符导入不同，常规导入不受主要的单下划线命名约定的影响:

```javascript
>>> import my_module
>>> my_module.external_func()
23
>>> my_module._internal_func()
42
```

单下划线是一种Python命名约定，表示某个名称是供内部使用的。它通常不是由Python解释器强制执行的，只是对程序员的提示。

----------

##### 2. 单尾划线： **`var_`**

有时候，一个变量最合适的名字已经被一个关键字代替了。因此，类或def之类的名称在Python中不能用作变量名。在这种情况下，你可以添加一个下划线打破命名冲突:

```javascript
>>> def make_object(name, class):
SyntaxError: "invalid syntax"

>>> def make_object(name, class_):
...     pass
```

总之，惯例使用单个尾划线(后缀)来避免与Python关键字的命名冲突。在PEP 8中解释了这种约定。

------------------

##### 3. 双首下划线：**`__var`**

到目前为止，我们所讨论的命名模式仅从约定的约定中获得它们的含义。对于以双下划线开头的Python类属性(变量和方法)，情况略有不同。

双下划线前缀导致Python解释器重写属性名，以避免子类中的命名冲突。

这也叫做名字拼写——解释器改变变量的名字的方式使得在以后扩展类时很难产生冲突。

是不是听起来很抽象。所以我把这个小代码示例放在一起，可以用于理解:

```javascript
class Test(object):
    def __init__(self):
        self.foo = 11
        self._bar = 23
        self.__baz = 42
```

让我们看看这个对象的属性使用内置的dir()函数:

```javascript
>>> t = Test()
>>> dir(t)
['_Test__baz', '__class__', '__delattr__', '__dict__', '__dir__',
 '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
 '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__',
 '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
 '__setattr__', '__sizeof__', '__str__', '__subclasshook__',
 '__weakref__', '_bar', 'foo']
```

这会给我们一个带有对象属性的列表。让我们看看这个列表，并查找原始变量名foo、_bar和_ bazi—时保证会发现一些有趣的变化。

`self.foo`在属性列表中，foo变量显示为未修改的foo。

self._bar的行为方式是一样的——它在类中显示为_bar。就像我之前说的，前导下划线只是一种惯例。给程序员的提示。

然而`self.__baz`看起来有点不同。当在该列表中搜索_baz时，将看到没有具有该名称的变量。

那么，到底发生了什么呢?

如果仔细查看，就会看到这个对象上有一个名为_testbaz的属性。这是Python解释器应用的命名混乱。这样做是为了保护变量不被子类覆盖。

让我们创建另一个类，扩展测试类，并尝试重写添加到构造函数中的现有属性:

```javascript
class ExtendedTest(Test):
	def __init__(self):
    	super().__init__()	
		self.foo = 'overridden'
       	self._bar = 'overridden'
       	self.__baz = 'overridden'
```

```javascript
>>> t2 = ExtendedTest()
>>> t2.foo
'overridden'
>>> t2._bar
'overridden'
>>> t2.__baz
AttributeError: "'ExtendedTest' object has no attribute '__baz'"
```

天呐， 当我们试图检查`t2.__baz`的值时，为什么会得到那个AttributeError ?名字混乱又来了!事实证明，这个对象甚至没有一个`__baz`属性:

```javascript
>>> dir(t2)
['_ExtendedTest__baz', '_Test__baz', '__class__', '__delattr__',
 '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__',
 '__getattribute__', '__gt__', '__hash__', '__init__', '__le__',
 '__lt__', '__module__', '__ne__', '__new__', '__reduce__',
 '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
 '__subclasshook__', '__weakref__', '_bar', 'foo', 'get_vars']
```

正如你所看到的，为了防止意外修改，`__baz`变成了`_Extendedtest__baz`:

```javascript
>>> t2._ExtendedTest__baz
'overridden'
```

但是最初的`_Test__baz`仍然存在:

```javascript
>>> t2._Test__baz
42
```

双下划线命名错误对我们来说完全是清楚的。看看下面的例子可以证实这一点:

```javascript
class ManglingTest:
    def __init__(self):
        self.__mangled = 'hello'

    def get_mangled(self):
        return self.__mangled

>>> ManglingTest().get_mangled()
'hello'
>>> ManglingTest().__mangled
AttributeError: "'ManglingTest' object has no attribute '__mangled'"
```

名称修饰是否也适用于方法名称？ 是的，也适用。名称修饰会影响在一个类的上下文中，以两个下划线字符（"dunders"）开头的**所有**名称：

```javascript
class MangledMethod:
    def __method(self):
        return 42

    def call_it(self):
        return self.__method()

>>> MangledMethod().__method()
AttributeError: "'MangledMethod' object has no attribute '__method'"
>>> MangledMethod().call_it()
42
```

下面是另一个名字变形的例子，可能会让你大吃一惊:

```javascript
_MangledGlobal__mangled = 23

class MangledGlobal:
    def test(self):
        return __mangled

>>> MangledGlobal().test()
23
```

在本例中，我声明了一个名为`_Mangledglobal__mangled`的全局变量。然后，我在名为MangledGlobal的类的上下文中访问了这个变量。由于名称混乱，我能够在类的test()方法中引用`_Mangledglobal__mangled`全局变量，就像在类的test()方法中引用的那样。

Python解释器会自动将名称解析扩展为`_Mangledglobal__mangled`，因为它以两个下划线字符开头。这证明了名称混乱并不是特定地与类属性绑定在一起的。它适用于在类上下文中以两个下划线开头的任何名称。

##### 4. 双重领先和落后强调： **`__var__`**

也许令人惊讶的是，如果一个名字同时以双下划线开始和结束，则不会应用名称修饰。 由双下划线前缀和后缀包围的变量不会被Python解释器修改：

```text
class PrefixPostfixTest:
   def __init__(self):
       self.__bam__ = 42

>>> PrefixPostfixTest().__bam__
42
```

但是，Python保留了有双前导和双末尾下划线的名称，用于特殊用途。 这样的例子有，`__init__`对象构造函数，或`__call__ `--- 它使得一个对象可以被调用。

这些dunder方法通常被称为神奇方法 - 但Python社区中的许多人（包括我自己）都不喜欢这种方法。

##### 5. 单下划线 _

按照习惯，有时候单个独立下划线是用作一个名字，来表示某个变量是临时的或无关紧要的。

例如，在下面的循环中，我们不需要访问正在运行的索引，我们可以使用“_”来表示它只是一个

还可以在解包表达式中使用单个下划线。同样，这只是“按照约定”的意思，在Python解释器中没有触发任何特殊的行为。单个下划线只是一个有效的变量名。

在下面的代码示例中，我将一个car元组解压缩到单独的变量中，但我只对颜色和值感兴趣。然而，为了使解包表达式成功，我需要将tuple中包含的所有值赋给变量。这就是“_”作为占位符变量有用的地方:

```javascript
>>> car = ('red', 'auto', 12, 3812.4)
>>> color, _, _, mileage = car

>>> color
'red'
>>> mileage
3812.4
>>> _
12
```

除了用作临时变量之外，“_”在大多数Python REPLs中是一个特殊变量，它表示解释器计算的最后一个表达式的结果。

如果在解释器会话中工作，并且希望访问前面计算的结果，那么这是很方便的。或者，如果你正在动态构建对象，并且想要与它们交互，而不需要先给它们分配一个名称:

```javascript
>>> 20 + 3
23
>>> _
23
>>> print(_)
23

>>> list()
[]
>>> _.append(1)
>>> _.append(2)
>>> _.append(3)
>>> _
[1, 2, 3]
```

##### 5. 总结：Python下划线命名模式

本文中介绍的五个下划线模式在Python中的含义的简要概述。

| 图案               | 例        | 含义                                                         |
| :----------------- | :-------- | :----------------------------------------------------------- |
| 单引号下划线       | `_var`    | 表示名称的命名约定仅供内部使用。通常不由Python解释器强制执行（通配符导入除外），并且仅作为对程序员的提示。 |
| 单尾划线           | `var_`    | 按照惯例使用，以避免与Python关键字命名冲突。                 |
| 双领先下划线       | `__var`   | 在类上下文中使用时触发名称修改。由Python解释器实施。         |
| 双重领先和落后强调 | `__var__` | 表示由Python语言定义的特殊方法。避免为自己的属性使用此命名方案。 |
| 单下划线           | `_`       | 有时用作临时变量或无关紧要变量的名称（“无关紧要”）。         |

