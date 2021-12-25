# 何为路径参数

顾名思义，路径参数就是 `url` 中带的请求参数，比如根据 `id` 查询信息。

# 例:自动大写首字母

### main.py

```python
...
@app.get('/format/{name}')
async def fmt(name):
    new_name = name.title()
    return {'result':new_name}
...
```

### 启动服务后测试

访问：`http://127.0.0.1:8765/format/phyger`

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/23517171651549b7b1b88ca4c5926a50~tplv-k3u1fbpfcp-zoom-1.image "效果")

可以看到，功能已经实现！

### 存在的问题

访问：`http://127.0.0.1:8765/format/123`

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02562c86709141aeaee563059cd839d4~tplv-k3u1fbpfcp-zoom-1.image "效果")

虽然有结果返回，但是我们期望后台能够对请求参数的格式进行校验。请继续往下看。

> 默认的路径参数类型都是 `str`，所以 `123` 已经被转换成了 `str` 类型。

# 带格式的路径参数

当我们在处理数字的时候，我们不希望后台对非法类型的数据进行处理。

### 老代码

```python
...
@app.get('/format1/{num}')
async def fmt1(num):
    print(type(num))
    new_num = num+1
    return {'result':new_num}
...
```

### 路径参数为 123 的结果

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/04c79791c4dd42be8fe7134be7260dcc~tplv-k3u1fbpfcp-zoom-1.image "效果")

`Internal Server Error`，因为后台将 `123` 当做 `str` 处理了，所以在进行计算的时候出错了。

### 新代码

```python
@app.get('/format1/{num}')
async def fmt1(num:int):
    print(type(num))
    new_num = num+1
    return {'result':new_num}
```

### 路径参数为 123 的结果

![结果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32231118fc1a4ae59d7a71d34d78f557~tplv-k3u1fbpfcp-zoom-1.image "结果")

成功！

# 路径参数和查询参数的区别

路径参数是归属 `url` 的部分，而查询参数是在 `url` 后面使用`?`进行连接的参数。通常我们会将某个模块数据结构主键部分使用路径参数表达，而对于这个对象的附加信息我们通常使用查询参数表达。

顾名思义，路径参数指导我们拿到对象概要信息，而查询参数能够帮助我们查询到对象的更多信息。

关于更多查询参数的内容，我们下节内容继续分享。

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
