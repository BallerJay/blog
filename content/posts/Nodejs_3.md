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

### 三、express 路由

#### 1. 基本概念

什么是路由，广义来讲，路由就是映射关系。在 Express 中，路由指的是客户端的请求与服务器处理函数之间的映射关系 Express 中的路由由三部分组成，分别是请求的类型、请求的 URL 地址、处理函数，格式如下：

```xml
<!-- METHOD 是指请求的方式 -->
<!-- PATH，HANDLER 指的是请求的路径 HANDLER 是指请求事件处理函数 -->
SERVER.METHOD(PATH，HANDLER)
```

例子：

```xml
<!-- // 匹配 GET 请求，且请求 URL 为 / -->
server.get('/', function (req, res) {
    res.send( "Hello world!" )
})

<!-- // 匹配 POST 请求，且请求 URL 为 / -->
server.post('/', function (req, res) {
    res.send( " A POST request" )
})
```

#### 2. 路由匹配过程

每当一个请求到达服务器之后，需要先经过路由匹配，只有匹配成功之后，才会调用对应的处理函数。在匹配时，会按照路由的顺序进行匹配，如果请求类型和请求的 URL 同时匹配成功，则 Express 会将这次请求，转交给对应的 function 函数进行处理。

#### 3. 简单使用

在 Express 中使用路由最简单的方式，就是把路由挂载到 express()实例上，示例代码如下：

```javascript
const express = require( 'express')
// 创建 web 服务器，命名为 server
const server = express()
// 挂载路由
server.get('/',(req, res) => { res.send( 'Hello world.') })
server.post('/，(req, res) => { res.send( 'Post Request.') })

// 启动 web 服务器
server.listen(80,() =>{
    console.log('server running at http://127.0.0.1');
})
```

#### 4. 模块化路由

为了方便对路由进行模块化的管理，Express 不建议将路由直接挂载到 express() 实例上，而是推荐将路由抽离为单独的模块，将路由抽离为单独模块的步骤如下：

- 创建路由模块对应的 js 文件
- 调用 express.Router() 函数创建路由对象
- 向路由对象上挂载具体的路由
- 使用 module.exports 向外共享路由对象
- 使用 server.use()函数注册路由模块

实例 demo:

```javascript
<!-- // 02-router.js -->
var express = require('express')
var router = express.Router()
// 1.导入 express
// 2.创建路由对象
router.get("/user/list"，function (req，res){ // 3.挂我获取用户列表的路由
    res.send( 'Get user list.')
})
router.post('/user/add’， function (req， res){ // 4. 挂我添加用户的路由
    res .send( "Add new user.' )
})

module.exports = router    // 5.向外导出路由对象
```

使用

```javascript
<!-- // 02.js -->
const express = require("express")
const server = express()
// 1.导入路由模块
const router = require('./02-router.js')
// 2.注册路由模块
<!-- 注意: server.use()函数的作用，就是来注册全局中间件 -->
server.use(router)
server.listen(80，() => {
   console.log("router server is runing")
})
```

为路由模块添加统一的前缀

类似于托管静态资源时，为静态资源统一挂载访问前缀一样，路由模块添加前缀的方式也非常简单：

```javascript
// 1.导入路由模块
const userRouter = require("./02-router.js");

// 2.使用 server.use 注册路由模块，并添加统一的访问前缀 /api
server.use("/api", userRouter);

// 访问路由地址前统一加 /api
// 127.0.0/user/list => 127.0.0/api/user/list
```

### 四、express 中间件

#### 1. 什么是中间件

中间件（Middleware），特指业务流程的中间处理环节。现实生活中的中间件 在处理污水的时候，一般都要经过三个处理环节，从而保证处理过后的废水，达到排放标准。
![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/cV1Wp5.png)

处理污水的这三个中间处理环节，就可以叫做中间件。中间件都需要有输入和输出。

#### 2. Express 中间件的调用流程

当一个请求到达 Express 服务器之后，可以连续调用多个中间件，从而对这次请求进行预处理。
![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/W6C2mM.png)

在这些处理中，上一个中间件的输出会作为下一个中间件的输入依次调用。最终处理完毕之后进行响应。

#### 3. Express 中间件的格式

Express 的中间件，本质上就是一个 function 处理函数，Express 中间件的格式如下：
![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/3YYgCl.png)

注意：中间件函数的形参列表中，必须包含 next 参数。而路由处理函数中只包含 req 和 res。

#### 4. next 函数的作用

next 函数是实现多个中间件连续调用的关键，它表示把流转关系转交给下一个中间件或路由。
![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/8kcgpW.png)

#### 5. Express 中间件的初体验

##### 5.1 定义中间件函数

可以通过如下的方式，定义一个最简单的中间件函数：

```javascript
// 常量 mw 所指向的，就是一个中间件函数
const mw = function (req, res, next) {
  console.log("这是一个最简单的中间件医数");
  // 注意: 在当前中间件的业务处理完毕后，必须调用 next() 函数
  // 表示把流转关系转交给下一个中间件或路由
  next();
};
```

##### 5.2 定义全局生效的中间件函数

```javascript
<!-- 第一种方式 -->
// 定义一个简单的中间件函数 mw
const mw = function(req,res,next){
    console.log("这是一个最简单的中间件函数")
    // 注意: 在当前中间件的业务处理完毕后，必须调用 next() 函数
    // 表示把流转关系转交给下一个中间件或路由
    next();
}

// 将 mw 注册为全局生效的中间件
server.use(mw)

<!-- 第二种方式 -->
server.use(function(req,res,next){
  console.log("这是一个最简单的中间件函数")
  next();
})
```

##### 5.3 中间件的作用

多个中间件之间，共享同一份 req 和 res。基于这样的特性，我们可以在上游的中间件中，统一为 req 或 res 对象添加自定义的属性或方法，供下游的中间件或路由进行使用。
![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/9bvToz.png)

使用示例：

```javascript
const express = require("express")
const server = express()

/**
 * @description 格式化时间
 */
function dateFormat(dateStr){
    const dt = new Date(dateStr);
    let year = dt.getFullYear();
    let month = paddingZero(dt.getMonth() + 1);
    let day = paddingZero(dt.getDate());
    let hours = paddingZero(dt.getHours());
    let minutes = paddingZero(dt.getMinutes());
    let seconds = paddingZero(dt.getSeconds());
    return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}
// 定义补零
function paddingZero(num){
    return num > 9 ? num : "0"+num
}
// 常量 mw 所指向的，就是一个中间件函数
const mw = function(req,res,next){
    req.startTime = dateFormat(new Date());
    console.log("这是一个最简单的中间件函数")
    // 注意: 在当前中间件的业务处理完毕后，必须调用 next() 函数
    // 表示把流转关系转交给下一个中间件或路由
    next();
}

// 将 mw 注册为全局生效的中间件
server.use(mw)

server.get("/",(req,res)=>{
    res.send("home page visit time：" + req.startTime)
})

server.get("/user",(req,res)=>{
    res.send("user page visit time：" + req.startTime)
})

server.listen(80,() => {
   console.log("router server is runing：127.0.0.1")
})

访问：
http://127.0.0.1/user
http://127.0.0.1/
```

##### 5.4 定义多个全局中间件

可以使用 server.use() 连续定义多个全局中间件。客户端请求到达服务器之后，会按照中间件定义的先后顺序依次进行调用，示例代码如下：

```javascript
server.use(function(req，res，next){
  // 第1个全局中间件
  console.log('调用了第1个全局中间件')
  next()
})

server.use(function(req，res，next){
  // 第2个全局中间件
  console.log('调用了第2个全局中间件')
  next()
})
server.get('/user', (req, res) =>{
  //请求这个路由，会依次触发上述两个全局中间件
  res.send('Hpage')
})
```

##### 5.5 局部中间件

不使用 server.use() 定义的中间件，叫做局部生效的中间件，示例代码如下：

```javascript
 // 定义中间件函数 mw1
const mw1 = function(req. res. next) {
    console.log("这是中间件函数")
    next()
}
 // mw1 这个中间件只在“当前路由中生效”，这种用法属于“局部生效的中间件”
server.get("/", mw1, function(req, res) {
    res.send('Home page.')
})
// mw1 这个中间件不会影响下面这个路由
server.get("/user",function(req, res) {
  res.send( 'user page.')
})
```

##### 5.6 定义多个局部中间件

可以在路由中，通过如下两种等价的方式，使用多个局部中间件：

```javascript
// 以下两种写法是”完全等价”的，可根据自己的喜好，选择任意一种方式进行使用
server.get("/", mw1, m2, (req, res) => {
  res.send("Home page.");
});

server.get("/", [mw1, m2], (req, res) => {
  res.send("Home page.");
});
```

##### 5.7 中间件的注意事项

- 一定要在路由之前注册中间件
- 客户端发送过来的请求，可以连续调用多个中间件进行处理
- 执行完中间件的业务代码之后，不要忘记调用 next()函数
- 为了防止代码逻辑混乱，调用 next() 函数后不要在写额外的代码
- 连续调用多个中间件时，多个中间件之间，共享 req 和 res 对象

##### 5.8 中间件的分类

为了方便大家理解和记忆中间件的使用，Express 官方把常见的中间件用法，分成了 5 大类，分别是：

1.应用级别的中间件

通过 server.use() 或 server.get() 或 server.post() 绑定到 server 实例上的中间件，叫做应用级别的中间件，代码示例如下：

```javascript
// 应用级别的中间件《全局中间件》
server.use((req, res, next) => {
  next();
});

// 应用级别的中间件《局部中间件》
server.get("/", ml, (req, res) => {
  res.send("Hom page. ");
});
```

2. 路由级别的中间件

绑定到 express.Router() 实例上的中间件，叫做路由级别的中间件。它的用法和应用级别中间件没有任何区别。只不过，应用级别中间件是绑定到 server 实例上，路由级别中间件绑定到 router 实例上，代码实例如下：

```javascript
var server = express()
var router = express.Router()
// 路由级别的中间件
router.use(function (req, res, next) {
  console.log('Time:'， Date.now())
  next()
})

server.use('/', router)
```

3. 错误级别的中间件

错误级别中间件的作用：专门用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题。

格式：错误级别中间件的 function 处理函数中，必须有 4 个形参，形参顺序从前到后，分别是(err,req,res,next)。

```javascript
server.get("/", function (req, res) {
  // 1.路由
  throw new Error("服务器内部发生了错误! "); // 1.1 抛出一个自定义的错误
  res.send("Home Page .");
});
server.use(function (err, req, res, next) {
  // 2.错误级别的中间件
  console.log("发生了错误: " + err.message); // 2.1 在服务器打印错误消息
  res.send("Error! " + err.message); // 2.2 向客户端响应错误相关的内容
});
```

**注意：错误级别中间件必须注册在所有路由之后**

4. Express 内置中间件

自 Express 4.16.0 版本开始，Express 内置了 3 个常用的中间件，极大的提高了 Express 项目的开发效率和体验:

express.static 快速托管静态资源的内置中间件，例如: HTML 文件、图片、CSS 样式等(无兼容性)
express,json 解 JSON 格式的请求体数据(有兼容性，仅在 4.16.0+ 版本中可用)
express.urlencoded 解析 URL-encoded 格式的请求体数据 (有兼容性，仅在 4.16.0+ 版本中可用)

```javascript
// 配置解析 application/json 格式数据的内置中间件
server.use(express.json());
// 配置解 application/x-www-form-urlencoded 格式数据的内置中间件
server.use(express.urlencoded({ extended: false }));
```

demo1

```javascript
// 通过 express.json() 这个中间件，解析表单中的 JSON 格式的数据
server.use(express.json());

server.post("/json", function (req, res) {
  // 在服务端可以通过 req.body 接收客户端发送的请求体数据
  // 默认情况下 不配置解析json 数据的中间件，则  req.body 为undefined
  console.log(req.body, "JSON 格式数据");
  res.send("ok");
});
```

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/nFepzD.png)

demo2

```javascript
server.use(express.urlencoded({ extended: false }));

server.post("/urlForm", function (req, res) {
  // 在服务器端，可以通过 req.body 来获取 JSON 格式的表单数据和 url-encoded 格式的数据
  console.log("log---->JSON 格式数据, req.body", req.body);
  res.send("ok");
});
```

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-21/YKbu1c.png);

5. 第三方中间件

非 Express 官方内置的，而是由第三方开发出来的中间件，叫做第三方中间件。在项目中，大家可以按需下载并配置第三方中间件，从而提供项目的开发效率。

例如:在 express@4.16.0 之前的版本中，经常使用 body-parser 这个第三方中间件，来解析请求体数据。使用步骤如下:

- 运行 npm install body-parser 安装中间件
- 使用 require() 导入中间件
- 调用 app.use() 注册并使用中间件

```javascript
<!-- // 引入第三方中间件 -->
const parser = require("body-parser")
<!-- 注册第三方中间件 -->
server.use(parser.urlencoded({extended: false}))
<!-- // 使用第三方中间件解析数据 -->
server.post("/urlForm", function(req,res){
    console.log("log---->JSON 格式数据, req.body",req.body )
    res.send("ok")
})
```

注意：Express 内置的 express.urlencoded 中间件，就是基于 body-parse 这个第三方中间件进一步封装出来的。

6. 自定义中间件

自己手动模拟一个类似于 express.urlencoded 这样的中间件，来解析 POST 提交到服务器的表单数据。

实现步骤：

6.1. 定义中间件函数
6.2. 监听 req 的 data 事件

在中间件中，需要监听 req 对象的 data 事件，来获取客户端发送到服务端的数据，如果数据量比较大，无法一次性发送完毕，则客户端会把数据切割后，分批发送到服务器。所以 data 事件可能会触发多次，每一次触发 data 事件时，获取到数据只是完整数据的一部分，需要手动对接收到的数据进行拼接。

```javascript
// 定义变量，用来存储客户端发送过来的请求体数据
let str = "";
// 监听 req 对象的 data 事件 (客户端发送过来的新的请求体数据)
req.on("data", chunk => {
  // 拼接请求体数据，隐式转换为字符串
  str += chunk;
});
```

6.3. 监听 req 的 end 事件

当请求体数据接收完毕之后，会自动触发 req 的 end 事件。因此，我们可以在 req 的 end 事件中，拿到并处理完整的请求体数据。示例代码如下：

```javascript
// 监听 req 对象的 end 事件(请求体发送完毕后自动触发)
req.on("end", () => {
  // 打印完整的请求体数据
  console.log(str);
  // T0D0: 把字符串格式的请求体数据，解析成对象格式
});
```

6.4 使用 querystring 模块解析请求体数据

Nodejs 内置了一个 querystring 模块，专门用来处理查询字符串。通过这个模块提供的 parse 函数，可以轻松把查询字符串，解析成对象的格式。示例代码如下：

```javascript
// 导入处理 querystring 的 Node.js 内置模块
const qs = require("querystring");
// 调用 qs.parse() 方法，把查询字符串解析为对象
const body = qs.parse(str);
```

6.5 将解析出来的数据对象挂载为 req.body

上游的中间件和下游的中间件及路由之间，共享同一份 req 和 res。因此，我们可以将解析出来的数据，挂载为 req 的自定义属性，命名为 req.body，供下游使用。示例代码如下：

```javascript
req.on("end", () => {
  const body = qs.parse(str); // 调用 qs.parse() 方法，把查询字符串解析为对象
  req.body = body; // 将解析出来的请求体对象，挂载为 req.body 属性
  next(); // 最后，一定要用 next() 函数，执行后续的业务逻辑
});
```

6.6 将自定义中间件封装为模块

为了优化代码的结构，我们可以把自定义的中间件函数，封装为独立的模块，示例代码如下：

```javascript
// custom-body-parser.js 模块块中的代码
 const qs = require('querystring')
 function bodyparser(req. res, next) { /* 省略其它代码 */ }
 module.exports = bodyParser // 向外导出解折请求体数据的中间件函数
 // -----------分割线--------
 // 1，导入自定义的中间件模块
 const myBodyParser = require("custom-body-parser")
 // 2，注册自定义的中间件模块
 server.use(mybodySer)
```

未模块化完整 demo：

```javascript
const express = require("express");
// 导入 字符串解析模块
const qs = require("querystring");
// 创建 服务器 实例
const server = express();

// 中间件函数 解析表单数据
function myUrlEncoded(req, res, next) {
  // 定义中间件的具体处理逻辑
  let str = "";
  // 监听 data 事件
  req.on("data", chunk => {
    str += chunk;
  });
  // 监听 end 事件
  req.on("end", () => {
    console.log("str", str);
    const body = qs.parse(str);
    console.log("body", body);
    req.body = body;
    next();
    // TODO 把字符串格式的请求体数据转换为 json 对象格式
  });
}
server.use(myUrlEncoded);

server.post("/user", (req, res) => {
  res.send(req.body);
});

server.listen(80, () => {
  console.log("127.0.0.1 server is running");
});
```

### 五、使用 express 写接口

#### 1. 创建基本服务器

```javascript
<!-- 导入 express 模块 -->
const express = require('express')
<!-- 创建服务器 -->
const server = express();
<!-- 启动服务器 -->
server.listen(8080,()=>{
  console.log("127.0.0.1:8080, server is running")
})
```

#### 2. 创建 API 路由模块

```javascript
// apiRouter.js [路由横块]
const express = require('express')
const apiRouter  express.Router()
// bind your router here...
module.exports = apiRouter


// index.js [导入并注册路由模块]
const apiRouter = require("./apiRouter.js")
server.use('/api', apiRouter)
```

#### 3. 编写 GET 接口

```javascript
apiRouter.get("/get", (req, res) => {
  // 1.获取到客户端通过查询字符串，发送到服务器的数据
  const query = req.query
  // 2.调用 res.send() 方法，把数据响应给客户端
  res.send({
    status: 0,           // 状态，0 表示成功，1 表示失败
    msg: "GET请求成功!"  ，//状态描述
    data: query          // 需要响应给客户端的具体数据
  })
})
```

#### 4. 编写 POST 接口

```javascript
apiRouter.post("/post", (req, res) => {
  // 1.获取到客户端通过请求体，发送到服务器的 url-encoded 数据
  const body = req.body
  // 2.调用 res.send() 方法，把数据响应给客户端
  res.send({
    status: 0,           // 状态，0 表示成功，1 表示失败
    msg: "POST请求成功!"  ，//状态描述
    data: body          // 需要响应给客户端的具体数据
  })
})
```

**注意：如果要获取 URL-encoded 格式的请求题数据，必须配置中间件 `server.use(express.urlencoded({extended: false}))`**

#### 5. 解决跨域问题

刚才编写的 GET 和 POST 接口，存在一个很严重的问题：不支持跨域请求。解决接口跨域问题的方案主要有两种：

- CORS（主流的解决方案，推荐使用）
- JSONP（有缺陷的解决方案：只支持 GET 请求）

跨域 demo index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>跨域问题</title>
    <script src="https://cdn.staticfile.org/jquery/3.6.4/jquery.min.js"></script>
</head>
<body>
    <button id="BtnGet">GET</button>
    <button id="BtnPost">POST</button>
    <script>
        $(function(){
            % // 测试 get 接口
            $("#BtnGet").on("click", function(){
                $.ajax({
                    type: "GET",
                    url: "http://127.0.0.1/api/get",
                    data: { name: "张三", age: 12, gender: "男"},
                    success: function(res){
                        console.log(res)
                    }
                })
            })
            % // 测试 post 接口
            $("#BtnPost").on("click", function(){
                $.ajax({
                    type: "POST",
                    url: "http://127.0.0.1/api/post",
                    data: { bookName: "西游记", author: "施耐庵"},
                    success: function(res){
                        console.log(res)
                    }
                })
            })
        })
    </script>
</body>
</body>
</html>
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/00214764114842aebdd20a8e9c2eb5a5~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

##### 5.1 使用 cors 中间件解决跨域问题

cors 是 Express 的一个第三方中间件。通过安装和配置 cors 中间件，可以很方便地解决跨域问题，使用步骤如下：

- 运行`npm install cors`安装中间件
- 使用`const cors = require('cors')`导入中间件
- 在路由之前调用`app.use(cors())`配置中间件

**注意：一定要在路由之前，配置 cors 这个中间件，从而解决接口跨域的问题**

补充：
（1）什么是 CORS
CORS（Cross-Origin Resource Sharing，跨域资源共享）由一系列 HTTP 响应头组件，这些 HTTP 响应头决定资源器是否阻止前端 JS 代码跨域获取资源，浏览器的同源安全策略会组织网页“跨域”获取资源。但如果接口服务配置了 CORS 相关的 HTTP 相应头就可以接触浏览器端的跨域访问限制。

（2）CORS 的注意事项
CORS 主要在服务端进行配置，客户端浏览器无须做任何额外的配置，即可请求开启了 CORS 的接口。CORS 在浏览器中有兼容性。只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口（例如:IE10+、Chrome4+、FireFox3.5+）

（3）CORS 跨域响应头

- Access-Control-Allow-Origin

响应头部中可以携带一个 Access-Control-Allow-Origin 字段，其语法如下：
`Access-Control-Allow-Origin: <origin> | *`

其中，origin 参数的值制定了允许访问该资源的外域 URL。例如，下面的字段值将只允许来自 text.cn 的请求：`res.setHeader("Access-Control-Allow-Origin",'http://test.cn')`

- Access-Control-Allow-Headers

默认情况下，CORS 仅支持客户端向服务端发送如下的 9 个请求头：Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data Viewport-Width、width、Content-Type（值仅限于 text/plain、multipart/form-data、application/x-www-form-urlencoded 三者之一）如果客户端向服务器发送了额外的请求头信息，则需要在服务器端，通过 Access-Control-Allow-Headers 对额外的请求头进行声明，否则这次请求会失败！

```javascript
// 允许客户端额外向服务器发送 Content-Type 请求头和 X-Custom-Header 请求头
// 注意:多个请求头之间使用英文的逗号进行分割
res.setHeader("Access-Control-Allow-Headers", "Content-Type,X-Custom-Header");
```

- Access-Control-Allow-Methods

默认情况下。CORS 仅支持客户端发起 GET、POST、HEAD 请求。

如果客户端希望通过 PUT、DELETE 等方式请求服务器的资源，则需要在服务器端，通过 Access-Control-Allow-Methods 来指明实际请求所允许使用的 HTTP 方法，示例代码如下：

```javascript
// 只允许 POST、GET、DELETE、HEAD 请求方法
res, setHeader("Access-Control-Allow-Methods", "POST,GET,DELETE,HEAD");
// 允许所有的 HTTP 请求方法
res.setHeader("Access-Control-Allow-Methods", "*");
```

（4）CORS 请求分类
客户端在请求 CORS 接口时，根据请求方式和请求头的不同，可以将 CORS 的请求分为两大类，分别是：简单请求、预检请求。

**简单请求**

同时满足以下两大条件的请求，就属于简单请求：

1. 请求方式：GET、POST、HEAD 三者之一。

2. HTTP 头部信息不超过以下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data Viewport-Width、Width、Content-Type（只有三个值 text/plain、multipart/form-data、application/x-www-form-urlencoded ）

**预检请求**

只要符合以下任何一个条件的请求，都需要进行预检请求：

1. 请求方式为 GET、POST、HEAD 之外的请求 Method 类型

2. 请求头中包含自定义头部字段

3. 向服务器发送了 application/json 格式的数据

在浏览器与服务器正式通信之前，浏览器会先发送 OPTION 请求进行预检，以获取服务器是否允许该实际请求，所以这一次的 OPTION 请求称为“预检请求”，服务器成功响应预检请求后，才会发送真正的请求，并且携带真实的数据。

区别：
简单请求的特点：客户端与服务器之间只会发生一次请求，预检请求的特点：客户端与服务器之间会发生两次请求，OPTION 预检请求成功之后，才会发起真正的请求

##### 5.2 JSONP 接口

（1）概念
浏览器端通过`<script>`标签的 src 属性，请求服务器上的数据，同时，服务器返回一个函数的调用。这种请求数据的方式叫做 JSONP。

（2）特点
JSONP 不属于真正的 Ajax 请求，因为它没有使用 XMLHttpRequest 这个对象

JSONP 仅支持 GET 请求，不支持 POST、PUT、DELETE 等请求

创建 JSONP 接口的注意事项

如果项目中已经配置了 CORS 跨域资源共享，为了防止冲突，必须在配置 CORS 中间件之前声明 JSONP 的接口。否则 JSONP 接口会被处理成开启了 CORS 的接口。示例代码如下：

```javascript
// 优先创建 JSONP 接口 [这个接口不会被处理成 CORS 接口]
server.get("/api/jsonp", (req, res) => {});
// 再配置 CORS 中间件 [后续的所有接口，都会被处理成 CORS 接口]
server.use(cors());

// 这是一个开启了 CORS 的接口
server.get("/api/get", (req, res) => {});
```

（3）实现 JSONP 接口
步骤：

1. 获取客户端发送过来的回调函数的名字
2. 得到通过 JSONP 形式发送到客户端的数据
3. 根据前两步得到的数据，拼接出一个函数调用字符串
4. 把上一步拼接得到的字符串，响应给客户端的`<script>`标签进行解析执行

```javascript
server.get("/api/jsonp", (req, res) => {
  // 1.获取客户端发送过来的回调函数的名字
  const funcName = req.query.callback;
  // 2.定义要通过 JSONP 形式发送给客户端的数据
  const data = { name: "zs", age: 22 };
  // 3.根据前两步得到的数据，拼接出一个函数调用的宁符串 callBack(data)
  const scriptstr = `${funcName}(${JSON.stringify(data)})`;
  // 4.把上一步拼接得到的字符串，响应给客户端的 <script> 标签进行解析执行
  res.send(scriptStr);
});
```
