#### 3.1 Xpath积累

##### 1. 定义包含一个或多个class属性的xpath路径

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2.11.png)

```
//div[@class="vsb-container"]    ×
//div[@class="vsb-container container clearfix"]   √
//div[contains(@class, "clearfix")]   ×
//div[contains(@class, "vsb-container")]   ×
//div[contains(@class, "clearfix")]   ×
```

-----

##### 2. Xpath不包含某字段字符串的项目

不包含文本"详情"：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2.12.png)

```
//*[@id="content"]/div/div/div[2]/div[2]/ul/li/a[not(contains(text(),"详情"))]
```

---

##### 3. Xpath获取兄弟节点元素

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2.13.png)

preceding-sibling表示上面一个兄弟节点元素，反之，following-sibling则表示下面一个兄弟节点元素。

```
/html/body/div/div/div/div/a/div/../../../preceding-sibling::div[@class="zhicheng"][1]/text()
```

---

##### 4. Xpath获取特定长度的文本

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2.14.png)

前两项数据不是我们想要的数据，可以用如下语句排除掉：

```
//ul[@class="div_yan_ul"]/li/a[string-length(text())<7]
```

获取长度小于7的text字符串文本。

----

##### 5. Xpath选择不包含某一属性的节点

> 这里可以用到not。例如排除一个属性的节点可以用//tbody/tr[not(@class)]来写，排除一个或者两个属性可以使用//tbody/tr[not(@class or @id)]来选择。

------

##### 6. Xpath拼接符号 | 

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/2.16.png)![2.16.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.16.png?raw=true)

想获取全部文本内容可以用拼接符号|

```
//div/div/table/tbody/tr/td[2]/p/a/span/text()|//div/div/table/tbody/tr/td[2]/p/span/text()
```

