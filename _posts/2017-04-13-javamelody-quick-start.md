---
layout: post
title: JavaMelody 快速集成
descripetion: quick start
---

# JavaMelody 是什么？   

# 快速集成   

> 环境基于: winOS/CentOS , tomcat8 , JDK1.8 , MySql 。
> 监控目标: 使用maven构建的Web-App(SSM)  

### 集成第一步：添加pom信息引入jar包   

在web项目的pom.xml中加入一下pom信息，完成导入（1.65.0是截止到2017/04/13最新版本号）  
{% highlight ruby %}   
<!-- https://mvnrepository.com/artifact/net.bull.javamelody/javamelody-core -->
<dependency>
    <groupId>net.bull.javamelody</groupId>
    <artifactId>javamelody-core</artifactId>
    <version>1.65.0</version>
</dependency>
{% endhighlight %}   

### 第二步：web.xml中添加如下过滤器   

{% highlight ruby %}

<filter>
    <filter-name>monitoring</filter-name>
    <filter-class>net.bull.javamelody.MonitoringFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>monitoring</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
<listener>
    <listener-class>net.bull.javamelody.SessionListener</listener-class>
</listener>

{% endhighlight %}

### 集成完成，现在启动你的web项目，并输入ip+端口号/monitoring访问吧～

图略。
