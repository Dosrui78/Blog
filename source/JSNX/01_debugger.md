#### 5.1 JS调试干扰专题

> 进行devtool调试时经常会卡debugger或者有各种干扰项，还有F12摁了没反应的情况，此时可以在浏览器右上角找到调试入口。

##### 1. 构建debugger

简单粗暴型

```
while(!![]){
debugger;
}
```

借助构造器[Function](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)构造 

```js
Function("debugger;").call()
Function("debugger;").apply()

a = Function("debugger;").bind();
a()

Function().constrctor=Function()    =>	Function().constructor("debugger;").call();
									=>	(Function().constructor("debugger")).call();
									=>	(function(){}).constructor("debugger").call()
```

一般都带点混淆

```js
XXX.constructor("debugger").call("action")
(function(){return !![];})["constructor"]("debugger")["call"]("action") 

Function("debugger").call() 	
//这里根据后面带的参数个数、类型来定，如果参数为多个，或者类型为数组时，call需换成apply,或者bind
```



##### 2. 无限debugger处理方法

并不是真正的debugger，而是很频繁的去debugger，解决无限debugger才能进行下一步找到函数的入口；

常用方法如下：

- 如果是Function原理的debugger，可以重写函数构造器；
- 如果是eval型构造器，可以重构eval函数；
- 如果是定时器，且上条办法失效了，可以重构定时器；
- 如果以上都失效，向上找堆栈，在进入无限debugger之前打上断点将触发无限debugger的函数置空（最麻烦，但是适用性最广）。

debugger与计时器搭配型，他的原理就是定时器设置一个很短的时间，频繁的去执行含有debugger的方法，以达到无限debugger的效果，这里我就写一个简单的demo来举例吧

```js
function a(){
    debugger;
    b();
}
function b(){
    console.log("hello world!")
}
setInterval(a,200); 	//每0.2秒触发一次
```

这时候就可以通过hook的方式去重写这个定时器方法：

```js
setInterval_new=setInterval   	// 把setInterval赋给新的setInterval方法
setInterval=function(a,b){		// 重写旧的setInterval方法
if(a.indexOf("debugger")==-1){  // -1表示没有debugger
	return setInterval_new(a,b)
	}
}	// 如果这里的定时器只是单纯的用来频繁触发debugger的话，就直接将其干掉了
```

上面所提的eval手段也是通过这样的手段将其重写。

同上当出现"debugger"在constructor里面时，重写方法如下：

```js
Function.prototype.constructor_bc=Function.prototype.constructor;
Function.prototype.constructor=function(){
	if(arguments==="debugger"){
		"什么都不做"
	}	//利用arguments关键字的属性获取当前方法里面的参数
    else{
    	return Function.prototype.constructor_bc.apply(this,arguments)  
    	//跟上面处理debugger和定时器一起用的处理方法一样
    }
    }	//注："Function"的含义并不是某度上面有些人说"js不区分大小"，可以去看看比较正规一点的文档了解一下
```

----

##### 3. 调试干扰-调试检测

- 禁止F12右键的解决方案，浏览器的更多工具中的开发者工具
- 呼出控制台弹窗或者跳转：script断点，并且先尝试进行关键词搜索找线索。随机打上断点，不断缩小检测范围，直到找到。若是静态js/假动态直接可以[AutoResponse](https://blog.csdn.net/yu1014745867/article/details/72843259)干掉，若是真动态真在执行控制台该检测逻辑附近的时候重置这个函数（这个可以参考无限debugger处理方案，重写函数，hook关键位置等）

**案例一：猿人学第16题**

反调试类型：进入网页右键会自动重定向到主页。

解决办法：先在主页打上`script断点`后进入16题页面，然后一直按F8执行到js所在界面。如下图：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_22-23-36.png)

接着搜索open（别问我为啥知道，因为这种一般都是open相关的函数做了手脚）

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_22-27-16.png)

观察上图的逻辑可知这段是定义了一个`onOpen函数`，执行跳转红框中的网站的操作，打上断点如下：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_22-30-32.png)

F8执行到如下界面：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_23-14-17.png)

搜索onOpen，打上断点，跟进后再在控制台输入`e.onOpen`

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_23-18-03.png)

对`e.onOpen`置空，然后单步跟进，跳到下一断点

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_23-19-27.png)

断点停在了`ConsoleManager.onOpen=function(){...}`此时，在控制台输入`ConsoleManager.onOpen`会得到如下图所示函数，置空该函数

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_22-33-07.png)

这下我们置空了两个函数，这下反调试就被我们解决了，但是这个`Console was cleared`一直在闪烁

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-05_23-47-49.png)

在控制台执行下面语句即可

```js
console.clear = function(){}
console.info = function(){}
```



---



##### 4. 调试干扰-内存爆破

内存爆破，指js通过死循环/频繁操作数据库（包括cookie）**频繁**调取history等方式，使浏览器崩溃的一种反调试手段。

还有一种特性情况：js文件很大，电脑内存不足（这种情况在调试层面几乎无解）

特点：

1. 正常运行时，一切正常
2. 调试时利用时间差，调试特点等将控制流推入循环
3. 利用正则/toString()判断代码是否进行格式化
4. 利用浏览器指纹判断是否为浏览器环境



---

##### 5. js逆向-事件监听断点

- 非常频繁使用的**事件监听**断点： script、XHR

* 一般频繁使用的： Dom断点、Countrol、timer

* 不太常用但是偶尔用到的： Mouse

- 的可能只是在特殊情况下才会用到。



##### 6. 逆向分析找加密参数基本流程

- 打开抓包软件找到url的位置 进行重放攻击
  - 可行：搜索加密参数
    - 搜不到，检查数据是否是json数据，是的话打XHR断点
  - 不可行：
    - 可能是动态cookie，对cookie进行进一步分析