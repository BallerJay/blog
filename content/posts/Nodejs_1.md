---
title: "Node.js基础(1) - 内置模块"
date: 2023-06-11
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ["Node.js"]
categories: ["Node"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
image: "<image path/url>" # image path/url
alt: "<alt text>" # alt text
caption: "<text>" # display caption under cover
relative: false # when using page bundles set this to true
hidden: true # only hide on current single page
---

### 一、Node.js

Node.js 是基于 Chrome V8 引擎的 Javascript 运行环境。可以快速构建网络服务及应用，是用 JavaScript 语言构建的服务平台，可用于后端建立服务器。

### 二、Node.js 内置模块

#### 1. fs 文件系统

Node 官方提供的用来操作文件的模块，它提供了一些一系列方法，满足用户对文件的操作需求。
使用之前需要先导入：`const fs = require('fs')`

1.1 fs.readFile() 读取指定文件的内容

```javascript
fs.readFile(path [, option], callBack)
参数1：必选参数， 字符串， 表示文件路径
参数2：可选参数，表示以什么编码格式读取文件
参数3：必选参数，通过回调函数获得读取结果

# 这里 __dirname 表示的是当前路径

fs.readFile(__dirname + "/1.txt", "utf8", function (err, data) {
  if (err) {
    return console.log("读取文件失败", err.message);
  }
  console.log("读取文件成功", data);
});
```

1.2 fs.writeFile() 向指定文件中写入内容

```javascript
fs.writeFile(file, data [, option], callBack)
 参数1：必选参数，表示文件路径
 参数2：必选参数，写入文件的内容
 参数3：可选参数，表示以什么编码格式写入文件
 参数4：必选参数，通过回调函数获得写入结果

 fs.writeFile(__dirname + '/1.txt', 'aabbccdd', function(err,dataStr){
     if(err) {
         console.log("文件写入失败:",err.message)
         return
     }
     console.log("文件写入成功!")
 })
```

注意：fs.writeFile 只可以创建文件，不可以创建文件夹（路径），重复调用 fs.writeFile 写入同一个文件，新写入的内容会覆盖原内容。

#### 2. path 路径模块

Node.js 官方提供的，用来处理路径的模块，它提供了一些方法和属性，满足用户对路径处理的需求。
使用之前需要先导入：`const path = require('path')`

2.1 path.join() 多个路径拼接成一个完整的路径

```javascript
path.join([...paths])
不限制参数个数，参数为路径的字符串。

const pathStr = path.join("/a", "/b/c", "../", "/d")
console.log(pathStr, "pathStr")  //  \a\b\d   ../ 会抵消上一层路径
```

2.2 path.basename() 获取路径中的最后一部分，一般用来获取路径中的文件名

```javascript
path.basename(path[, ext])
path，第一个参数，必选参数，表示一个路径的字符串。
ext，第二个参数，可选参数，表示文件扩展名。
返回路径的最后一个部分。

const fullPath = "a/b/c/index.html"

const fullName = path.basename(fullPath)       // index.html
const name = path.basename(fullPath, ".html")  // index
```

2.3 path.extname() 获取路径中的扩展部分

```javascript
const extName = path.extname("a/b/c/index.html"); // .html
```

#### 3. http 模块

Node.js 官方提供的、用来创建 web 服务器的模块，通过 http 提供的 http.createServer() 方法就能将一台普通的电脑变成一台 web 服务器。

3.1 客户端与服务器

**在网络节点中，负责消费资源的站点叫做客户端，负责对外提供网络资源的站点叫做服务器**

普通电脑与服务器的区别在于，服务器电脑上安装了 web 服务器软件，例如：IIS、Apache 等。

在 Node.js 中，我们不需要安装 IIS、Apache 等第三方服务器软件，我们可以基于 Node 提供 http 模块，通过简单的代码，实现一个服务器软件，从而提供 web 服务。

3.2 服务器概念

**1. IP 地址**

互联网中每台计算机的唯一标识，具有唯一性，一般使用点分十进制表成(a.b.c.d)的形式，其中 a、b、c、d 都是 0 ～ 255 之间的整数。

**注意** 互联网中的每台 web 服务器都有自己的 IP 地址。在开发期间，我们自己的电脑既是一台服务器，也是一台客户端，为了方便测试，我们可以在浏览器中输入 127.0.0.1，将自己电脑当做服务器进行访问。

**2. 域名和域名服务器**

IP 是一台电脑在互联网中的唯一标识，使用数字来进行区分，不直观，不方便记忆，于是人们发明了另一套字符型的地址方案，即域名地址。

域名类似于 IP 的别名，辅助我们记忆服务器的地址，IP 与域名是一一对应的关系，这份对应关系存在一个叫域名服务器的电脑中（DNS，Domain Name Server），使用者只需要使用对应的域名即可对其相对应的服务器进行访问，转换工作由域名服务器实现。域名服务器就是提供 IP 和域名进行转化服务的服务器。

**注意：** 单纯使用 IP 地址，电脑也可以正常工作，有了域名的加持，能使互联网世界更加方便。在开发测试期间，127.0.0.1 这个 IP 对应的域名是 localhost，他们都代表我们自己的电脑，在效果上没有任何区别。

**3. 端口号**

计算机中的端口号就像我们生活中的门牌号一样，一台计算机可以运行成千上万的 web 服务，每一个 web 服务都对应的唯一端口号，客户端发送来的网络请求，可以通过端口号准确的交给 web 服务处理。

**注意：** 每个端口号不能同时被多个 web 服务所占用。在实际应用中，url 中的 80 端口可以被忽略。

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-11/j9qJIr.png)

3.1 创建基本的 web 服务器

- 导入 http 模块
- 创建 web 服务器实例
- 为服务器实例绑定 request 事件
- 启动服务器

```javascript
// 导入模块
const http = require("http");
// 创建实例
const server = http.createServer();
// 绑定事件 on 方法，为服务器绑定事件
server.on("request", (req, res) => {
  console.log("some on visit our server");
});
// 启动监听 listen(端口号, cb回调)
server.listen(80, () => {
  console.log("http server is runing at 127.0.0.1");
});
```

**req： 请求对象：** 只要服务器接收到了客户端的请求，就会调用 on 方法为服务器绑定 request 事件函数。req 请求对象中保存了客户端请求相关的数据和属性。

```javascript
server.on("request", (req, res) => {
  // req.url 客户端请求的url
  // req.method 客户端请求的 method 类型
  let str = `your request url is ${req.url}, request method is ${req.method}`;
  console.log(str);
});
```

**res 响应对象：** 包含了服务器相关的数据和属性。

```javascript
server.on("request", (req, res) => {
  // req.url 客户端请求的url
  // req.method 客户端请求的 method 类型
  let str = `your request url is ${req.url}, request method is ${req.method}`;
  console.log(str);
  // res.end() 向客户端发送指定的内容， 并结束此次请求过程
  res.end(str);
});
```

**解决中文乱码问题：** 设置响应头 Content-type 值为: text/html;charset=utf-8

```javascript
server.on("request", (req, res) => {
  // req.url 客户端请求的url
  // req.method 客户端请求的 method 类型
  let str = `此次请求 url 为 ${req.url}, 请求方法为 ${req.method}`;
  res.setHeader("Content-type", "text/html;charset=utf-8");
  // res.end() 向客户端发送指定的内容， 并结束此次请求过程
  res.end(str);
});
```

根据不同的 url 得到不同的内容

```javascript
// 创建服务器
const http = require("http");
// 读文件
const fs = require("fs");
// 处理路径
const path = require("path");

const server = http.createServer();
server.on("request", (req, res) => {
  const url = req.url;
  let content = "404 Not Found";
  if (url == "/" || url == "/index") {
    content = "<h1>首页</h1>";
  }
  if (url == "/about") {
    content = "<h1>关于我们</h1>";
  }
  // console.log(content,"content")
  res.setHeader("Content-type", "text/html;charset=utf-8");
  res.end(content);
});
server.listen(80, () => {
  console.log("http server is runing at 127.0.0.1");
});
```
