# 常用基础嵌套

使用 `FastAPI`，你可以定义、校验、记录文档并使用任意深度嵌套的模型（归功于 `Pydantic`）。

### 基础模型

```python
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: list = []
```

### 定义类型的字类型

假设我们需要制定一个字段 `tags:list` 的元素都为 `str` 类型，怎么做呢？

答案是：使用 `typing` 中的 `List` 类。（指定其他字类型亦是如此）

```python
from typing import List

tags: List[str]
```

如上，就定义了一个元素类型为 str 的列表。

# 嵌套模型

为了方便组合拆解，我们可以将某些模型对象单独声明，然后在其他模型中引用。

```python
class Image(BaseModel):
    url: str
    name: str


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = []
    image: Optional[Image] = None
```

如上，我们定义了一个 `Image` 模型，在 `Item` 模型中直接使用了。

在实际请求时，我们按照以下格式发送请求体即可。

```python
{
    "name": "Foo",
    "description": "The pretender",
    "price": 42.0,
    "tax": 3.2,
    "tags": ["rock", "metal", "bar"],
    "image": {
        "url": "http://example.com/baz.jpg",
        "name": "The Foo live"
    }
}
```

# 特殊类型

`HttpUrl`：由 `pydantic` 提供。

```python
class Image(BaseModel):
    url: HttpUrl
    name: str
```

> 以上的使用方式，`FastApi` 支持自动联想补全，数据转换，数据校验，文档自动生成等。

# 实践

## 基础类型

```python
class Md1(BaseModel):
    name:str
    age:int

@app.post('/model/1')
async def m1(md:Md1):
    return {'msg':'model is ok!'}
```

**执行测试：**

![测试结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e1334f05a674e80868bf2e6a5531ff5~tplv-k3u1fbpfcp-zoom-1.image "测试结果")

> 根据测试结果，我们可以知道：①`FastApi` 的模型检验功能很好用。② 当整数被双引号包裹，`FastApi` 可以根据其限定的类型自动转换。③ 对于数据结构检验有明确的提示。

## 嵌套类型

```python
class Md1(BaseModel):
    name:str
    age:int

class Md2(BaseModel):
    city_info:str
    people_info:Md1

@app.post('/model/1')
async def m1(md:Md1):
    return {'msg':'model is ok!'}

@app.put('/model/2')
async def m2(md:Md2):
    return {'msg':'relation model cheking pass!'}
```

**执行测试：**

![测试结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4e581a2c410b4bf38303d3d67079a5df~tplv-k3u1fbpfcp-zoom-1.image "测试结果")

> 根据以上测试结果，我们可以清晰的了解到 `FastApi` 借助 `pydantic` 实现的模型嵌套非常优雅。模型嵌套结合动态请求体，我们可以很方便的应对业务变化导致的数据模型变化。

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
