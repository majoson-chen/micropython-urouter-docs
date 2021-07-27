# consts
此页罗列了 `urouter` 中内置的常量及其说明.

## consts:
- 路由访问方式:
  - GET
  - POST
  - HEAD
  - PUT
  - OPINOS
  - DELETE
  - TRACE
  - CONNECT

- 路由工作模式
  - NORMAL_MODE
  - DYNAMIC_MODE

**用法:**  
以上所有常量均可以直接通过 `urouter` 模块导入,  
例子:

```python
from urouter import uRouter
from urouter import GET, POST, HEAD, PUT, DYNAMIC_MODE

app = uRouter(mode=DYNAMIC_MODE)

@app.route("/rest-api", (GET, POST, HEAD, PUT))
def rest():
    method = app.request.method

    if method == GET:
        ...
    elif method == POST:
        ...
    ...
```