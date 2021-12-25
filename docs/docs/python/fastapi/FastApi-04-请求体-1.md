# 何为请求体

顾名思义，请求体就是在请求过程中客户端携带的数据。

请求体不是一定要携带的，而且请求体不建议用 `GET` 请求发送，通常我们会使用 `POST` 请求发送请求体，当然是用 `PUT、DELETE、PATCH` 方式也可以发送请求体。

# 举个栗子

根据请求体做相应的响应，如下返回请求体内容。

> `FastApi` 的请求体需要为 `dict` 格式

### 代码

```python
@app.post('/models/')
async def add_model(model:str):
    return model
```

### 接口测试

正常情况

![正常情况](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0637192805204bb18f4448050f481e75~tplv-k3u1fbpfcp-zoom-1.image "正常情况")

异常情况

![异常情况](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa66b161a6a14528891727aa309c6ac4~tplv-k3u1fbpfcp-zoom-1.image "异常情况")

`FastApi` 会帮助我们进行类型检测，基本格式校验等

# 请求体校验

通常，我们在开发的时候，需要用户根据特定的结构体来发起请求，从而防止攻击和过滤用户。

### FastApi 的数据模型

在 `FastApi` 中，我们可以借助 `pydantic` 的 `BaseModel` 类来实现请求结构体的定义。

### 代码

```python
from pydantic import BaseModel

class Mds(BaseModel):
    name: str
    age: int
    home: str

@app.post('/models/')
async def add_model(model:Mds):
    return model
```

### 接口测试

正常情况

![正常情况](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/838906080765425984e05388717d1827~tplv-k3u1fbpfcp-zoom-1.image "正常情况")

异常情况

![异常情况](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7700ac63ef8e4973b946964604c8ec9c~tplv-k3u1fbpfcp-zoom-1.image "异常情况")

对于错误的，缺失的结构体字段，`FastApi` 都会帮我们检测处理。

### 可选字段

`FastApi` 的可选字段有以下两种场景

1. 字段有默认值时
2. 字段类型为 `Optional` 时

### 代码

```python
from pydantic import BaseModel
from typing import Optional

class Mds(BaseModel):
    name: str
    age: int = 18
    home: str
    height: Optional[str]

@app.post('/models/')
async def add_model(model:Mds):
    return model
```

### 接口测试

只携带必选参数

![只携带必选参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b929762aba9140899fcd1c0534ed1184~tplv-k3u1fbpfcp-zoom-1.image "只携带必选参数")

携带全部参数

![携带全部参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a32a4d2acc614359a96f033045ae45b5~tplv-k3u1fbpfcp-zoom-1.image "携带全部参数")

携带多余参数（多余参数会被忽略）

![携带多余参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe61d4cd56d7426eb96bc620dff0eab8~tplv-k3u1fbpfcp-zoom-1.image "携带多余参数")

缺少必选参数

![缺少必选参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0d21ad31ea0548c78bbdf3d5d1bb29db~tplv-k3u1fbpfcp-zoom-1.image "缺少必选参数")

**结论**

> 请求结构体的字段，可以多，不可缺少必选的。

# 请求体使用

我们在视图函数内部可以直接使用请求体的属性。

### 代码

```python
from pydantic import BaseModel
from typing import Optional

class Mds(BaseModel):
    name: str
    age: int = 18
    home: str
    height: Optional[str]

@app.post('/models/')
async def add_model(Mds:Mds):
    ret = {}
    if Mds.name:
        ret.update({"Name":Mds.name})
    if Mds.height:
        ret.update({"height":Mds.height})
    return ret
```

### 接口测试

![接口测试](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5fe7b5ede0042b7a6750a7e09fe9896~tplv-k3u1fbpfcp-zoom-1.image "接口测试")

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
