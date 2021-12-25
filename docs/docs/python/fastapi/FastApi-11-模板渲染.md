# 什么是模板

简单理解，模板就是 `web` 后端向前端发送的 `html` 模型。

在前面的学习中，我们已经知道了关于 `FastApi` 的请求和参数的使用方法。但是我们在实际的 `web` 开发中，都会以网页的形式展现。那么在 `FastApi` 中我们也可以借助第三方的模板引擎来实现后端对网页模板的渲染。

> 注：大家都知道目前 `web` 项目大都采用比较主流的前后端分离模式。但是为了学习方便，我们先采用后端渲染的方式。

# FastApi 的模板详解

## 关于引擎

`FastApi` 常用 `Jinja2` 模板引擎实现页面渲染。

虽然 `FastApi` 的 `starlette` 已经封装了 `Jinja2` 相关的类对象，但是你在使用中，仍然需要安装 `jinja2` 引擎。

**这个地方很奇葩**

# 普通渲染

## 项目结构

![](https://gitee.com/phygerr/picture/raw/master/2021-2-3/1612356627423-image.png)

## 使用引擎

```python
from fastapi import FastAPI

# 导入Request上下文对象，用来在前后台之间传递参数
from starlette.requests import Request

# 导入jinja2模板引擎对象，用于后续使用
from starlette.templating import Jinja2Templates

app=FastAPI()

# 实例化一个模板引擎对象，指定模板所在路径
templates=Jinja2Templates(directory='templates')

@app.get('/index/{info}')

# 在视图函数中传入request对象，用于在模板对象中传递上下文（同时接收路径参数info，将其传递到上下文中）
async def index(request:Request,info:str):
    # 返回一个模板对象，同时使用上下文中的数据对模板进行渲染
    return templates.TemplateResponse(name='index.html',context={'request':request,'info':info})

if __name__ == '__main__':
    import uvicorn
    uvicorn.run(app='main:app',host='127.0.0.1',port=8765,reload=True)
```

## index.html 内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FastApi</title>
</head>
<body>
    <h1>Index</h1>
    <h1>INFO:{{ info }}</h1>
</body>
</html>
```

## 浏览器中的效果

`http://127.0.0.1:8765/index/phyger`

![](https://gitee.com/phygerr/picture/raw/master/2021-2-3/1612356793798-image.png)

如上，我们简单的展示了如何使用 `FastApi` 进行模板的渲染。

# 循环渲染

## 视图代码

我们将循环渲染的视图放到了 `FastApi` 的 `Router`（路由）中。

```python
@router.get('/indexs')

# 在视图函数中传入request对象，用于在模板对象中传递上下文（同时接收路径参数info，将其传递到上下文中）
async def index(request:Request):
    # 定义一个列表
    info = ["phyger","python","go"]
    # 返回一个模板对象，同时使用上下文中的数据对模板进行渲染
    return templates.TemplateResponse(name='index.html',context={'request':request,'info':info})
```

## 页面代码

```html
<body>
    <h1>Info</h1>
    <b>| 后面的reverse是用反转顺序的。</b>
    <ol>
        {% for msg in info|reverse  %}
            <li>{{ msg }}</li>
        {% else %}
            <li>沒有任何值</li>
        {% endfor %}
    </ol>
    <h1>{{ info }}</h1>
</body>
```

## 页面效果

![](https://gitee.com/phygerr/picture/raw/master/2021-8-11/1628662271653-image.png)

> 由于我们在 `jinja` 模板中对循环结果进行了反转，所以列表的顺序和原始的列表数据是相反的。

> 感谢您的阅读，别忘了关注，点赞，评论，转发四连哟！