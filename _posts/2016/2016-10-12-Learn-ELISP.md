---
title: Emacs Lisp 学习(4)
tags: [编程,笔记]
---
上一次我们学习了 `let` 特殊形式, 今天学习的内容学习它的表弟: `let*`. 之后, 我们学习几个和 Buffer 操作相关的一些函数, 并试着在用这些函数来写自己的 Emacs Lisp 函数.

`let*` 长得和 `let` 非常像, 用起来也非常的像, 只有很少的区别. 首先看一下 `let*` 的使用模版:

`(let* VARLIST BODY...)`

其中 VARLIST 是一个列表, 这个列表的元素可以是两种:
1.  符号, 就是定义一个参数, 值绑定为 nil. 和 `let` 中的一样.
2.  子列表, 形式是 `(SYMBOL VALUEFORM)` 把 SYMBOL 的值绑定为了 VALUEFORM 的值.

依次的计算 BODY 的每个语句, 最后一个 BODY 语句的值, 作为 `let*` 的返回值返回.

以上的内容, 和 `let` 的用法其实是完全一样的, 那么和 `let` 的不同在哪里呢? 不同在于 VALUEFORM 的内容上. `let` 中的 VALUEFORM 的内容, VARLIST 列表中的符号都还没有定义, 所以 VALUEFORM 如果使用 VARLIST 中的符号就会提示知道不到变量. `let*` 中不是这样. `let*` 中 VALUEFORM 可以是在同一个 VARLIST 中, 位于前面的符号. 那个例子来看一下:

    (setq test-variable 'global); this variable "test-variable" is global define
    (let ((a "hello")
          (b a))                  ;let can NOT find variable "a", a error happend.
         (message "b:%s" b))
    (let* ((a "hello")
          (b a))                  ;let* CAN find varibale "a"
         (message "b:%s" b))
    (let ((test-variable 'local) ;this variable is a local varibale
          (b test-variable))      ;What's is value of b?
         (message "b:%s" b))
    (let* ((test-variable 'local);this variable also local
           (b test-variable))     ;What's is value of b?
          (message "b:%s" b))

执行第一个 `let` 语句, 会发生一个错误, 提示信息就是说找不到变量 a. 第一个 `let*` 不会提示错误, `b:hello` 会出现在回显区域. 这说明 `let` 对 VARLIST 中的符号做值绑定的时候, 是不会在 VARLIST 中查找符号的定义的, 而 `let*` 却会在 VARLIST 中找. 执行第二个 `let` 会看到回显区域显示的是: `b:global`. 执行第二个 `let*` 在回显区域中看到的是 `b:local`. `let` 情况容易解释, 因为 `let` 对 b 做值绑定的时候, 根本在在它的 `VARLIST` 中查找符号的值, 所以 VARLIST 的第二个元素 `(b test-variable)` 中的 `test-variable` 只能是我们全局定义的那个 test-variable. 在第一个 `let*` 的调用的时候, 我们知道 `let*` 也会到外面的找符号的值, 第二个 `let*` 的计算告诉我们, `let*` 不仅仅在内部找符号的值, 而且如果在内部找到的话就不再到外部去找了. 第二个 `let*` 的例子还告诉我们 `let*` 是从内向外找符号的值的. 仔细体会这几个例子, 就应该能明白 `let` 与 `let*` 的差别.

在 Emacs 中如果要选中全部的内容, 使用的快捷键是 `C-x h`, 对应的 Emacs Lisp 函数是 `mark-whole-buffer`. 现在我们来写一个我们自己的版本. 在 Emacs 中选定一个区域的内容, 首先创建一个标记, 然后移动光标到目的位置, 创建标记的位置到目的位置之间的内容, 就是区域的内容, 也是 Emacs 选中的内容. 选中一个buffer 的全部的内容, 值需要在这个 buffer 开始的位置设置一个标记, 然后把光标移动到这个 buffer 结尾的地方就可以了(反过来, 把标记设置到 buffer 的结尾, 把光标放到 buferr 开始的地方也可以, 我们这里使用第一种方法). 那么问题就变成了:
1.  如何使用 Emacs Lisp 在特定的位置设置标记?
2.  如何把光标移动到特定的位置呢?
3.  buffer 开始的是固定的 1. 但是怎么获得一个 buffer 最后的位置呢?

我们依次来说, 首先是如何获得一个buffer 的最大位置, 简单来说使用函数 `point-max`, 但是实际上, 调用 `point-max` 返回的不总是是 buffer的最后的位置, 当这个 buffer 处于 narrow 模式的时候, point-max 返回的值就不在是这个 buffer 最后的位置了, 而是 这个narrow 区域的最后的位置. 可以猜到 `point-min` 正常模式下一定就是1 , 在 narrow 模式下不是 1, 而是这个 narrow 开始的位置在这个 buffer 中的位置. 现在我们不考虑 narrow 的情况, 假设就是在正常模式下, 那么 buffer 开始位置和结束位置问题加解决了.

移动光标到一个特定的位置, 用的是函数 `goto-char`. 在特定位置设置标记是 `push-mark`. 更多的内容, 可以参考 Emacs 帮助文档来 `C-h f goto-char` , `C-h f push-mark` 来了解. 一下是我写的最简单的选择全部 buffer 内容的版本.

    (defun select-whole-buffer ()
        "selecte whole buffer to regin"
        (interactive)
        (push-mark 1 nil 't)
        (goto-char (point-max)))

我发现, 现在学习的内容, 其实更多的已经不是 Emacs Lisp 语言的问题了, 更多的是 Emacs Lisp 的库函数怎么使用的问题了. 如果后面的内容没有更多的关于 Emacs Lisp 语言的问题, 我就不在更新内容, 等我学习完 Emacs Lisp 语言之后, 库的问题在一点点看. 或者集中起来把 Emacs Lisp 常用的库介绍一下.
