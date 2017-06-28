
---
layout: post
title: 这是Java程序员跑路写python的故事
description: 从Flask是个啥到我的webapp部署成功了
---


# Flask 学习记录


## 1. 简介

+ 1.1 Flask是什么?

Flask一个小型框架，很多人甚至称其为“微框架” （不严谨的说：类似Java体系里的Spring boot）

它被设计成一个可以任意扩展的框架.首先，他有一个包含基本服务，功能强大的核心，其他功能可以通过扩展实现。

简单的说就是要用什么就“扩展”什么，整个应用完成之后可以达到没有无用的模块。

+ 1.2 核心功能有什么？

Flask的两个主要依赖: 路由 和 Web Server Gateway Interface (WSGI) 由 Werkzeug 提供。

模版系统，由Jinja2 提供。

Flask原生不支持数据库访问、Web表单验证、用户认证等功能（又可以参考 spring的Web系列 和 spring boot 的区别了）

但是可以随便扩展，比如想使用 sqlalchemy 以 ORM 的方式访问关系型数据库，扩展就是了。

甚至可以扩展自己实现的数据库访问模组，其他的类似，不理解也没关系啦，反正后面会演示。


## 2. 喜闻乐见的从环境配置到弃坑～（python 2.7 ,MacOS）

+ 2.1 书上说的最佳实践 （参考《FlaskWeb 开发：blablabla开发实战》）  

1. 使用虚拟环境 virtualenv (不要复制 $ 符)

输入：  
> $ virtualenv --version  

如果显示错误，说明需要安装 virtualenv

> $ pip install virualenv

如果提示权限不够就在前面加个 sudo

__notice : 关于python 环境，pip ,easy_install(Mac OS X), virtualenv 等工具的安装均略暂时过，如不清楚还请google。__
__Linux 和 Mac 用户相对轻松一些，Windows 7 及以下版本的用户，耐心查吧，Windows10 没用过，没说法__

2. 创建并激活虚拟环境 (依然是Mac OS X ，以下不重复说明)

打开终端,输入：
> $ virtualenv venv

预期显示(仅供参考，有的版本的Installing 是分开几行显示的,python版本不同 Installing 的内容也不同):
>New python execut /Users/yourusername/yourcodepath/venv/bin/python
>Installing setuptools, pip, wheel...done.

notice : 可以观察以下这个路径下都包含什么

这个时候 virtualenv 已经帮我们生成好了一个全新的虚拟环境，它包含一个私有Python解释器，正式使用之前我们需要激活以下～

在终端中输入，（路径就是之前创建虚拟环境时提示的路径，把python换成activate就好）:

> $ source /Users/yourusername/yourcodepath/venv/bin/activate

虚拟环境被激活后，其中 Python 解释器的路径就被添加进 PATH 中，但这种改变是临时的，它只会影响当前终端的会话。为了能提示你当前在哪个虚拟环境中，命令行提示符会被修改，变成如下的样子:

> (venv) $

notice : 如果你使用了非系统默认的 shell 比如 我用了 zsh ， 它会以其他的形式出现，自己寻找一下吧～

3. 退出虚拟环境

当你想回到全局Python解释器中的时候，在终端中输入:
> (venv)$ deactivate

观察命令行提示符，你会发现已经回去啦～

4. 删除虚拟环境

直接删除生成的目录即可～

## 3. 好了，我们有了虚拟环境了，下一步做咩捏～ （安装开发需要的python包）

+ 当然是先安装要用的Python包啦～  （呃，新同学注意区分 包:package 和 模组/模块:module）

> (venv) $ pip install flask

+ 然后验证下安装成功没～

> (venv) $ python
> >>> import flask

没有任何错误提醒那就是ok咯（Linux 的哲学，没消息就是好消息）～


## 4. 久等了，开始一个WebApplication的编写吧～～

   还记得目标么，用 Flask 写个 WebApplication，如果你被环境配置快搞晕了，那就先喝杯咖啡放空一下，然后精神抖擞的进入整体～～

### 4.1 路由(route)和视图函数(view function)

  平时浏览网页的时候，其实就是从浏览器向服务器发起请求，服务器拦截到请求之后转发给运行在服务器上的 Flask 实例，也就是等下我们要写的 WebApplication。
这个WebApplication知道请求的URL对应的是哪个python函数。这种处理 URL 到 函数的映射关系的程序就叫路由(route)，而 url 对应的 python函数 就是标题里说的 view function。

  用Java写过web开发的肯定对这个概念特别熟悉，比如 SpringMVC 中的 DispatcherServlet ，按照配置好的 servlet-mapping 规则把到达的请求分配给写好的 Controller。

#### 先来看两个简单的例子，以及如何运行它们～

  talk is cheap ,来通过一个简单的例子来大概理解下路由和视图函数。

{% highlight ruby %}

   @app.route('/')
   def index():
       return '<h1>Hello World!</h1>'

{% endhighlight %}

__notice : 这里 return 了一段html代码只是为了方便理解，实际开发中写这样的 hardcode ，会挨骂的，什么语言都是。__

再来演示一段包含动态参数的url  www.xyz.com/user/lament-wy

{% highlight ruby %}

   @app.route('/user/<username>')
   def index(username):
       return '<h1>Hello %s!</h1>' % username

{% endhighlight %}

当然咯～要把上面的代码真正运行起来需要 Web 服务器（就像 Tomcat 之于 Java Web 项目）

这里我们使用 Flask集成的Web服务器来运行（生产环境会使用其他的服务器，后面会讲）：

{% highlight ruby %}

   if __name__ == '__main__':
         # 两种写法
         # app.debug = True
         # app.run()
         app.run(debug=True)

{% endhighlight %}

其中
{% highlight ruby %}
   __name__ == '__main__'
{% endhighlight %}
是python的习惯性写法，暂时照做就是了。

app.run() 可以接收多种参数，比如例子中的开启debug模式～

> （对tomcat熟悉的童靴理解这个应该蛮容易的，这个就相当于直接调用tomcat的main方法并指定参数～）

#### 来实战一下～

例子看过两个了，动手尝试一下吧～

哦，首先要先介绍一下文件目录结构  

{% highlight ruby %}

/projectName/
      |app/
          /static/        (静态资源目录:js ，css什么的)
          /templates/      (模版目录: Jinja2模版都放在这里)
          /main/          （flask程序）
              __init__.py
              errers.py
              forms.py
              views.py
          __init__.py
          email.py           （邮件通知相关）
          migrtations/       (迁移相关脚本，比如数据库)
          tests/            （测试目录）
              __init__.py     （测试的init）
              test*.py         (测试)  
        venv/                 （虚拟环境）
        ReadMe.md／txt         (介绍项目，以及列出依赖等，方便他人部署)
        config.py             （配置信息）
        manage.py              (启动程序)

{% endhighlight %}


 S   创建数据库链接
