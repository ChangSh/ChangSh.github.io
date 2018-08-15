---
title: IDEA Web项目debug时引发的思考
date: 2018-08-15 23:42:00
tags:
---
**背景：** ide:IDEA，服务框架dubbo，本地点对点调试

今天从本地点对点调试两个接口，一个是provider，一个是consumer，两个接口都加了断点，consumer的断点在调用provider之前。
之后释放consumer的断点，再次请求的时候发现调用的provider方法没有反应，之后发现是因为provider的断点还没有释放。

一个疑问涌向心头：用tomcat做servlet容器的时候，不是每一个请求都单独开启一个线程吗，为什么会阻塞了呢？

其实是IDEA dubug模式的机制问题，默认情况下，是ALL，也就是全局阻塞
![image](http://182.61.41.64/images/1534347499897.jpg)
改成右边的线程模式就可以了
