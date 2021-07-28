# 路由变量

在很多情况下, 我们定义一个死的路由路径并不能很好地完成我们想要的目的, 所以路由变量应运而生.

- 定义

要想定义一个路由变量, 必须遵循其书写规则:

```
<type:var_name>
```

type可选:

- string
- float (包括 int )
- int
- path
- custom (用户自定义 regex 表达式规则)

## 使用
​以下代码演示如何使用路由变量:

```python
@app.route ('/goods/<string:goods_name>/<float:price>/<int:amount>/')
def index (goods_name:str, price:float, amount:int):
    total = price * amount
    return "<h1>%s total %s$</h1>" % (goods_name, total)
```
**注意:** 当您定义路由变量时, 处理函数中必须有名称与其对应的参数.

​当你访问 `/goods/apple/5/20` 时, 浏览器将会显示

![vars](/staitcs/rule_vars.png)

路由变量支持多种类型: 
- string类型  
定义一个字符串  
例如: `apple`, `pair` ...  
​不包括 `apple/pair`

- int类型  
​定义一个整数  
​例如: `100` , `50` , `125`  
​不包括: `100.0` , `99.5`

- float 类型  
​定义一个浮点数, 包括整数  
​例如: `100` , `100.23456`

- path 类型  
​定义一个路径, **在 `path` 类型之后的一切变量都可能失效.**

- custom类型  
​custom类型是一个特殊的变量, 命名规则为 `<custom=regex:var_name>`  
​该功能还没有经过充分的测试, 当你尝试使用 `custom` 类型时, 可能会导致程序发生奇怪的错误.