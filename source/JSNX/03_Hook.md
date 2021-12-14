### 5.3 Hook的使用

#### 1. Hook定义

> Hook技术又叫钩子函数，在系统没有调用该函数前，钩子程序就先捕获该消息，钩子函数先得到控制权，这时钩子函数既可以加工处理（改变）该函数的执行行为，还可以强制结束消息的传递。简单来说，就是把**系统的程序**拉出来变成我们自己执行代码片段。

在js中，系统程序可以指浏览器API，也可以之代码中实现的一些方法等

分类：手动hook，自动hook

---

####  2. Hook步骤

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

#### 3. Hook案例

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

#### 4. 油猴脚本Hook

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

------

#### 5. Hook set-cookie

**HTTP cookies**

> HTTP Cookie（也叫 Web Cookie 或浏览器 Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向**同一服务器**再发起请求时被携带并发送到服务器上。通常，它用于告知服务端两个请求是否来自**同一浏览器**，如保持用户的登录状态。Cookie 使基于[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#http_is_stateless_but_not_sessionless)的HTTP协议记录稳定的状态信息成为了可能。Cookie 主要用于以下三个方面：
>
> - 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
> - 个性化设置（如用户自定义设置、主题等）
> - 浏览器行为跟踪（如跟踪分析用户行为等）
>
> Cookie 曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被淘汰。由于服务器指定 Cookie 后，浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 。

**创建Cookie**

> 当服务器收到 HTTP 请求时，服务器可以在**响应头**里面添加一个 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 选项。**浏览器**收到响应后通常会保存下 Cookie，之后对该服务器每一次请求中都通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) **请求头部**将 Cookie 信息发送给服务器。另外，Cookie 的过期时间、域、路径、有效期、适用站点都可以根据需要来指定。

服务器使用 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 响应头部向用户代理（一般是浏览器）发送 Cookie信息。一个简单的 Cookie 可能像这样：

```
Set-Cookie: <cookie名>=<cookie值>
```

服务器通过该头部告知客户端保存 Cookie 信息。

```
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[页面内容]
```

现在，对该服务器发起的每一次新请求，浏览器都会将之前保存的Cookie信息通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 请求头部再发送给服务器。

```
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

----

上一节讲了hook动态cookie的方法，那么如何hook set-cookie呢？从大佬那白嫖来的方法如下：

```
var cookie_cache = document.cookie;
Object.defineProperty(document, 'cookie', {
    get: function() {
        console.log(cookie_cache);
        return cookie_cache;
    },
    set: function(val) {
    	debugger;
        var cookie = val.split(";")[0];
        var ncookie = cookie.split("=");
        var flag = false;
        var cache = cookie_cache.split(";");
        cache = cache.map(function(a){
        	if (a.split("=")[0] === ncookie[0]){
                flag = true;
                return cookie;
        }
        return a;
        })
        cookie_cache = cache.join(";");
        if (!flag){
        	cookie_cache += cookie + ";";
        	}
        },
   });

```

