---
title: 关于Cookie的一些说明
date: 2018-08-09 19:56:25
tags:
---
#### 关于Cookie的一些说明

java 通过后台设置cookie的方式：

```
  response.setHeader("Set-Cookie", "uid=112; Path=/; HttpOnly");

  或者  
  Cookie cookie = new Cookie("123", "123");
  response.addCookie(cookie);

```

两种方法都是降cookie的相关信息(key, value)放在response header中

第一次请求

![image](http://182.61.41.64/images/1533816120139.jpg)

第二次请求

![image](http://182.61.41.64/images/1533816178635.jpg)

Stack Overflow 关于cookie的说法是

![image](http://182.61.41.64/images/1533816282463.jpg)



