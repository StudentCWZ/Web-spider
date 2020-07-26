# 第五章 爬虫库 Urllib
## Urllib 简介
1. `Urllib`是`Python`自带标准库，无需安装，直接引用即可。`Urllib`用常用于爬虫开发、`API`(应用程序编程接口)数据获取和测试。在`Python2`和`Python3`中，`Urllib`在不同版本中语法有明显的改变。
2. `Python2`分为`Urllib`和`Urllib2`，`Urllib2`可以接收一个`Request`对象，并以此设置一个`URL`的`Headers`，但是`Urllib`只接收一个`URL`，意味着不能伪装用户代理字符串等。`Urllib`模块可以提供进行`Urlencode`的方法，该方法用于`GET`查询字符串的生成，`Urllib2`不具有这样的功能。这也是`Urllib`与`Urllib2`经常在一起使用的原因。
3. 在`Python3`中，`Urllib`模块是一堆可以处理`URL`的组件集合，就是将`Urllib`和`Urllib2`合并在一起使用，并且命名为`Urllib`。
4. 在`Python3`中，`Urllib`是一个收集几个模块来使用`URL`的软件包，大致具备以下功能：
```
(1) urllib.request: 用于打开和读取URL。
(2) urllib.error: 包含提出的例外urllib.request。
(3) urllib.parse: 用于解析URL。
(4) urllib.rootparser: 用于解析robots.txt文件。
```
## 发送请求
1. `urllib.request.urlopen`的语法如下：
```
urllib.request.urlopen(url, data=None, [timeout, ]*, cafile=None, capath=None, cadefault=False, context=None)
```
2. `urllib.request.urlopen`参数如下：
```
(1) url: 需要访问的URL地址，url格式必须完整，如https://movie.douban.com/ 为完整的url，若url为movie.douban.com/ ，则程序运行会提示无法识别url的错误。
(2) data: 默认值为None，Urllib判断参数data是否为None从而区分请求方式。若参数data为None，则代表请求方式为GET；反之请求方式为POST，发送POST请求。参数data以字典的形式存储数据，并将参数data由字典类型转换成字节类型才能完成POST请求。
(3) Timeout: 超时设置，指定阻塞才做(请求时间)的超时(如果未指定，就使用全局默认超时设置)。
(4) cafile、capath和cadefault: 使用参数指定一组HTTPS请求的可信CA证书。cafile应指向包含一组CA证书的单个文件；capath应指向证书文件的目录；cadefault通常使用默认值即可。
(5) context: 描述各种SSL选项的实例。
```
3. 在实际使用中，采用的参数有`url`、`data`和`timeout`。若在爬虫中遇到证书验证，则可将证书验证直接关闭，也可设置参数指向证书的信息和位置。相比而言，设置证书比较耗时，而且通用性不强。
4. 当对网站发送请求时，网站会返回相应的响应内容。`urlopen`对象提供获取网站响应内容的方法函数，分别介绍如下：
```
(1) read()、readline()、readlines()、fileno()和Close(): 对HTTPResponse类型数据操作。
(2) info(): 返回HTTPMessage对象，表示远程服务器返回的头信息。
(3) getcode: 返回HTTP状态码。
(4) geturl: 返回请求的URL。
```
- 例一：实现`Urllib`模块对网站发送请求并将响应内容写入文档
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 19:48:21
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 20:04:16
'''



# 导入urllib
import urllib.request
# 打开URL
response = urllib.request.urlopen('https://movie.douban.com/', None, 2)
# 读取返回的内容
html = response.read().decode('utf8')
# 写入txt
f = open('html.txt', 'w', encoding='utf8')
f.write(html)
f.close()
```
## 复杂的请求
1. `urllib.request.Request`的语法如下：
```
urllib.request.Request(url, data=None, header={}, method=None)
-- 功能说明：声明一个request对象，该对象可自定义header(请求头)等请求消息。
```
2. `urllib.request.Request`的参数解释：
```
(1) url: 完整的url格式，与urllib.request.urlopen的参数url一致。
(2) data: 请求参数，与urllib.request.urlopen的参数data一致。
(3) Headers: 设置request请求头信息。
(4) method: 设定请求方式，主要是POST和GET方式。
```
3. 一个完整的`HTTP`请求必须要求有请求头信息，而`urllib.requestRequest`的作用是设置`HTTP`的请求头信息。
- 例一：使用`urllib.request.Request`设置请求头
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 19:48:21
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 20:04:16
'''


# 导入urllib
import urllib.request
# 输入爬取数据的URL
url = r'https://movie.douban.com/'
# 自定义请求头
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3983.2 Safari/537.36'}

# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers)
# 使用urlopen打开URL
response = urllib.request.urlopen(req)
# 读取返回的内容
html = response.read().decode('utf8')
# 写入txt
f = open('html.txt', 'w', encoding='utf8')
f.write(html)
f.close()
```
## 代理IP
1. `代理IP`的原理：以本机先访问`代理IP`，在通过`代理IP`地址访问互联网，这样网站(服务器)接收到的`访问IP`就是`代理IP`地址。
2. `Urllib`提供了`urllib.requestProxyHander()`方法可以设置`代理IP池`，`代理IP`主要以字典格式写入方法。完成`代理IP`设置后，将设置好的`代理IP`写入`urllib.request.build_opener()`方法，生成对象`opener`，然后通过`opener`的`open()`方法向网站(服务器)发送请求。
- 例一：设置代理池
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 20:07:04
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 20:41:29
'''


# 导入urllib
import urllib.request
# 输入爬取数据的URL
url = r'https://movie.douban.com/'
# 设置代理IP
proxy_handler = urllib.request.ProxyHandler({'http': '163.204.247.173:9999'})
# 自定义请求头
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3983.2 Safari/537.36'}
# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers)
# 必须使用build_opener()函数来创建带有代理IP功能的opener对象
opener = urllib.request.build_opener(proxy_handler)
# 请求url
response = opener.open(req)
# 读取返回的内容
html = response.read().decode('utf8')
# 写入txt
f = open('html_01.txt', 'w', encoding='utf8')
f.write(html)
f.close()
```
3. 由于使用`代理IP`，因此连接IP的时候有可能出现超时而导致错误，遇到这种情况只要更换其他`代理IP`地址或者再次访问即可。以下是常见报错信息：
```
(1) ConnectionResetError: [winError 10054]远程主机强迫关闭了一个现有的连接。
(2) urllib.error.URLError: urlopen error Remote end closed connection without response(结束没有响应的远程连接)
(3) urllib.error.URLError: urlopen error[WinError 10054] 远程主机强迫关闭了一个现有的连接。
(4) TimeoutError: [WinError 10060]由于连接方在一段时间后没有正确答复或连接的主机没有反应，因此连接尝试失败。
(5) urllib.error.URLError: urlopen error[WinError 10061] 由于目标计算机拒绝访问，因此无法连接。
```
## 使用 Cookies
1. `Cookies`主要用于获取用户登录信息，比如，通过提交数据实现用户登录之后，会生成带有登录状态的`Cookies`，这是可以将`Cookies`保存在本地文件中，下次程序运行的时候，可以直接读取`Cookies`文件来实现用户登录。特别对于一些复杂的登录，如验证码、手机短信验证登录这类网站，使用`Cookies`能简单解决重复登录的问题。
- 例一：`Urllib`提供`HTTPCookieProcessor()`对`Cookies`操作
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 20:44:40
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 21:39:41
'''


# 导入第三方库
import urllib.request
from http import cookiejar


# cookie文件路径
filename = 'cookie.txt'

# 输入爬取数据的URL
url = r'https://movie.douban.com/'

# 自定义请求头
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3983.2 Safari/537.36'}

# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers)

# MozillaCookieJar保存cookie
cookie = cookiejar.MozillaCookieJar(filename)

# HTTPCookieProcessor创建cookie处理器
handler = urllib.request.HTTPCookieProcessor(cookie)

# 创建自定义opener
opener = urllib.request.build_opener(handler)

# open方法打开网页
response = opener.open(req)

# 保存cookie文件
cookie.save()
```
- 例二：读取`Cookies`
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 21:11:56
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 21:39:19
'''


# 导入第三方库
import urllib.request
from http import cookiejar

# cookie文件路径
filename = 'cookie.txt'

# 输入爬取数据的URL
url = r'https://movie.douban.com/'

# 自定义请求头
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3983.2 Safari/537.36'}

# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers)

# 创建MozillaCookieJar对象
cookie = cookiejar.MozillaCookieJar()
# 读取cookie内容到变量
cookie.load(filename)
# HTTPCoookieProcessor创建cookie处理器
handler = urllib.request.HTTPCookieProcessor(cookie)

# 创建opener
opener = urllib.request.build_opener(handler)
# opener打开网页
response = opener.open(req)
# 输出结果
print(cookie)
```
2. 在实际开发中，为了提高安全性, 可以在保存和读取`Cookie`时设置参数，使`Cookie`信息隐藏在文件中。
- 例三：`Cookie`信息隐藏
```
cookie.save(ignore_discard=True, ignore_expires=True)
cookie.load(filename, ignore_discard=True, ignore_expires=True)
```
## 证书验证
1. `SSL`证书就是遵守`SSL`协议，由受信任的数字证书机构颁发`CA`，在验证服务器身份后颁发，具有服务器身份验证和数据传输加密功能。
2. `SSL`证书在客户端浏览器和`Web`服务器之间建立一条`SSL安全通道(Secure Socket Layer, SSL)`，安全协议是由`Netscape Communication`公司设计开发的。该安全协议主要用来提供对用户和服务器的认证，对传送的数据进行加密和隐藏，确保数据在传送中不被改变，即数据的完整性现已成为该领域中全球化的标准。
- 例一：关闭证书验证
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 21:48:36
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 22:07:54
'''

# 导入第三方库
import urllib.request
import ssl
# 关闭证书验证
ssl._create_default_https_context = ssl._create_unverified_context
url = r'https://kyfw.12306.cn/otn/leftTicket/init'
response = urllib.request.urlopen(url)
# 输出状态码
print(response.getcode())
```
## 数据处理
1. `Urllib`在请求服务器的时候，如果发生数据传递，就需要对内容进行编码处理，将包含`str`或`bytes`对象的两个元素元组序列转换成百分比编码的`ASCII`文本字符串。
- 例一：`Urllib`发送`POST`请求
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 22:08:22
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 22:19:28
'''

# 导入第三方库
import urllib.request
import urllib.parse


# 输入爬取数据的URL
url = r'https://movie.douban.com/'
# 自定义请求头
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3983.2 Safari/537.36'}

# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers)

# post请求参数
data = {
	'value': 'true'
}


# 数据处理
data = urllib.parse.urlencode(data).encode('utf-8')

# 设置request的请求头
req = urllib.request.Request(url=url, headers=headers, data=data)

# 使用urlopen打开URL
response = urllib.request.urlopen(req)

# 读取返回的内容
html = response.read().decode('utf8')

# 写入txt
f = open('html_02.txt', 'w', encoding='utf8')
f.write(html)
f.close()
```
- 例二：`Urllib`提供`quote()`和`unquote()`编码处理
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-06 22:25:43
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-06 22:36:38
'''


import urllib.parse
url = '%2523%25E7%25BC%2596%25E7%25A8%258B%2523'
# 第一次解码
first =urllib.parse.unquote(url) # unquote解码
print(first)

# 第二次解码
second = urllib.parse.unquote(first)
print(second)
-- 上述代码输出结果
%23%E7%BC%96%E7%A8%8B%23
#编程#
```
## 本章小结
1. 本章主要讲解了`Python`自带模块`Urllib`的功能和使用。`Urllib`通常用于爬开发和`API`(应用程序编程接口)数据获取和测试。
2. 在`Python2`和`Python3`中，`Urllib`的语法有明显的改变，其常用的语法有以下几种：
```
(1) urllib.request.urlopen: urllib最基本的使用功能，用于访问URL(请求链接)的唯一方法。
(2) urllib.request.Request: 声明request对象，该对象可自定义请求头(header)、请求方式等信息。
(3) urllib.request.ProxyHandler: 动态设置代理IP池，可加载请求对象。
(4) urllib.request.bulid_opener: 创建请求对象，用于代理IP和Cookies对象加载。
(5) urllib.parse.urlencode(data).encoding('utf8'): 请求数据格式转换。
(6) urllib.parse.quote(url): URL编码处理，主要对URL上的中文等特殊符号编码处理。
(7) urllib.parse.unquote(url): URL解码处理，将URL上的特殊符号还原。
```
