---
title: "总结 ES2015 的所有新特性"
date: 2022-05-19T19:05:49+08:00
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ["前端下载图片或者文件的方式"]
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
## 前端下载资源的方式

目前前端直接下载web服务器或者CDN静态资源的方式有两种，一个是利用 `<a>`标签，另一个通过 `window.open()` 函数。今天重点讲一下 `<a>` 标签。

### `<a>`标签

<a> 标签就是html中的超链接标签，但是用过这个标签的同学应该都有这种印象，当超链接链接的内容是图片、视频或者pdf时，点击超链接往往会在浏览器的新标签页中打开对应的资源，而不是开始下载。但是其他像压缩包这样的资源，也就是浏览器没办法直接打开的资源则会直接开始下载。问题来了，怎么样让所有的文件都 默认下载而不是打开呢？

HTML5给 `<a>` 标签增加了一个download属性，当 `<a>` 标签戴上了download属性时，点击超链接则会被浏览器解析为下载而不是打开。

**下面我们实际操作一下**

一个不带download属性的 `<a>` 标签案例:

![image-20220708225511893](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225511893.png)

浏览器打开之后，点击下载图片查看效果。

![image-20220708225543974](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225543974.png)

随后浏览器打开了图片，但是并没有下载。

![image-20220708225620317](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225620317.png)

`<a>`标签添加download属性后,在试一次

![image-20220708225645027](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225645027.png)

添加 `download` 属性后，再次点击

![image-20220708225710338](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225710338.png)

可以看到我们的图片已经下载，达到了我们想要的效果。

**我们把 `<img>` 和 `<a>` 标签的路径换成一张网络图片，而非本地的图片。**

![image-20220708225804111](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225804111.png)

点击下载图片，会发现并没有下载，而是在浏览器中打开了图片。

注意: 如果你的图片渲染不出来，请在标签内加入 `<meta name="referrer" content="no-referrer">`

**我们开始分析没有下载的原因**

很可能是浏览器的 **同源策略** 导致download属性失效造成的。同源策略是浏览器的一种安全策略，所谓的同源指的是URL地址里面的协议、域名和端口号均相同，如果一个站点开启了同源策略，也称为禁止跨域访问；这时，这个站点的资源自允许在该站点内部跳转和访问，不允许被第三方站点访问和跳转。我们这个案例中由于源cnblogs的服务器设置了禁止跨域，在我们这个场景下的表现就是不能在非cnblogs站点下直接下载cnblogs文件，所以这里download属性会失效，而失效之后做的仅仅是跳转功能。

![image-20220708225831096](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225831096.png)

[参考文章](https://blog.csdn.net/PGguoqi/article/details/106817181)

**解决方案**

换个推流的方式，也就是用js将资源按照二进制流的方式读取，对二进制流生成一个url，这个url是我们自己站点可访问的url，没有禁止跨域的限制。把url绑定到 `<a>` 标签的href属性中，因为浏览器无法打开二进制流文件，所以对于这样的资源，浏览器将开始下载而不是在浏览器中的打开资源。

![image-20220708225936656](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708225936656.png)

**提示**

1. 在浏览器渲染html时，点击下载图片后会提示跨域报错的问题，这个可以通过 [传送门](https://jingyan.baidu.com/article/148a1921c9dbf24d71c3b11f.html ) 暂时屏蔽掉这个问题。

![image-20220708230030158](https://cdn.jsdelivr.net/gh/BallerJay/oss_images@master/blog/2022/07/2022-07-08/image-20220708230030158.png)

下面是一篇关于谷歌浏览器跨域问题的博客，有时间可以看看：[传送门](https://segmentfault.com/a/1190000022506474)