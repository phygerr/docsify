# FastApi是什么

顾名思义，FastApi就是一个用于构建高性能api的web框架。

# FastApi的特点

* 快速：比肩NodeJs和Go
* 高效：开发效率提升一倍多
* 少BUG：减少开发错误率
* 智能：自动补全
* 简单：易于学习
* 简短：代码简小精悍
* 健壮：生产级别可用
* 文档：自动生成交互式文档
* 标准化：基于OpenApi

# FastApi的安装

```
pip install fastapi[all]
```

# FastApi之hello world

### main.py
```python
from fastapi import FastAPI

app = FastAPI()

@app.get('/')
async def root():
    return {'message':'hello world!'}
```

### 命令行启动
```
uvicorn.exe main:app --reload
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [18784] using statreload
INFO:     Started server process [23504]
INFO:     Waiting for application startup.
INFO:     Application startup complete.   
```

打开 http://127.0.0.1:8000 查看效果

![helloworld](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b5c86843edb4efe9c843d042c3470b5~tplv-k3u1fbpfcp-zoom-1.image "helloworld")

可以看到，后台程序已经成功返回。

### 主函数启动

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

@app.get('/')
async def root():
    return {'message':'hello world!'}

if __name__ == "__main__":
    uvicorn.run(app='main:app',host='127.0.0.1',port=8765,reload=True,debug=True)
```
启动：
```
python main.py
```

# 交互式的API文档

### docs
浏览器访问：127.0.0.1:8765/docs

![docs](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03dd8c5227b94dd3a9b7423055991ad5~tplv-k3u1fbpfcp-zoom-1.image "docs")

展开看下接口详细信息

![接口详细信息](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/321f5ee55ce8453697c5889183600e43~tplv-k3u1fbpfcp-zoom-1.image "接口详细信息")

点击右侧的Try it

![Try it](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/664983720beb45a4877ad3af19bda1f9~tplv-k3u1fbpfcp-zoom-1.image "Try it")

即可实现接口调试！

完美！

### redoc
浏览器访问：127.0.0.1:8765/redoc

![redoc](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2bfa55ddcbbd44d2ac97710ac0463fec~tplv-k3u1fbpfcp-zoom-1.image "redoc")

# FastApi和Flask的区别

经常看到有人把 FastAPI 和 Flask 放到一起比较，但是却没有意识到这完全是两种东西——前者是基于 Web 框架 Starlette 添加了 Web API 功能支持的（框架之上的）框架，而后者是和 Starlette 同类的通用 Web 框架，所以他两本就不是相同的东西，所以还是不要强行比较，选择适合自己的才是正确的。

至于说FastApi使用了asyncio而使得它的性能提升很大，在我看来没有网上介绍的那么夸张。因为在gevent的加持下，其他web框架也可以做到很高的并发，况且一般的服务都是会借助中间件和集群来实现高并发的，所以对于FastApi的高性能大家还是理性看待。感兴趣的同学可以去测试看看实际的结果。

本系列主要是介绍FastApi这个新的web框架，让大家能够对它有所了解，从而合理使用。

至此，FastApi的简单介绍结束。

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！
