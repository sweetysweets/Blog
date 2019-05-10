---
title: jwt刷新问题
date: 2019-04-11 15:29:47
tags:
- jwt
---

## jwt 无感知刷新token

https://blog.csdn.net/superdog007/article/details/80704234

https://blog.csdn.net/sinat_25235033/article/details/80324006

## jwt与token对比

（1）简单的说，token只是一个标识，以token加redis为例，服务端将token保存在redis中，客服端访问时带上token，如果在redis中能够查到这个token，说明身份有效。

    （2）jwt不需要查库，本身已经包含了用户的相关信息，可以直接通过服务端解析出相关的信息，与session，token的最大区别就是服务端不保存任何信息。
---------------------




在postman中如果是redirct的请求会报错

