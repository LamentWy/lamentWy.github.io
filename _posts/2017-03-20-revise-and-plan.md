---
layout: post
title: FXXK the title!
description: 起名困难症，见笑
---

> 吐槽：将近一个月没有更新，结果发现subline text告诉我，不花钱连保存都不行，一看70$。   
> 好好赚钱 T_T    
> AND I delete the subline text. turn to the Atom.   
> thanks github.   

## 入职2个月踩过的那些坑们   

1. 在写多媒体资源url拼接时发现，本地测试一切ok，打包发布测试后失效。

I need be more careful with String.

2. SpringMVC   
FileUploadException: the request was rejected because no multipart boundary was found   
SpringMVC会自动根据客户端POST的参数类型来确定Header的Content-Type ,I didn't set any Header ，   
but still got this Exception , then I change anther http client tool , it's ok .   
so be careful with your tools .   

3. 用确定的类型去判断，养成习惯，不然你会发现在赶进度的时候，每个单元测试都不想跟你说话并向你抛出一   
堆空指针异常...

something like :  
{% highlight ruby %}
 null == someThing  
 Integer.equels(someThingInger)
{% endhighlight %}

I miss the Optional in swift.    
And I miss python when quick developing(我猜快速开发是这么拼的！).   

## what's next

1. 按照app端发来的需求列表（别问我为什么app端给后端发需求）   

本周接下来要沟通用户状态；要完成用户消息的拉取模式，并构思推送模式；要写好独立的支付模块，并完成    
支付相关的接口开发；哦，还要写一个音频/视频的通用上传服务，然后注入到接口中。

2. 写给不知道啥时候才能有时间的自己，估摸着也就下周了吧       

处理掉所有的hardcode，该enum enum，该写properties里的都丢进去。   
使用lambda简化Controller中的代码，其实好像整理一下抽取出公共部分然后利用aop的特性切入进去？   
给手头的项目做个监控系统，看一下如何做成独立的，仅需些许配置文件就可以监控其他项目的系统。貌似可以   
参考dubbo中的监控中心。    
学习一下服务器虚拟化技术，把公司那台几乎可以算是闲着没用的测试服务器搞成类似简化版阿里云的服务器。   
然后模拟线上环境在测试服务器上进行部署和备份的演练。    
再然后把热部署什么的搞上去。。。不过这个还要现学现卖。    
再然后把原项目的测试搞起来。   

先到这里吧。。。其他的事情等上面的做完再说。  
