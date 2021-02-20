---
title: Selenium库常用操作记录
top: false
cover: false
toc: true
mathjax: false
date: 2020-09-18 19:06:09
password:
summary:
tags: Selenium
categories: Python
---

# 1：Selenium简介

Selenium库是一个用于浏览器自动化pyhon库。对提高爬虫容错率有很好的效果。它可用于驱动浏览器，来帮助我们实现获取网站js脚本执行后的网页。

# 2：Selenium安装

## 2.1 安装

```shell
pip install selenium
```

## 2.2 浏览器驱动

下载浏览器驱动：

Firefox浏览器驱动：[geckodriver](https://link.zhihu.com/?target=https%3A//github.com/mozilla/geckodriver/releases)

Chrome浏览器驱动：[chromedriver](https://link.zhihu.com/?target=https%3A//sites.google.com/a/chromium.org/chromedriver/home) , [taobao备用地址](https://link.zhihu.com/?target=https%3A//npm.taobao.org/mirrors/chromedriver)

IE浏览器驱动：[IEDriverServer](https://link.zhihu.com/?target=http%3A//selenium-release.storage.googleapis.com/index.html)

Edge浏览器驱动：[MicrosoftWebDriver](https://link.zhihu.com/?target=https%3A//developer.microsoft.com/en-us/microsoft-edge/tools/webdriver)

Opera浏览器驱动：[operadriver](https://link.zhihu.com/?target=https%3A//github.com/operasoftware/operachromiumdriver/releases)

PhantomJS浏览器驱动：[phantomjs](https://link.zhihu.com/?target=http%3A//phantomjs.org/)

将驱动放到某个路径下，并在环境变量中指明。例如：

驱动放在目录：`C:/abc/webdriver.exe`

则需要在用户或者系统环境变量中，给Path变量添加新变量：`C:/abc`

或者**指定驱动路径**

```python
from selenium import webdriver
path = "/path/to/chromedriver" #注意这个路径需要时可执行路径（chmod 777 dir or 755 dir）
driver = webdriver.Chrome(executable_path=path)
```

不同的浏览器启动方式：

```python
from selenium import webdriver
driver = webdriver.Firefox()   # Firefox浏览器

driver = webdriver.Chrome()    # Chrome浏览器

driver = webdriver.Ie()        # Internet Explorer浏览器

driver = webdriver.Edge()      # Edge浏览器

driver = webdriver.Opera()     # Opera浏览器

driver = webdriver.PhantomJS()   # PhantomJS
```

# 3：浏览器选项和操作

## 3.1 浏览器选项

每次当selenium启动chrome浏览器的时候，chrome浏览器很干净，没有插件、没有收藏、没有历史记录，这是因为selenium在启动chrome时为了保证最快的运行效率，启动了一个裸浏览器，这就是为什么需要配置参数的原因，但是有些时候我们需要的不仅是一个裸浏览器。

selenium启动配置参数接收是ChromeOptions类，创建方式如下：

```python
from selenium import webdriver
option = webdriver.ChromeOptions()
# 添加启动参数
option.add_argument()

# 添加扩展应用 
option.add_extension()
option.add_encoded_extension()

# 添加实验性质的设置参数 
option.add_experimental_option()

# 设置调试器地址
option.debugger_address()

# 添加UA
options.add_argument('user-agent="MQQBrowser/26 Mozilla/5.0 (Linux; U; Android 2.3.7; zh-cn; MB200 Build/GRJ22; CyanogenMod-7) AppleWebKit/533.1 (KHTML, like Gecko) Version/4.0 Mobile Safari/533.1"')

# 指定浏览器分辨率
options.add_argument('window-size=1920x3000') 

# 谷歌文档提到需要加上这个属性来规避bug
chrome_options.add_argument('--disable-gpu') 

 # 隐藏滚动条, 应对一些特殊页面
options.add_argument('--hide-scrollbars')

# 不加载图片, 提升速度
options.add_argument('blink-settings=imagesEnabled=false') 

# 浏览器不提供可视化页面. linux下如果系统不支持可视化不加这条会启动失败
options.add_argument('--headless') 

# 以最高权限运行
options.add_argument('--no-sandbox')

# 手动指定使用的浏览器位置
options.binary_location = r"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 

#添加crx插件
option.add_extension('d:\crx\AdBlock_v2.17.crx') 

# 禁用JavaScript
option.add_argument("--disable-javascript") 

# 设置开发者模式启动，该模式下webdriver属性为正常值
options.add_experimental_option('excludeSwitches', ['enable-automation']) 

# 禁用浏览器弹窗
prefs = {  
    'profile.default_content_setting_values' :  {  
        'notifications' : 2  
     }  
}  
options.add_experimental_option('prefs',prefs)

driver=webdriver.Chrome(chrome_options=chrome_options)
```

其他配置参数

```python
–user-data-dir=”[PATH]” 
# 指定用户文件夹User Data路径，可以把书签这样的用户数据保存在系统分区以外的分区

　　–disk-cache-dir=”[PATH]“ 
# 指定缓存Cache路径

　　–disk-cache-size= 
# 指定Cache大小，单位Byte

　　–first run 
# 重置到初始状态，第一次运行

　　–incognito 
# 隐身模式启动

　　–disable-javascript 
# 禁用Javascript

　　--omnibox-popup-count="num" 
# 将地址栏弹出的提示菜单数量改为num个

　　--user-agent="xxxxxxxx" 
# 修改HTTP请求头部的Agent字符串，可以通过about:version页面查看修改效果

　　--disable-plugins 
# 禁止加载所有插件，可以增加速度。可以通过about:plugins页面查看效果

　　--disable-javascript 
# 禁用JavaScript，如果觉得速度慢在加上这个

　　--disable-java 
# 禁用java

　　--start-maximized 
# 启动就最大化

　　--no-sandbox 
# 取消沙盒模式

　　--single-process 
# 单进程运行

　　--process-per-tab 
# 每个标签使用单独进程

　　--process-per-site 
# 每个站点使用单独进程

　　--in-process-plugins 
# 插件不启用单独进程

　　--disable-popup-blocking 
# 禁用弹出拦截

　　--disable-plugins 
# 禁用插件

　　--disable-images 
# 禁用图像

　　--incognito 
# 启动进入隐身模式

　　--enable-udd-profiles 
# 启用账户切换菜单

　　--proxy-pac-url 
# 使用pac代理 [via 1/2]

　　--lang=zh-CN 
# 设置语言为简体中文

　　--disk-cache-dir 
# 自定义缓存目录

　　--disk-cache-size 
# 自定义缓存最大值（单位byte）

　　--media-cache-size 
# 自定义多媒体缓存最大值（单位byte）

　　--bookmark-menu 
# 在工具 栏增加一个书签按钮

　　--enable-sync 
# 启用书签同步
```

## 3.2 浏览器操作

- 控制浏览器窗口大小

```python
driver.set_window_size(480, 800)
```

- 浏览器后退，前进

```python
# 后退 
driver.back() 
# 前进 
driver.forward()
```

- 刷新

```python
driver.refresh() # 刷新
```

# 4：元素定位和操作

## 4.1 元素定位

获取首个匹配元素：

```python
find_element_by_id()
find_element_by_name()
find_element_by_class_name()
find_element_by_tag_name()
find_element_by_link_text()
find_element_by_partial_link_text()
find_element_by_xpath()
find_element_by_css_selector()
```

如果具有多个匹配属性元素，可使用：

```python
find_elements_by_class_name()
```

即在element后加s

## 4.2 输入、提交

- 点击和输入

```python
driver.find_element_by_id("kw").clear() # 清楚文本 driver.find_element_by_id("kw").send_keys("selenium") # 模拟按键输入 driver.find_element_by_id("su").click() # 单机元素
```

- 提交

可以在搜索框模拟回车操作

```python
search_text = driver.find_element_by_id('kw') search_text.send_keys('selenium') 
search_text.submit()
```

- 其他

size： 返回元素的尺寸。

text： 获取元素的文本。

get_attribute(name)： 获得属性值。

is_displayed()： 设置该元素是否用户可见。

## 4.3 鼠标操作

在 WebDriver 中， 将这些关于鼠标操作的方法封装在 ActionChains 类提供。

ActionChains 类提供了鼠标操作的常用方法：

```
click（on_element = None）-鼠标左键

click_and_hold（on_element = None）-单击鼠标左键，不松开

context_click（on_element = None）-点击鼠标右键

double_click（on_element = None）-双击鼠标左键

drag_and_drop（源，目标）-拖拽到某个元素然后松开

drag_and_drop_by_offset（source，xoffset，yoffset）-拖拽到某个坐标然后松开

key_down（value，element = None）-点击某个键盘上的键

key_up（value，element = None）-松开某个键

move_by_offset（xoffset，yoffset）-鼠标从当前位置移动到某个坐标

move_to_element（to_element）-鼠标移动到某个元素

move_to_element_with_offset（to_element，xoffset，yoffset）-移动到距离某个元素（左上角坐标）多少距离的位置

perform（）-执行链中的所有动作

释放（on_element = None）-在某个元素位置松开鼠标左键

send_keys（* keys_to_send）-发送某个键到当前焦点的元素

send_keys_to_element（element，* keys_to_send）-发送某个键到指定元素
```

举个例子：

```python
from selenium import webdriver
# 引入 ActionChains 类
from selenium.webdriver.common.action_chains import ActionChains

driver = webdriver.Chrome()
driver.get("https://www.baidu.cn")

# 定位到要悬停的元素
above = driver.find_element_by_link_text("设置")
# 对定位到的元素执行鼠标悬停操作
ActionChains(driver).move_to_element(above).perform()
```

## 4.4 键盘事件

以下为常用的键盘操作：

- send_keys(Keys.BACK_SPACE) 删除键（BackSpace）
- send_keys(Keys.SPACE) 空格键(Space)
- send_keys(Keys.TAB) 制表键(Tab)
- send_keys(Keys.ESCAPE) 回退键（Esc）
- send_keys(Keys.ENTER) 回车键（Enter）
- send_keys(Keys.CONTROL,'a') 全选（Ctrl+A）
- send_keys(Keys.CONTROL,'c') 复制（Ctrl+C）
- send_keys(Keys.CONTROL,'x') 剪切（Ctrl+X）
- send_keys(Keys.CONTROL,'v') 粘贴（Ctrl+V）
- send_keys(Keys.F1) 键盘 F1
- ……
- send_keys(Keys.F12) 键盘 F12

```python
# 输入框输入内容
driver.find_element_by_id("kw").send_keys("seleniumm")

# 删除多输入的一个 m
driver.find_element_by_id("kw").send_keys(Keys.BACK_SPACE)
```

## 4.5 获取断言信息

```python
title = driver.title # 打印当前页面title
now_url = driver.current_url # 打印当前页面URL
user = driver.find_element_by_class_name('nums').text # # 获取结果数目
```

## 4.6 等待页面加载完成

- 显示等待

显式等待使WebdDriver等待某个条件成立时继续执行，否则在达到最大时长时抛出超时异常（TimeoutException）。

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Firefox()
driver.get("http://www.baidu.com")

element = WebDriverWait(driver, 5, 0.5).until(
                      EC.presence_of_element_located((By.ID, "kw"))
                      )
element.send_keys('selenium')
driver.quit()
```

WebDriverWait类是由WebDirver 提供的等待方法。在设置时间内，默认每隔一段时间检测一次当前页面元素是否存在，如果超过设置时间检测不到则抛出异常。具体格式如下：

```python
WebDriverWait(driver, timeout, poll_frequency=0.5, ignored_exceptions=None)
```

- driver ：浏览器驱动。
- timeout ：最长超时时间，默认以秒为单位。
- poll_frequency ：检测的间隔（步长）时间，默认为0.5S。
- ignored_exceptions ：超时后的异常信息，默认情况下抛NoSuchElementException异常。
- WebDriverWait()一般由until()或until_not()方法配合使用，下面是until()和until_not()方法的说明。
- until(method, message=‘’) 调用该方法提供的驱动程序作为一个参数，直到返回值为True。
- until_not(method, message=‘’) 调用该方法提供的驱动程序作为一个参数，直到返回值为False。

在本例中，通过as关键字将expected_conditions 重命名为EC，并调用presence_of_element_located()方法判断元素是否存在。

- 隐式等待

如果某些元素不是立即可用的，隐式等待是告诉WebDriver去等待一定的时间后去查找元素。 默认等待时间是0秒，一旦设置该值，隐式等待是设置该WebDriver的实例的生命周期。

```python
from selenium import webdriver
driver = webdriver.Firefox()    
driver.implicitly_wait(10) # seconds    
driver.get("http://somedomain/url_that_delays_loading")    
myDynamicElement = driver.find_element_by_id("myDynamicElement") 
```

## 4.7 在不同的iframe之间移动

```python
driver.switch_to_window("windowName")
driver.switch_to_frame("frameName")
```

以直接取表单的id 或name属性。如果iframe没有可用的id和name属性，则可以通过下面的方式进行定位。

```python
#先通过xpth定位到iframe
xf = driver.find_element_by_xpath('//*[@id="x-URS-iframe"]')

#再将定位对象传给switch_to_frame()方法
driver.switch_to_frame(xf)
```

一旦我们完成了frame中的工作，我们可以这样返回父frame:

```python
driver.switch_to_default_content()
```

## 4.8 警告框处理

```python
alert = driver.switch_to_alert()
```

- text：返回 alert/confirm/prompt 中的文字信息。
- accept()：接受现有警告框。
- dismiss()：解散现有警告框。
- send_keys(keysToSend)：发送文本至警告框。keysToSend：将文本发送至警告框。

## 4.9 下拉框选择

```text
from selenium import webdriver
from selenium.webdriver.support.select import Select
from time import sleep

driver = webdriver.Chrome()
driver.implicitly_wait(10)
driver.get('http://www.baidu.com')
sel = driver.find_element_by_xpath("//select[@id='nr']")
Select(sel).select_by_value('50')  # 显示50条
```

## 4.10 文件上传

```text
driver.find_element_by_name("file").send_keys('D:\\upload_file.txt')  # # 定位上传按钮，添加本地文件
```

## 4.11 cookie操作

WebDriver操作cookie的方法：

- get_cookies()： 获得所有cookie信息。
- get_cookie(name)： 返回字典的key为“name”的cookie信息。
- add_cookie(cookie_dict) ： 添加cookie。“cookie_dict”指字典对象，必须有name 和value 值。
- delete_cookie(name,optionsString)：删除cookie信息。“name”是要删除的cookie的名称，“optionsString”是该cookie的选项，目前支持的选项包括“路径”，“域”。
- delete_all_cookies()： 删除所有cookie信息

## 4.12 调用JavaScript代码

```python
js="window.scrollTo(100,450);"
driver.execute_script(js) # 通过javascript设置浏览器窗口的滚动条位置
```

通过execute_script()方法执行JavaScripts代码来移动滚动条的位置。

## 4.13 窗口截图

```python
driver.get_screenshot_as_file("D:\\baidu_img.jpg") # 截取当前窗口，并指定截图图片的保存位置
```

## 4.14 关闭浏览器

- close() 关闭单个窗口
- quit() 关闭所有窗口

## 4.15 直接用cookie登录方法

链接：[https://www.jianshu.com/p/773c58406bdb](https://link.zhihu.com/?target=https%3A//www.jianshu.com/p/773c58406bdb)

1. 手动获取网页的cookie，将其序列化并存储在本地
2. 写入代码

```python
for item in cookies:
    driver.add_cookie(item)
```

## 4.16 导出html到beautifulsoup或文件

```python
soup = BeautifulSoup(driver.page_source,"html.parser")
```

```python
def writetohtml(driver,path):
    f = open(path,'wb')
    f.write(driver.page_source.encode("utf-8", "ignore"))
    f.close()   
```

# 5：控件操作

## 5.1 Select

`Select`类提供了三种选择某些选项的方法：

```python
select_by_index(index)
select_by_value(value)
select_by_visible_text(text)
```

> index从0开始，第一个option为0
>
> value是标签的属性值
>
> visible_text是option标签中间的值，即下拉框可见值

自然的，有选择必然有反选，即取消选择。`Select`提供了四个方法给我们取消原来的选择：

```
deselect_by_index(index)
deselect_by_value(value)
deselect_by_visible_text(text)
deselect_all()
```

前一种分别于`select`相对应，第四种是全部取消选择，是的，你没看错，是全部取消。有一种特殊的选择标签，即设置了`multiple="multiple"`属性的`select`，这种`select`框是可以多选的，你可以通过多次`select`，选择多个选项，而通过`deselect_all()`来将他们全部取消。

当我们选择了选项之后，想要看看选择的是哪项，替代的是否是我想选的，怎么办？别担心，`Select`为你提供了相应的方法（或者应该说是属性了）：

```
options
all_selected_options
first_selected_option
```

上面三个属性，分别返回这个`select`元素所有的`options`，所有被选中的`options`以及第一个被选中的`option`。

## 5.2 Radio和Checkbox

```html
<html>
<body>
Checkbox:
<input type="checkbox" value="cv1" name="c1">
<input type="checkbox" value="cv2">
<input type="checkbox" value="cv3" name="c1">
<input type="checkbox" value="cv4">
<p>
Radio:
<input type="radio" value="rv1" name="r1">
<input type="radio" value="rv2" name="r1">
</body>
</html>
```

选择操作即点击事件，清除选择即键盘操作

```python
# checkbox
driver.find_element_by_xpath('//input[@value="cv1"]').click()  # click
driver.find_element_by_xpath('//input[@value="cv2"]').send_keys(Keys.SPACE)  # send space

# radio
driver.find_element_by_xpath('//input[@value="rv1"]').send_keys(Keys.SPACE)  # send space
sleep(1)
driver.find_element_by_xpath('//input[@value="rv2"]').click()  # click
```

# 6：参考资料

《[Selenium Python 教程](https://zhuanlan.zhihu.com/p/111859925)》

《[selenium启动Chrome配置参数问题](https://zhuanlan.zhihu.com/p/60852696)》

《[Python硒-模拟鼠标键盘操作（ActionChains）](https://huilansame.github.io/huilansame.github.io/archivers/mouse-and-keyboard-actionchains)》

《[Python硒-操作选择标签的下拉选择框](https://huilansame.github.io/huilansame.github.io/archivers/drop-down-select)》

《[Python+selenium使用cookie登录淘宝](https://www.jianshu.com/p/773c58406bdb)》