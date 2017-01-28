---
layout: post
title: 如何使用Jekyll写blog
---

使用Jekyll编写博客与常规博客网站编写有一点点不同，那就是Jekyll需要“头信息”。
也就是说一篇博客会包含两大部分：1、头信息；2、正文。

什么是头信息？
{% highlight ruby %}
---
layout: post
title: 这篇blog的标题
---
{% endhighlight ruby %}
上方代码就是一个标准的头信息，有了这个头信息Jekyll就可以识别这个文件，并对它进行特殊处理啦～
详细说明可以参考：头信息（http://jekyllcn.com/docs/frontmatter/）

头信息就是代码快里的内容，你可以不去了解他，只要复制一份到你这篇blog的开头就行了，把title：空格 后面的标题换成自己的标题即可。

   注意：空格不能少哟～

然后在头信息下面随便写点什么就可以保存啦～

保存时需要注意：
	1、文件名需要遵循： 年-月-日-标题.MARKUP。 不知道啥是markup也无所谓，保存成.md 就行了。 
	2、保存/上传路径：你的github中对应gitpage的仓库（{userName}.github.io）中的_posts文件夹，当然你如果有同步到本地的对应仓库的话同理。

保存至本地仓库的话，commit并同步给远程github仓库，等几分钟刷新你的博客就可以看到啦～

---
譬如本篇地址：
http://lament-wy.com/2017/01/17/how-to-use-Jekyll-to-bolg.html
