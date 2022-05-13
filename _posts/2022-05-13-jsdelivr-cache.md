---
layout: post
title: "jsdelivr 缓存刷新方法"
date: 2022-05-13 18:06:57 +0800
categories: jsDelivr CDN
---

简介：jsDelivr 是一个免费、开源的加速CDN公共服务。

## jsdelivr 缓存刷新方法
只需要将链接中的cdn 更改为 purge，访问一下，后面就会刷新，如果没有更新就多刷新几次

https://cdn.jsdelivr.net/xxxx

切换为

https://purge.jsdelivr.net/xxx

然后再访问原来的链接就好了
https://cdn.jsdelivr.net/xxx