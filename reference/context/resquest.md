# request
`request` 对象用来获取访问客户端的一些信息, 例如用户IP地址, 访问方式, 访问的URL, 访问参数, 表单信息等等.

## functions:
无

## Attributes:
- host: str  
  请求客户端的IP地址

- port: int  
  请求客户端的发起端口

- uri: str  
  该请求的完整访问地址(带参数)  
  例如: `/book?id=1&name=book1`

- url: str
  该请求的访问地址(不带参数)  
  例如: `/book`

- method: int  
  访问方式, 可以是 `GET`, `POST` 等.

- http_version: str  
  客户端提供的访问HTTP版本

- args: dict  
  解析 `uri` 中参数的结果  
  例如: 访问`/book?id=1&name=book1`  
  将会解析为
  ```python
  {
      "id": "1",
      "name": "book1"
  }
  ```

- headers: dict  
  获取客户端发送过来的header字段, 这是一个惰性生成的对象, 如果您在处理的过程中没有使用该属性, 浏览器发送的header数据将不会被读取.  
  使用例子:
  ```python
  @app.route('/')
  def index():
      host = app.request.headers.get("Host")
      print(host)
  ```

- form: dict  
  用于获取POST请求时发送的表单数据, 同 `headers` 一样, `form` 只会在被调用时才从客户端读取并解析.  
  **注意:** 目前只有 `application/x-www-form-urlencoded` 表单格式被支持(即默认的格式), 由于解析 `multipart/form-data` 需要大量的内存资源, 此方式尚未实现, 也可能不会实现.

- client: socket.socket  
  此属性指向客户端的 `socket` 对象, 如果您需要进行一些自定义的操作, 可以使用它来完成您的需求. 在您调用它之前, 客户端发送的 `header` 数据会自动被读取并清除.