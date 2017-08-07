---
layout: post
title: Jenkins Start
description: 安装 集成maven 等等
---

## Jenkins Info

[官网](https://jenkins.io/)

[用户手册-PDF](https://jenkins.io/user-handbook.pdf)

## Install

### Pre-install

+ 推荐系统配置

  Java 8
  1GB Free Memory
  50GB+ Free Disk space （为啥要50GB硬盘...）

+ Notes
  Jenkins 可以单独安装，也可以当作一个Servlet安装到一下现有的架构(framework)里面，比如tomcat之类的服务器上

### 开始安装

本例使用系统 MacOS Sierra。

所以肯定使用 brew 安装啦～

> % brew install jenkins

tips： 如果需要最新版本 则安装 jenkins-lts ，或者看 brew 提示。

#### 罗列一下安装信息:

+ 端口说明:
> When using launchctl the port will be 8080 .

+ brew 启动:
To have launchd start jenkins now and restart at login:
> % brew services start jenkins
Or, if you don't want/need a background service you can just run:
> % jenkins

#### 启动
打开 终端 ，然后输入:
> % jenkins

能看到一大堆启动信息，Jetty啦，spring啦 ，然后还能看到日志里面输出了一长串密码。

启动完成后，打开浏览器输入:
> localhost:8080
访问Jenkins，这里输入密码后（没看到密码输出日志也没关系，这里会有引导），Jenkins会在页面上引导进一步的安装（插件啦，构建工具什么的），这里我选择了 Install suggested plugins 选项.

然后就可以看到Jenkins自己去下插件了，等下载完成的时候可以看一下这些插件都是什么，能干啥。

安装完成后会跳出 Create First Admin User 的页面，输入自己的信息就好了。

#### 用起来～

当然了 先看看都有些什么插件。

点击 系统管理 ，点击 管理插件 ，可以看到关于插件的列表信息，git ,maven 相关的插件都全乎了。

好了 gogogo

#### 新建

这个版本的Jenkins 去掉了 maven2/3项目 的选项，添加了一些其他的，扫了一眼没太看懂，所以直接用 free style project。

+ 起个名字
+ 然后源码管理选择 Git,
+ 把git仓库的url丢进去
比如你在github的仓库链接 git@github.com:<username>/<projectname>.git
分支暂时使用默认的master。
+ 配置构建触发器，我想要在 post-commit hook 进行远程自动构建，选中 Poll SCM ,然后在日程表（Schedule）中编写“日程”表达式(语法基于 linux 中的 cron ,玩过大名顶顶的定时任务框架 Quartz 的小伙伴应该不会模式),留空，如果不配置任何代码在 schedules 中就可以符合我的要求了。
（No schedules so will only run due to SCM changes if triggered by a post-commit hook）
+ 构建环境
暂且略过
+ 构建
选择 Invoke top-level Maven targets
然后Goals 中输入省略掉 mvn 的 maven 命令。

> clean package
由于我的代码仓库目前还是空的 ，所以等一下一定会构建失败。

+ 构建后操作 (post-build Actions)

点开可以看到很多选项，比如发个邮件通知啦，构建其他project啦之类的，这里我选择 Publish JUnit test result report 。
输入:
> **/target/surefire-reports/*.xml

这样可以显示测试结果啦。

当然除了这个 还有构建好的jar/war包的归档(archive)，再次点击 增加构建后操作步骤 ,选中 Archive the artifacts .

输入:
> **/target/*.jar
注意你构建的到底是jar包还是war包还是别的什么的.

好了，我要动手写真正的项目了。
