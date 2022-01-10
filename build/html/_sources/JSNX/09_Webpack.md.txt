#### 5.9 webpack打包解决方案

##### 1. webpack简介

> > 何谓webpack? **webpack** 是一个用于现代 JavaScript 应用程序的 *静态模块打包工具*。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，然后将你项目中所需的每一个模块组合成一个或多个 *bundles*，它们均为静态资源，用于展示你的内容。

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-07_00-32-12.png)

---

##### 2. webpack结构

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-07_01-18-18.png)

①展开这个e，它是一个大的对象，里边都是些分块的数字

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-07_01-00-01.png)

②这个`n(r)`是一个**导库（包）函数**，在webpack中叫做加载器，所有的webpack都有一个加载器。

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2022-01-07_01-10-31.png)

③紧接着是**运行内容**（一定是通过n这个函数走的），比如把`454`传到加载器中，通过判断上图中的`t[r]`是否存在，存在就返回它的函数`t[454].exports`，不存在则把`t[r]`即`t[454]`赋值给r并创立一个有导入属性`{exports: {}}`的对象，然后就会进入到`e[454]`中，并且执行(.call)这个e[454]

---

##### 3. webpack调试技巧

- 寻找函数入口 

- 处理预设加载器（缺什么拿什么，删掉所有初始化方法，只保留加载器）

- 暴露密文
- 最后处理

-----

##### 4. 插桩法

- 中间人攻击

- 找桩点，右键打桩

- 看日志，分析正常执行逻辑

- 抠出核心逻辑

**某些极端情况下，插桩法可以实现环境自吐，也就是环境自吐法**

**实际上 某某宣传的环境自吐法，只是插桩的特殊情况罢了**

**对于** **vmp**来讲，插桩法是一种行之有效的处理方案**



![](https://dosrui.oss-accelerate.aliyuncs.com/img/202201091426114.png)
