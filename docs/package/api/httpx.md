# 前言

相信你和我一样，在日常的工作中，`requests` 库被广泛使用。今天我要为你介绍一款号称下一代全功能的 `HTTP` 客户端：`httpx`。

# 什么是 httpx

`httpx` 是 `Python3` 的全功能客户端，支持同步&异步 `API`，同时支持 `HTTP/1.1` 和 `HTTP/2`。

相比其他 `HTTP` 库，`httpx` 具有更加易用的接口，更加强大的功能，是未来 `Python` 开发者的不二选择。

# 安装和使用

## 安装

```
pip install httpx
```

## get

```python
import httpx

# get obj
res = httpx.get('https://www.baidu.com')
print(res,res.status_code)

# response content,text
print(res.content,res.text)
```

![代码运行结果](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626677776799-image.png "代码运行结果")

> 对于 `content` 和 `text` 的去区别，从图中可以直观的看到，`content` 的类型为 `bytes`，而 `text` 的类型为 `str`。

## post

```python
# post obj

res = httpx.post('http://127.0.0.1:4523/mock/351132/pet',data={'name':'Python测试和开发','status':'Python_Lab'})

print(res.text,type(res.content),res.status_code,res.encoding)
```

![代码运行结果](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626678664347-image.png "代码运行结果")

> 因为我们使用了 `mock server`，所以接口返回的数据可能和我们请求的不一致。

## put

```python
# put pbj

res = httpx.put('http://127.0.0.1:4523/mock/351132/pet',params={'apifoxResponseId':'321249'})
print(res.status_code,res.encoding,res.text)
```

![代码运行结果](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626679259438-image.png "代码运行结果")

## delete

```python
# delete obj

res = httpx.delete('http://127.0.0.1:4523/mock/351132/pet/1')
print(res.status_code,res.encoding,res.text)
```

![代码运行结果](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626679914029-image.png "代码运行结果")

# 高级用法

## 处理 json

> 通常对于 `content` 返回是 `bytes` 或者 `str` 的数据，我们需要单独使用 `json` 模块进行数据转换，但是现在我们可以直接使用 `httpx` 为我们提供的 `json` 方法拿到字典对象，从而方便的进行数据处理。

```python
import httpx

# json
res = httpx.get('https://getman.cn/mock/post')
print(type(res.text),type(res.content),type(res.json()),res.json())
```

![代码运行结果](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626680258284-image.png "代码运行结果")

## 处理二进制

当我们需要下载图片的时候，通常我们是将 `content` 的内容分块读取，然后写入文件。但是 `httpx` 建议我们使用 `pillow` 和 `io` 来处理图片的二进制内容。

```python
import httpx
from PIL import Image
from io import BytesIO

# bytes
res = httpx.get('http://localhost:8765/um/imgs/')
print(res.status_code)
ff = Image.open(BytesIO(res.content))
ff.save('xx.png')
```

![下载的图片](https://gitee.com/phygerr/picture/raw/master/2021-7-19/1626683241801-image.png "代码运行结果")

> `httpx` 支持所有 `requests` 的 `raise_for_status()`等所有方法，还新增了部分特性，例如 `httpx.codes.OK` 代替 `200` 响应码的短语等动能。总之，`httpx` 是一款值得学习的优秀软件。

## 官方文档

[https://www.python-httpx.org/](https://www.python-httpx.org/)

> 以上就是今天的全部内容了，感谢您的阅读，我们下节再会。