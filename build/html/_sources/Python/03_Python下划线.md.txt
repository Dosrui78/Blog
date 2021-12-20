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

###### 访问限制

在Class内部，可以有属性和方法，而外部代码阔以通过直接调用实例变量的方法来操作数据，这样就隐藏了内部的复杂逻辑。

但是，从前面Student类的定义来看，外部代码还是可以自由地修改一个实例的`name`、`score`属性：

```python
>>> bart = Student('Bart Simpson', 59)
>>> bart.score
59
>>> bart.score = 99
>>> bart.score
99
```

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量`（private）`，只有内部可以访问，外部不能访问，所以，我们把Student类改一改：

```python
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))
```

改完后，对于外部代码来说，没什么变动，但是已经无法从外部访问`实例变量.__name`和`实例变量.__score`了：

```python
>>> bart = Student('Bart Simpson', 59)
>>> bart.__name
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute '__name'
```

这样就确保了外部代码不能随意修改对象内部的状态，这样通过访问限制的保护，代码更加健壮。

但是如果外部代码要获取name和score怎么办？可以给Student类增加`get_name`和`get_score`这样的方法：

```python
class Student(object):
    ...

    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
```

如果又要允许外部代码修改score怎么办？可以再给Student类增加`set_score`方法：

```python
class Student(object):
    ...

    def set_score(self, score):
        self.__score = score
```

你也许会问，原先那种直接通过`bart.score = 99`也可以修改啊，为什么要定义一个方法大费周折？因为在方法中，可以对参数做检查，避免传入无效的参数：

```python
class Student(object):
    ...

    def set_score(self, score):
        if 0 <= score <= 100:
            self.__score = score
        else:
            raise ValueError('bad score')
```

需要注意的是，在Python中，变量名类似`__xxx__`的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用`__name__`、`__score__`这样的变量名。

有些时候，你会看到以一个下划线开头的实例变量名，比如`_name`，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问`__name`是因为Python解释器对外把`__name`变量改成了`_Student__name`，所以，仍然可以通过`_Student__name`来访问`__name`变量：

```python
>>> bart._Student__name
'Bart Simpson'
```

但是强烈建议你不要这么干，因为不同版本的Python解释器可能会把`__name`改成不同的变量名。

总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉。

最后注意下面的这种*错误写法*：

```python
>>> bart = Student('Bart Simpson', 59)
>>> bart.get_name()
'Bart Simpson'
>>> bart.__name = 'New Name' # 设置__name变量！
>>> bart.__name
'New Name'
```

表面上看，外部代码“成功”地设置了`__name`变量，但实际上这个`__name`变量和class内部的`__name`变量*不是*一个变量！内部的`__name`变量已经被Python解释器自动改成了`_Student__name`，而外部代码给`bart`新增了一个`__name`变量。不信试试：

```python
>>> bart.get_name() # get_name()内部返回self.__name
'Bart Simpson'
```

再看一个例子：

```python
class Test(object):
    def __init__(self):
        self.foo = 11
        self._bar = 23
        self.__baz = 42
```

让我们看看这个对象的属性使用内置的dir()函数:

```python
>>> t = Test()
>>> dir(t)
['_Test__baz', '__class__', '__delattr__', '__dict__', '__dir__',
 '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',
 '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__',
 '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__',
 '__setattr__', '__sizeof__', '__str__', '__subclasshook__',
 '__weakref__', '_bar', 'foo']
```

这会给我们一个带有对象属性的列表。让我们看看这个列表，并查找原始变量名`foo`、`_bar`和`__ baz`—时保证会发现一些有趣的变化。

`self.foo`在属性列表中，`foo`变量显示为未修改的`foo`。

`self._bar`的行为方式是一样的——它在类中显示为`_bar`。就像我之前说的，前导下划线只是一种惯例。给程序员的提示。

然而`self.__baz`看起来有点不同。当在该列表中搜索`__baz`时，将看到没有具有该名称的变量。

那么，到底发生了什么呢?

如果仔细查看，就会看到这个对象上有一个名为`_Test__baz`的属性。这是Python解释器应用的命名混乱。这样做是为了保护变量不被子类覆盖。

让我们创建另一个类，扩展测试类，并尝试重写添加到构造函数中的现有属性:

```python
class ExtendedTest(Test):
    def __init__(self):
		super().__init__()
		self.foo = 'overridden'
        self._bar = 'overridden'
        self.__baz = 'overridden'
```

```python
>>> t2 = ExtendedTest()
>>> t2.foo
'overridden'
>>> t2._bar
'overridden'
>>> t2.__baz
AttributeError: "'ExtendedTest' object has no attribute '__baz'"
```

天呐， 当我们试图检查`t2.__baz`的值时，为什么会得到那个AttributeError ?名字混乱又来了!事实证明，这个对象甚至没有一个`__baz`属性:

```python
>>> dir(t2)
['_ExtendedTest__baz', '_Test__baz', '__class__', '__delattr__',
 '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__',
 '__getattribute__', '__gt__', '__hash__', '__init__', '__le__',
 '__lt__', '__module__', '__ne__', '__new__', '__reduce__',
 '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__',
 '__subclasshook__', '__weakref__', '_bar', 'foo', 'get_vars']
```

正如你所看到的，为了防止意外修改，`__baz`变成了`_Extendedtest__baz`:

```python
>>> t2._ExtendedTest__baz
'overridden'
```

但是最初的`_Test__baz`仍然存在:

```python
>>> t2._Test__baz
42
```

双下划线命名错误对我们来说完全是清楚的。看看下面的例子可以证实这一点:

```python
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

```python
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

```python
_MangledGlobal__mangled = 23

class MangledGlobal:
    def test(self):
        return __mangled

>>> MangledGlobal().test()
23
```

在本例中，声明了一个名为`_Mangledglobal__mangled`的全局变量。然后，我在名为MangledGlobal的类的上下文中访问了这个变量。由于名称混乱，能够在类的test()方法中引用`_Mangledglobal__mangled`全局变量，就像在类的test()方法中引用的那样。

Python解释器会自动将名称解析扩展为`_Mangledglobal__mangled`，因为它以两个下划线字符开头。这证明了名称混乱并不是特定地与类属性绑定在一起的。它适用于在类上下文中以两个下划线开头的任何名称。

##### 4. 双重领先和落后强调： **`__var__`**

也许令人惊讶的是，如果一个名字同时以双下划线开始和结束，则不会应用名称修饰。 由双下划线前缀和后缀包围的变量不会被Python解释器修改：

```python
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

```python
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

```python
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

