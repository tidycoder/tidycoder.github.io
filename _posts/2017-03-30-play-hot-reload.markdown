
---
layout:     post
title:      "play framework!"
subtitle:   ""
date:       2017-03-07 12:00:00
header-img: "img/post-bg-2015.jpg"
tags:
    - play framework
---

### 问题

在Intellij中修改了scala.html, 会导致重新编译。 并重启服务器。 这个编译重启的过程大概二十几秒， 非常烦。

### hot reload
	
	
从文档中可以看到play是支持hot swap的。 编译完代码， 并不需要重启服务器。 于是在activator ui中， 打开项目并测试了一下， 确实不用重启服务器，编译时间只有几秒钟， 体验大大提升。 但是没有找到在Intellij中的配置方法。所以以后索性只用Intellij做调试好了。

### 其他方法

深入思考： 为什么要改*.scala.html呢， 如果做成单页应用。 比如说做一个Vue App， 或者React App。 那几本上不需要在服务端去写*.scala.html了。 后台只处理请求并返回JSON数据。 剩余的交给前端去处理好了。 修改html完全不会影响到后台，后台的改动就少了，这样也免去了后台编译的时间。而且单页应用也会提升用户体验。



