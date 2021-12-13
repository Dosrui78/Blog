#### 1.6 Python日志

##### 1. 日志基础教程

| 级别       | 何时使用                                                     |
| :--------- | :----------------------------------------------------------- |
| `DEBUG`    | 细节信息，仅当诊断问题时适用。                               |
| `INFO`     | 确认程序按预期运行。                                         |
| `WARNING`  | 表明有已经或即将发生的意外（例如：磁盘空间不足）。程序仍按预期进行。 |
| `ERROR`    | 由于严重的问题，程序的某些功能已经不能正常执行               |
| `CRITICAL` | 严重的错误，表明程序已不能继续执行                           |

>默认的级别是 `WARNING`，意味着只会追踪该级别及以上的事件，除非更改日志配置。所追踪事件可以以不同形式处理。最简单的方式是输出到控制台。另一种常用的方式是写入磁盘文件。

-----------

更改显示消息的格式

要更改用于显示消息的格式，你需要指定要使用的格式:

```python
import logging
logging.basicConfig(filename='myapp.log', format='%(asctime)s - %(funcName)s - %(levelname)s: %(message)s', level=logging.DEBUG, datefmt='%m/%d/%Y %I:%M:%S %p')
```

output（其实在运行栏看不到输出，直接存到log文件了）

```
11/29/2021 11:37:35 AM - <module> - INFO: lalala
```

> filename为日志储存的位置，format为日志显示的格式，asctime表示日期和时间，funcName表示日志输出在哪个函数下，levelname表示日志级别，message表示日志要输出的内容，level指定日志最低级别，datefmt表示日期格式转换的格式。

-----------

##### 2. 进阶日志教程

日志库采用模块化方法，并提供几类组件：记录器、处理器、过滤器和格式器。

- 记录器暴露了应用程序代码直接使用的接口。
- 处理器将日志记录（由记录器创建）发送到适当的目标。
- 过滤器提供了更细粒度的功能，用于确定要输出的日志记录。
- 格式器指定最终输出中日志记录的样式。

日志事件信息在 [`LogRecord`](https://docs.python.org/zh-cn/3/library/logging.html#logging.LogRecord) 实例中的记录器、处理器、过滤器和格式器之间传递。

通过调用 [`Logger`](https://docs.python.org/zh-cn/3/library/logging.html#logging.Logger) 类（以下称为 *loggers* ， 记录器）的实例来执行日志记录。 每个实例都有一个名称，它们在概念上以点（句点）作为分隔符排列在命名空间的层次结构中。 例如，名为 'scan' 的记录器是记录器 'scan.text' ，'scan.html' 和 'scan.pdf' 的父级。 记录器名称可以是你想要的任何名称，并指示记录消息源自的应用程序区域。

在命名记录器时使用的一个好习惯是在每个使用日志记录的模块中使用模块级记录器，命名如下:

```python
logger = logging.getLogger(__name__)
```

配置日志记录

handlers种类（常用）

1. logging.StreamHandler
   使用这个Handler可以向类似与sys.stdout或者sys.stderr的任何文件对象(file object)输出信息。
   它的构造函数是：StreamHandler([strm])
   其中strm参数是一个文件对象。
   默认是sys.stderr
2. logging.FileHandler
   和StreamHandler类似，用于向一个文件输出日志信息。不过FileHandler会帮你打开这个文件。
   它的构造函数是：FileHandler(filename[,mode])
   filename是文件名，必须指定一个文件名。
   mode是文件的打开方式。
   默认是’a'，即添加到文件末尾。

输出到控制台：

```python
import logging

# create logger
logger = logging.getLogger('simple_example')
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# create formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch
ch.setFormatter(formatter)

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.debug('debug message')
logger.info('info message')
logger.warning('warn message')
logger.error('error message')
logger.critical('critical message')
```

输出到文件（接上条）：

```python
fh = logging.fileHandler("文件路径")
fh.setLevel(logging.DEBUG)
fh.setFormatter(formatter)
logger.addHandler(fh)
```

从命令行运行此模块将生成以下输出：

```
$ python simple_logging_module.py
2005-03-19 15:10:26,618 - simple_example - DEBUG - debug message
2005-03-19 15:10:26,620 - simple_example - INFO - info message
2005-03-19 15:10:26,695 - simple_example - WARNING - warn message
2005-03-19 15:10:26,697 - simple_example - ERROR - error message
2005-03-19 15:10:26,773 - simple_example - CRITICAL - critical message
```

###### 

------

##### 3. loguru

> 上面的实现方式已经是一个较为可行的配置方案了。然而，我还是会感觉到有些 Handler 配起来麻烦，尤其是新建一个项目的很多时候懒得去写一些配置。即使是不用上文的配置，用最基本的几行 logging 配置，像如下的通用配置：

```
import logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger("__name__")
logger.info("hi~")
```

loguru基本使用

```python
from loguru import logger
logger.debug('this is a debug message')
```

看到了吧，不需要配置什么东西，直接引入一个 logger，然后调用其 debug 方法即可。 在 loguru 里面有且仅有一个主要对象，那就是 logger，loguru 里面有且仅有一个 logger，而且它已经被提前配置了一些基础信息，比如比较友好的格式化、文本颜色信息等等。 上面的代码运行结果如下：

```python
2021-11-29 13:49:02.765 | DEBUG    | __main__:<module>:22 - this is a debug message!
```

可以看到其默认的输出格式是上面的内容，有时间、级别、模块名、行号以及日志信息，不需要手动创建 logger，直接使用即可，另外其输出还是彩色的，看起来会更加友好。 以上的日志信息是直接输出到控制台的，并没有输出到其他的地方，如果想要输出到其他的位置，比如存为文件，我们只需要使用一行代码声明即可。 例如将结果同时输出到一个 runtime.log 文件里面，可以这么写：

```
from loguru import logger

logger.add('runtime.log')
logger.debug('this is a debug')
```

很简单吧，我们也不需要再声明一个 FileHandler 了，就一行 add 语句搞定，运行之后会发现目录下 runtime.log 里面同样出现了刚刚控制台输出的 DEBUG 信息。 上面就是一些基本的使用，但这还远远不够，下面我们来详细了解下它的一些功能模块。

loguru详细使用

```python
from loguru import logger
trace = logger.add('runtime.log', format="{time}{level}{message}", filter="my_module", level="INFO", rotation="500 MB", retention="10 days", compression="zip")
logger.debug('this is a debug message!')
logger.remove(trace)
logger.debug('this is a debug message!')
```

rotation也可以换成rotation='00:00'，rotation='1 week'。