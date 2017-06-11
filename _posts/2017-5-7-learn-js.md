---
layout: post
title: Day One
description: 快速入门
---

# 快速入门

[wiki的简介](https://zh.wikipedia.org/wiki/JavaScript):  
JavaScript，一种高级编程语言，通过解释执行，是一门动态类型，面向对象（基于原型）的直译语言。它已经由ECMA（欧洲电脑制造商协会）通过ECMAScript实现语言的标准化[4]。它被世界上的绝大多数网站所使用，也被世界主流浏览器（Chrome、IE、FireFox、Safari、Opera）支持。JavaScript是一门基于原型、函数先行的语言[5]，是一门多范式的语言，它支持面向对象编程，命令式编程，以及函数式编程。它提供语法来操控文本，数组，日期以及正则表达式等，不支持I/O，比如网络，存储和图形等，但这些都可以由它的宿主环境提供支持。


## 准备工作  

基础知识：

JavaScript代码可以嵌入到页面任何地方运行，但是通常会放在 <head> </head> 中,如下

{% highlight markdown %}
<html>
<head>
  <script>
    alert('Hello, world');
  </script>
</head>
<body>
  ...
</body>
</html>
{% endhighlight %}

另一种则是通过导入*.js文件来执行:

{% highlight markdown %}
<html>
<head>
  <script src="/static/js/abc.js"></script>
</head>
<body>
  ...
</body>
</html>
{% endhighlight %}

编码工具： Atom

调试工具： Chrome浏览器自带的开发者工具,Console中可以直接执行JavaScript代码  

## JavaScript语法  

### 基本语法  
   与Java类似，语句后要加; 代码块用{...}来包裹，区别在于JavaScript中“;”不是必须的，浏览器执行JavaScript代码时会自动补上“;”。  
   不过可想而知，程序自动判断语义没准什么时候会出错，反正Java写了很久了，之后的代码中都会自己手动写“;”。

#### 定义变量  
{% highlight markdown %}
   var a = 1;
{% endhighlight %}
#### if/else判断

#### 注释  
{% highlight markdown %}
 //行注释

 /*
 段落注释  
 */  
{% endhighlight %}

### 数据类型  
