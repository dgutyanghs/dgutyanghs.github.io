---
title : Parameters 与 Arguments 辨析
tags  : [日志]
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org6dcb6a5">1. 数学上的区分</a></li>
<li><a href="#orga5c5021">2. 计算机领域的区分</a></li>
<li><a href="#org59b6966">3. 总结</a></li>
</ul>
</div>
</div>
和中文参数对应的英文单词有两个一个是 parameter, 一个是 argument.
看计算机书籍的时候, 很多时候会迷惑, 搞不清楚是什么意思.
中文的时候, 彻底的不知道是什么意思. 英文的时候, 有个模糊的概念,
但是自己还是不是能更好的区分这两个词的区别.
所以会这样, 是因为就是在计算机领域中, 这两个单词很多时候也是可以互换的.
一致都打算把这两个次搞清楚, 今天通过维基百科, 总算把这个问题搞定了.

这两个词的差别别有两个来源, 第一个是数学语境, 第二个是计算机相关的语境.
但是归根到底的话, 其实还是数学语境下的用法.
下面从就分别看看这两个语境下 argument 与 parameter 有什么不同.


<a id="org6dcb6a5"></a>

# 数学上的区分

数学语境中, 一般来说, 这两个参数是不能替换的.

常说的函数的输入参数, 一般指的是 argument. 比如 $\sin(x)$ 中的 $x$ 就是 argument.
所以 argument 表示函数的输入参数. 在函数确定之后, argument 就决定函数的输出.

在数学上, 函数是这样定义的:

> A function *f* from *X* to *Y* is a subset of the Cartesian product $X \times Y$
> subject to the following condition: every element of X is the first component of
> one and only one ordered pair in the subset. In other words, for every x in X
> there is exactly one element y such that the ordered pair (x, y) is contained
> in the subset defining the function f. 
> 
> 从 X 到 Y 的函数 f 是笛卡尔积 $X \times Y$ 的子集, 投影的条件是:
> 有序对的第一部分在子集中出现一次, 而且之出现一次.
> 也就是说, 在函数 f 的定义中, 对于 *X* 中的每个 *x*, 只有一个唯一的元素 *y*
> 构成有序对 *(x,y)*.

从函数定义的角度了说, argument 就是的 $x$ ,
这也就是为什么中文中叫做 "**输入** 参数" 的原因. 

那么 parameter 是什么呢? 先看个实际的例子
$\log_{a}(x)$ 这是一个对数函数, 但是它的底是不确定的,
用参数 $b$ 表示, 这里的 "参数" 就是函数的 parameter.
$\log_{b}{x}$ 给出的实际上是一个 **函数族**, 而不是一个唯一的函数.
给定 parameter $b$ 才能唯一的确定一个函数.
所以 **parameter 是用来从对数函数族中确定的一个函数的**.
Parameter 是用来确定函数自身的. 根据定义函数是一个规则,
这个规则本身就定义了它的域的范围 *X*.
当然也规定了怎么把 *x* 变成一个 *y*.
一旦一个函数的 parameter 发生了变化, 那么函数就变化了,
也就是说规则就变化了, 所以几乎影响所有的输出.

那么数学上是怎么来表示一个函数的 argument 与 parameter 呢?
看个例子就知道了:

$$
f(x;b) \label{1}
$$ 

公式 $\ref{1}$ 描述的是一个函数族 *f*, 它 的 argument 是 *x*,
parameter 是 *b*. `;` 用来分割 argument 和 parameter.


<a id="orga5c5021"></a>

# 计算机领域的区分

在计算机领域, 这两个参数一般情况下可以互换,
因为可以根据上下文来确定意义.

Parameter 有时候也叫做 formal parameter, 中文就是形式参数,
指的是函数定义的时候使用的变量.
而 argument (有时也叫 actual parameter 实际参数),
指的是实际的传入值.

Argument 实际上还是沿用的数学上的用法,
实际输入的内容就是 argument.
Parameter 是在数学上表示用来定义函数符号,
所以计算机领域就是形式参数, 函数定义时用的变量.

在机器学习, 尤其是深度学习中还有所谓的 hyper-parameter,
翻译为中文就是超参数. 为什么是 hyper-parameter
而不是 hyper-argument 呢?
还是用 parameter 表示定义函数的符号这个意思.
Hyper-parameter 也是用来定义学习算法自身的行为的.
而学习算法是要确定一个函数 $f(x)$.

大部分的情况下, 最多知道 *f* 是那个函数族 $f(x;p)$, 或者是多个 $f\_1 \ldots f\_n.$

机器学习实际上就是:

1.  通过调整 hyper-argument 来确定函数族 $f(x;p)$
2.  为函数族 $f(x;p)$ 确定合适的的 p


<a id="org59b6966"></a>

# 总结

今天总算理解 argument 和 parameter 的含义了.
简单来说:

-   Argument 是实际的输入内容.
-   Parameter 是定义函数特性的符号.

