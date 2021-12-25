# 何为查询参数

在 `FastApi` 中，声明不属于路径参数的其他函数参数时，它们将被自动解释为"查询字符串"参数。

# 示例代码

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()
goods=['xiaomi','apple','huawei','oppo']

@app.get('/goods/')
async def get_goods(start:int = 0,end:int = 3):
    return goods[start:end]

if __name__ == "__main__":

    uvicorn.run(app='main:app',host='0.0.0.0',reload=True,debug=True)
```

### 接口测试

**带参数**

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f11fed80861b4f64845aa57bc400e8f8~tplv-k3u1fbpfcp-zoom-1.image "结果")

**带部分参数**

因为在后台的视图函数中，两个查询参数都有默认值，所以我们可以对个别参数进行修改后进行请求测试。

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1e96f6b7830a422e9454c2a8f0315550~tplv-k3u1fbpfcp-zoom-1.image "结果")

**使用默认参数**

当我们在发送请求的时候，不携带查询参数，则后台在接收到请求后会使用默认参数进行处理。即使用`start=1，end=3`

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de4564376140446680d456dc763ffe46~tplv-k3u1fbpfcp-zoom-1.image "结果")

# 可选参数

在我们实际开发中，某些参数可能需要，也可能不需要，这个根据用户的需求来定。

### 示例代码

```python
@app.get('/name/{name}/age/{age}')
async def get_userinfo(name:str,age:int,j:Optional[bool]=False):
    ret = {'Name':name}
    if j:
        ret.update({'Age':age})
    return ret
```

> 可选参数 `j` 作为判断条件，默认不返回年龄 `age`，当 `j` 为真时就会返回年龄信息。

### 接口测试

普通请求，默认不会返回年龄信息

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fc2b4aaf74ea4e73b29522fef87c48ac~tplv-k3u1fbpfcp-zoom-1.image "结果")

携带参数 `j` 进行请求，会返回年龄信息

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cff3076c05c4e89af9656a4284e0155~tplv-k3u1fbpfcp-zoom-1.image "结果")

# 参数类型转换

如上，我们在携带参数 `j` 进行请求的时候其值为 `True`，`FastApi` 为我们提供了更为方便的方式，就是查询参数的类型转换，即当其值为任何真值时，都可完成请求处理。

### 示例请求

在 `j=1` 时进行请求操作

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8016bc15e5b54412a17cb4e303b16aa2~tplv-k3u1fbpfcp-zoom-1.image "结果")

在 `j=0` 时进行请求操作

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82d839211cbd4ef79bca5b72bbb90913~tplv-k3u1fbpfcp-zoom-1.image "结果")

# 必选的查询参数

在实际开发中，我们可能需要用户必须携带某个参数才能完成请求。

### 代码

我们只需要给上面的代码加上一个参数 `q` 即可，其余工作 `FastApi` 都会帮我们完成。

```python
@app.get('/name/{name}/age/{age}')
async def get_userinfo(name:str,age:int,q:bool,j:Optional[bool]=False):
    ret = {'Name':name}
    if j:
        ret.update({'Age':age})
    return ret
```

### 接口测试

当不带 `q` 参数进行请求

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b93f1af6e87a406d945c07eec9676aa4~tplv-k3u1fbpfcp-zoom-1.image "结果")

_提示查询参数 `q` 缺失_

当带上 `q` 参数进行请求

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/972b2f78b3f5476db79f288221ac2481~tplv-k3u1fbpfcp-zoom-1.image "结果")

_请求成功_

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
