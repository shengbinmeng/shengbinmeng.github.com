---
layout: post
title: 用shell脚本生成文本文件
date: 2014-12-17 12:00:00
category: 技术
tags: [Linux, Shell]
---

在Linux上写shell脚本的一个很重要的意图是自动化。假如我们希望自动生成一个具有特定内容的文本文件，应该怎么实现呢？

<!--more-->

我用过两个方法，下面分别举例说明。为了更通用，这两个例子中的文本内容都是多行的。

## 使用echo命令

	echo "Hello, World!
	My name is Shengbin." > readme.txt

这种方法其实就是把`echo`的输出重定向到了文件。`echo`会原样保留换行符，所以多行也是支持的。

## 使用cat命令

	cat > readme.txt << END_TEXT
	Hello, World!
	My name is Shengbin.
	END_TEXT

上面的`END_TEXT`是一个自定义的标识符，二者之间的文本将被认为是一个文件的内容，这个文件作为`cat > readme.txt`的输入参数。
这是一种被称为[here document](http://en.wikipedia.org/wiki/Here_document)的技术。

这种方法有一点优势就是在脚本里写的文本内容的格式与想要呈现在文本文件中的一模一样。上一种方法则要求内容的第一行必须在`echo`的同一行。

## 区分单引号与双引号

Shell中双引号之间的内容会被进行变量展开和命令执行，如果想原样保留文本字符串，应该使用单引号（这种单双引号的区分在别的语言里也有，如PHP、Perl之类的脚本语言）。

例如：

	echo "Your working directory can be read from the variable $PWD." >> readme.txt

和

	cat > readme.txt << END_TEXT
	Your working directory can be read from the variable $PWD.
	END_TEXT
	
产生的readme.txt内容都是类似这样的：

	Your working directory can be read from the variable /Users/shengbin/Desktop.

但实际希望的内容是：

	Your working directory can be read from the variable $PWD.

所以正确的脚本应该这么写：

	echo 'Your working directory can be read from the variable $PWD.' >> readme.txt

或

	cat > readme.txt << 'END_TEXT'
	Your working directory can be read from the variable $PWD.
	END_TEXT
	
没错，把起始标识符用单引号包起来，就会使其中的文本被认为是在单引号之间。
