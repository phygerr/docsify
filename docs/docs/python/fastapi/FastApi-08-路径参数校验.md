# 背景

和查询参数一样，路径参数也需要进行限定。

# Path

通常，我们会直接使用 `name:str='phyger'`的方式来限定路径参数的类型和默认值，但是对于路径参数的高级元数据，我们需要借助 `FastApi` 为我们提供的 `Path` 对象来实现。

> 通常路径参数时必须的，所以即便你指定了默认参数，其依然是必须的。

## 路径参数的 title

```python
from fastapi import Path
@app.get('/path/{name}')
async def pth(name:str=Path(...,title='path name')):
    return {'path_name':name}
```

## 限定路径参数的格式

**数值校验**

```python
大于2：gt=2
小于2：lt=2
大于等于2：ge=2
小于等于2：le=2
```

```python
from fastapi import Path
@app.get('/path/{name}')
async def pth(* ,name:int=Path(...,title='path name',ge=2),q:str):
    return {'path_name':name}
```

![Path](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/33cc0014c1254d1190bfd5e342155322~tplv-k3u1fbpfcp-zoom-1.image "Path")

## 路径参数的顺序问题

当我们的业务模型中有两个uri，一个路径参数是固定值，另一个是路径参数是变量，该如何处理呢？

假设当前业务中的视图有如下两条：

```python
@app.get('/path/{name}')
async def pth(name:str=Path(...,title='path name')):
    return {'path_name':name}   

@app.get('/path/default')
async def f1():
    return {'msg':'I am default path.'}
```

分析以上代码，当我们请求`/path/default`的时候，我们会得到`{'msg':'I am default path.'}`的结果。

测试一下：

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00e8cb60c4734eff9df30ea8784b107c~tplv-k3u1fbpfcp-zoom-1.image "结果")

这是怎么回事？和我预期的不一致。

> 这是因为代码中的两个uri冲突了，对于`/path/default`而言，`/path/{name}`已经拦截了所有符合`/path/xxx`的请求。我们只要将他两的顺序调整为固前变后（固定路径参数在前，变化路径参数在后）即可。

### 修改的代码

```python
@app.get('/path/default')
async def f1():
    return {'msg':'I am default path.'}


@app.get('/path/{name}')
async def pth(name:str=Path(...,title='path name')):
    return {'path_name':name}
```

**再次测试：**

![测试结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5d057c17631c4ed1afc1f097ae03f8b0~tplv-k3u1fbpfcp-zoom-1.image "测试结果")

可以看到，`/path/default`已经成功拦截并返回正确的结果。

**再测试下非固定参数的功能：**

![非固定参数](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b7b55412bad47489f304469b204166d~tplv-k3u1fbpfcp-zoom-1.image "非固定参数")

可以看到，我们的非固定路径参数的其他功能均正常。

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
