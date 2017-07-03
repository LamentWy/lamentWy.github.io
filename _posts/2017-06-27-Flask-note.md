---
layout: post
title: 这是Java程序员跑路写python的故事
description: 从 Flask是个啥 到 我的webapp部署成功了
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

{% highlight ruby %}

 (venv) $ pip install flask

{% endhighlight %}


+ 然后验证下安装成功没～
{% highlight ruby %}

 (venv) $ python
      >>> import flask

{% endhighlight %}


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

新建一个文件 helloflask.py

然后粘贴如下代码

{% highlight python %}
#coding=utf-8

from flask import Flask

app = Flask(__name__)


@app.route('/')
def index():
    return '<h1> hello flask!</h1>'

@app.route('/user/<username>')
def sayhello(username):
    return '<h1>Hello %s!</h1>' % username


if __name__ == '__main__':
    app.run(debug=True)

{% endhighlight %}    

然后在命令行中执行～

> $ python helloflask.py  

接下来能看到如下输出信息～快在浏览器中访问看看，记得试试 /user/<username>

{% highlight python %}
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 186-779-723
{% endhighlight %}


####  玩过上面的例子之后，来稍微了解下 Flask 框架的设计  

web程序（也就是上文中的WebApplication/webapp）说白了就是处理浏览器发来的http请求

无论什么语言编写的webapp都是如此

##### flask 中的 Context

Flask收到浏览器发送的http请求之后，就要让视图函数(view function)调用／访问一些对象，然后才真正开始处理这个http请求

举个简单的例子，当用户访问某个页面时，webapp 返回用户使用的浏览器类型.

{% highlight python %}

#coding=utf-8
from flask import request

@app.route('/brower')
def getBrower():
    user_agent = request.headers.get('User_Agent')
    return '<h1>您正在使用 %s 浏览器' % str(user_agent)

{% endhighlight %}

__notice: Flask 有两种 Context : The Application Context 和 The Request Context,只有相应的Context激活时，才可以使用Context所提供的对象（Object）__

__同时这也是Flask框架背后的设计思想的体现，将代码执行之后发生的一切，分为两种不同的"状态(states),[参考Flask文档](http://flask.pocoo.org/docs/0.12/appcontext/#purpose-of-the-application-context)"__

好了细节之后再说，我们继续～

上面的代码中可以看到，我们在视图函数 getBrower()中，像使用全局变量一样使用了 flask.request 对象，这是因为当请求生效（active）时，The Application Context的对象（比如这里的flask.request）会指向当前的请求（request），这时所有的代码都可以使用这个对象。（[参考](http://flask.pocoo.org/docs/0.12/appcontext/#purpose-of-the-application-context)  

##### URL映射，或者说，路由。

接下来是 URL映射，服务器收到浏览器发来的请求之后，Flask会在我们的WebApplication的 URL映射中查找对应的URL，这个所谓的 URL映射就是URL和视图函数之间的对应关系。Flask支持两种方式来生成这个映射关系：

1. app.route修饰器 （使用方式有点像Java中的@requestMapping()）
2. app.add_url_rule() 或者

同时python支持在 python shell 中查看我们在代码中的映射：

{% highlight python %}

（venv）$ python
>>> from helloflask import app
>>> app.url_map
Map([<Rule '/brower' (HEAD, OPTIONS, GET) -> getbrower>,
<Rule '/' (HEAD, OPTIONS, GET) -> index>,
<Rule '/static/<filename>' (HEAD, OPTIONS, GET) -> static>,
<Rule '/user/<username>' (HEAD, OPTIONS, GET) -> sayhello>])

{% endhighlight %}

##### 请求钩子简介

类似Spring的aop，也通过修饰器实现。

{% highlight python %}

1. before_first_request : 第一个请求执行前，注册一个函数.
2. before_request : 每次请求执行之前，注册一个函数.
3. after_request : 每次请求之后，且请求未抛出未经处理的异常时，注册一个函数.
4. teardown_request : 每次请求之后，且无论是否抛出异常，注册一个函数.

{% endhighlight %}
