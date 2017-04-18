---
title : Nodejs 中的行号
tags: [编程,日志]
---

这几天的工作都是用 Nodejs 做的, 编码少不了调试. Nodejs 事件编程本身就不是一个轻松的事情, 再加上 Nodejs 没有什么特别好的调试工具, 所以,少不了用 输出信息的方式来调试代码的运行结果. 其实很多时候, 当程序运行的不符合自己的预期的时候, 在线的调试未必能够理顺出错的原因. 所以很多时候, 还是需要使用答应信息的方法来调试代码的. 要使用信息输出的方式来调试代码, 那么就必须在代码允许的时候, 知道代码的行号, 这样才能根据调试输出的信息,准确的定位到输出的位置. 在 C/C++ 中, 使用宏定义 `__LINE` 可以直接给出代码所在的行. 但是在 Nodejs 中没有这样的对应物存在. 在 Nodejs 中有 `__filename` 来对应 C/C++ 中的 `__FILE` 宏定义, 以及一个独特的的 `__dirname` 来表示运行代码的目录名. 可是偏偏没有变化最多的行号的信息.

在我最初的工作的时候, 只好自己手动的硬编码代码的行数, 这样如果我在源码中增加了或减少了一些代码, 那么这个硬编码的行号并不会自动的更改, 再次运行的时候, 就会出现运行日志提示的行数和实际的行数不一致的情况. 因此非常的麻烦. 所以我试着在网络上寻找答案. 还真有人给出了一个答案 [Nodejs 像C语言中那样输出行号][1], 但是代码不能正常的运行. 可能是因为使用的 Nodejs 的版本不一样造成的吧. 虽然如此, 这个解决方案还是给了我很多启发的, 虽然他的代码不能工作, 但是他的解决思路是没有问题的. Nodejs 的 Error 类的实例的 stack 字段本身就是用字符描述的栈信息, 栈信息中是还有文件名和行号的, 我可以从这个信息中提取这些东西嘛! 有了思路, 自己就动手试一下了.

当然还是应该向 `global` 对象添加一个 `__line` 的属性, 方法和 [Nodejs 像C语言那样输出行号] 中给出的一样. 通过 `Object.defineProperty()` 向 `global` 对象添加 `__line` 属性, 并且为这个属性指定 `get` 的代理函数, 这样每次访问 `global.__line` 的时候,实际上就是执行代理函数. Nodejs 中 `global` 对象的任意成员, 都是全局有效的, 所以我们可以直接方法 `__line` 来执行我们指定的函数. 更重要的是, 因为我们是直接为 `global` 对象添加属性的, 所以我们不需要导出我们的代码. 其他想用 `__line` 的 Nodejs 程序, 只要在程序运作的最初的时候, 使用语句 `require('./__line');` 这样的语句引入这个文件, 让下面的代码执行一次, 就可以在程序的其他任何地方都使用 `__line` 了.

```Javascript
Object.defineProperty(global,"__line",{
    get:function(){
        var oldLimet=Error.stackTraceLimit;
        Error.stackTraceLimit=2;//只要2最顶部的两个栈的内容就够了
        var error = new Error();
        Error.stackTraceLimit.oldLimet;
        var callStack= error.stack.split("\n")[2];
        return /\(.*:(\d+):.*\)/.exec(callStack)[1] -0;//从字符串中匹配出行号信息,并把它转化成整数
    }
});
```

这个改进可以使得在 Nodejs 的所有版本中使用, 因为我们只有了 Nodejs 的 Error 的特殊信息, 其他的一切都用的是标志的 Javascript 的语法. 甚至可以说我们用的是限制模式的 Javascript 的语法, 这段代码可以在限制模式的代码中运行, 而网上的代码, 因为其中有 `argument.cell`, 在限制模式下运行, 就会直接报错.

-----
在有些情况下, 上面的代码会出错误, 这是因为在上面的代码中, 我们期望 `callStack` 的内容是 " xxxx at (xxxx.js:noline:clo_NO)". 但有时候, 它的内容不是这样的, 而是直接就是 `at xxx.js:noline:col_NO`, 所以有时间程序就会给出错误. 改正代码如下:

```Javascript
Object.defineProperty(global,"__line",{
    get:function(){
        var oldLimet=Error.stackTraceLimit;
        Error.stackTraceLimit=2;//只要2最顶部的两个栈的内容就够了
        var error = new Error();
        Error.stackTraceLimit.oldLimet;
        var callStack= error.stack.split("\n")[2];
        var list = callStack.split(":");
        return list[list.length-2] -0;//从字符串中匹配出行号信息,并把它转化成整数
    }
});
```

注意最后一行的返回语句 `list[list.length-2]` 这是说要去倒数第二个值, 为什么这样做呢? 因为在 Windows 平台下, 文件名中是有盘符的, 像 "D:\test\test.js" 这样, 如果我们用 冒号做分隔符来分割字符串, 并取分割后的第二个内容来作为行号的话, 那么在 Windows 平台下, 就是不正确的. 相反, 像上面的代码中那样, 我们取倒数第二个, 就避免了这个问题. 因为不论什么平台下, 用冒号分割的字符串, 最后一个一定是列号, 倒数第二个总是行号.

[1]:http://www.cnblogs.com/hangj/p/5002545.html
