---
layout: post
title: 如何使用Jekyll写blog(二)
---

经过第一篇，你可能已经成功发布了第一篇blog。

回忆一下之前的几个要点：头信息，命名格式，文件后缀名，以及存放目录。

-----复习结束------

写手们会发现很多问题，比如我例子中的1、2、没有分段。

几乎可以称得上是没有排版。

--------------
说到排版就要略微提一下jekyll的特点了。
他的特点就是，只关注blog本身。

也就是说你跟我一样不喜欢用鼠标，对用鼠标在网站的编辑器里戳来戳去十分反感，对word的排版厌恶至极，希望听着音乐唱着歌，一边写一边就把版给排了，全程不知道鼠标是个啥。

jekyll就是干这个的。
她支持包括Markdown、Textile等多种语法格式。详情继续参照第一篇的链接。

这里只介绍Markdwon，因为就我的自身需求而言，只需要学习一种格式即可。

markdown语法参考url：http://wowubuntu.com/markdown/basic.html

练习环境：http://daringfireball.net/projects/markdown/dingus

rock & roll
-

首先是，段落标题代码块。

{% highlight ruby %}
= 代表大标题 - 代表小标题 
{% endhighlight ruby%}

{% highlight ruby %}
最高标题，字最粗！
=
次级标题，稍微小点～
-
{% endhighlight ruby %}

效果如下：

最高标题，字最粗！
=

次级标题，稍微小点～
-

啥你说不够用？还有一种：

{% highlight ruby %}
# : 1~6个"#" 对应1～6级标题 
{% endhighlight ruby%}

在文本编辑器里输入：

{% highlight ruby %}
# 演示一下，我是一级
## 我是二级
### 三级
#### 四级
##### 五级
###### 六级
{% endhighlight ruby%}

效果如下：

# 演示一下，我是一级

## 我是二级

### 三级

#### 四级

##### 五级

###### 六级

然后来看段落，看起来是空的 空行 即为段落，对，一段写完了敲俩回车就是分段了！ 为啥要说看起来是空的？因为夹杂了tab or 空格的空行也算空行啦～

现在你明白为啥有人在知乎写答案花式空一行了吧 :) just a joke

代码块～类似我上面频繁使用的那些框。

语法

> “>”后面跟代码

比如你想输出前面的框
“ # 演示一下，我是一级 ”
在文本编辑器中输入(去掉引号)："># 演示一下，我是一级"

发布之后就是下面的效果啦～

># 演示一下，我是一级

之所以说类似是因为代码框我用了另外一种标记语言的语法，java写多了，啥都想括号括起来 =,=

{% highlight ruby linenos %}
{% highlight ruby %}
System.out.println("hello world!"); 
{% endhighlight ruby%}
{% endhighlight ruby%}

let's see what happen!

好吧，不是很好～ 代码嵌套带来了一些问题。虽然你们看到的时候可能已经正常了。

段落、标题、代码块就到这里啦～



