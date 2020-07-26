# 第七章 Requests-Cache 爬虫缓存
## Requests-Cache 简介及安装
1. `Requests-Cache`是`Requests`模块的一个扩展功能，它是根据`Requests`的发送请求来生成相应的缓存数据。
2. 当`Requests`重复向同一个`URL`发送请求的时候，`Requests-Cache`会判断当前的请求是否已产生缓存，若已有缓存，则从缓存里读取数据作为响应内容；若没有缓存，则向网站服务器发送请求，并将得到的响应内容写入相应数据库里。
3. `Requests-Cache`的作用非常重要，它可以减少网络资源重复请求的次数，不仅减轻了本地的网络负载，而且还减少了爬虫对网站服务器的请求次数，这也是解决反爬虫机制的一个重要手段。
4. 安装`Requests—Cache`可以通过`pip`指令完成，在`CMD`窗口下输入`pip install requests-cache`是否安装成功，具体操作如下：
```
C:\Users\000>python
>>>import requests_cache
>>>requests_cache.__version__
'0.4.13'
```
## 在Requests 中使用缓存
1. `Requests-Cache`遵循`Requests`的使用规则：功能强大并且使用简单，整个缓存机制由`install_cache()`方法实现。`install_cache()`方法定义如下所示：
```
install_cache(cache_name='cache', backend=None, expire_after=None, allowable_codes=(200,), allowable_methods=('GET',), session_factory=CachedSession, **backend_options)
```
2. `install_cache()`定义了多个函数参数，每个参数的说明如下：
```
(1) cache_name: 默认值为cache，这是对缓存的存储文件进行命名。
(2) backend: 设置缓存的存储机制，默认值为None，即默认sqlite数据库存储。
(3) expire_after: 设置缓存的有效时间，默认值None，即为永久有效。
(4) allowable_codes: 设置HTTP的状态码，默认值为200。
(5) allowable_methods: 设置请求方式，默认值是只允许GET请求才能生成缓存。
(6) session_factory: 设置缓存的执行对象，由CachedSession类实现，该类是由Requests-Cache定义。
(7) backend_options: 设置存储配置，若缓存的存储选择sqlite、redis或mangoDB数据库，则该参数是设置数据库的连接方式。
```
3. 在实际应用中，`install_cache()`可以直接使用，无需设置任何参数，因为`Requests—Cache`已对相关的参数设置了默认值，这些默认值基本能满足日常开发需求。
4. 使用`Requests—Cache`之前，首先创建一个简单的网站系统，这是由`Flask`框架开发的`Web`系统，主要是方便验证`Requests—Cache`的缓存功能。
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 15:44:41
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-16 15:49:46
'''


from flask import Flask
# 创建一个Flask实例
app = Flask(__name__)

# 设置路由器，即url
@app.route('/')

# url对应的函数
def hell0_world():
	# 返回页面
	return 'hell0 World!'

# 程序运行
if __name__ == '__main__':
	app.run()
```
5. 使用浏览器访问图上的地址链接即可看到网站的首页，浏览器每次成功访问网站，都会在网站后台出现相关的请求信息。
6. 根据这个规则，使用`Requests+Requests—Cache`对网站进行两次访问，查看网站后台请求信息出现次数。如果请求信息只出现一次，说明爬虫缓存正常使用，反之则说明`Requests-Cache`无法生成缓存。
7. `Requests-Cache`的使用方法如下所示：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 15:50:57
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-16 16:33:51
'''


import requests
import requests_cache

# 使用install_cache()方法
requests_cache.install_cache()
# 清除已有的缓存
requests_cache.clear()
# 访问自定义的Web系统
url = 'http://127.0.0.1:5000/'
# 创建Session会话
session = requests.session()
# 执行两次访问
for i in range(2):
	response = session.get(url)
	# from_cache是requests_cache的函数
	# 若输出True，说明生成缓存。
	print(response.from_cache)
```
8. 如果短时间内多次访问网站服务器，很容易遭到服务器的拦截，从而认定这些请求是通过爬虫程序执行，而非人为操作，这就是反爬虫常见机制之一。
9. 为了降低访问频率，可以在每个请求之间设置一个`time.sleep()`函数，虽然能降低访问频率，但这样处理就显得不太友好。因为两次请求之间，第一次才是真正访问网站后台，而第二次是直接从数据库读取缓存数据，所以这两次请求无需设置延时。
10. 那么如何判断这次请求是否已有缓存，每个请求之间应如何合理地设置延时等待？为此，`Requets-Cache`可以自定义钩子函数，通过函数去合理判断是否设置延时，函数的定义与使用方法如下：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 15:50:57
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-16 16:33:51
'''

import time 
import requests_cache

# 定义钩子函数
def make_throttle_hook(delay=1.0):
  def hook(response, *args, **kwargs):
    # 如果没有缓存，则添加延时
    if not getattr(response, 'from_cache', False):
      print('delayTime')
      time.sleep(delay)
    return response
  return hook
      
      
if __name__ == '__main__':
  requests_cache.install_cache()
  requests_cache.clear()
  # 钩子函数的使用
  s = requests_cache.CachedSession()
  s.hooks = {'response': make_throttle_hook(2)}
  s.get('http://127.0.0.1:5000/')
  s.get('http://127.0.0.1:5000/')
```
## 缓存的存储机制
1. `Requests-Cache`支持`sqlite`、`redis`和`mongoDB`数据库存储缓存信息。此外，还可以将缓存存储在计算机的内存中。也就是说`Requests-Cache`支持4种不同的存储机制：`memory`、`sqlite`、`redis`、`mongoDB`，4种存储机制说明如下：
```
(1) memory: 每次程序运行都会将缓存以字典形式保存在内存中，程序运行完毕，缓存也随之销毁。
(2) sqlite: 将缓存存储在sqlite数据库，这是Request-Cache默认的存储机制。
(3) redis: 将缓存存储在redis数据库，通过redis模块实现数据库的读写。
(4) mongoDB: 将缓存存储在mongoDB数据库，通过pymongo模块实现数据库的读写。
```
2. 在`Requests-Cache`设置不同的存储机制只需对`install_cache()`方法的参数`backend`进行设置即可，具体设置如下：
```
#!/usr/bin/python3
# -*- coding: utf-8 -*-
'''
# @Author: StudentCWZ
# @Date:   2019-12-16 19:46:13
# @Last Modified by:   StudentCWZ
# @Last Modified time: 2019-12-16 21:00:13
'''


import requests_cache

# 设置memory储存
requests_cache.install_cache(backend='memory')
# 设置sqlite缓存
requests_cache.install_cache(backend='sqlite')
# 设置redis存储
requests_cache.install_cache(backend='redis')
# 设置mongo存储
requests_cache.install_cache(backend='mongo')
```
## 本章小结
1. `Requests-Cache`是`Requests`模块的一个拓展功能，它是根据`Requests`的发送请求来生成相应的缓存数据，其作用非常重要，可以减少网络资源重复请求的次数，不仅减轻了本地的网络负载，而且还可以减少爬虫对网站服务器的请求次数，这也是解决反爬虫机制的一个重要手段。
2. 整个缓存机制由`install_cache()`方法实现，该方法的参数说明如下：
```
(1) cache_name: 默认值为cache，这是对缓存的存储文件进行命名。
(2) backend: 设置缓存的存储机制，默认值为None，即默认sqlite数据库存储。
(3) expire_after: 设置缓存的有效时间，默认值None，即为永久有效。
(4) allowable_codes: 设置HTTP的状态码，默认值为200。
(5) allowable_methods: 设置请求方式，默认值是只允许GET请求才能生成缓存。
(6) session_factory: 设置缓存的执行对象，由CachedSession类实现，该类是由Requests-Cache定义。
(7) backend_options: 设置存储配置，若缓存的存储选择sqlite、redis或mangoDB数据库，则该参数是设置数据库的连接方式。
```
3. `Requests-Cache`支持4种不同的存储机制：`memory`、`sqlite`、`redis`、`mongoDB`，4种存储机制说明如下：
```
(1) memory: 每次程序运行都会将缓存以字典形式保存在内存中，程序运行完毕，缓存也随之销毁。
(2) sqlite: 将缓存存储在sqlite数据库，这是Request-Cache默认的存储机制。
(3) redis: 将缓存存储在redis数据库，通过redis模块实现数据库的读写。
(4) mongoDB: 将缓存存储在mongoDB数据库，通过pymongo模块实现数据库的读写。
```
