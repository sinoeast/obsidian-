# Selenium
## 单步调试
`cd C:\Program Files (x86)\Google\Chrome\Application &&chrome.exe --remote-debugging-port=9222 --user-data-dir="C:\Users\sinoeast\Desktop\selenium"`
```python
chrome_options = webdriver.ChromeOptions()
chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
wd = webdriver.Chrome(options=chrome_options)
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814235823.png)
## 1. 启动
需要把驱动先丢到python文件目录，不然需要加驱动路径参数
```python
wd = webdriver.Chrome("驱动路径")  
  
# 访问百度首页  
wd.get(r'https://www.baidu.com/')  
  
# 关闭浏览器  
wd.close()
```
[ChromeOptions](https://blog.csdn.net/zwq912318834/article/details/78933910)
```python
# 无界面的浏览器  
option = webdriver.ChromeOptions()  
option.add_argument("headless")  
wd = webdriver.Chrome(options=option)  
  
# 访问百度首页  
wd.get(r'https://www.baidu.com/')  
  
# 关闭浏览器  
wd.close()
```
## 2. wd方法自查
查找元素：
`wd.find_element(By.ID, 'kw')`
`wd.find_elements(By.ID, 'kw')`
访问页面：
`wd.get(r'https://www.baidu.com/')`
关闭当前窗口：
`wd.close()`
关闭selenium打开的所有窗口：
`wd.quit()`
截图：
`wd.get_screenshot_as_file('截图1.png')`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220815202859.png)
网页中的基础属性获取：
```python
# 网页标题
print(wd.title)
# 当前网址
print(wd.current_url)
# 浏览器名称
print(wd.name)
# 网页源码
print(wd.page_source)
```
cookie：
`wd.get_cookies()`
`wd.add_cookie()`
`wd.delete_all_cookies()`
刷新：
`wd.refresh()`
网页中的后退：
`wd.back()`
网页中的前进：
`wd.forward()`
设置为全屏：
`wd.maximize_window()`
设置为100x100分辨率：
`set_window_size(100，100)`
## 3.元素方法自查
点击：
`element.click()`
form表单提交
`element.submit()`
清除：
`element.clear()`
是否被选中了[单选框（radio）和复选框（checkbox）]
`element.is_selected()`
输入字符/键盘的操作：
`element.send_keys('python')`
`element.send_keys(Keys.ENTER)`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220815203344.png)

获取css属性：
`element.get_attribute('class')`
获取文本等属性：
`element.text`
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814213437.png)
## 4.特殊元素select下拉框
- 1.找到元素进行Select初始化
```python
from selenium.webdriver.support.select import Select
select = Select(wd.find_element(By.NAME,"帅哥"))  
```
- 2.选择下拉框元素
```python
select.select_by_visible_text("小草")  # 通过文本值定位
select.select_by_index(0)              # 通过索引定位
select.select_by_value("草儿")         # 通过value属性定位
```
- 3.返回options信息（和元素的结果一致可以获取id，text等信息）
```python
select.voptions                    # 返回所有的元素
select.all_selected_options        # 返回已选中的元素
select.first_selected_options      # 返回已选中的第一个元素
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814224029.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814224935.png)

- 4.取消选中
```python
select.deselect_all()                      # 取消全部的已选择项
select.deselect_by_visible_text("小草")    # 根据文本值取消已选中的对象
select.deselect_by_index(0)                # 根据索引值取消已选中的对象
select.deselect_by_value("草儿")           # 根据value属性取消已选中的对象
```
- 注意：对于select多选框，要选中某几个选项，要**先去掉原来已经选中的选项**。
## 5. Frame切换
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814225537.png)


```python
wd.switch_to.frame(wd.find_element(By.TAG_NAME, "iframe")) #切换到指定的frame
wd.switch_to.default_content()                             #切换到默认frame
wd.switch_to.parent_frame()                                #切换到父级frame
```
## 6.操作alert常用的方法
```python
wd.switch_to.alert      #切换到当前页面上的警告框
wd.text                   #返回弹框文本信息
wd.accept()               #接受弹框，确认
wd.dismiss()              #取消弹框
wd.send_keys(keysToSend)  #发送文本至弹框
```
## 7.多窗口切换
```python
wd.current_window_handle #获取当前的窗口句柄()
wd.window_handles        #获取当前会话下的所有窗口句柄()
wd.switch_to.window(句柄)      #切换窗口()
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814233029.png)
## 8. 获取当前焦点元素
switch_to还有一个方法叫`wd.switch_to.active_element`，返回当前焦点的WebElement对象，用于判断当前焦点是否位于某个web元素。有时候需要测试打开一个网页，焦点是否在输入框，比如账号密码登录页面。 
## 9.模拟鼠标操作
### ActionChains
双击`double_click()`
右键`context_click()`
```python
from selenium.webdriver.common.action_chains import ActionChains

element = wd.find_element_by_link_text('新闻')
ActionChains(wd).context_click(element).perform()
```
在上述操作中

> `ActionChains(wd)`：调用`ActionChains()`类，并将浏览器驱动`wd`作为参数传入
> 
> `context_click(element)`：模拟鼠标双击，需要传入指定元素定位作为参数
> 
> `perform()`：执行`ActionChains()`中储存的所有操作，可以看做是执行之前一系列的操作

拖拽`drag_and_drop(source,target)`
```python
# 开始位置
source = browser.find_element_by_css_selector("#draggable")

# 结束位置
target = browser.find_element_by_css_selector("#droppable")

# 执行元素的拖放操作
actions = ActionChains(browser)
actions.drag_and_drop(source, target)
actions.perform()
```
悬停：`move_to_element()`
### TouchAction
ActionChains无法操作H5页面，TouchAction可以对H5页面进行操作，实现点击，滑动，拖拽，模拟手势等各种操作。

**手势控制方法**
>double_tap 双击
>flick 滑动
>flick_element 从某个元素位置开始滑动
>long_press 长按
>move 手势移动指定偏移
>Perform 执行
>release 释放手势
>scroll 点击并滚动
>scroll_from_element 从某个元素位置开始手势点击并滚动(向下滑动为负数,向上滑动为正数)
>flick_element——从某个元素位置开始手势滑动(负数：向上滑动，正数：向下滑动)
>tap 在指定元素上点击
>tap_and_hold 在指定元素上点击但不释放
## 10.等待
### 1.强制等待
就很简单了，直接`time.sleep(n)`强制等待n秒，在执行`wd.get`方法之后执行。
### 2.隐式等待
`implicitly_wait()`设置等待时间，如果到时间有元素节点没有加载出来，就会抛出异常。
隐式等待只需要声明一次，一般在打开浏览器后进行声明。
_**声明之后对整个drvier的生命周期都有效，后面不用重复声明**_。
```python
wd = webdriver.Chrome()
# 隐式等待，等待时间10秒
wd.implicitly_wait(10)
```
但是隐式等待依然存在一个问题，那就是程序会一直等待整个页面加载完成，也就是一般情况下你看到浏览器标签栏那个小圈不再转，才会执行下一步，但有时候页面想要的元素早就在加载完成了，但是因为个别js之类的东西特别慢，我仍得等到页面全部完成才能执行下一步，某些情况下会影响脚本执行速度。
### 3.显式等待
_**显示等待与隐式等待相对，显示等待必须在每个需要等待的元素前面进行声明。**_
```python
#引入WebDriverWait
from selenium.webdriver.support.ui import WebDriverWait
#引入expected_conditions类，并重命名为EC
from selenium.webdriver.support import expected_conditions as EC
#引入By类
from selenium.webdriver.common.by import By
 
#设置等待
wait = WebDriverWait(driver,10,0.5)
wait.until(EC.presence_of_element_located((By.ID,'KW')))
```
显示等待需要用到两个类：

WebDriverWait和expected_conditions两个类。

1、WebDriverWait(driver,timeout,poll_frequency=0.5,ignored_exceptions=None)
>driver：浏览器驱动
>timeout：最长超时时间，默认以秒为单位
>poll_frequency：检测的间隔步长，默认为0.5s
>ignored_exceptions：超时后的抛出的异常信息，默认抛出NoSuchElementExeception异常。

WebDriverWait()中的until()和until_not()方法：
`until`
```python
WebDriverWait(driver,10).until(method，message="")
#调用该方法提供的驱动程序作为参数，直到返回值为True
```
method: 在等待期间，每隔一段时间（__init__中的poll_frequency）调用这个传入的方法，直到返回值不是False
message: 如果超时，抛出TimeoutException，将message传入异常
`until_not`
```python
WebDriverWait(driver,10).until_not(method，message="")
#调用该方法提供的驱动程序作为参数，直到返回值为False
```
与until相反，until是当某元素出现或什么条件成立则继续执行，
until_not是当某元素消失或什么条件不成立则继续执行，参数也相同。

2、expected_conditions类

各种类，达到某种条件，返回True和False

expected_conditions类提供的预期条件判断的方法
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220815204728.png)

显示等待,自定义等待条件
```python
#设置等待
wait = WebDriverWait(driver,10,0.5)
#使用匿名函数
wait.until(lambda diver:driver.find_element_by_id('kw'))
```
总结：
其实隐式等待和显示等待在本质上是一致的，只是显示等待多了一个指定元素条件超时时间，在使用场景上，可以使用隐式等待来做一个全局的控制，例如设置全局隐式等待6秒；
如果某个控件比较特殊，需要更长的时间加载，比如十几秒或者更长，就可以使用显示等待对其进行单独处理；
## 11.运行JavaScript
还有一些操作，比如下拉进度条，模拟`javaScript`，使用`execute_script`方法来实现。
```python
from selenium import webdriver

wd = webdriver.Chrome()
# 知乎发现页
wd.get('https://www.zhihu.com/explore')

wd.execute_script('window.scrollTo(0, document.body.scrollHeight)')
wd.execute_script('alert("To Bottom")')
```
## 12.反屏蔽
一般来讲，使用*selenium*访问某个网址（浏览器以Chrome为例），都会有*Chrome正受到自动测试软件的控制*的提示

- 以百度为例：

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.get('https://baidu.com')
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220815205910.png)


有些网站会通过检测进行屏蔽，拒绝访问。

*检测的基本原理：*

检测当前浏览器窗口下的`window.navigator`对象是否包含`webdriver`这个属性。因为在正常使用浏览器的情况下，这个属性是`undefined`的，然而，一旦我们使用了`selenium`，`selenium`会给`Window.navigator`设置`webdriver`属性，很多网站就通过JavaScript判断，如果`webdriver`存在就直接屏蔽。

*解决方法：*
可以使用CDP来解决这个问题，通过CDP，我们可以实现在每个页面刚加载时执行JavaScript代码。

> CDP：Chrome Devtools-Protocol,Chrome开发工具协议

- 示例代码如下（仍以百度为例）：

```python
from selenium import webdriver

option = webdriver.ChromeOptions()
option.add_experimental_option('excludeSwitches', ['enable-automation'])
option.add_experimental_option('useAutomationExtension', False)
driver = webdriver.Chrome(options=option)
driver.execute_cdp_cmd('Page.addScriptToEvaluateOnNewDocument', {
    'source': 'Object.defineProperty(navigator, "webdriver", {get:()=>undefined})'
})
driver.get('https://baidu.com')
```
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220815210053.png)
