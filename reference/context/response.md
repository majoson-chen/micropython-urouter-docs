# response
本对象用于对发起请求的客户端执行回应操作.

## functions:

### send_file():
发送一个以 `root_path` 为相对路径的本地静态文件, 如果找不到该文件, 自动发送一个 `404` 状态码.

params:
- path: str  
  用于指定欲发送文件的路径, (相对于 `root_path`)

**注意:**  
我们推荐不要发送过于庞大的静态文件, 如果您的项目需要很多静态文件, 建议将他们储存在云端进行访问, 这可以很大程度上减轻设备负担.

### redirect()
将请求重定向到另外一个地址.

params:
- location: str  
  欲重定向的网址, 例如 `https://www.baidu.com/`

- statu_code: int = 302  
  指定重定向方法:
  - `302`: 临时重定向
  - `301`: 永久重定向

### abort():
中断一个请求, 通常用于用户访问了不该访问的页面或者访问权限等级不够高.

params:
- statu_code: int = 500  
  指定响应的 `statu_code`.

### make_response():
此函数用于发送一个最普通的响应给浏览器, 且发送的数据大小是确定的.  
在响应时, 如果您尚未指定 `mimetype`, 我们会尝试猜测其内容类型并且自动为您填上 `mimetype`.  

如果一切都顺利进行, 那么它应该会返回 `True`
如果您在请求路由函数中返回了一个值且没有调用此函数, 我们会自动尝试调用此函数并将您的返回值传入 `data` 参数中.

params:
- data: any  
  指定欲发送的数据, 可以是以下几种类型:
  - str, bytes, bytearray
  - io.BytesIO (未经过充分测试)
  - list, tuple , dict (将会自动被转化成json文本)

### make_stream():
此函数用于创建一个流式传输响应, 适用于一些无法获取具体长度的数据, 例如监控视频数据.  
在 `make_stream` 之后, 如若您希望结束响应, 必须调用 `finish_stream`

**注意!!** 一次响应只能是一种传输模式, 即如果您调用了 `make_response`, 无法再次调用此函数.

params:  
没有任何参数.

### send_data():
**注意:** 此方法只能用于 `stream` 传输模式, 即调用 `make_stream` 之后才可以使用.
此方法用于向客户端发送一次数据, 在 `finish_stream()` 之前可以多次被调用.

params:
- data: any  
  此参数与 [make_response](#make_response) 中的 `data` 参数相同.

### finish_stream()
在您调用 `make_stream()` 用于创建一个流式响应传输之后, 如若您需要结束本次传输, 必须调用本函数. 如果在您的处理函数返回之前尚未调用此函数, 我们会尝试自动帮您调用一遍.

params:  
没有任何参数.

## Attributes:
- headers: dict  
  用于设置响应报文的 `headers` 内容.

- statu_code: int  
  用于自定义响应的状态码.

- mime_type: str  
  用于自定义发送的 `mimetype`

**注意:** 以上所有属性必须在 `make_reponse` 和 `make_stream` 被调用之前被设置, 否则无法生效.

**注意:** 一次响应过程中只能有一次发送请求, `make_reponse`, 和 `make_stream` 都会被视为一次发送请求, 即无法同时调用两者或者重复调用两者.