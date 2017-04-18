---
title: Emacs Lisp 学习(3)
tags: [笔记,编程]
---

我们学习了如何定义一个全局的变量 (set, setq), 以及如何定义一个函数. 函数可以有参数, 这个函数的参数作用域是属于这个函数的, 但是如果我们在定义函数的时候, 除了函数的参数外, 还需要定义一些临时的变量, 哪怎么办呢? 这就是今天学习的内容之一: `let` 特殊形式. 今天学习的另外的一个特殊形式是 `if`.

# let 特殊形式介绍<a id="sec-1" name="sec-1"></a>

在 Emacs 中使用 `C-h f RET let` , 我们能看到 `let` 帮助文档:

    let is a special form in `C source code'.

    (let VARLIST BODY...)

    Bind variables according to VARLIST then eval BODY.
    The value of the last form in BODY is returned.
    Each element of VARLIST is a symbol (which is bound to nil)
    or a list (SYMBOL VALUEFORM) (which binds SYMBOL to the value of VALUEFORM).
    All the VALUEFORMs are evalled before any symbols are bound.

第一行明确的告诉我们, `let` 是一个特殊形式, 而且是用 C 语言定义的. 接下来的内容是 `let` 使用的模板. 后面的内容就是对 `let` 解释了. 下面使用代码示例来解释 `let` 的用法.

    (let (
          symble
          (symble-with-value 'value)
          (symble2-with-value (+ 2 3)))
      (message "symble: %s, symble-with-value:%s, symble2-with-value:%s" symble symble-with-value symble2-with-value)
      symble2-with-value )

把光标移动到代码的最后, 按下 `C-x C-e` 你看到的是 5, 现在 按下 `C-x b *Message*`, 你会看到在 \*Message\* buffer 中最后的两行应该是:

    symble: nil, symble-with-value:value, symble2-with-value:5
    5 (#o5, #x5, ?\C-e)

现在我们看看在上面的例子中, `let` 都做了什么. 在我们上面的例子中, 变量列表(VARLIST) 是 `(symble (symble-with-value 'value) (symble2-with-value (+ 2 3)))`, 这个列表有三个元素. 第一个是一个符号 (symble). 第二个元素是一个列表, 这个子列表, 第一个元素还是符号, 第二个元素是一个符号的应用. 第三个元素也是一个列表, 这个子列表, 第一个元素是一个符号, 第二个元素又是一个子列表. 在 \*Message\* buffer 中的信息我们看到 symble 的值是nil, symble-with-value的值 value , symble2-with-value 的值是 5. 最后 `let` 的返回值是 `let` 体的最后一个语句的值.

在 let 这个表达式中, 变量列表中定义的变量都可以被使用, 变量列表是一个列表, 这个列表的元素, 要么是一个符号, 要么是一个符号, 值对构成的子列表. 对变量列表中的符号, 这个符号的值是 nil, 对于符号-值对子列表, 符号的值有 Lisp 计算值的内容得到.

在 let 执行结束之后, 它的符号列表中定义的符号, 都消失了, 比如现在如果我们计算 `symble`, `symble-with-value`, 以及 `symble2-with-value` 我们得到的提示都是符号没有定义.

# swap-symble-value 函数<a id="sec-2" name="sec-2"></a>

所以 let 特殊形式, 是 Emacs Lisp 提供的一个特殊的程序块, 在这个程序块中可以定义一些临时变量用. 我们可以在 `defun` 中使用 let . 例如下面是一个交换两个值的内容的代码:

    (setq hello "hello" world "world"); set two global variables 'hello' and 'world'
    (defun swap-symbles-values (s1 s2)
      "swap the symbles's values of s1 and s2"
      (let ( (tem-val (eval s1)) )
        (set s1 (eval s2))
        (set s2 tem-val)
        't))
    (swap-symble-value 'hello 'world)
    (message "Now \nhello=%s\nworld=%s" hello world)

第一行比较简单, 我们使用 `setq` 定义了两个全部的变量 hello, world, 并给他们分别赋予了字符串值.

第 2 ~ 7 行是 swap 函数的定义, swap 函数用来交换两个符号的值. 首先我们接收的是符号, 而不是符号的值. 函数定义的内部的let 定义了一个临时变量, 用来记录第一个符号的值的内容. 然后把第二个符号的值的内容赋值给第一个符号, 把 tem-val 的值的内容, 付值给第二个符号. 这样就完成了两个符号值的交换. 最后返回符号 t 表示成功.

第 8 行是对 swap 的调用, 因为函数的参数应该是符号而不是符号的值, 所以需要用单引号. 第 9 行检查是不是, 真的达到的我们的预期目的.

# if 特殊形式<a id="sec-3" name="sec-3"></a>

还是沿用一样的思路, 我们先看看 if 的帮助文档怎么说. 在 Emacs 中输入 `C-h f if`, 我们会看到如下的帮助信息:

    if is a special form in `C source code'.

    (if COND THEN ELSE...)

    If COND yields non-nil, do THEN, else do ELSE...
    Returns the value of THEN or the value of the last of the ELSE's.
    THEN must be one expression, but ELSE... can be zero or more expressions.
    If COND yields nil, and there are no ELSE's, the value is nil.

好吧 if 在 Emacs Lisp 中也是一个 C 定义的特殊形式. 简单来说 if 分成了条件部分 COND, 条件成功执行部分 THEN, 以及条件失败执行部分 ELSE.

在 C 语言中的布尔值表示方法, 只有 0 表示 false, 其他值都是 ture. 与此类似, 在 Emacs 中只有 nil 表示布尔值的的false, 其他值都是 ture. 需要说明的是这个 nil , 就是一个空的列表.

需要注意的是 THEN 部分, 只能是一个表达式, 当然这个表达式, 可以是一个符号, 也可以是一个列表. 其他的部分就都是 ELSE的部分了. ELSE 可以有多个表达式. 如果我们的 THEN 需要多个语句才能做完, 那么就只能用一个语句块才好. 说道语句块那么就用到了我们的 let 特殊形式.

在上面我们写的 swap-symble-value 函数中, 输入参数必须是符号, 当如果输入的参数不是符号的时候, 我们上面的程序立刻就崩溃了. 现在修改我们的程序, 如果输入的不是一个符号的时候, 可以给出相应的提示.

    (defun swap-symbles-values (s1 s2) ; swap-symbles-values v2
       "swap the sybles value"
       (if (not (and
                  (eq  (type-of s1) 'symbol)
                  (eq  (type-of s2) 'symbol)))
           (message "The type of s1 and s2 is symble")
           (let ( (tem-value (eval s1)) )
             (set s1 (eval s2))
             (set s2 tem-value))))
    (setq hello "hello" world "world")
    (swap-symbles-values 'hello 'world)
    (swap-symbles-values hello world)
    (message "hello=%s\nworld=%s" hello world)

我们没有提到逻辑操操作的问题, `and` , `or`, `not` 稍微有一些编程经验, 就知道他们的含义, 所以这些内容就不在继续的讨论. 在 Lisp 中, 天然的不会存在计算优先级错误的问题, 因为所有的计算的顺序, 都在写代码的时候规定好了.

# 讨论<a id="sec-4" name="sec-4"></a>

今天, 我们学习了 let 和 if 特殊形式. 并定义了一个叫做 swap-symbles-values 的函数, 现在问题是, 我们这样定义的函数, 无论如何都都达到我们的预期目的吗? 如果不是的话, 什么情况下不能达到我们的预期目的? 有在怎么修改代码, 以堵上这个漏掉呢?

对于这最后一个问题, 就目前我学习的知识还不能给出答案. 所以就这几个问题, 大家不妨讨论一下.
