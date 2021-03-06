**https://github.com/GcsSloop/AndroidNote/blob/master/CustomView/Base/%5B02%5DAngleAndRadian.md**

#一.前言#
**1.为什么讲这个？**

在我们自定义View，尤其是制作一些复杂炫酷的效果的时候，实际上是将一些简单的东西通过数学上精密的计算组合到一起形成的效果。

这其中可能会涉及到画布的相关操作（旋转），以及一些正余弦函数的计算等，这些内容就会用到一些角度、弧度相关的知识。

**2.为什么对角的描述存在角度和弧度两种单位？**

简单来说就是为了方便，为了精确描述一个角的大小引入了角度和弧度的概念。

由于两者进制是不同的（角度是60进制，弧度是10进制），在合适的地方使用合适的单位来描述会更加方便。
> 例如：角度是60进位制，遇到30°6′这样的角，应该转化为10进制的30.1°。但弧度就不需要，因为弧度本身就是十进制的实数。

#二.角度与弧度的定义#

**角度和弧度一样都是描述角的一种度量单位，下面是它们的定义：**

角度：两条射线从圆心向圆周射出，形成一个夹角和夹角正对的一段弧。当这段弧长正好等于圆周长的360分之一时，两条射线的夹角的大小为1度.

弧度：两条射线从圆心向圆周射出，形成一个夹角和夹角正对的一段弧。当这段弧长正好等于圆的半径时，两条射线的夹角大小为1弧度.

**如图：**

![](https://camo.githubusercontent.com/1a05c67f37e6e25212d403d78a6398e4395959b9/687474703a2f2f7777312e73696e61696d672e636e2f6c617267652f30303558746469326a77316631733066393735686d6a333038633064776d78682e6a7067)![](https://camo.githubusercontent.com/e8f7014184e559b0d2684c02b8faa0efe56d0184/687474703a2f2f7777332e73696e61696d672e636e2f6c617267652f30303558746469326a7731663173306733726367326a333038633064773379772e6a7067)

#三.角度和弧度的换算关系#

> 维基百科的公式：

> rad是弧度，deg是角度

> rad = deg x π / 180

#四.一些细节问题#

由于默认屏幕坐标系和常见数学坐标系的小差别，所以在角上必然也会存在一些区别，例如：

**在常见的数学坐标系中角度增大方向为逆时针，**

**在默认的屏幕坐标系中角度增大方向为顺时针。**

![](https://camo.githubusercontent.com/95f8d4ccfd184d68c2a7e3e9cc905e02eb4c8569/687474703a2f2f7777332e73696e61696d672e636e2f6c617267652f30303558746469326a773166317332776e736577666a333038633064777439342e6a7067)