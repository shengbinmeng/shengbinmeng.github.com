---
layout: post
title: C/C++中的调用约定和名称修饰
date: 2015-03-26 12:00:00
category: 技术
tags: [C/C++, 编程, 微软, ARM, Visual Studio]
---
{% include JB/setup %}

调用约定和名称修饰一般是程序员不需要接触的，它们通常由编译器搞定。
但如果你需要把汇编代码和C代码一起编译（甚至仅仅是把C代码和C++代码一起编译），可能就会遇到与本文所介绍内容相关的问题了。

<!--more-->

调用约定和名称修饰都属于应用程序二进制接口（Application Binary Interface, ABI）这个大的范畴。ABI包含的东西很多，这篇文章只涉及其中一二。

## 调用约定

调用约定并不是编程语言本身规定的内容，而是实现相关的，也就是编译器在将源程序转换为二进制时才涉及的。
不同的编译器对函数调用的实现是不同的，包括如何传递参数和返回结果，以及如何清理栈，等等。具体如何实现还与平台（例如有多少寄存器可用）有关。
区分这些不同做法的就是调用约定。

在有些平台上，例如ARM上，调用约定真的是大家约好的，也就是各家编译器都遵循唯一且同样的函数调用实现方法。
但在另一些平台上，例如x86上，却存在多种调用约定。常见的`__stdcall`， `__cdecl`， `__fastcall`， `WINAPI`
这些东西都是来指定x86上的调用约定的。`WINAPI`被定义为`__stdcall`；`__cdecl`是大多数编译器生成32位x86程序的默认调用约定；
`__fastcall`则是生成64位x86程序（x64）的默认调用约定。

虽然看上去挺混乱，但一般来说按照编译器默认处理就不会出现什么问题。程序员不用管调用约定也能很好的编程。
但如果想要自己指定，在函数声明前增加上述那些修饰即可（不同编译器的指定方法可能略有差异）。

## 名称修饰

我们在源程序中起的函数名经过编译后会被修改，这就是名称修饰。

名称修饰是编译器为了满足链接时名称的唯一性要求采取的一种手段。
一些语言（例如C++）支持命名空间、函数重载等特性，使得程序员可以对不同的函数使用同一个标识符。
这些函数在编译时就要经过名称修饰来作区分，例如通常把函数的参数类型和返回值类型加入函数名称中。

名称修饰规则没有标准方式，所以不同的编译器（甚至同一编译器的不同版本，或相同编译器在不同平台上）的名称修饰规则都截然不同。
具体可以参见维基百科上的[总结](http://en.wikipedia.org/wiki/Name_mangling)。

本来像C语言这样简单纯粹的语言是不需要名称修饰的，但像微软这样喜欢把简单问题复杂化的公司则[反其道而行之](https://msdn.microsoft.com/en-us/library/x7kb4e2f.aspx)。
微软之所以要给C语言也进行名称修饰竟然是为了支持多种调用约定：在链接时，通过观察修饰后的名称来判断是哪种调用约定的函数！
不过微软这种做法只针对32位环境，在64位环境下又取消了C语言的名称修饰。

## 为什么了解这些

大多数情况下程序员不需要考虑上面这些东西，但出问题的时候才会发现有些还是应该知道的。我之所以了解这些正是因为遇到了相关的问题。

在一个汇编语言与C语言混合编程的项目中，我用Visual Studio生成程序时32位配置下能成功，64位配置下却出现链接错误。
找了好久才发现原因正是上面提到的一点：64环境下Visual Studio没有对C语言进行名称修饰。汇编代码是不经编译的，自然也没有名称修饰这一步，
所以里面程序员对函数的命名就应该是修饰后的版本；当它与C代码的修饰规则不一致时，二者一起链接就会提示找不到符号。
因此，在32位和64位下汇编代码中函数的命名要分别处理。如果早知道了本文前面介绍的内容，就很容易定位这种问题的原因了。

另外一个编程实践是大多数人都已经知道的，即在C++代码里调用C库函数的话，需要用`extern "C"`把调用的C函数声明包起来（很多C头文件干脆会在所有函数声明外面特意为C++包上这句）。
这是用来告诉C++编译器，这些函数是C语言的，不要对其进行名称修饰；一旦按C++的方法进行了修饰，修饰后的函数名称就在C库里面找不到了，链接时会出问题。
不过这是C语言发展为C++时不可避免的问题，相比于那些因为不同实现厂商导致的复杂状况，这个容易接受多了。