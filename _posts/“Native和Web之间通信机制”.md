title: “Native和Web之间通信机制”
date: 2016-01-01 18:14:35
tags: Native Web 通信
---

## Bridge
可以实现一个通用模块（Bridge）来维护不同平台上的“Web与Native双向通信机制”功能。如图1-1所示为Web调用Native的Bridge时序图。

![](http://7xjx9u.com1.z0.glb.clouddn.com/Web%E8%B0%83%E7%94%A8Native%E7%9A%84Bridge%E6%97%B6%E5%BA%8F%E5%9B%BE.png)

Web调用Native的实现原理如下：

1 Web端调用Bridge.callByJS（{name:’func1’, callback: function（）{}, param:{}}），由Bridge根据特定“Web调用Native”方式通知Native执行相应方法（图中的“func1”）。

2 Native执行完毕后通过“Native调用Web”的方式调用Bridge. callByNative（{token: ‘t1234’ }）。
3其中JavaScript回调函数会映射为字符串型的token，通过这个方式来保证最终触发JavaScript的回调函数（包括匿名函数和通过闭包实现的私有函数）。

![](http://7xjx9u.com1.z0.glb.clouddn.com/Native%E8%B0%83%E7%94%A8Web%E6%97%B6%E7%9A%84Bridge%E6%97%B6%E5%BA%8F%E5%9B%BE.png)
参考资料
跨终端Web之Hybrid App： http://www.infoq.com/cn/articles/hybrid-app，

Hybrid App 开发模式: http://blog.pandocloud.com/?p=188

http://segmentfault.com/a/1190000002587623

http://www.infoq.com/cn/presentations/hybrid-html5-ios-client/
