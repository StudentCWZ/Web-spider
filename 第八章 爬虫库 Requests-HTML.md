# 第八章 爬虫库 Requests-HTML
## Requests-HTML 简介以及安装
1. `Requests-HTML`是在`Requests`的基础上进一步封装，两者都是由同一个开发者开发的。`Requests-HTML`除了包含`Requests`所有的功能之外，还新增了数据清洗和`Ajax`数据动态渲染。
2. 数据清洗是由`lxml`和`PyQuery`模块实现的，这两个模块分别支持`XPath Selectors`和`CSS Selectors`定位，通过`XPath`或`CSS`定位，可以精准地提取网页里的数据。
3. `Ajax`数据动态渲染是将网页的动态数据加载到网上再抓取。
网页数据可以使用`Ajax`向服务器发送`HTTP`请求，再由`JavaScript`完成数据渲染，如果直接向网页的`URL`地址发送`HTTP`请求，并且网站的部分数据是来自`Ajax`，那么，得到的网页信息就会有所缺失。
4. `Requests-HTML`可以将`Ajax`动态数据加载到网页信息，无需爬虫开发者分析`Ajax`的请求信息。
5. `Requests-HTML`的安装可使用`pip`指令完成，但`Requests-HTML`只支持`Python3.6`以上的版本。
6. 本书以`Python3.7`为例，在`CMD`窗口输入安装指令`pip install requests-html`模块并输出模块里的属性`DEFAULT-URL`的属性值，从而验证`requests-html`模块是否安装成功，如下所示：
```
C:\Users\000>python
>>> import requests_html
>>> requests_html.DEFAULT_URL
'https://example.org/'
```
## 请求方式
1. `Requests-HTML`向网站发送请求的方法是来自`Requests`模块，但是`Requests-HTML`只能使用`Requests`的`Session`模式，该模式是将请求会话持久化，使这个请求保持连接状态。
2. `Session`模式好比我们在打电话的时候，只要双方没有挂断电话，就会一直保持一种会话(连接)状态。`Session`模式对`HTTP`的`GET`和`POST`请求也是由`get()`和`POST()`方法实现，具体的使用方法如下：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 21:38:58
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-17 09:14:19
'''


from requests_html import HTMLSession

# 定义会话Session
session = HTMLSession()
url = r'http://movie.douban.com/'
# 发送GET请求
response = session.get(url)
# 发送POST请求
response = session.post(url,data = {})
# 输出网址的URL地址
print(response.html)
```
3. 上述代码分别对同一个`URL`使用`get()`和`post()`方法，由于`get()`和`post()`方法都来自`Requests`模块，因此还可以对这两个方法设置相关参数，如`请求参数`、`请求头`、`Cookies`、`代理IP`以及`证书验证`等。
4. `Requests_HTML`在请求的过程中还做了优化处理，如果没有设置请求头，`Requests-HTML`默认使用源码定义的请求头以及编码方式。
## 数据清洗
1. `Requests-HTML`不仅优化了请求过程，还提供了数据清洗的功能，而`Requests`模块只提供请求方法，并不提供数据清洗，这也体现`Requests-HTML`的一大特点。使用`Requests`开发的爬虫，数据清洗需要调用其他模块实现，而`Requests-HTML`则将两者结合在一起。
2. `Requests-HTML`提供了各种各样的数据清洗方法，比如网页里的`URL`地址、`HTML`源码内容、文本信息等，使用方法如下所示：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 21:38:58
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-17 09:14:19
'''


from requests_html import HTMLSession

# 定义会话Session
session = HTMLSession()
url = r'http://movie.douban.com/'
# 发送GET请求
response = session.get(url)
# 输出网页的URL地址
print(response.html)
# 输出网址的全部URL地址
print(response.html.links)
# 输出网页里精准的URL地址
print(response.html.absolute_links)
# 输出网页的HTML信息
print(response.text)
# 输出网页的全部文本信息，即去除HTML代码
print(response.html.text)
```
3. 上述代码只是提取了网站的基本信息，如果想要精准地提取某个数据，可以使用`find()`、`xpath()`、`search()`和`search_all()`方法实现。首先了解这四种方法的定义及相关参数说明：
```
# 定义
find(selector, containing, clean, first, _encoding)
# 参数说明
selector:使用CSS Selector定位网页元素。
containing：字符串类型，默认值为None，通过特定文本查找网页元素。
clean:是否清除HTML的<script>和<style>标签，默认值为False。
first:是否只查找第一个网页元素，默认值为False即查找全部元素。
_encoding:设置编码格式，默认值为None。


# 定义
xpath(selector, clean, first, _encoding)
# 参数说明
selector:使用xpath selector定位网页元素。
clean:是否清除HTML的<script>和<style>标签，默认值为False。
first:是否只查找第一个网页元素，默认值为False即查找全部元素。
_encoding:设置编码格式，默认值为None。

# 定义
search(template)
# 参数说明
template:通过元素内容查找第一个元素

# 定义
search_all(template)
# 参数说明
template:通过元素内容查找全部元素
```
4. 以豆瓣电影网页为例，实现上述四种办法的精准提取。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 21:38:58
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-17 09:14:19
'''


from requests_html import HTMLSession

# 定义会话Session
session = HTMLSession()
url = 'http://movie.douban.com/'
# 发送GET请求
response = session.get(url)


# 通过CSS Selector定位li标签，“.title”代表class属性
# first=True代表获取第一个元素。
print(response.html.find('li.title', first = True).text)
# 输出当前标签的属性值
print(response.html.find('li.title', first = True).attrs)
print('____分割线____')


# 查找特定文本的元素
# 如果元素所在的HTML含有containing的属性值即可提取
for name in response.html.find('li', containing='星球'):
	# 输出电影名
	print(name.text)
print('____分割线____')


# 查找全部电影名
for name in response.html.find('li.title'):
	# 输出电影名
	print(name.text)
	# 输出电影名所在标签的属性值
	print(name.attrs)
print('____分割线____')


# 通过XPath Selector定位ul标签
x = response.html.xpath('//*[@id="screening"]/div[2]/ul')
for name in x:
	print(name.text)
print('____分割线____')



# search()通过关键字查找内容
# 一个()代表一个内容，内容可为中文或英文等
print(response.html.search('星球大战{}{}'))
print('____分割线____')

# search_all()通过关键字查找整个网页符合的内容
# 一个()代表一个内容，内容可为中文或英文等
print(response.html.search_all('星球大战{}{}'))
```
## Ajax 动态数据抓取
1. 如果使用`Requests-HTML`请求网页网址，相应的响应内容与开发者工具`Doc`选项卡的响应内容是一致的。
2. 如果网页数据是通过Ajax请求并由`JavaScript`渲染到网页上，还需要使用`Requests-HTML`模拟`Ajax`请求来获取网页信息。
3. 对于爬虫开发者来说，模拟`Ajax`请求是一件相当痛苦的事情，比如构建参数、请求参数的构建方式繁多而复杂，这非常考验开发者对网站的分析能力。
4. 为了降低难度，`RequestS-HTML`提供了`Ajax`加载的功能，加载后的网页信息与开发者工具的`Elements`选项卡的网页信息是一致的。这个加载功能是通过调用谷歌的`Chromium`浏览器实现，`Chromium`是谷歌为发展`Chrome`而开启的计划，它可以理解为`Chrome`的工程版或实验版，新功能都会率先在`Chromium`上实现，待验证后才会应用在`Chrome`上。
5. Ajax加载功能由`render()`方法实现，初次使用`render()`方法会自动下载`Chromium`浏览器，下载`Chromium`浏览器必须保证当前网络能正常访问谷歌首页，否则无法下载。
6. 完成了`Chromium`浏览器配置，可以编写以下代码来实现`Requests-HTML`的`Ajax`加载功能：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 21:38:58
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-17 09:14:19
'''


from requests_html import HTMLSession

url = r'http://y.qq.com/portal/singer_list.html'
session = HTMLSession()
response = session.get(url)
# 使用Chromium浏览器加载网页
response.html.render()
# 定位歌手姓名
singer = response.html.find('h3.singer_list_title')
# 输出歌手姓名
for i in singer
	print(i.text)
```
## 本章小结
1. `Requests-HTML`是在`Requests`的基础上进一步封装，这两个爬虫库都是由同一个开发者开发的。`Requests-HTML`除了包含`Requests`所有功能之外，还新增了数据提取和`Ajax`数据动态渲染。
2. `Requests-HTML`只能使用`Requests`的`Session`模式，该模式是将请求会话实现持久化，可使这个请求保持连接状态。`Session`模式好比我们打电话，只要双方没有挂电话，就会一直保持一种会话(连接)状态。
3. 数据提取是由`lxml`和`PyQuery`模块实现，这两个模块分别支持`XPath Selectors`和`CSS Selectors`定位，可以精准地提取网页里的数据。
4. `Ajax`数据动态渲染是将网页的动态数据加载到网页上再抓取，它是由`Requests-HTML`的`render()`方法实现，通过调用`Chromium`浏览器来加载`Ajax`功能，从而实现网页信息加载。
