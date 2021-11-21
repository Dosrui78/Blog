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

