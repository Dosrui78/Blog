### 4.3 Hook的使用

#### 4.31 Hook定义

> Hook技术又叫钩子函数，在系统没有调用该函数前，钩子程序就先捕获该消息，钩子函数先得到控制权，这时钩子函数既可以加工处理（改变）该函数的执行行为，还可以强制结束消息的传递。简单来说，就是把**系统的程序**拉出来变成我们自己执行代码片段。

在js中，系统程序可以指浏览器API，也可以之代码中实现的一些方法等

分类：手动hook，自动hook

---

####  4.32 Hook步骤

1. 寻找hook点
2. 编写hook逻辑
3. 调试

函数hook公式：

```
old_func = func
func = function(argument){
	my task;
	return func.apply(argument)
}
func.prototype...... = ......
//func：要hook的函数
```

对象中属性Hook公式：

```
obj_attr = obj.attr
Object.defineProperty(obj,'attr',{
		get:function(){
			console.log(cookie_cache);
			return old_attr
		},
		set:function(val){
			return ......
		}
})
```

----------

#### 4.34 Hook案例

##### 中国专利网（常用案例）

> Hook eval函数

```
eval_bk = eval
eval = function(val){
	debugger;
	return eval_bk(val);  // 重写eval函数
}
eval.toString = function(){
	return "function eval() { [native code] }"  // 指纹的伪装
}
```

----

#### 4.35 油猴脚本Hook

```
@name :脚本名，你爱叫什么叫什么。
@namespace ：脚本的命名空间。（一般就是写个url，告诉自己用在哪里）。
@version：版本，你爱写多少写多少。
@author：作者，你爱写谁写谁。
@description：描述，你爱怎么描述怎么描述。反正别人也看不懂。
@homepage, @homepageURL, @website and @source： 一般正常人都不写这几个参数。从脚本												名称链接到给定页面的作者主页。
@icon, @iconURL and @defaulticon：一般正常人都不写这几个参数，低分辨率脚本图标。
@icon64 and @icon64URL：一般正常人都不写这几个参数，高分辨率脚本图标。
@updateURL： 更新检查的url，@version参数开启，会向url检查更新。
@downloadURL：定时监测到更新会自动下载url内容，若为 none则不检查
@include @match @exclude：匹配规则相关。支持精确匹配与正则匹配。@exclude是排除，						    不写相当于脚本白写
@require @resource ：导包，支持url引入。比如引入 jquery，可以使用$
@connect：标记定义域，感觉很有用，但是我菜，所以很少用。
@grant：白名单函数：比如：@grant window.close 。很强大，如果默认不写则会对一些原生		函数如：eval等进行保护，显然这不是我们所期待的，所以就直接 @grant none了
@antifeature ：官方文档的意思：开发人员是否允许别人把脚本货币化。
@noframes：在主页上运行而不在iframes上运行。
@unwrap：卵用没有，官方文档的意思：在chrome上，不需要它，自动被忽略了（淦！）
@nocompat：一般默认，官方文档写了好长，大概的意思指支持标记浏览器。如： 								@nocompat Chrome，这样就不能在火狐浏览器运行它了。

逆向中最重要的参数！！！！
@run-at： 指定油猴脚本在什么时候执行，默认是在所有js加载完成后执行。（那hook脚本还有个屁用啊！）
参数：
@run-at document-start  ：脚本尽快注入（相当于script断点之后，进入页面一瞬间注入）
@run-at document-body ：如果页面body元素存在，则注入（所以练习平台第二题就注不进去了）
@run-at document-end ：脚本将在DOMContentLoaded事件发生注入。
@run-at document-idle：默认值。脚本将在DOMContentLoaded事件发生之后才注入。
@run-at context-menu：如果在浏览器上下文菜单中单击脚本（仅限于基于桌面Chrome的浏览						 器），则会注入脚本。
```

油猴脚本——万能hook eval函数

```
// ==UserScript==
// @name         万能hook eval函数
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  eval-everything
// @author       You
// @match        https://www.tampermonkey.net/scripts.php?version=4.13&ext=dhdg&updated=true
// @icon         https://www.google.com/s2/favicons?domain=tampermonkey.net
// @grant        none
// @run-at       document-start
// ==/UserScript==

alert('hook success');
var eval_bk = eval;
eval = function(val){
    debugger;
    console.log(val)
    return eval_bk(val);
}
eval.toString = function(){
	return "function eval() { [native code] }"
	};
eval.length = 1;
```

