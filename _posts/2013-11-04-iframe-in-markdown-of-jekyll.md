---
layout: post
title: Markdown文件中iframe的问题
date: 2013-11-04 23:00:0
category: 技术
tags: [Web, Markdown, Jekyll]
---

Markdown文件中可以直接插入HTML语句。但Jekyll中的Markdown解析器maruku在解析HTML时对iframe似乎有特殊的要求。
例如，直接插入下面的代码时会报错：

{% highlight html %}
<iframe src="https://skydrive.live.com/embed?cid=8B504C1595CD3973&resid=8B504C1595CD3973%2126382&authkey=AJzDcN30q6g4W0Y&em=2" width="700px" height="500px" frameborder="0" scrolling="no"></iframe>
{% endhighlight %}

<!--more-->
错误信息显示：

    Illegal character '&' in raw string "https://skydrive.live.com/embed?cid=8B504C1595CD3973&resid=8B504C1595CD3973%2126382&authkey=AJzDcN30q6g4W0Y&em=2"

改成如下这样就可以了：

{% highlight html %}
<iframe src="https://skydrive.live.com/embed?cid=8B504C1595CD3973&amp;resid=8B504C1595CD3973%2126382&amp;authkey=AJzDcN30q6g4W0Y&amp;em=2" width="700px" height="500px" frameborder="0" scrolling="no"> </iframe>
{% endhighlight %} 

区别在于所有的`&`都换成了`&amp;`，而且在`</frame>`之前加了一个空格。

我原想这是maruku对src属性里的URL的要求。但试验发现，这种要求不仅限于iframe也不仅限于URL，所有其他HTML元素里使用`&`时都要进行类似的转换。
因为maruku遇到HTML代码时会先对其进行一次解析，解析时需要支持[HTML字符实体](http://www.w3school.com.cn/html/html_entities.asp)。
作为HTML字符实体的起始符，&肯定需要转换了。以后写Markdown文件时需要注意这一点。

关于那个空格，如果不加的话，maruku会将iframe元素转换成`<iframe ... />`的形式，这将导致页面的后半部分显示不出来。所以还是加上吧。