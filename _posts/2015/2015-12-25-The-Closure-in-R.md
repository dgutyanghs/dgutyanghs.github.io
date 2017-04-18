---
title: R中的闭包
tags: [编程,日志]
---
在Javascript中，可以在一个函数中再定义一个函数，并把这个内部函数作为值返回到函数外面，这样这个被作为返回值的内部函数，还能继续的访问，定义它的那个函数的变量，这就形成了简单的闭包。

```javascript
function Generator( a ,b,c){
    return function(t){
        a = a+t;
        console.log("a:"+a+",b:"+b+",c:"+c);
    }
}
addA=Generator(3,4,5);
addA(2);//a:5,b:4,c:5
addA(6);//a:11,b:4,c:5
```

一个编程语言，只要允许嵌入函数的存在，那么就在一定程度上支持闭包。R语言中，是允许嵌入函数的。但是R语言中和别的语言中的不一样是，R语言中的无法从语法上区分函数的本地变量还是环境的变量，语言默认的都是本地变量，所以，如果还是像上面的那段Javascript代码那样来转写到R代码中，就不能得到预期的效果。

```R
Generator <- function(a,b,c){
    return function(t){
        a = a + t;
        cat(sprintf("a:%f,b:%f,c:%f\n",a,b,c));
    }
}
addA <- Generator(3,4,5);
addA(2);#a:5,b:2,c:3
addA(6);#a:9,b:2,c:3
```

可以看到在R中，a的值没有累加起来，a在`Gennerator()`中的值一直都是3。只有这样，才能解释为什么，在调用`addA(6)`的时候才能得到9的结果。

这里的原因是这样的，在内部函数中，做`a+t`的运算的时候，对a是做读操作，这个时候，内部函数在它的环境中查找a的值（查找的顺序为：首先函数体的局部变量，参数；然后是外部函数中的局域变量，参数；最后是全局变量）。为`a`赋值，是一个写操作，这个操作在内部函数体中创建名为a的变量，并保存计算的结果。以后再次使用a变量的时候，在函数体中就立刻找到了a，所以能得到，加之后的值。但是我们知道，在`Generator()`函数体中的a的值，是没有变的，所以第二次再次调用`addA()`的时候，并没有得到预期的效果。

其实这个问题，不仅仅是R中闭包的问题，问题的本质，就是如何在R中写外部环境中的变量的。其实在R语言程序包，自身带的教程中，就给出了问题的解决方案。现在我先给出答案，然后在看[An Introduction to R](https://www.cran.r-project.org/doc/manuals/R-intro.pdf "An Introduction to R")这本书中的例子。

要对外部环境中的变量进行写操作，需要用到一个特殊的操作符`<<-`,所以要想我们的R代码，能如Javascript代码那样工作，我们需要把R代码修改如下：

```R
Generator <- function(a,b,c){
    return function(t){
        a <<- a + t;
        cat(sprintf("a:%f,b:%f,c:%f\n",a,b,c));
    }
}
addA <- Generator(3,4,5);
addA(2);#a:5,b:2,c:3
addA(6);#a:11,b:2,c:3

```

在《An Introduction to R》中，给出的使用`<<-`的例子，代码如下：

```R
open.account <- function(total) {
    list(
        deposit = function(amount) {
            if(amount <= 0)
            stop("Deposits must be positive!\n")
            total <<- total + amount
            cat(amount, "deposited. Your balance is", total, "\n\n")
        },
        withdraw = function(amount) {
            if(amount > total)
            stop("You don’t have that much money!\n")
            total <<- total - amount
            cat(amount, "withdrawn. Your balance is", total, "\n\n")
        },
        balance = function() {
            cat("Your balance is", total, "\n\n")
        }
    )
}

account1 <- open.account(100);
account1$balance();
```

这段代码是模拟面向对象，通过函数返回一个对象，来创建对象的实例，这个对象的实例用list来表示其中的方法，用函数调用的环境，保存实例的数据成员。list中的属性，就是方法，每个方法都是一个函数，这些函数可以通过`<<-`来操作环境中的`total`。

`open.account()`相当于构造函数。返回的对象，有`deposit()`,`withdraw()`和`balance()`三个方法。但是必须使用区list域的方法来访问这些方法。如上面代码中的`account1$balance()`那样来调用方法。而不是使用点操作。在R中，'.'是可以用来命名变量的，比如上面的代码中这个函数的名字就是`account.open`这是一个完整的名字，而不是说有一个account的对象，取这个对象的open域。
