---
layout: post
title: 如何使用Jekyll写blog(二)
description: markdown语法简介
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

# 段落 标题 代码块 

{% highlight ruby %}
= 代表大标题 - 代表小标题 
{% endhighlight %}

{% highlight ruby %}
最高标题，字最粗！
=
次级标题，稍微小点～
-
{% endhighlight %}

效果如下：

最高标题，字最粗！
=

次级标题，稍微小点～
-

啥你说不够用？还有一种：

{% highlight ruby %}
# : 1~6个"#" 对应1～6级标题 
{% endhighlight %}

在文本编辑器里输入：

{% highlight ruby %}
# 演示一下，我是一级
## 我是二级
### 三级
#### 四级
##### 五级
###### 六级
{% endhighlight %}

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

之所以说类似是因为代码框我用了另外一种标记语言的语法，也是jekyll自带的一个语法高亮，可以继续参考之前的链接。

段落、标题、代码块就到这里啦～

# 斜体和加粗

使用*和_来标记需要强调（斜体or加粗）的内容。

{% highlight markdown %}
这样可以 *斜体*
这样也可以 _斜体_
{% endhighlight %}

效果如下:

这样可以 *斜体* 

这样也可以 _斜体_

{% highlight markdown %}
**加粗** 是这样的
__加粗__ 也可以这样
{% endhighlight %}

效果如下：

**加粗** 是这样的

__加粗__ 也可以这样，好吧字体的原因看起来没粗多少 =，=

> tips: 建议使用星号,下划线会偶尔产生一些问题，比如加粗之后的空格去掉对比一下效果

# 列表

1. 无序列表使用星号、加号和减号来做为列表的项目标记.

{% highlight markdown %}
* 吃饭
* 遛狗
* 更新blog
{% endhighlight %}

* 吃饭
* 遛狗
* 更新blog

{% highlight markdown%}
+ 吃饭
+ 遛狗
+ 更新blog
{% endhighlight %}

+ 吃饭
+ 遛狗
+ 更新blog

减号同理，注意符号和项目名之间要有一个空格

2. 有序的列表则是数字加英文.

{% highlight markdown %}
1. 把冰箱门打开
2. 把大象放进去
3. 把冰箱门关上
{% endhighlight %}

1. 把冰箱门打开
2. 把大象放进去
3. 把冰箱门关上

# 链接

Markdown 支援两种形式的链接语法: *行内* 和 *参考*

* 行内 
{% highlight markdown %}
欢迎大家关注我的[知乎](https://www.zhihu.com/people/lament42 "去看看"),虽然最近很少上 o_o 
{% endhighlight %}

欢迎大家关注我的[知乎](https://www.zhihu.com/people/lament42 "去看看"),虽然最近很少上 o_o

* 引用

引用类似各种paper中的资料引用，也就是说链接可以丢到最后去，不必加入正文。

{% highlight markdown %}
jekyll的相关内容来自[jekyllcn][1].

[1]: http://jekyllcn.com/
{% endhighlight %}

jekyll的相关内容来自[jekyllcn][1].

[1]: http://jekyllcn.com/

# 图片

图片的语法跟链接极其相似。
由于手头没有合适的图片，回头再补。

lastupdate 2017-02-08 23:40  碎叫！