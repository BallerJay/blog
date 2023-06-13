---
title: "Node.js基础(3) - Express"
date: 2023-06-13
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

### 一、初识 express

#### 1. 基本概念

官方给出的概念：Express 是基于 Node.js 平台，快速、开放、极简的 Web 开发框架。

通俗的理解： Express 的作用和 Node.js 内置的 http 模块类似，是专门用来创建 Web 服务器的。

Express 的本质：就是一个 npm 上的第三方包，提供了快速创建 Web 服务器的快捷方法。

Express 的中文官网：[中文官网](https://www.expressjs.com.cn/)

#### 2. 进一步理解 Express

Q：不使用 Express 能否创建 Web 服务器

A：能，使用 Node.js 提供的原生 http 模块即可。

Q：既生瑜何生亮，有了 http 内置模块，为什么还有用 Express?

A：http 内置模块用起来很复杂，开发效率低;Express 是基于内置的 http 模块进一步封装出来的，能够极大的提高开发效率.

Q：http 内置模块与 Express 是什么关系?

A：类似于浏览器中 Web API 和 jQuery 的关系。后者是基于前者进一步封装出来的。

Q：Express 能做什么

A：对于前端程序员来说，最常见的两种服务器，分别是：

- Web 网站服务器：专门对外提供 Web 网页资源的服务器
- API 接口服务器：专门对外提供 API 接口的服务器

使用 Express，我们可以方便、快速的创建 Web 网站服务器或 API 接口的服务器。

### 二、express 基本使用

#### 1. 安装

```
npm i express
```

#### 2. 创建服务器

```javascript
// 引入
const express = require("express");
// 创建
const server = express();
// 启动
server.listen(80, () => {
  console.log("erpress server is working");
});
```

#### 3. 监听 GET 请求

通过 express 实例的 get() 方法，可以监听客户端的 GET 请求，具体的语法格式如下:

```javascript
// 参数1: 客户端请求的 URL 地址
// 参数2: 请求对应的处理函数
// req: 请求对象(包含了与请求相关的属性与方法)
// res: 响应对象 (包含了与响应相关的属性与方法)
const express = require("express");
const server = express();
server.get('请求URL'，function(req，res){ /*处理的数*/})
```

#### 4. 监听 POST 请求

通过 express 实例的 post() 方法，可以监听客户端的 POST 请求，具体的语法格式如下:

```javascript
// 参数1: 客户端请求的 URL 地址
// 参数2: 请求对应的处理函数
// req: 请求对象(包含了与请求相关的属性与方法)
// res: 响应对象 (包含了与响应相关的属性与方法)
const express = require("express");
const server = express();
server.post('请求URL'，function(req，res){ /*处理的数*/})
```

#### 5. 把响应内容给客户端

通过 res.send() 方法,可以把处理好的内容，发送给客户端:

```javascript
const express = require("express");
const server = express();
server.get('/user", (req, res) => {
   // 向客户端发送 JSON 对象
   res.send({ name: 'zs', age: 20, gender: "男' })
})
server.post('/user', (req, res) => {
  // 向客户端发送文本内容
  res.send({ name: 'zs', age: 20, gender: "男' })
})
```

#### 6. 托管静态资源

express 提供了一个非常好的函数，叫做 express.static()，通过它，我们可以非常方便地创建一个静态资源服务器。

例如，通过如下代码就可以将 public 目录下的图片、CSS 文件、JS 文件对外开放访问了：

```javascript
const express = require("express");
const server = express();
const path = require("path");
// server.use(express.static(path.join(__dirname, "/public")));
server.use(express.static("./public"));
```

现在，你就可以访问 public 目录中的所有文件了:

[http://localhost:3000/images/bg.jpg](http://localhost:3000/images/bg.jpg)

**注意：Express 在指定的静态目录中查找文件，并对外提供资源的访问路径，因此，存放静态文件目录名（public）不会出现在 URL 中。**

如果我们想托管多个静态资源目录，只需要多次调用 express.static() 方法就可以了。

```
server.use(express.static('./public' ))
server.use(express.static('./files'))
```

访问静态资源文件时，express.static() 函数会根据目录的添加顺序查找所需的文件。

例如，在上面代码中，设置了两个静态资源文件夹 public 和 files，如果两个文件夹内都有 index.html 这个文件，当我们访问 127.0.0.1/index.html，会先访问 public 文件夹中的 index.html 文件。

**挂载路径前缀**

如果希望在托管的静态资源访问路径之前，挂载路径前缀，则需要使用如下的方式：

```
express.use('/public', express .static("./public'))
```

现在，就可以通过带有 /public 前缀地址来访问 public 目录中的文件了。

[http://localhost:3000/public/images/kitten.jpg](http://localhost:3000/public/images/kitten.jpg)
