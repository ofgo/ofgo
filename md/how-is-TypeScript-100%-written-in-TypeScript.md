# 如何100%使用TypeScript实现TypeScript编译器

tags: TypeScript, Compiler, Boostrapping

## 问题来源

来看看这张图，观察一下，有哪里不对劲么？

![](media/14777475789601.jpg)

上图来自[Microsoft/TypeScript](https://github.com/Microsoft/TypeScript)的GitHub Repo，细心的你一定发现，TS的实现语言是居然TS本身？！Why？How？

TS是JS的超集，TS编译器能将TS变成JS，但是TS编译器是用TS实现的，听起来是不是很奇怪又似曾相似？这不就是先有鸡还是先有蛋的问题么！

为了弄明白，我条件反射地打开Google，然后定位到Stackoverflow上的同样的[问题](http://stackoverflow.com/questions/39235612/how-is-typescript-100-0-written-in-typescript)

## Stackoverflow之回答

最高票答主：
> 这种做法称为[compiler bootstrapping](https://en.wikipedia.org/wiki/Bootstrapping_(compilers))，之所以这么做有很多原因，尤其是（not least）当前语言能帮助你更好的理解你正在实现的语言本身。
> 很多主流语言都有这种self-hosting编译器，如（C，C++)。这种做法能在语言发开过程中，让编译器编译大量当前语言，而这，就是一种很好的编译器功能测试。
> 最开始的时候，第一个编译器必须使用另外一种已经存在的语言来编写。一旦完成第一个版本的编译器，之后就可以使用这个编译器来编译下一个版本的编译器，如此往复。很明显，当你在开发第n版编译器的时候，只能使用第n-1版本编译器所具备的功能。

Boostrapping（自举）是指用语言A编程实现A语言编译器的过程。语言自举就产生了[self-hosting compiler](https://en.wikipedia.org/wiki/Self-hosting#Compiler)，即编译器能编译该实现编译器的源码。

不是很懂，再来看看知乎的回答。

## 知乎之回答

在知乎上与此相关的问题是[编译器的自举原理是什么](https://www.zhihu.com/question/28513473)。

最高票答主解释地更加详细，下面是按照自己的理解对其回答拓展了一下

创造一门V语言时，用V语言来写V编译器的过程如下：
	0. 设计好V语言的基本特性；
	1. 用一门已经存在的语言（如C++，对TS的话，可以是JS）把V编译器A写出来，这个过程会产生很多V语言测试用例；（产出A.exe）
	2. 根据V语言的基本特性，用V语言编写与A具有同样功能的编译器B，用A.exe编译B，直到通过所有测试用例为止；（产出B.exe）
	3. 用B.exe编译B源码得到B2.exe，修改B源码直到所有测试用例都通过为止，此步骤是为了保证B至少是能编译自己的，即使现在的B还有很多bug；
	4. 当你对已经做修改的B源码有信心时，用A.exe把B源码编译一遍，得到B.exe，此时A.exe及其源码就不再被需要了；（产出修复bug后的B.exe）
	5. 之后就不断的用B.exe来编译下一个版本的B就可以了。

## 总结

看完上面的两个回答，应该就能够理解如何使用TS来编写TS编译器了。基本上就是一个**自我进化**的过程。举个栗子就像是，父母把我们造出来后，拥有人的基本特性的我们，通过学习获得新能力，然后又用新能力去掌握更新的能力，如此反复，进而不断的提升我们自己的能力。其中，人的基本特征对应语言的基本特性，新能力对应于开发者给语言添加的新功能。

这又回到“道生一，一生二，二生三，三生万物”的宇宙基本原理上了。对现在所有的语言进行溯源的话，你会发现，最终都会到达统一的起点---汇编。

