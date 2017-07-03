---
layout: post
title: let's Https
description: 申请个人使用的免费https证书
---

## 原计划是let‘s encrypt

然而幸苦读了半天文档...

不知道自己的博客要怎么办 =，=

域名是在狗爹申请的

dns解析是用国内的云解析

然后直接让域名指向了github提供的地址（这个已经https了，只是我自己的域名没有https）

无论是狗爹还是某dns服务商（它逼我实名认证，我不想宣传它）都不支持Let's Encrypt


## 于是最后使用了免费的DVSSL

嗯，证书申请下来了，发现跟原计划不太一样。。。

还在测试中。


## 关联了独立域名的博客，github pages 并不支持自己上传证书  

于是换成cloudflare，具体步骤见[链接](https://zhuanlan.zhihu.com/p/22667528)

简单的说就是，首先把dns服务器改成cloudflare的，然后开启ssl和强制https 。
然后等新的dns生效就是了。

目前还没有被墙。
