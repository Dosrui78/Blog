#### 2.3 Selenium

##### 2.31 selenium启动Chrome配置参数问题

>每次当selenium启动chrome浏览器的时候，chrome浏览器很干净，没有插件、没有收藏、没有历史记录，这是因为selenium在启动chrome时为了保证最快的运行效率，启动了一个裸浏览器，这就是为什么需要配置参数的原因，但是有些时候我们需要的不仅是一个裸浏览器。selenium启动配置参数接收是ChromeOptions类，创建方式如下：

```
from selenium import webdriver
options = webdriver.ChromeOptions()
```

[Chromium 命令行开关列表](https://peter.sh/experiments/chromium-command-line-switches/)

```python
# 添加UA
options.add_argument('user-agent="MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1"')

# 指定浏览器分辨率
options.add_argument('window-size=1920x3000') 

# 最大化运行（全屏窗口）,不设置，取元素会报错
options.add_argument('--start-maximized')

# 禁用 gpu 硬件加速
chrome_options.add_argument('--disable-gpu') 

 # 隐藏滚动条, 应对一些特殊页面
options.add_argument('--hide-scrollbars')

# 不加载图片, 提升速度
options.add_argument('blink-settings=imagesEnabled=false') 
options.add_experimental_option("prefs", {"profile.managed_default_content_settings.images": 2})

# 浏览器不提供可视化页面. linux下如果系统不支持可视化不加这条会启动失败
options.add_argument('--headless') 

# 解决DevToolsActivePort文件不存在的报错
options.add_argument('--no-sandbox')

# 手动指定使用的浏览器位置
options.binary_location = r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 

#添加crx插件
option.add_extension('d:\crx\AdBlock_v2.17.crx') 

# 禁用JavaScript
option.add_argument("--disable-javascript") 

# 设置开发者模式启动，该模式下webdriver属性为正常值，实现了规避检测
options.add_experimental_option('excludeSwitches', ['enable-automation']) 

#禁止打印日志
options.add_experimental_option('excludeSwitches', ['enable-logging'])

# “--log-level” 设置最小日志级别。有效值为 0 到 3：INFO = 0、WARNING = 1、LOG_ERROR = 2、LOG_FATAL = 3
options.add_argument('log-level=4')

# 禁用浏览器弹窗
options.add_experimental_option("prefs",{'profile.default_content_setting_values':{'notifications' : 2}})

# 隐身模式（无痕模式）
options.add_argument('--incognito')

# 禁用浏览器正在被自动化程序控制的提示
options.add_argument('--disable-infobars')

# ssl_client_socket_impl.cc报错解决
options.add_argument('--ignore-certificate-errors')
options.add_argument('--ignore-ssl-errors')

# 新版本關閉“chrome正受到自動測試軟件的控製”信息options.add_experimental_option("excludeSwitches", ["enable-automation"])

# 隐藏window.navigator.webdriver
options.add_argument("--disable-blink-features=AutomationControlled")

# 新版本關閉“請停用以開發者模式運行的擴展程序”信息
option.add_experimental_option("useAutomationExtension", False)
```

----

##### 2.32 如何将整个HTML作为字符串获取

```
html = execute_script("document.documentElement.??")   #??可以是innerHTML、outerHTML、innerText、outerText
```

###### innerHTML、outerHTML、innerText、outerText的区别

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset= 'utf-8'>
	<title>HTML5自由者</title>
</head>
<body>
	<div id="test1">这是div中的文字<span>这是span中的文字</span></div>
 
	<script type="text/javascript">
	 	console.log('innerHTML:'+test1.innerHTML);
	 	console.log('outerHTML:'+test1.outerHTML);
	 	console.log('innerText:'+test1.innerText);
	 	console.log('outerText:'+test1.outerText);
	</script>
</body>
</html>
```

**我们先来看下console控制台显示的运行结果：**

```
innerHTML:这是div中的文字<span>这是span中的文字</span>
outerHTML:<div id="test1">这是div中的文字<span>这是span中的文字</span></div>
innerText:这是div中的文字这是span中的文字
outerText:这是div中的文字这是span中的文字
```

**结构图：**

![img](https://img-blog.csdn.net/20140413221543250)

可以得出结论：
 ① innerHTML 获取对象起始和结束标签内的 HTML，即这里的对象是div标签，亦即这个标签里面所有的内容包含span标签也获取出来，即 “这是div中的文字<span>这是span中的文字</span>”（注意HTML这个字的意思，也就是结构）
 ② outerHTML 是在①innerHTML基础上获取它的outer对象标签内容，也就是“<div id="test1">这是div中的文字<span>这是span中的文字</span></div>” 这些里面有什么内容及标签结构都获取出来。 （注意HTML这个字的意思，也就是结构）
 ②  innerText和outerText在获取时是相同效果 都是获取<div> </div>标签里的文本内容，去除掉了<div> ,<span>标签，只显示div,span标签里的文本内容，即 “这是div中的文字这是span中的文字”（注意Text这个字的意思，也就是文本）

-----

##### 2.33 iframe

> ​        在做web自动化的过程中会遇到一些弹出的登录页面，定位后，执行程序发现还是出现报错，其实定位可能没有问题，而是表单再作怪，也就是iframe，iframe是HTML标签，作用是文档中的文档，或者浮动的框架(FRAME)。iframe元素会创建包含另外一个文档的内联框架，也就html中在嵌套一个网页，可以通过F12查看。

![img](https://github.com/Dosrui78/Blog-Images/blob/master/2.33.jpg?raw=true "2.33")

###### 如何定位iframe

我们知道什么是iframe了，那么如何定位？我们可以使用selenium中自带的一个方法 switch_to_frame ，这里的定位iframe可以是id属性也可以是name属性。

**网易云音乐iframe**：

```
from selenium import webdriver
import time
driver = webdriver.Chrome()
driver.get('https://music.163.com/#/song?id=1891469546')
driver.implicitly_wait(5)
iframe = driver.find_element_by_xpath('//iframe')
driver.switch_to.frame(iframe)
```

