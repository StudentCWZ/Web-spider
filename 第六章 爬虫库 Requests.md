# 第六章 爬虫库 Requests
## Requests 简介及安装
1. `Requests`是`Python`的一个很实用的`HTTP`客户端库，完全满足如今网络爬虫的需求。
2. 与`Urllib`对比，`Requests`不仅具备`Urllib`的全部功能；在开发使用上，语法简单易懂，完全符合`Python`优雅、简洁的特性；在兼容性上，完全兼容`Python2`和`Python3`，具有较强的适用性。
3. `Requests`可以通过`pip`安装，具体如下：
```
(1) Windows系统：pip install requests
(2) Linux系统：sudo pip install requests
```
4. 除了使用`pip`安装之外，还可以下载`whl`文件安装，方法如下：
```
1. 访问www.lfd.uci.edu/~gohlke/pythonlibs ，按Ctrl+F组合键搜索关键字“requests”。
2. 单击下载requests-2.20.0-py2.py3-none-any.whl，把下载文件直接解压，将解压出来的文件直接放入Python的安装目录Lib\site-packages中即可。
3. 除了解压whl，还可以使用pip安装whl文件。例如把下载的文件保存在E盘，打开CMD(终端)，将路径切换到E盘，输入安装命令：pip install requests-2.20.0-py2.py3-none-any.whl。
```
5. 完成`Requests`安装后，在`终端(CMD)`下运行`Python`，查看`requests`版本信息，检测是否安装成功。方法如下：
```
E:\>Python
>>> import requests
>>> requests.__version__
'2.20.0'
```
## 请求方式
1. `HTTP`的常用请求是`GET`和`POST`，`Requests`对此区分两种不同的请求方式。`GET`请求有两种形式，分别是不带参数和带参数，以百度为例：
```
# 不带参数
https://www.baidu.com/
# 带参数 wd
https://www.baidu.com/s?wd=python
```
2. 判断`URL`是否带有参数，可以对符号“?”判断。一般网址末端(域名)带有“?”，就说明该`URL`是带有请求参数的，反之则不带有参数。
3. `GET`参数说明如下：
```
(1) wd是参数名，参数名由网站(服务器)规定。
(2) python是参数值，可由用户自行设置。
(3) 如果一个URL有多个参数，参数之间用“&”连接。
```
4. `Requests`实现`GET`请求，对于带参数的`URL`有两种请求方式：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 20:48:14
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 21:28:47
'''

import requests

# Request实现GET请求，对于带参数的URL有两种请求方式
# 第一种方式
response = requests.get('https://www.baidu.com/s?wd=python')
# print(response.status_code) # 输出状态码
# 第二种方式
url = 'https://www.baidu.com/s'
params = {'wd': 'python'}
# 左边params在GET请求中表示设置参数
response = requests.get(url, params = params)
# print(response.status_code) # 输出状态码
# 输出生成的URL
print(response.url)
```
5. 利用`GET`请求去访问百度
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 20:48:14
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 21:28:47
'''

import requests

response = requests.get('https://www.baidu.com/')
response.encoding = 'utf-8' # 注意编码问题
print(response.status_code)
print(response.text)
```
6. `POST`请求就是我们常说的提交表单，表单的数据内容就是`POST`的请求参数。
7. `Requests`实现`POST`请求需要设置请求参数`data`，数据格式可以为`字典`、`元组`、`列表`、和`JSON`格式，不同的数据格式有不同的优势。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 20:48:14
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 21:28:47
'''

import requests

# 字典类型
data = {'key1':'value1', 'key2': 'Value2'}
# 元组或列表
data = (('key1', 'value1'), ('key2', 'value2'))
# JSON
import json
data = {'key1':'value1', 'key2': 'Value2'}
# 将字典转换成JSON
data = json.dumps(data)
# 发送POST请求
response = requests.post('https://www.baidu.com/', data = data)
print(response.text)
```
8. `Requests`的`GET`和`POST`方法的请求参数分别是`param`和`data`，别混淆两者的使用要求。
9. 当向网站(服务器)发送请求时，网站会返回相应的`响应(response)对象`，包含服务器响应的信息。`Requests`提供以下方法获取响应内容：
```
(1) response.status_code: 响应状态码。
(2) response.raw: 原始响应体，使用response.raw.read()读取。
(3) response.content: 字节方式的响应体，需要进行解码。
(4) response.text: 字符串方式的响应体，会自动根据响应头部的字符编码进行编码。
(5) response.headers: 以字典对象存储服务器的响应头，但是这个字典比较特殊，字典键不区分大小写，若键不存在，则返回None。
(6) response.json(): Requests中内置的JSON解码器。
(7) response.raise_for_status(): 请求失败(非200响应)，抛出异常。
(8) response.url: 获取请求链接。
(9) response.cookies: 获取请求后cookies。
(10) response.encoding: 获取编码格式。
```
## 复杂的请求方式
1. 从上一章得知，复杂的请求方式通常有`请求头`、`代理IP`、`证书验证`和`Cookies`等功能。
2. `Requests`将这一系列的请求做了简化，将这些功能在发送请求中以参数的形式传递并作用到请求中。
- 添加请求头：请求头以字典的形式生成，然后发送请求中设置的`headers`参数，指向已定义的请求头。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

url = 'https://www.baidu.com/' # 输入URL
# 定义请求头
headers = {
	'content-type': 'appliaction/json',
	'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.7 Safari/537.36'
}
# Requests发送GET请求
response = requests.get(url, headers = headers)
response.encoding = 'utf-8' # 注意编码问题
print(response.status_code) # 查看状态吗
print(response.text) # 输出网页源码
```
- 使用`代理IP`：`代理IP`的使用方法与请求头的使用方法是一致的，设置`proxies`参数即可。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

# 自定义代理IP
proxies = {
	'http': 'http://106.85.139.163:9999'
}

# 使用代理利用Requests发送GET请求
response = requests.get(url, headers=headers, proxies = proxies)
response.encoding = 'utf-8' # 注意编码问题
print(response.status_code) # 查看状态吗
print(response.text) # 输出网页源码
```
- 证书验证：通常设置关闭验证即可。在请求参数`verify=False`时就能关闭证书的验证，默认情况下是`True`。如果需要设置证书文件，那么可以设置参数`verify`值为证书路径。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

url = 'https://kyfw.12306.cn/otn/leftTicket/init'

# 关闭证书验证
response = requests.get(url, verify = False)
print(response.status_code)
# 开启证书验证
# response = requests.get(url, verify = True)
# 设置证书所在路径
# response = requests.get(url, verify = '/path/to/certfile')
```
- 超时设置：发送请求后，由于网络、服务器等因素，请求到获得响应会有一个时间差。如果不想程序等待时间过长或者延长等待时间，可以设置`timeout`的等待秒数，超过这个时间之后通知等待响应。如果服务器在`timeout`秒内没有应答，将会引发一个异常。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

# response = requests.get('https://www.baidu.com/', timeout = 0.01) # 出现异常
# 捕获异常语句
try:
	response = requests.get('https://www.baidu.com/', timeout = 0.01) # 出现异常
	print(response.status_code)
except:
	print('TimeOut')
```
- 使用`Cookies`:在请求过程中使用`Cookies`也只需要设置参数`Cookies`即可。`Cookies`的作用是标识用户身份，在`Request`中以字典或者`RequestSCookieJar`对象作为参数。获取的方式主要是从浏览器读取和程序运行所产生。下面的例子进一步讲解如何使用`Cookies`。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

# 从浏览器中获取Cookies
temp_cookies = 'BAIDUID=C10A71AB1F84D5C90EE00A826196FB92:FG=1; BIDUPSID=C10A71AB1F84D5C90EE00A826196FB92; PSTM=1559475252; BD_UPN=123253; BDUSS=2xDaFozUzhMUGl-Rmk4c1RQd1pJa285dHBYVm8xOWsxcnFhY2Zid1h6RXRsSjVkRUFBQUFBJCQAAAAAAAAAAAEAAAC1zuMzY3d6XzE5OTQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAC0Hd10tB3ddT; BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; delPer=0; BD_CK_SAM=1; PSINO=3; H_PS_PSSID=1454_21106_30210_20692_26350; COOKIE_SESSION=779697_0_5_3_7_2_1_1_4_1_0_1_1726556_0_0_0_1575635242_0_1576414870%7C9%23366_17_1567391567%7C9; BD_HOME=1'

# 将Cookies拆分成键值对形式
cookies_dict = {}
for i in temp_cookies.split(';'):
	value = i.split('=')
	cookies_dict[value[0]] = value[1]
# print(cookies_dict)
response = requests.get(url, headers = headers, cookies=cookies_dict)
response.encoding = 'utf-8' # 注意编码问题
print(response.status_code) # 查看状态吗
print(response.text) # 输出网页源码
```
- 当程序发送请求时(不设置`cookies`)，会自动生成一个`RequestsCookieJar`对象，该对象用与存放`Cookies`信息，`Requests`提供`RequestsCookieJar`对象和字典对象的相互转换。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

url = 'https://movie.douban.com'
headers = {
	'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.7 Safari/537.36'
}
response = requests.get(url, headers=headers)
print(response.status_code)

# response.cookies是RequestCookieJar对象
print(response.cookies)
mycookies = response.cookies

# RequestCookieJar转换字典
cookies_dict = requests.utils.dict_from_cookiejar(mycookies)
print(cookies_dict)

# 字典转换RequestsCookiesJar
cookies_jar = requests.utils.cookiejar_from_dict(cookies_dict, cookiejar = None, overwrite = True)
print(cookies_jar)

# 在RequestsCookiejar对象添加Cookies字典中
print(requests.utils.add_dict_to_cookiejar(mycookies, cookies_dict))
```
- 如果要将`Cookies`写入文件，可使用`http`模块实现`Cookies`的读写。除此之外，还可以将`Cookies`以字典的形式写入文件，此方法相对`http`模块读写`Cookies`更为简单，但安全性相对较低。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

url = 'https://movie.douban.com'

headers = {
	'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.7 Safari/537.36'
}

response = requests.get(url, headers = headers)

print(response.status_code)
print(response.cookies)

mycookies = response.cookies

# RequestsCookieJar转换成字典
cookies_dict = requests.utils.dict_from_cookiejar(mycookies)

# 写入文件
f = open('cookies.txt', 'w', encoding = 'utf-8')
f.write(str(cookies_dict))
f.close()

# 读取文件
f = open('cookies.txt', 'r', encoding = 'utf-8')
dict_value = f.read()
f.close()

# eval(dict_value)将字符串转换成字典
print(eval(dict_value))
response = requests.get(url, headers = headers, cookies = eval(dict_value))
print(response.status_code)
```
## 下载与上传
1. 下载文件：主要从服务器获取文件内容，然后将内容保存到本地。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests
'''
代码变量url是一个图片文件URL地址，对文件所在地址发送请求(大多数是GET请求方式)；服务器将文件内容作为响应内容，然后将得到的内容以字节流(Bytes)写入自定义文件，这样就能实现文件下载。
'''

url = r'https://www.python.org/static/img/python-logo.png'
response = requests.get(url)
f = open('python.jpg', 'wb')
# response.content获取响应内容(字节流)
f.write(response.content)
f.close()
```
2. 文件上传
```
(1) 除了文件下载外，还有更为复杂的文件上传，文件上传是将本地文件以字节流的方式上传到服务器，再由服务器上传内容，并做出相应的响应。文件上传存在一定的难度，其难点在于服务器接收规则不同，不同的网站接受的数据格式和数据内容会不一致。
(2) POST数据对象是以文件为主，上传文件时使用files参数作为请求参数。Requests对提交的数据和文件所使用的的请求参数作了明确规定。
参数files也是以字典形式传递的，每个Content-Disposition为字典的键值对，Content-Disposition的name为字典的键，value为字典的值。
```
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

import requests

# 微博文件上传
url = 'https://weibo.cn/mblog/sendmblog?rl=0&st=bd6702'
cookies = {'XXX': 'XXX'}
files = {
	'content': (None, 'Python爬虫'),
	'pic': ('pic', open('test.png', 'rb'), 'image/png'),
	'visible': (None, '0')
}
response = requests.post(url, files = files, cookies = cookies)
print(response.status_code)
```
3. 常用的文件上传方法(不同的网站设置对`files`参数的设置也是不一样的)
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-15 21:29:23
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-15 23:30:25
'''

# 单独一个文件请求
{
	"file1": open("filePath1", "rb").read()
}


# 同时选中多个文件
{
	"file1":[
	("filename1", open("filePath1", "rb")),
	("filename2", open("filePath2"), "rb", "image/png"),
	open("filePath3", "rb"),
	open("filePath4", "rb").read()
	]
}
```
## 本章小结
1. `Requests`是`Python`的一个很实用的`HTTP`客户端库，可完全满足如今编写网络爬虫的程序的需求，是爬虫开发人员首选的爬虫库。其具有语法简单易懂，完全符合`Python`优雅和简洁的特性，在兼容性上完全兼容`Python`任何版本，具有较强的适用性。
2. `Requests`的`GET`和`POST`将请求中所需要使用的功能都以参数的形式直接作用到请求中。一个发送请求的语句就已包含了`请求头`、`代理IP`、`Cookies`、`证书验证`、`文件上传`等功能。



