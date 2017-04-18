---
title: Emacs Lisp 学习(2)
tags: [笔记,编程]
---

昨天, 学习了 Emacs Lisp 的一些基本的概念, 今天学习如何定义函数, 如何使用函数. 在 Emacs Lisp 中有几种定义函数的方法, 今天学习的是使用宏 `defun` 来定义函数的方法.

# 本次学习涉及到的专业术语<a id="sec-1" name="sec-1"></a>

首先我们介绍一些, 在这次学习中遇到的一些专业术语, 比如 `宏`, `特殊形式`, 还有 `原始函数` 等几个概念.

首先我们来说一下原始函数. Lisp 解析器是一个可执行的程序, 这个程序是用别的语言实现的. Emacs Lisp 解释器使用 C 实现的, 在 Emacs Lisp 实现的时候, 为了执行效率或其他方面的考虑, 直接使用 C 语言实现的 Lisp 的基本操作函数, 称为原始函数. 在 Emacs 的函数描述文档, 你就能看到不少的 C 实现的 Lisp 函数, 比如 `+`. 我们按下 `C-h f RET +` 就能看到函数 `+` 的帮助文档:

    + is a built-in function in `C source code'.

    (+ &rest NUMBERS-OR-MARKERS)

    Return sum of any number of arguments, which are numbers or markers.

Emacs 的帮助文档中, 称为内建函数, 也就是我们说的原始函数. 内建函数, 或者原始函数, 和我们直接用 Lisp 语言定义的函数有什么区别吗? 在我们写 Emacs Lisp 程序的时候, 基本上是没什么区别的. 对于 Emacs Lisp 来说, 都是作为函数来使用的.

然后我们来说说, 特殊形式, 形式(form) 在 Lisp 中其实可以和 s 表达式这个属于相互替换. 所以, 当我们说特殊形式的时候, 意思就是 Lisp 对这种 s 表达式的的计算不是按照常规的作法来做的, 不是按照我们在介绍 Lisp 的原理的时候, 介绍的那样来处理的表达式, 就是特殊表达式. 只要 Lisp 对表达式的处理和 Lisp 对常规表达式的处理方法不一样, 那么表达式就是特殊形式, 而不管这个特殊形式自己是怎么定义的. 特殊形式有可能是内建函数, 也可能是宏来实现. 总之, 如果 Lisp 对一个函数的调用, 与其他的表达的处理不一样, 那么这个函数的调用就是特殊形式. Lisp 和其他语言的有很大的不同, Lisp 解释器定义的关键字很少, Lisp 解释器本身不定义 Lisp 语言, Lisp 语言的定义由 `宏`, `原始函数` 定义的特性的形式, 来定义 Lisp 语言的语义.

所以宏就是用 Lisp 语言实现的特殊形式的定义, 是特殊形式 Lisp 实现.

或者简单一点, 就把特殊形式, 理解为其他语言的关键字吧. 这次我们学习特特殊形式 `defun`, 定义函数的宏.

# `defun` 宏<a id="sec-2" name="sec-2"></a>

Emacs Lisp 中 `defun` 宏用来定义一个函数, 具体的用法是:

    (defun FUNCTION_NAME ARGUMENT_LIST
     OPTION_DOCSTRING OPTION_INTERACTION  FUNCTION_BODY)

在上面的代码模版中, 显然是一个 Lisp 合法的列表. 第一个元素是一个 **符号** `defun`. 第二个元素 `FUNCTION_NAME` 是也一个 **符号**, 这个符号表示函数的名字. 第三个元素 `ARGUMENT_LIST` 是一个 **列表**, 这个子列表的元素全部是符号, 表示函数的参数. 第四个是一个可选的, 是一个 **字符串**, 用来了描述这个函数的功能. 在 Emacs 中使用 `C-h f function-name` 得到的函数的帮助文档, 就是通过文档字符串生成的. 所以, 虽然是可选的部分, 仍然强烈建议, 自己写 Emacs Lisp 函数的时候, 要写上文档字符串. 原来在学习 Python 的时候, 第一次接触到了文档字符的概念, 当时觉得 Python 的处理真是巧妙, 使用文档字符来生产随机的帮助文档, 连额外的处理都不用, 比 Java 的文档注释高明多了. 学习 Emacs Lisp 才知道, 原来 Python 这个东西也是从这里借鉴的. 第四个元素 `OPTION_INTERACTION` 也是可选的元素, 这是一个 **列表**, 是 `interactive` 调用. 我们后面讲. 第五个元素 `FUNCTION_BODY` 是任意多个的 s 表达式. 这是函数体. 函数执行的时候, 依次计算 `FUNCTION_BODY` 的每个表达式, 最后的一个表达式的值作为函数的结果返回.

为什么说 `defun` 是一个特殊形式呢? 因为按照上面解释, FUNCTION<sub>NAME</sub> 是一个符号, 如果是正常的函数调用, 那么 Lisp 应该求这个符号的值的才对的, 但是这里没有, 就是把它当作符号来使用. 而 `ARGUMENT_LIST` 是一个列表, 按照 Lisp 的常规处理, 对子列表, 应该首先计算这个子列表的值, 但是这个列表全是符号, 第一个符号, 显然未必就是一个函数的, 按照正常计算的方法来计算这个列表的值, 显然会出问题的. 因此, 我们知道 Emacs Lisp 在处理 `defun` 的时候, 和处理其他 S 表达式的时候, 是不一样的, 是特殊对待这个表达式的, 所以这个表达式, 虽然看来想是对 `defun` 函数的调用, 但是他不是安装普通函数调用来处理的, 所以这个表达式是特殊形式.

写道这里, 回忆昨天学习的内容 `set` 和 `setq` , 显然 `setq` 也是一个特殊形式!

现在来写一个实际的函数, 一个简单的把一个数翻倍的函数:

    (defun double (number)
           "double function, return number is double as input number "
           (* number 2))
    (double 3)

这样我们就定义了一个叫做 `double` 的函数了, 现在把光标放在这个函数的定义后(上面代码的第 3行后), 按下 `C-x C-e`, 你会在回显去看到函数名 `double` 显示出来了.  `defun` 返回值就是函数名. 那么怎么调用我们写的函数呢? 像上面的代码中第4行那样来调用. 现在把光标移动到上面第 4 行的末尾, 再次输入 `C-x C-e`, 可以看到在回显区域显示出了正确的计算结果 6. 如果我们在计算 `double` 的定义之前就计算 `(double 3)` 的值, 很可能得到的结果是错误信息, 原因是 Emacs Lisp 找不到 `double` 定义. 一旦我们执行了 `double` 的定义表达式之后, `double` 就在Emacs Lisp 中落下了跟, 直到你的 Emacs 退出运行. 当下一次从新启动 Emacs 的时候, 就需要从新让 Emacs Lisp 执行 `double` 的定义列表, 好让 Emacs 知道 `double` 函数的定义是什么.

如果想让 Emacs 启动的时候, 就自动的加载我们的定义的函数, 有几种方法, 第一种是把函数的定义写到你的 `.emacs` 文件中. 第二种方法是把你的函数写在一个独立的文件中, 然后在你的 `.emacs` 文件中, 使用 `load` 来加载这个独立的文件. 这些也就是我们加载一个 Emacs 插件做的工作. Emacs 插件, 也就是 Emacs Lisp 程序而已.

在使用 Emacs 的时候, 我们还常常使用 `M-x funcation-name` 来调用函数. 这样调用函数叫做交互式调用. 但是现在我们写的函数, 只能通过计算 s 表达式的方法来调用, 而不能通过交互式调用来调用. 那么怎么让我们的函数可以交互的被调用呢? 那就是解释 `defun` 的使用模版文件中提到的, 第四个元素 `OPTION_INTERACTION` 的功能.

# 无参数交互函数<a id="sec-3" name="sec-3"></a>

下面我们通过经典的 `Hello World` 的程序来看看交互的程序怎么定义. 首先还是按照原来的非交互的方法来定义 `Hello-World` 函数.

    (defun Hello-World ()
           "Hello-World function, just print String \"Hello World\""
           (message "Hello World"))

在使用 `C-x C-e` 计算了 `Hello-World` 的定义之后, 我们输入 `M-x Hell<Tab>` 并没有看到期望的 `Hello-World` 的补全, 这就是说 Emacs Lisp 找不到交互的 `Hello-World` 函数.

现在我们把 `Hello-World` 修改为交互的形式:

    (defun Hello-World () ; interaction version
           "Hello-World function, just print String \"Hello World\""
           (interactive)
           (message "Hello World"))

现在把光标放在上面的代码后面, 输入 `C-x C-e` 重新计算 `Hello-World` 的定义, 然后再次输入 `M-x Hell<TAB>`, 现在你应该看到 `Hello-World` 的的补全了. 按回车, 你看到在回显区域, 显示除了 `Hello World`. 比较以上的两段代码, 唯一的差别, 就是交互性版本, 在第 3 行 多了 `(interactive)` 这个表达式. 我们知道, 这个表达式是调用函数 `ineractive`. 所以, 对函数 `interactive` 的调用, 使得定义的函数(这里指的是 Hello-World), 可以交互式的被调用了. 当然, 使用计算 S 表达式的方法还是可以调用的. 不信你就在 `(Hello-World)` 后面按下 `C-x C-e`, 看看是不是得到了和交互式调用一样的效果.

如果你仔细, 你会发现, 其实交互式调用和计算 S 表达式得到的结果其实是不一样的, 计算 S 表达式得到的结果, 是 "Hello World", 而交互式调用得的的结果是 Hello World, 少了引号. 这是因为, 对于交互式调用来说, 函数调用的副作用, 把字符串显示出来, 是期望的结果. 人关心的是信息, 而不是返回的值的类型, 所以这里没有引号. 而计算 S 表达式, 意味着 Emacs Lisp, 关心的不是副作用, 所以 Emacs Lisp 就原原本本的把副作用显示出来.

# 有参数的交互函数<a id="sec-4" name="sec-4"></a>

上面的例子是对 `interactive` 函数, 最简单的用法. 现在看几个稍微高级点的例子. 在学习其他编程语言的时候, 我们都有输入输出的的问题, 那么在 Emacs Lisp 中如何输入呢? 这也是 `interactive` 函数来完成的. 例如下面的函数定义, 可以交互的输入一个数字, 然后把显示出来:

    (defun Show (number)
          "Show function, show the number"
          (interactive "nPlease input a number:")
          (message "number is %s" number))

把 `Show` 函数加载到Emacs Lisp 之后, 我们使用 `M-x Show` 就能看到提示信息, 然后展示出来结果.

可能猜到了, 所以会有提示信息出来, 一定是因为 `(interactive "nPlease input a number:")` 的原因, 因为提示信息和参数字符串非常的相识. 但是有一点点的不同, `Please` 前面的 `n` 没有在提示信息中出现, 那么这个 `n` 在这里起什么作用呢? 它是用来表示交互输入的内容要转化成一个数字(number), 然后复制给 `number`.

现在我们知道了, 如何交互的输入一个参数了, 那么如何交互的输入两个数字参数呢? 看下来的代码:

    (defun add (n1 n2)
           "add n1  n2 show result"
           (interactive "nPlease input a number for n1:\nnPlease input a number for n2:")
           (message "%s + %s = %s" n1 n2 (+ n1 n2)))

加载 `add` 函数进入 Emacs 中, 输入 `M-x add` 然后按回车, 你首先看到提示输入 `n1` 的值, 输入正确之后, 你看到提示输入 n2 的值, 再次输入后, 你会看到结果.

仔细看上面代码对 `interactive` 的调用, 你也许能猜到秘诀可能在 `\n` 上. 的确 `interactive` 就是用 `\n` 来分割代码字母的. 代码字母, 就是和上面的 `Please` 前面的 `n` 一类的字母, 这些字母告诉 Emacs Lisp 交互调用时, 每个参数的实际值如何获得. 以下是 `interactive` 函数的代码字母中的几个.

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">
<caption class="t-above"><span class="table-number">Table 1:</span> interactive 代码字母说明</caption>

<colgroup>
<col  class="left" />

<col  class="left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="left">代码字母</th>
<th scope="col" class="left">意义</th>
</tr>
</thead>

<tbody>
<tr>
<td class="left">a</td>
<td class="left">函数名: 输入的符号的值是一个函数的定义</td>
</tr>


<tr>
<td class="left">b</td>
<td class="left">一个存在的 buffer 的名字</td>
</tr>


<tr>
<td class="left">B</td>
<td class="left">一个 buffer 的名字, 可能不存在</td>
</tr>


<tr>
<td class="left">c</td>
<td class="left">字符</td>
</tr>


<tr>
<td class="left">C</td>
<td class="left">命令名: 交互式函数定义的符号</td>
</tr>


<tr>
<td class="left">d</td>
<td class="left">把光标位置的作为一个整数, 传感对应的参数</td>
</tr>


<tr>
<td class="left">D</td>
<td class="left">目录名</td>
</tr>


<tr>
<td class="left">f</td>
<td class="left">已经存在的文件的名字</td>
</tr>


<tr>
<td class="left">F</td>
<td class="left">可能不存在的文件的名字</td>
</tr>


<tr>
<td class="left">n</td>
<td class="left">从 minibuffer中读入的数字</td>
</tr>


<tr>
<td class="left">p</td>
<td class="left">前缀参数转化为一个数字.</td>
</tr>
</tbody>
</table>

更多的内容查看 Emacs 帮助文档. 关于前缀参数, 如果不知道的话, 回忆 Emacs 中 `C-u` 的用法.
