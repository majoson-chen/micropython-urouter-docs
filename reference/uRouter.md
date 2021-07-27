# class uRouter
实例化这个类以获得一个 `webapp` 对象.

## functions:

### \_\_init\_\_()
初始化 `uRouter`

params:
- host: str = "0.0.0.0"  
  指定欲监听的IP地址, 如果指定 '127.0.0.1', 则外部设备将无法访问Web服务.

- port: int = 80  
  指定监听的端口, 默认为 80

- root_path: str = "/www"  
  指定web静态文件的根目录

- mode: int = NORMAL_MODE  
  指定工作模式, 可选 `NROMAL-MODE` 或者 `DYNAMIC-MODE`

- keep_alive=False  
  指定是否启用 `keep-live` 功能, 启用该功能可以减少HTTP请求开销, 但是可能在高并发请求的情况下发生异常.  
  **对于API访问, 此功能可能没有效果.**

- backlog: int = 5
  指定socket对象的 `backlog` 数量, 上限将会取决于你的开发板, 修改此选项以修改socket可以积压的请求数量

- sock_family: int = socket.AF_INET  
  指定欲使用的 `socket_family`, 详见 `socket`. 该功能可以用启用IPV6监听(micropython似乎还未支持).

### route()
此函数用来装饰一个监听路由.  
<font color="#dd0000">注意: 这是一个装饰器
</font>

params:
- rule: str  
  设置路由规则文本, 可以使用路由变量, [详见](../quick-start/rule-var.html)

- methods: iter = (GET, )  
  设置欲监听的访问方式, 可选 `GET`, `POST` 等, 详见[常量](./consts.html)

- weight: int = 0  
  设置响应权重, 当多个请求同时发生的时候, 权重大的会优先被处理.

例子:
```python
from urouter import uRouter, GET, POST

app = uRouter()

@app.route("/say/hi", (GET, POST), 10000)
def say_hi():
    return "<p>Hi</p>"

@app.route("/say/hello", (GET, POST), 500)
def say_hi():
    return "<p>hello</p>"
```
在上面的例子中, 我们定义了两个路由: `say-hi` 和 `say-hello`, 但是如果浏览器同时访问这两者, `say-hi` 将会被优先响应.

### websocket():
同 `route()` 一样, 这也是一个装饰器, 用于定义一个 websock 服务处理函数.  
<font color="#dd0000">
注意: 此功能尚未实现
</font>

### check():
此函数用于检查目前待请求的连接数量, 如果已经有挤压的请求, 将不会重新检查.

params:
- timeout: int = -1
  此参数用于指定等待连接和请求的时长, 如果在此时间内产生了新的请求, 将会立即返回, 如果设置为 `-1`, 将会尝试一直等待.
  **在DYNAMIC-MODE**的情况下, 此参数不起作用, 本函数在调用只会将会立即返回(理论上)

return: int, 待处理请求的数量

**注意**:  
本函数在检查的同时也会将相应的 `HTTP` 请求添加到队列中, 此过程中程序将会尝试读取 `HTTP` 的首行内容并尝试解析, 因此如果在网络环境差或者请求过多的情况下, 可能会造成一定时间的阻塞.

### serve_once():
此函数用于处理一次请求, 若无请求, 则会自动调用 `check()` 函数.

param:
- timeout: int = -1  
  与 `check()` 相同, 此参数用于指定在调用 `check()` 时将会使用的超时时间.

### serve_forever():
本函数将一直尝试监听并处理请求, 直到调用 `close` 或者 `stop` 时才会停止(此方法会一直阻塞线程.)

### stop():
停止 `serve_forever` 的阻塞.

### close():
同 `stop`, 同时关闭服务, 若想再次启用 `web` 服务, 可以重新初始化此实例. 在 `close` 过后, 可以使用 `del` 关键字来彻删除本实例以释放内存.

## Attributes:

### request:
用于客户端的请求信息, 详见[request](./context/resquest.hmtl)

### response:
用于相应来自客户端的请求, 详见[response](./context/response.html)

### session:
用于获取客户端与服务端直接的回话信息, 详见[session](./context/session.html)