# 请求体+路径参数

实际开发中，我们经常会遇到请求体和路径参数同时存在的场景。

### 代码

```python
from pydantic import BaseModel

class Mds(BaseModel):
    name: str
    age: int = 18
    home: str
    height: Optional[str]

@app.post('/models/{name}/')
async def add_model(Mds:Mds,name:str):
    ret = {}
    ret.update({"request_name":name})
    if Mds.name:
        ret.update({"Name":Mds.name})
    if Mds.height:
        ret.update({"height":Mds.height})
    return ret
```

如上，根据路径参数进行不同的响应。

### 接口测试

![接口测试](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/abadb475679d470ebb0bc00a66b59735~tplv-k3u1fbpfcp-zoom-1.image "接口测试")

# 请求体 + 路径参数 + 查询参数

### 代码

```python
from pydantic import BaseModel

class Mds(BaseModel):
    name: str
    age: int = 18
    home: str
    height: Optional[str]

@app.put('/models/{name}')
async def add_model(Mds:Mds,name:str,q: Optional[bool] = False):
    ret = {}
    if q:
        ret.update({"request_name":name})
    if Mds.name:
        ret.update({"Name":Mds.name})
    if Mds.height:
        ret.update({"height":Mds.height})
    return ret
```

### 接口测试

![接口测试](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bfc905895cb4fb8befba7e6de643c8c~tplv-k3u1fbpfcp-zoom-1.image "接口测试")

> 如上，`FastApi` 可以智能的，自动的识别各类型参数，并从正确的位置获取数据。

# 多结构体时的请求体

通常，为了方便结构体定义，我们可能会将不同类型的结构体分别定义在不同的数据模型中。这样，在视图函数的参数中，也将有多个模型参数，那具体怎么使用呢？

### 代码

```python
from pydantic import BaseModel
from typing import Optional

class Mds(BaseModel):
    name: str
    age: int = 18
    home: str
    height: Optional[str]

class Mm(BaseModel):
    title: str
    phone: str = 'huawei'

@app.put('/models/{name}')
async def add_model(Mds:Mds,Mm:Mm,name:str,q: Optional[bool] = False):
    ret = {}
    if q:
        ret.update({"request_name":name})
    if Mds.name:
        ret.update({"Name":Mds.name})
    if Mds.height:
        ret.update({"height":Mds.height})
    if Mm.title:
        ret.update({"Title":Mm.title})
    return ret
```

### 接口测试

![接口测试](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/350e510fefbb40da98d58e69c3adcd52~tplv-k3u1fbpfcp-zoom-1.image "接口测试")

如上，当存在 `Mds:Mds`,`Mm:Mm `两个结构体参数的时候，正确的请求体格式为：`{<模型类名>:<模型>}`

```json
{
	"Mds": {
		"name": "phyger",
		"home": "xian",
		"ok": "ok"
	},
	"Mm": {
		"title": "TSE",
		"phone": "xiaomi"
	}
}
```

对于结构体明确的请求体，我们可以参考本篇内容进行开发实践，那如果在进行代码重构或者业务调整的时候，请求体的数据结构发生了变化，应该如何应对呢？下篇文章我们继续探索！

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
