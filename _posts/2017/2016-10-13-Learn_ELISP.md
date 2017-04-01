---
title: Emacs Lisp 学习(5)
tags: [编程,笔记]
---

今天学习 Lisp 中几个基础的函数, `car`, `cdr` 和 `cons`.

# 名字问题<a id="sec-1" name="sec-1"></a>

这几个函数的名字看起来和其他的函数不一样, 其他的函数, 通过其名称, 我们都多少能获取一点这个函数是做什么的信息, 但是这三个, 完全看不出来. 为什么会这样呢? 这主要是历史问题照成的. 这几个函数在 Lisp 被发明的时候就存在了, 快 30 多年过去了, 当初写这几个函数的人大概也没想到这几个函数会有这么长的生命力吧. 在当时它们是有意义的, 但是现在我们已经不能从它们的名字上看出它们完成什么功能了. 岁月流逝, 物是人非, 很多在当初是常识性的内容, 现在都被历史湮没了.

`cons` 是 'construct' 缩写, 这个还依稀能看出来一点信息. 容易猜到 `cons` 用来构造一个什么东西的. 这个 "什么东西" 就是 Lisp 列表. cons 用来构造 Lisp 列表.

`car` 是 'Contents of Address part of the Register' (寄存器地址部分内容)的缩写, 像很多缩写一样, 如果不给出来全部的定义, 很难知道他们到底是什么意思. `cdr` 是 'Contents of the Decrement part of the Register'(寄存器低位部分) 的缩写. 现在我们知道 `car` 和 `cdr` 分别是什么的缩写了, 但是还是难以猜到他们是做什么的. 这就是历史原因照成的问题. 在 Lisp 最初发明的时候, 使用两个 16 位的域, 分别叫做是寄存器的地址部分和低位部分. 现在 Lisp 早已不是这样来做了, 写 Lisp 代码的人, 还在使用 `car`, `cdr`, 所以名字也就一直沿用到现在了. 以至于到现在我们都不知道 `car` 和 `cdr` 到底是什么意思了.

这其实是一个深刻的教训, 当我们在命名自己的函数的时候, 一定要给它取一个顾名思义的名字, 而且不要把自己的思维限制在专业的领域中, 认为根据目前的专业常识, 大家能顾名思义, 因为技术在进步, 思想在演化, 今天的常识, 很多年之后很可能就不在是常识了. 一旦你的函数命名并发布之后, 后面使用你的代码的人就会使用你的这个名字. 如果你的名字不能顾名思义, 很可能就会变成新一代的 `car` `cdr` 和 `cons`.

# car cdr cons 的用法<a id="sec-2" name="sec-2"></a>

现在我们来看看 `car` 和 `cdr` 的真正的作用. `car` 和 `cdr` 都只有一个参数, 而且参数类型都是列表. `car` 获取这个列表的第一个元素, 而 `cdr` 是获取这个列表除了第一个元素之外的元素. 所以, `car` 更加应该命名为 `first`; 而 `cdr` 应该命名为 `rest`. 现在我们看看例子.

    (car '(hello world)) ;show hello in echo area
    (cdr '(hello world)) ;show (world) in echo area
    (cons 'hello ())     ;show (hello) in echo area

`car` 把它的参数的第一个元素作为返回值. 而 `cdr` 把他的参数的, 第一个元素外的其他元素作为一个列表返回. cons 把它的第一个参数做为第一个元数, 后面的列表作为其他元素, 构成一个新的元素作为结果返回.

这里要注意的是, `cons`, `car` 以及 `cdr` 都不会修改他的参数的值, 他们都是根据参数来生产一个新的结果返回. 如果我们传给他们的参数是变量, 而不是直接使用符号引用的话, 在计算结果之后, 我们查看这些变量的值是否修改, 就能看出 `cons`, `car` 和 `cdr` 是否修改他的参数值了.

    (setq l '(hello world)) ; l is a list '(hello world)
    (car l)                 ; we get firs element of l: hello
    l                       ; l stil list '(hello world)
    (cdr l)                 ; we get rest of l :'(world)
    (cons 'say l)           ; we construct a new list:'(say hello world)
    l                       ; l stil list '(hello world)

`car` 的参数是一个列表, 返回的是这个列表的第一个元素, 那么, 如果给它传入一个空的列表, 返回什么呢? `(car '())`, 返回值是 `nil`, `nil` 就是空列表. 如果不为 `car` 传入参数呢? 比如 `(car)` 那会提示错误, 输入的参数个数不正确. 如果参数不是一个列表呢? 比如是一个原子 `(car 'hello)`, 计算的结果是提示参数的类型不对.

`cdr` 对参数的要求与 `car` 一样, 当输入的参数不符号要求的时候, 给出的错误的信息也几乎一样.

# 原子列表与对(pari)<a id="sec-3" name="sec-3"></a>

`cons` 的第一个参数的类型没有做要求, 但是第二参数, 上面我们说是列表, 如果不是一个列表呢? 如果是一个原子类型会怎么样呢? 比如 `(cons 'a 'b)` 这会产生一个对(pair): `(a . b)`. 其实, 只要第二个元素不是列表的时候, 都会产生对, 比如 `(cons '(a) 'b)` 返回的是 `((a) . b)`. 对看起来像只有两个元素的列表. 但是有和普通的列表不一样, 它有一个特殊的 `.` 在中间.

Lisp 在列表中, '.' 有特殊的意义, 而不是只是作为一个原子的. 尤其是, 在他的后面还更有别的原子的时候. 代码~'(.)~ 的值是 ~'(\.)~, 而代码 ~'(. b)~ 的值却不是 ~'(\. b)~. 当 ~.~ 在 Lisp 中作为一个符号使用的时候, 它有特定的意义, 它是作为 Lisp 对的标志存在的. ~.~ 是对的标志, 用来隔开一个对的两个部分. 下面我看看 car cdr 怎么处理一个对.

如果我们把一个对作为参数传给 `car` 和 `cdr` 会得到什么呢? `(car '(a . b))`, 的结果是 `a`. `(car '((a) . b))` 的结果是 `(a)`. `(cdr '(a . b))` 的结果是 `b`. `(cons 'a '(b . c))` 得到的是 `(a b . c)`. 看起来好像 Lisp 就把 对当作列表一样! 但是 `(a b . c)` 到底是和 `(a b c)` 有什么不一样呢? 完全一样吗? 不一样的, 看看一下的代码:

```lisp
(cdr '(a b . c))         ;'(b . c)
(cdr (cdr'(a b . c)))    ;'c
(cdr '(a b c))           ;(b c)
(cdr (cdr '(a b c)))     ;(c)
```

`cdr` 完全像处理列表一样处理对. `(cdr '(b . c))` 的结果 c, 我们也可以看作 `cdr` 完全把 `'(b . c)` 作为列表来处理的, 只是结果 `(. c)` 的值 就是 c . 我们计算 `'(. c)` 的值, 就能看到的确就是 c. 所以原子实际上就是一个特殊的对, 这个对只有第二个元素. 那么只有第一个元素的对这样 `'(c . )` 在 Lisp中有意义吗? 执行一下, 你会发现没有意义. 那么 `'(c . nil)` 呢? 这个得到的就是 `'(c)`, 这说明列表, 实际上是一个特殊的对, 这个对的结尾是一个nil. 那么 `'(nil . nil)` 呢? 它的值是 `'(nil)` 也就是 `'(())`. 总结一下如下表所示.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="left" />

<col  class="left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="left">对表达式</th>
<th scope="col" class="left">等价表达</th>
</tr>
</thead>

<tbody>
<tr>
<td class="left">'(. c)</td>
<td class="left">'c</td>
</tr>


<tr>
<td class="left">'(. nil)</td>
<td class="left">'nil</td>
</tr>


<tr>
<td class="left">'(c . nil)</td>
<td class="left">'(c)</td>
</tr>


<tr>
<td class="left">'(c .)</td>
<td class="left">无效语法</td>
</tr>


<tr>
<td class="left">'(nil . nil)</td>
<td class="left">'(())</td>
</tr>
</tbody>
</table>

表的第一行说明, 原子是特殊的对, 特殊之初在于, 这个对的第一个是什么都不是, 只有最后一个, 也就是原子自身. 而第二行说明了 nil 的常特殊性, 它可以被认为是一个原子. 第三行说明列表也是特殊的对, 这个对的最后一个元素是 nil. 第四行说明, 对可以没有第一元素, 但是不能没有最后一个元素. 第五个需要特殊的解释一下, `(nil nil)` 中的第一个 nil 最后成为了结果的 `(())` 内部的那个空列表, 而 第二个 nil 是外部的那个列表. 第一个 nil 是作为一个原子来用的, 第二是作为一个列表来用的. 原来我们认为 Lisp 的表达式要不是原子, 要么是列表, 现在我们知道, 其实 Lisp 原子和列表都是特殊的对. 对列表来说, 对的最后的原子是 nil, 对原子来说, 它是只有最后一个元素的对.

仔细体会这个表, 可以加深对 Lisp 列表的理解.

如果用 C 语言来实现 Lisp 的原子, 列表和对的话. 我想应该这样来实现:

    struct atom{};
    struct List{
    struct atom* elenent;
    struct atom* next;
    };
    enum PariNode{ struct atom; struct List};
    struct Pair{
    struct PairNode* frist;
    struct PariNode last;
    };
