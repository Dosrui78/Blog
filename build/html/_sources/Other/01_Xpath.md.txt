#### 2.1 Xpath拓展

##### 2.11 定义包含一个或多个class属性的xpath路径

![2.11.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.11.png?raw=true)

```
//div[@class="vsb-container"]    ×
//div[@class="vsb-container container clearfix"]   √
//div[contains(@class, "clearfix")]   ×
//div[contains(@class, "vsb-container")]   ×
//div[contains(@class, "clearfix")]   ×
```

-----

##### 2.12 Xpath不包含某字段字符串的项目

不包含文本"详情"：

![2.12.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.12.png?raw=true)

```
//*[@id="content"]/div/div/div[2]/div[2]/ul/li/a[not(contains(text(),"详情"))]
```

---

##### 2.13 Xpath获取兄弟节点元素

![2.13.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.13.png?raw=true)

preceding-sibling表示上面一个兄弟节点元素，反之，following-sibling则表示下面一个兄弟节点元素。

```
/html/body/div/div/div/div/a/div/../../../preceding-sibling::div[@class="zhicheng"][1]/text()
```

---

##### 2.14 Xpath获取特定长度的文本

![2.14.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.14.png?raw=true)

前两项数据不是我们想要的数据，可以用如下语句排除掉：

```
//ul[@class="div_yan_ul"]/li/a[string-length(text())<7]
```

获取长度小于7的text字符串文本。

----

##### 2.15 Xpath选择不包含某一属性的节点

> 这里可以用到not。例如排除一个属性的节点可以用//tbody/tr[not(@class)]来写，排除一个或者两个属性可以使用//tbody/tr[not(@class or @id)]来选择。

------

##### 2.16 Xpath拼接符号 | 

![2.16.png](https://github.com/Dosrui78/Blog-Images/blob/master/2.16.png?raw=true)

想获取全部文本内容可以用拼接符号|

```
//div/div/table/tbody/tr/td[2]/p/a/span/text()|//div/div/table/tbody/tr/td[2]/p/span/text()
```

