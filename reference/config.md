# CONFIG
此页向你展示如何修改和设置 `urouter`

## functions:

### buff_size():
当 `webapp` 实例被创建时, 会有一块固定的内存区域被分配用于处理 HTTP 请求, 这样可以避免重复申请内存导致的不稳定以及算力浪费, 一般的, 这个缓冲区的大小越大, 响应 `HTTP` 请求的速度越快. 如果您开发板内存足够, 推荐设置为4096(默认1024), 当这个缓冲区过大时, 可能会造成负面影响.

此函数可以用于获取或者设置缓冲区大小, 当传入 `value` 参数时, 会被视为设置缓冲区大小, 否则将会返回该 `webapp` 的缓冲区大小.

params:
- app: uRouter obj.  
  欲被设置缓冲区大小的 `webapp` 实例

- value: int = None  
  欲设置的缓冲区大小

### Attributes:
- charset = 'utf-8'  
  此属性用于设置 `webapp` 在尝试编码和解码时使用的字符集编码.

- request_timeout = 7  
  此属性用于设置 `socket` 对象在尝试获取浏览器数据时超时的时间, 如果超时, 将会被判定为请求失败并且关闭该请求. 提高这个数值可以提升路由响应的稳定性, 但是在网络环境差的情况下浏览器请求可能会占用大量的开发板处理时间.

- max_connections = 5  
  如果您使用了 `keep-alive` 或者 `websocket` 这种需要长连接的选项, 此属性用于指定可以同时存在的 `socket` 连接数量, 数量越大, 可以同时被读取的请求会更多, 但是响应的资源占用也会更大. 连接数量的上限取决于开发板, 例如 `esp32` 的最大限制是 `9`. 如果在运行过程中连接数量超过了限制, 本框架会打印出一个警告并且自动修改为最大安全数值.

- debug = False  
  此属性用于控制是否开启 `debug` 模式, 在此模式下, 一些错误将会被抛出用于帮助开发者定位错误的所在位置, 如若关闭, `urouter` 会自动尝试使用最稳定的方式来运行.

- logger_level = INFO  
  设置打印日志的级别, 可以设置更高的级别来提升处理速度或者是降低级别来帮助排查问题
  可选:
  - DEBUG    : 10
  - INFO     : 20
  - WARN     : 30
  - ERROR    : 40
  - CRITICAL : 50

    本框架使用我自己开发的 `micropython-ulogger` 模块进行日志记录, 关于更多信息, 详见[GitHub](https://github.com/Li-Lian1069/micropython-ulogger)

此外, 我们还开放了获取 `logger` 对象的接口:  
```py
from urouter import get_logger
logger = get_logger("main.app")
logger.info('hello ', 'world')

>>> hello world
```
  