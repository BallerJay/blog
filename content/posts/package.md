---
title: "package.json 与 package-lock.json 的关系"
date: 2022-08-06
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ["package.json 与 package-lock.json 的关系"]
categories: ["JavaScript"]
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

`package.json` 和 `package-lock.json` 之间的关系。

### 1. Package.json

**Package.json 用来描述项目及项目所依赖的模块信息**。

通过 npm 管理，使用`npm init -y`就可以自动生成 package.json，安装包依赖关系都由 package.json 来管理。

#### 语义版本控制

版本号由三部分组成: `major.minor.patch`，对应的就是 主版本.次版本号.修补版本号。

例如: 1.2.3，主要版本 1，次要版本 2，补丁 3。

- 补丁中的更改表示不会破坏任何内容的错误修复
- 次要版本的更改表示不会破坏任何内容的新功能
- 主要版本的更改表示了一个破坏兼容性的大变化。如果用户不适应主要版本，则内容将无法正常工作。

#### 安装依赖包的版本如何指定

相信大家都会经历过，我们安装一些依赖包的时候，版本号前面都会带`^` 或者 `~` 的符号，那么大家有思考过这两个符号代表的是什么吗？

`^` 会匹配最新的大版本依赖包，比如^1.2.3 会匹配所有 1.x.x 的包，包括 1.3.0，但是不包括 2.0.0

`~` 会匹配最近的小版本依赖包，比如~1.2.3 会匹配所有 1.2.x 的版本，但是不包括 1.3.0

`*` 安装最新版本的依赖包,比如\*1.2.3 会匹配 x.x.x

那么该如何选择呢？当然你可以**指定特定的版本号**，直接写 1.2.3，前面什么**前缀**都没有，这样固然没问题，但是如果依赖包发布新版本修复了一些小 bug，那么需要手动修改 package.json 文件;`^`和`~` 则可以解决这个问题。

但是需要注意`^`版本更新可能比较大，会造成项目代码错误 ❌，所以建议使用`~` 来标记版本号，这样可以保证项目不会出现大的问题，也能保证包中的小 bug 可以得到修复。

版本号写`*`，这意味着安装最新版本的依赖包，但缺点同上，可能会造成版本不兼容，慎用 ⚠️。

#### 多人开发时依赖包安装的问题

看了上面版本号的指定后，我们可以知道，当我们使用了`^`和`~`来控制依赖包版本的时候，多人开发，就有可能存在大家安装的依赖包版本不一样的情况，就会存在项目运行的结果不一样。

##### 举个例子:

假设我们在项目中安装了 vue，当我们运行`npm install vue -save` 的时候，在项目中的 package.json 的 vue 版本是 vue: ^3.0.0，我们电脑安装的 vue 版本就是 3.0.0 版本，我们把项目代码提交后，过了一段时间，vue 发布了新版本 3.0.1，这时来了一个新同事，重新`git clone`项目，执行`npm install`安装的时候，在他的电脑的 vue 版本就是 3.0.1 了，因为^只锁定了主要版本，这样我们电脑中的 vue 版本就会不一样，从理论上讲（大家都遵循语义版本控制的话），他们应该仍然是兼容的，但也许 bugfix 会应该影响我们正在使用的功能，而且当使用 vue 版本 3.0.0 和 3.0.1 运行时，我们的应用程序会产生不同的结果。

大家想一想，这样的话，不同的人电脑安装的依赖版项目，是不是都有可能不一样，就会导致每个人电脑运行的应用程序产生不同的结果。就会存在 bug 的隐患。

这时也许会有同学想到，那么我们在`package.json`上面锁死依赖包的版本号不就可以了？直接写`vue:3.0.0`锁死，这样大家安装 vue 的版本都是 3.0.0 版本了。

这个想法固然不错的，但是你只能控制你自己的项目锁死版本号，那依赖包的依赖包怎么办呢？你怎么控制限制别人锁死版本号呢？

所以为了解决以上问题，确保项目代码在安装所执行的运行结果都一样，这时`package.json`就应运而生了。

### 2. Package-lock.json

Package-lock.json 是在 npm(^5.x.x.x)后才有的，中途有几次更改。

#### 介绍

官方文档是这样解释的：`package-lock.json`它会在 npm 更改 node_modules 目录树或者 package.json 时自动生成的，它准确的描述了当前项目 npm 包的依赖树，并且在随后的安装中会根据 package-lock.json 来安装,保证是相同的一个依赖树，不考虑这个过程中是否有某个依赖有小版本的更新。

它的产生就是来对整个依赖树进行版本固定的(锁死)。

当我们在一个项目中`npm install`时候，会自动生成一个 package-lock.json 文件，和 package.json 在同一级目录下。`package-lock.json`记录了项目的一些信息和所依赖的模块。这样在每次安装都会出现相同的结果。不管你什么机器上面或什么时候安装。

当我们下次再`npm install`时候，npm 发现如果项目中有`package-lock.json`文件，会根据`package-lock.json`里的内容来处理和安装依赖而不再根据`package.json`。

注意 ⚠️：使用`cnpm install`时候，并不会生成`package-lock.json`文件，也不会根据`package-lock.json`来安装依赖包，还是会使用`package.json`来安装。

#### package-lock.json 生成逻辑

简单描述一下`package-lock.json`生成的逻辑。假设我们现在有三个 package，在项目 lock-test 中，安装依赖 A，A 项目依赖 B，B 项目依赖 C。

![2](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/2.png)

在这种情况下`package-lock.json`，会生成类似下面铺平的结构。

![3](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/3.png)

如果后续无论是直接依赖的 A 发版，或者间接依赖的 B、C 发版，只要我们不动`package-lock.json`，`package-lock.json`都不会重新生成。

A 发布了新版本 1.1.0，虽然我们 package.json 写的是^1.0.0 但是因为`package-lock.json`的存在，`npm i`并不会自动升级。不过我们可以手动运行`npm i A@1.1.0`来实现升级。

因为 1.1.0 和`package-lock.json`里记录的A@1.0.0 是不一致的，因此会更新`package-lock.json`里的 A 的版本为 1.1.0。

B 发布了新版本 1.1.0，此刻如果我们不做操作是不会自动升级 B 的版本的，但如果此刻 A 发布了 1.1.1，虽然并没有升级 B 的依赖，但是如果我们项目里升级A@1.1.1，此时`package-lock.json`里会把 B 直接升到 1.1.0，因为此刻^1.0.0 的最新版本就是 1.1.0。经过这些操作后项目 lock-test 的 package.json 编程

![4](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/4.png)

对应的`package-lock.json`文件

![5](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/5.png)

这个时候我们将 B 加入我们 lock-test 项目的依赖，B@1.0.0，package.json 如下

![6](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/6.png)

我们执行这个操作后，`package-lock.json`并没有改变，因为现在`package-lock.json`里B@1.1.0满足^1.0.0 的要求。

但是如果我们将 B 的版本固定到 2.x 版本，package-lock.json 就会发生改变。

![7](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/7.png)

因为存在了两个冲入的 B 版本，`package-lock.json`文件会变成如下形式

![8](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/08/2022-08-06/8.png)

因为 B 的版本出现了冲突,npm 使用嵌套描述了这种行为。

#### package-lock.json 可能被意外更改的原因

1. Package.json 文件修改了
2. 挪动了包的位置

将部分包的位置从 dependencies 移动到 devDependencies 这种操作，虽然包未变，但是也会影响 `package-lock.json`，会将部分包的 dev 字段设置为 true

3. `registry`的影响

   经过实际使用发现，如果我们 node_modules 文件夹下的包中下载时，就算版本一样，安装源 `registry` 不同，执行 npm i 时也会修改 package-lock.json。

   可能还存在其他的原因，但是 `package-lock.json` 是不会无缘无故被更改的，一定是因为 **package.json 或者 node_modules 被更改了**，因为 正如上面提到的 package-lock.json 为了能够精准的反映出我们 node_modules 的结构

#### 开发的建议

一般情况下 `npm install` 是可以的，他能保证根据 `package-lock.json` 还原出开发时的 `node_modules`。

但是为了防止出现刚刚提到的意外情况，除非涉及到对包的调整，其他情况下建议使用 `npm ci `来安装依赖，会避免异常的修改 `package-lock.json`，

持续集成工具中更推荐是用 `npm ci`，保证`构建环境的准确性`，**npm i 和 npm ci 的区别** 可以参考官方文档 npm-ci

[参考文章](https://mp.weixin.qq.com/s/TPDZ5foRGLS05-tAX50_eA)
