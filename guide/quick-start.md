# 快速开始
在本文, 我将引导您快速地熟悉本框架.  
在阅读本文之后, 如果您将开始基于本框架进行开发, 我推荐您先阅读一遍我们的[参考手册](../reference)

相信您已经在[README](/)中了解到一些关于本框架的用法了, 本文将作为补充, 带您详细介绍关于本框架的一些用法.

## 关于micropython设备的调试和开发
如果您还不知道如何向 `micropython` 设备上传文件或是对其进行调试开发. 您可以使用 [thonny](https://thonny.org/) 工具来开发您的 `micropython` 设备.

`thonny` 支持向 `micropython` 设备上传文件, 也支持基于串口的 `repl` 交互.

![thonny](/staitcs/thonny.png)

关于 `thonny` 的更多使用方法和内容, 您可以自行上网搜索.

## 上手
### 基本路由
我们先来看一个最简单的路由:
```python
from urouter import uRouter

app = uRouter()

@app.route("/")
def index():
    return "<p>Hello World!</p>"

app.serve_forever()
```
把这段代码写入到 `main.py` 并上传到您的开发板中后重新上电, 当您在浏览器中访问 `开发板IP` , 即可显示 `Hello World!`(确保您的开发板已经连接到网络)

此时此框架会一直阻塞线程, 并相应HTTP请求, 这是一种比较稳定的响应方式.

![hello-world](/staitcs/quick-start/微信截图_20210728135004.png)

![日志截图](/staitcs/quick-start/微信截图_20210728135103.png)

如果您不希望一直运行, 您可以使用 `app.stop()` 或者在 `repl` 环境中按下 `Ctrl+C` 来停止`HTTP`服务
```python
from urouter import uRouter

app = uRouter()

@app.route("/")
def index():
    return "<p>Hello World!</p>"

@app.route("/stop")
def stop():
    app.stop()

app.serve_forever()
```
当浏览器访问 `/stop` 之后, 监听服务就停止了:
![stop](/staitcs/stop.png)

### 一次性服务
不要想歪, 此性服务非彼性服务, 这是一次性服务(狗头)

有时候我们不希望监听HTTP请求的程序一直阻塞线程, 毕竟我们的开发板也许会有其他的事情要做, (例如采集数据, IO操作)这时候如果 `HTTP` 监听服务阻塞了线程, 我们无法完成任何任务.

我们可以使用 `app.serve_once()` 来控制 `webapp` 只接受一次HTTP请求.
```python
from urouter import uRouter
from machine import Pin

led = Pin(2, Pin.OUT)
app = uRouter()

@app.route("/")
def index():
    return "<p>Hello World!</p>"

statu:int = 0
while True:
    statu = 0 if statu else 1
    app.serve_once()
    led.value(statu)

    # do some thing else...
```
在上面的例子中, 每处理一次 `HTTP` 请求, LED灯就会闪烁一次, 同时还允许执行其他的操作.

但是, `app.serve_once()` 会一直阻塞直到新的请求进入, 如果一直没有请求来临, 那么它会一直阻塞住线程, 我们可以给它设置一个预期的timeout来解决此问题.
```python
app.serve_once(2000)
```
如果您使用上面的例子来监听, 程序会尝试等待两秒钟, 如果两秒钟内没有新的 `HTTP` 请求进入, 该函数会自动返回.

如果您希望此函数不阻塞, 您可以启用 `DYNAMIC-MODE`
```python
from urouter import uRouter, DYNAMIC_MODE
app = uRouter(mode=DYNAMIC_MODE)
```
在 `DYNAMIC-MODE` 下工作时, 程序不会阻塞等待新的请求, 如果没有新的请求已经就绪, 会立即返回. 这可以使 `WEB` 服务对您其他业务的影响最小化.

### 只检查, 不处理
```python
from urouter import uRouter, DYNAMIC_MODE
app = uRouter(mode=DYNAMIC_MODE)

waiting_quantity = app.check()
print(waiting_quantity, " task is waiting.")
```
在我们的程序内部, 有一个队列用来存放正在等待且还未处理的 `HTTP` 任务, 当您调用 `app.check()` 时, 它会去检查这个队列中任务的数量, 如果队列中没有任务, 它会阻塞(在`NORMAL-MODE`下)并等待新连接或新请求的进入才返回.  
`app.check()` 同样支持 `timeout` 参数, 用于指定等待新请求的时间. (单位毫秒)

### keep-alive
您可以使用 `keep-alive` 来支持与浏览器的长连接, 这可以减少频繁建立 `TCP` 请求所带来的资源浪费
```python
from urouter import uRouter, DYNAMIC_MODE
app = uRouter(keep_alive=True)
```
使用 `keep-alive` 功能可以有效提升服务效率和性能, 但也许会降低服务的稳定性. 如果您的`WEB`服务是不需要被浏览器访问的而是作为`API`接口程序, 开启此选项可能会没有作用.

**注意:** 此功能未经过严禁的测试, 可能会存在些许问题.

### context对象
与 `flask` 类似, 此框架也有 `request`, `response`, `session` 对象.

但与flask有些许不同的是, 此框架的 `context` 对象不是直接被导入的, 而是集成在 `webapp` 对象之中.
```python
@app.route("/")
def index():
    request = app.request
    return "<p>A HTTP request from: %s:%s </br> url:%s </p>" % (
        request.host, 
        request.port, 
        request.url
        )
```
这是一个使用 `request` 的例子

![request](/staitcs/request.png)

关于其他 `context` 对象的详细内容, 详见:
[reference/context](../reference/context)

### 流式传输
某些时候, 我们要发送的数据内容大小是无法被猜测或者无穷无尽的, 例如从摄像头采集的视频数据. 因此我们可以使用流式传输方式来传输数据.
```python
@app.route("/video")
def index():
    response = app.response
    response.mime_type = "video/mp4"
    # 告诉浏览器返回的数据是啥玩意.

    response.make_stream()

    while True:
        # get video data
        # ...
        if data:
            response.send_data(data)
        else:
            break
    
    response.finish_stream()
```

流式传输的核心主要有三个函数:
- `make_stream()`  
  开始流式传输.

- `send_data()`  
  发送流式数据.

- `finish_stream()`  
  结束流式传输.

### 路由响应函数
被 `app.route()` 装饰的函数我们将其称之为 `响应函数`, 应该符合以下标准: 
- 不能在处理过程中只能调用一次 `response` 对象的响应方法(`response.make_stream()`, `response.make_response()`), `make_stream()` 与 `make_response()` 两者不能被重复或同时调用.

- 如果在处理过程中没有调用 `response` 的响应方法, 则应该有一个确切的返回值(哪怕是空文本)  
  返回值支持以下类型: `tuple`, `dict`, `list`, `str`, `bytes`, `bytearray`, `io.BytesIO`, 详见[这里](../reference/context/response#make_response)

- 如果您使用了路由变量, 请确保有相同数量的参数且参数名称与定义的路由变量名称相对应

- 最好不要在执行过程中抛出错误.

- `mime-type` 和 `statu-code` 的设置应该在调用 `response` 的响应方法之前.

- 如果在执行的过程中未调用过响应的方法, 我们会将您的返回值作为数据对客户端进行响应.

- 如果您开启了 `stream` 模式而且未 `finish`, 我们会自动尝试 `finish`, 但是我们任然推荐您最好手动进行 `finish`.

### 获取表单和访问参数
有时候在访问某个地址的时候, 通常会带有一些参数
例如: `https://www.baidu.com/?s=xxxx` 其中 `?` 后面的内容就是访问的参数了, 我们可以通过 `request.args` 来获取访问的参数.

同时, 如果 `POST` 请求提交了表单数据, 我们可以使用 `request.form` 来获取相应的表单信息

`args` 和 `form` 都是一个 `dict`, 且只在用户需要的时候才从客户端读取数据并解析.

例子:
```python
@app.route ('/')
def index ():
    print(app.request.args)
    return app.request.args
```
![args](/staitcs/args.png)

![args](/staitcs/args1.png)

