---
layout: post
title: Git常用命令速查表
description: 文字版+图片
---

## 创建版本库（范围当前目录/版本库）

{% highlight markdown %}
$ git clone <url>    #克隆远程库

$ git init           #初始化版本库  
{% endhighlight %}

## 修改和提交（范围当前库）

{% highlight markdown %}
$ git status              #查看状态   

$ git diff                #查看变更内容   

$ git add .               #追踪所有改动过的文件  

$ git add <file>          #追踪指定文件   

$ git mv <old> <new>      #文件重命名  

$ git rm <file>           #删除指定文件  

$ git rm --cached <file>  #停止追踪指定文件,但不删除  

$ git commit -m "commit message"   #提交所有更新/新增的文件  

$ git commit --amend      #修改最近一次提交  
{% endhighlight %}

## 查看提交历史(范围当前库)

{% highlight markdown %}
$ git log                 #查看历史提交记录   

$ git log -p <file>       #查看指定文件的历史提交记录  

$ git blame <file>        #以列表形式查看指定文件的历史提交记录  
{% endhighlight %}

## 撤销 （<!> 慎重操作） 

{% highlight markdown %}
$ git reset --hard HEAD    #撤销工作区中所有未提及的修改内容 <!>  

$ git checkout HEAD <file> #撤销指定文件的未提交的修改内容 <!>  

$ git revert <commit>      #撤销指定的提交 <!>  
{% endhighlight %}

## 分支和标签（范围当前库）

{% highlight markdown %}
$ git branch                #显示所有分支  

$ git branch <new-branch>   #创建新分支  

$ git branch -d <branch>    #删除指定分支  

$ git tag                   #显示所有标签  

$ git tag <tag>             #显示指定标签  

$ git tag -d <tag>          #删除指定标签  

$ git checkout <branch/tag> #切换到指定分支/标签  
{% endhighlight %}

## 合并和重建

{% highlight markdown %}
$ git merge <branch>    #合并指定分支到当前分支  

$ git rebase <branch>   #结合指定分支与当前分支进行重建  

$ git rebase -continue  #在rebase <branch>遇到冲突，并解决冲突后继续  

$ git rebase -abort     #在rebase <branch>遇到冲突，但并不想处理冲突时放弃rebase  

$ git rebase -skip      #在rebase <branch>遇到冲突，不处理冲突，直接用指定分支替换当前分支  
{% endhighlight %}

## 远程操作

{% highlight markdown %}
$ git remote -v                  #查看远程创库信息		

$ git remote show <remote>       #查看指定远程库信息  

$ git remote add <remote> <url>  #将远程库与本地库进行关联  

$ git fetch <remote>             #从指定远程库获取最新版本，但不自动合并（merge）<推荐>  

$ git pull <remote> <branch>     #从指定远程库获取指定分支，并自动合并 <!>  

$ git push <remote> <branch>     #将当前分支（仅包含已提交的更新）上传到指定远程库的指定分支，并自动合并   

$ git push --tags                # 上传所有标签  
{% endhighlight %}

# 图片版
![temp_git_pic](http://lament-wy.com/assets/images/gitcmd0.jpg "临时借用")



> ######  #Git-CMD-SHEET-v1       #仅供参考       #2017-2-9   --by 小野不是妹纸 && 六六
