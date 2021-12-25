# 问题抛出

前面我们已经了解了查询参数，但是实际开发中我们可能需要限定参数的类型，长度等其他属性。这个时候我们就需要对查询参数进行校验。

> 类型我们可以通过显示类型进行限制，但是长度等其他属性我们需要借助 `FastApi` 的 `Query` 对象来实现。

# 实例

### 实现方式

参数限定，我们需要借助 `fastapi` 中的 `Query` 对象来实现。

导入 Query

```python
from fastapi import Query
```

### 限定参数最大长度

使用参数：max_length

```python
@app.get('/len')
async def get_len(q:str=Query(...,max_length=2)):
    res = {'name':'phyger'}
    if q:
        res.update({'q':q})
    return res
```

![max_length](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/35fcc24fae5540a5901ddc743be9189b~tplv-k3u1fbpfcp-zoom-1.image "max_length")

从以上测试可知：

查询参数 `q` 为可选参数、最大长度为 `2`，超过最大长度 `fastapi` 将会报错。

### 限定参数最小长度

使用参数：`min_length`

```python
@app.get('/len')
async def get_len(q:Optional[str]=Query(None,min_length=2,max_length=5)):
    res = {'name':'phyger'}
    if q:
        res.update({'q':q})
    return res
```

![min_length](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/39e8726fee534c4db20b7f7375430148~tplv-k3u1fbpfcp-zoom-1.image "min_length")

> `Query` 类型的查询参数必须制定默认值。形如：`Query(...,min_length=2,max_length=5)`

**除了限定长度，我们还可以通过 `regex` 属性来通过正则表达式限定参数格式**

### 默认查询参数

**可选查询参数：** 通常我们使用 `Optional` 类型结合 `Query` 类型的默认值来实现；

```python
async def get_len(q:Optional[str]=Query(None,min_length=2,max_length=5)):
    pass
```

```python
async def get_len(q:str=Query(None,min_length=2,max_length=5)):
    pass
```

以上两种方式都可。

**必选查询参数：** 通常我们指定 `Query` 的默认值为`...`来实现。

```python
async def get_len(q:str=Query(...,min_length=2,max_length=5)):
    pass
```

> 当 `Optional` 类型和`...`默认参数同时存在的时候，查询参数依然为必选。

### 多个查询参数

当我们需要设计多个查询参数的时候，我们可以这样写。

```python
from typing import List
from fastapi import Query
@app.get('/len')
async def get_len(q:Optional[List[str]]=Query(...,min_length=2,max_length=5)):
    res = {'name':'phyger'}
    if q:
        res.update({'q':q})
    return res
```

![多查询参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cc94dbe57dea4c3f9d3edce04b6f4f11~tplv-k3u1fbpfcp-zoom-1.image "多查询参数")

直接使用 `list` 也可以

```python
from typing import List
from fastapi import Query
@app.get('/len')
async def get_len(q:Optional[list]=Query(...)):
    res = {'name':'phyger'}
    if q:
        res.update({'q':q})
    return res
```

![多查询参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70f8b0c00a454048ba03fbd9f148a5da~tplv-k3u1fbpfcp-zoom-1.image "多查询参数")

# 更多的元数据

### title

`Query` 的 `title` 属性可以让我们指定参数的标题，用做提示。

```python
from fastapi import Query
@app.get('/title/')
async def title(name:str = Query(...,title="test title",max_length=5,min_length=2)):
    return {'result':name}
```

### description

```python
from fastapi import Query
@app.get('/title/')
async def title(name:str = Query(...,title="test title",max_length=5,min_length=2,description='test desc')):
    return {'result':name}
```

### doc 中的效果

![doc 中的效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2a59d605df248fe8607c8fab27b3b78~tplv-k3u1fbpfcp-zoom-1.image "doc 中的效果")

### 别名

实际应用中，有时候查询参数可能形如 `x-name`，这在 `python` 中不是合法的变量，为了解决类似问题，`FastApi` 为我们提供了别名 `alias` 功能。

```python
from fastapi import Query
@app.get('/title/')
async def title(name:str = Query(...,max_length=5,min_length=2,alias='x-name')):
    return {'result':name}
```

![别名](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dfcc172f17ac4cd0b35b6ab805b0992a~tplv-k3u1fbpfcp-zoom-1.image "别名")

### 参数弃用

当我们计划在后续版本中废弃参数时，我们可以通过 `deprecated` 属性来提示用户。

```python
from fastapi import Query
@app.get('/title/')
async def title(name:str = Query(...,max_length=5,min_length=2,alias='x-name',deprecated=True)):
    return {'result':name}
```

### 接口文档中的效果

![接口文档中的效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/44741c4686b84609ac3609aaf47ec7b5~tplv-k3u1fbpfcp-zoom-1.image "接口文档中的效果")

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
