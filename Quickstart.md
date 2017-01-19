# Quickstart

## 产生一个请求
```python
import requests

# http methods
requests.get
requests.post
requests.delete
requests.head
requests.options
```

## 在URLs中传递参数
```
# httpbin.org/get?key1=value1&key2=value2
payload = {'key1': 'value1', 'key2': 'value2'}

# httpbin.org/get?key1=value1&key2=value2&key2=value3
payload = {'key1': 'value1', 'key2': ['value2', 'value3']}
r = request.get('httpbin.org/get', params=payload)
print(r.url)
```

## 响应内容
```
# requests根据response头自动对返回内容进行编码
r.text

# 查看并更改encoding,再次调用r.text时使用设置的encoding
r.encoding
r.encoding = 'ISO-8859-1'

# 可以通过网页内容的响应字段判断使用哪种encoding
r.content
```

## Binary Response Content
```
from PIL import Image
from io import BytesIO
r.content
i = Image.open(BytesIO(r.content))
```

## JSON Response Content
```
r.json()

# 检查request是否成功
r.status_code
```

## Raw Response Content
```
# 请求必须使用stream=True参数
r = requests.get('https://api.github.com/events', stream=True)
r.raw

# 一般用法
with open(filename, 'wb') as f:
    for chunk in r.iter_content(chunk_size=128):
        f.write(chunk)
```

## 自定义请求头
给`headers`参数传一个`dict`
```
url = 'https://api.github.com/events'
headers = {'user-agent': 'my-app/0.1'}
r = requests.get(url, headers=headers)
```

## 更复杂的POST请求
给`data`参数传一个`dict`或`string`
```
# 一般形式
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post("http://httpbin/org/post", data=payload)

# JSON形式
import json
r = requests.post("http://httpbin/org/post", data=json.dumps(payload))

# 或直接将dict传递给json参数
r = requests.post(url, json=payload)
```

## POST a Multipart-Encoded File
`files`参数
```
url = 'http://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}

# 可以指定文件细节,这里的report.xls为服务器上存放的文件名
files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}

# 可以POST一个字符串作为文件内容
files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

r = requests.post(url, files=files)
```

## Response Status Codes
```
r.status_code

# 内置的状态对象
r.status_code == requests.codes.ok
```
如果收到错误的响应,可以用Response.raise_for_status()抛出响应的HTTPError
```
bad_r = requests.get('http://httpbin.org/status/404')
bad_r.status_code
bad_r.raise_for_status()
```

## 响应头
python字典形式
```
r.headers

# HTTP头中的内容大小写不敏感
r.headers['content-type']
```

## Cookies
如果响应中有cookies,可以很容易的访问
```
# cookies作为一个RequestsCookieJar对象返回
r.cookies
r.cookies['some_name']
```

将值传给`cookies`参数来发送一个请求
```
# 字典形式
cookies = dict(cookies_are='working')
r = requests.get(url, cookies=cookies)

# Cookie jar形式
jar = requests.cookies.RequestsCookieJar()
jar.set('tasty_cookie', 'yum', domain='httpbin.org', path='/cookies')
r = requests.get(url, cookies=jar)
```

## Redirection and History
Requests默认将对除了HEAD以外的动作进行重定向
使拥Response对象的`history`属性追踪重定向
```
r = requests.get('http://github.com')
r.history

# 可以使用allow_redirects禁用重定向
r = requests.get('http://github.com', allow_redirects=False)
```

## Timeouts
`timeout`参数,收到响应的时间,不是接受完响应的时间
```
requests.get(url, timeout=0.1)
```

## Errors and Exceptions
所有异常继承于`requests.exception.RequestException`
`ConnectionError`
`HTTPError`
`Timeout`异常
`TooManyRedirects`异常
