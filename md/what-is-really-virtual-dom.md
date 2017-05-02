# 探秘Virtual DOM

## DOM

DOM是对HTML的抽象（Abstraction），HTML中的元素对应DOM中的节点。

VDOM是对DOM的抽象。

## DOM操作历史演进

原始做法：
先获取要操作的DOM对象，然后更改其属性。这个过程可以使用jQuery等库来简化操作。

MVC做法：
实现了数据和视图的隔离，当数据变更后，使用模板重新生成新视图（大部分情况是局部视图），然后使用新视图将这个旧视图替换掉。这是一种简单粗暴的做法，因为这会引起视图可能因为一个小小的改动就导致整个视图的重新渲染。

MVVM做法：
维护应用状态，自动更新视图。

## VDOM

DOM对象之复杂巨大勿需多言，创建一个DOM元素需要耗费多少资源直接看它拥有的属性就知道了。

VDOM就是对DOM的抽象，它使用简单的JS对象来描述DOM对象，并利用这些JS对象来重构出所有DOM元素的层级关系。这些JS对象可称之为VNode，他说mini版的DOM对象，只包含最基本的属性。使用VNode构建出来的层级关系可称之为VTree，它的层级关系与DOM层级关系一致。

VDOM更新原理：

1. 构建VDOM1，然后构建一个真正的DOM1，此时VDOM1 == DOM1;
2. 状态变更后，重新构建，生成VDOM2；
3. diff(VDOM2,VDOM1) = Patch;
4. patch(Patch, DOM1) = DOM2;
5. 此时有VDOM2 == DOM2;

> VDOM本质上是在JS和DOM之间做一个缓存，它类似于CPU和硬盘之间的内存。CPU（JS）只操作内存（VDOM），有必要的时候写入硬盘（DOM）。

其中，关键步骤就是render、diff、patch。

### 为什么VDOM比DOM快

要想改变UI最终都还是要操作DOM的。之所以说VDOM比DOM快，比较的并不是直接操作DOM的快慢，而是指VDOM比直接替换整个视图的DOM操作要快。
VDOM消耗的时间 = 生成VDOM + Diff + Patch。VDOM节省的是重复创建已有的DOM元素的时间。

举个栗子🌰：
想把一个列表从1变成2：

```html
<!-- list 1 -->
<ul>
	<li>0</li>
	<li>1</li>
	<li>2</li>
</ul>

<!-- list 2 -->
<ul>
	<li>3</li>
	<li>4</li>
	<li>5</li>
	<li>6</li>
</ul>
```
可以达到的最优操作是： 3次innerHTML操作+1次DOM创建和添加（1个Li）。然而这种做法不够通用，而后期也无法维护。

使用MVC耗费的操作是：1次innerHTML操作（ul的innerHTML）+4次DOM创建（4个Li）；

使用MVVM耗费的操作是：1次VDOM render+1次Diff+3次innerHTML操作+1次DOM创建和添加；

因此，相比多次的DOM创建，1次VDOM render+1次Diff的操作还是占有很大优势的。如果哪天DOM本身的操作（创建、添加、删除等）已经非常快了，那么就不需要VDOM了。

## 总结

VDOM解决的问题是，在尽量减小性能消耗的情况下，实现“维护状态，更新视图”。它允许我们可以直接修改轻量级的JS对象来更新视图，而不是直接操作DOM。


## Reference

[知乎：怎样更好的理解虚拟DOM](https://www.zhihu.com/question/29504639)
[如何实现一个Virtual DOM算法](https://github.com/livoras/blog/issues/13)

