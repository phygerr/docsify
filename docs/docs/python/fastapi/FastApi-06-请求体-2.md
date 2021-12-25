# 动态请求体

诉求：当我们想要动态的向请求体中增加一个字段，但是不想修改原有的请求体数据模型，怎么办呢？

> 答案是：使用动态请求体 `Body`

# 实例

### 原有数据模型

```python
class Mds(BaseModel):
    name: str
    age: int = 18 
    home: str
    height: Optional[str]

class Mm(BaseModel):
    title: str
    phone: str = 'huawei'
```

### 原有的视图函数

```python
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

### 向请求体增加一个字段

此时，我们需要修改视图函数即可。

```python
from fastapi import Body
@app.put('/models/{name}')
async def add_model(Mds:Mds,Mm:Mm,name:str,q: Optional[bool] = False,weight:str=Body(...)):
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

![接口测试](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8e5ef7a34b68467991b406b9e07a2923~tplv-k3u1fbpfcp-zoom-1.image "接口测试")

> 如上图，我们已经成功实现动态增加请求体字段

# 注意

当请求体为单个模型时，动态字段和请求模型原字段同级。当请求体为多个模型时，动态字段和请求模型类名同级。

### 单个数据模型时的请求体

```python
{
	"Mds": {
		"name": "phyger",
		"home": "xian",
		"ok": "ok",
		"weight": "70kg"
	}
}
```

### 多个数据模型时的请求体

```python
{
	"Mds": {
		"name": "phyger",
		"home": "xian",
		"ok": "ok"
	},
	"Mm": {
		"title": "TSE",
		"phone": "xiaomi"
	},
	"weight": "70kg"
}
```

对于请求体的介绍我们到这里就基本结束了，我们可以以此作为借鉴，在实际开发中多多实践，以充分感知请求体在开发中的作用。

# 附：常见的请求体类型

|序号|类型|解释|
|--|--|--|
|1|multipart/form-data|以表单的形式上传文件|
|2|application/x-www-from-urlencoded|以键值对的数据格式提交，当action为post时，浏览器将form数据封装到http body中|
|3|raw |上传任意格式的文本（text，js，json，html，xml）|
|4|binary|以二进制的形式上传文件|

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
