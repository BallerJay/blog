---
title: "Node.js基础(2) - 模块化"
date: 2023-06-12
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

### 1. 基本概念

模块化是指：在解决复杂问题的时候，自顶向下逐步将系统划分为若干模块的过程，对于整个系统来说，模块是可组合、可分解和可更换的单元。
在编程领域中，一般遵循固定的规则，把一个大文件拆分成独立并且相互依赖的小模块。
提高代码的复用性、可维护性、按需加载。
可以理解为解决复杂问题时的拆分思想。

#### 1.1 模块分类

根据模块来源不同，可以将 Node.js 分为三类：

- 内置模块：Node.js 官方提供的
- 自定义模块：用户创建的每个 js 文件都是自定义模块
- 第三方模块：由第三方开发的模块，非官方提供的内置模块，也不是用户自己创建的自定义模块，使用前需要先下载。

#### 1.2 模块加载

任何种类的模块都可以使用`require()`方法进行加载引用。

```javascript
// 内置模块
const fs = require("fs");
// 自定义模块
const custome = require("./custome.js");
// 第三方模块
const moment = require("moment");
```

注意：

- 在使用 require() 方法加载其他模块时，会执行被加载模块的代码。
- 在使用 require() 方法用户自定义模块时，可以省略 .js 后缀名。

```javascript
// custome.js
console.log("custome is require");

// test.js

const custome = require("./custome.js");
console.log(custome);
//  输出：
//  custome is require
//  {}
```

#### 1.3 模块作用域

与函数作用域相似，在自定义模块中的变量、方法等成员，只能在当前模块中被访问，这种模块级别的访问限制叫做模块作用域。
好处：防止了全局变量的污染问题。

```javascript
// custome.js
const userName = "kelly";

function sayHello() {
  console.log("Hello, my name is" + userName);
}

// test.js

const custome = require("./custome");
console.log(custome); // {}
```

#### 1.4 模块作用域成员共享

module 对象：每个 js 自定义模块，都有一个 module 对象，里面存储了和当前模块有关的信息。

![img](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/06/2023-06-12/xSErlE.png)

module.exports 对象可以将模块内的成员共享出去，供外部使用，在使用 require() 方法导入自定义模块时，得到的就是 module.exports 所指向的对象。
在自定义模块中,module.exports 默认指向空对象。

```javascript
// custome.js
module.exports.userName = "kelly";
module.exports.sayHello = function () {
  console.log("hello");
};

// test.js

const custome = require("./custome");
console.log(custome);
// { userName: 'kelly', sayHello: [Function (anonymous)] }
```

**注意：使用 require（）导入模块时，永远以 module.exports 指向的对象为准。**

```javascript
// custome.js
module.exports.userName = "kelly";
module.exports.sayHello = function () {
  console.log("hello");
};
module.exports = {
  nickName: "alice",
  sayHi: function () {
    console.log("hi");
  },
};

// test.js
const custome = require("./custome");
console.log(custome);
// { nickName: 'alice', sayHi: [Function: sayHi] }
```

**exports 对象**: 由于 module.exports 单词写起来比较复杂，为了简化向外共享成员的代码，Node 提供了 exports 对象，默认情况下，module.exports 和 exports 指向的是同一个对象,**最终共享结果以 module.exports 为准**

```javascript
// exports.js
console.log(exports); // {}
console.log(module.exports); // {}
console.log(exports === module.exports); // true

exports.userName = "aa";
exports.sayHello = function () {
  console.log("hello");
};

// test.js
const exportData = require("./exports");
console.log(exportData);
// { userName: 'aa', sayHello: [Function (anonymous)] }
```

需要注意的是，**require（）模块的时候，得到的永远是 module.exports 指向的对象。**

```javascript
**** egs1
// exportTest.js
exports.userName = "bb"
module.exports = {
    gender: "男"
}

// test.js
const exportData = require("./exportTest")
console.log(exportData)
// { gender: "男" }



**** egs2
// exportTest.js
module.exports.userName = "bb"
exports = {
    gender: "男"
}

// test.js
const exportData = require("./exportTest")
console.log(exportData)
// { userName: "bb" }



**** egs3
// exportTest.js
module.exports.userName = "bb"
exports.gender = "男"

// test.js
const exportData = require("./exportTest")
console.log(exportData)
// { userName: "bb", gender: "男" }



**** egs4
// exportTest.js
exports = {
    gender: "男",
    userName: "bb"
}
module.exports = exports;
module.exports.age = 12;

// test.js
const exportData = require("./exportTest")
console.log(exportData)
// { userName: "bb", gender: "男", age:12 }
```

#### 1.5 模块化规范

模块化规范是指，对代码进行拆分和组合的时候需要遵循的规则。如，使用什么样的语法格式引用模块，在模块中使用什么样的语法规范向外暴露成员。
大家都遵守同样的规范写代码，降低了沟通成本，方便各个模块之间相互调用。

Node.js 遵循了 CommonJS 模块化规范，CommonJS 规范了模块的特性以及各个模块之间如何实现相互依赖。

CommonJS 规范

- 在每个模块内部，module 代表当前模块。
- module 变量是一个对象，它的 exports 属性是对外的接口(即 module.exports)。
- 加载某个模块时，其实是加载某个模块的 module.exports 属性，require() 方法用于加载模块。
