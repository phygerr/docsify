# 什么是 Example

你可能注意到了，之前的 `docs` 中在 `response` 中的 `Example Value` 中是没有实例的，这个怎么做呢？

答案是使用 `Filed` 对象，也可以在模型中使用 `schema_extra`。

# Requests Example Value

## Filed

```python
from pydantic import BaseModel,Field

class ss(BaseModel):
    name:str = Field(...,example='phyger')
    age:int = Field(...,example=18)

@app.post('/ff/')
async def get_ff(ff:ss):
    res = {'res':ff}
    return res
```

## docs

![docs](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4d1ac0d08a3a445293e1a1667b77de18~tplv-k3u1fbpfcp-zoom-1.image "docs")

可以看到，已经有了实例数据

## schema_extra

```python
from pydantic import BaseModel,Field

class ss(BaseModel):
    name:str
    age:int
    class Config:
        schema_extra = {
            "example":{
                "name":"phyger678",
                "age":20
            }
        }

@app.post('/ff/')
async def get_ff(ff:ss):
    res = {'res':ff}
    return res
```

## docs

![docs](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84c556cd2ea64d608adaf598d7d29a27~tplv-k3u1fbpfcp-zoom-1.image "docs")

可以看到，效果已经展现出来了。

# Response Example Value

搞定了 Requests 的示例信息，现在我们一起来研究下 Reponse 的示例信息。

## 代码

```python
from pydantic import BaseModel
class info(BaseModel):
    name:str
    age:int

@router.post('/test')
def test(info:info):
    msg = {"people_info":info}
    return msg
```

## 效果

你会发现如上的代码在 `docs` 中是没有 `Reponse` 的示例信息的。

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/439d8142ad6e49afb29f567dcab13aa5~tplv-k3u1fbpfcp-zoom-1.image "效果")

## 代码-New

```python
from pydantic import BaseModel
class info(BaseModel):
    name:str
    age:int

class repMd(BaseModel):
    people_info:info

@router.post('/test',response_model=repMd)
def test(info:info):
    msg = {"people_info":info}
    return msg
```

## 效果-New

你会发现，`Reponse` 的示例数据已经搞定。

![效果](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cdbf685273d946d0ba1fb25dd394fbf5~tplv-k3u1fbpfcp-zoom-1.image "效果")

# 这样做有什么用呢？

除了上面我们介绍的可以在 `docs` 中对接口进行信息的完善和请求体的提示以外。`Example` 部分还可以对整个项目的重构，以及系统对接，交付测试等起到意想不到的良好效果。

还是建议大家在实际开发过程中，能够添加 `Requests` 和 `Reponse` 的示例信息，磨刀不误砍柴工，只有在我们项目的初始阶段就定好规则，大家一起遵守，这样才能做出一个好的产品。

以上都是小编在实际工作中深切体会到的，如果前期没有完善的相关注释和文档，将会为后续的维护和测试等造成很大的困扰。返工的成本远比当时盲目赶工时取得的眼前收益大得多！

做产品还是得脚踏实地，稳步前进！

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
