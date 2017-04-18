---
title: 如何写Makefile(二)
tags: [编程,日志]
---

今天讲一下，如何用Makefile来对源文件进行条件编译。
在C/C++中条件编译通过宏`#if`,`#ifdef`等宏来实现。例如下面的代码：

````c
#include <stdio.h>
#define DEBUG
#define TEST 1
int main(){
    #ifdef DEBUG
         printf("DEBUG!\n");
    #endif
    #ifdef TEST
        #if TEST == 1
            printf("TEST == 1\n");
        #else
            printf("TEST != 1\n");
        #endif
    #endif
    return 0;
}
````

编译之后，这个程序输出的内容如下：

```text
DEBUG!
TEST == 1
```
如果我们修改文件开头的宏定义，当然会有不同的输出效果，但是这样的修改其实是很烦人的。我们可以直接在编译器的参数中指定宏定义的内容，如果我们把上面第2、3行的宏定义删除，而使用如下形式来编译，也能得到上面的输出结果：

```bash
gcc -DDEBUG -DTEST=1 test.c #这里假设上面的代码存在test.c中
```
如果用以下的参数编译:

```bash
gcc -DTEST=2 test.c #这里假设上面的代码存在test.c中
```
执行程序，输出为：

```text
TEST != 1
```
通过在编译器的参数中指定宏变量，不用改变源码就可以达到条件编译的目的。这显然是比在源码中定义那些调试的宏变量好得多。

如果我们手动的构建程序，直接给编译器传递参数就可以了，如果我们用make来构建程序，那么我们该如何给编译器传递参数呢？

类似于在C的源代码中定义调试宏变量一样，可以在Makefile中使用两个不同的目标，来生成不同的条件编译目标，这是可行的。其中的问题和在源码中定义宏一样是，如果我们需要修改宏定义的值了，依旧需要修改Makefile。

那么如何不修改Makefile的代码，就给make传入变量呢？答案是`-e`参数。

make的`-e`参数，用来指定系统变量。
例如下面的makefile：

```makefile
ifndef CONF
CONF = DEFAULT_CONF_VALUE
endif
(info CONF:$(CONF))

all:
```
使用不同的`-e`参数来指定make程序，得到的输出信息是不一样的：

```text
> make
CONF:DEFAULT_CONF_VALUE
make: Nothing to be done for 'all'.
```

```text
$ make -e CONF=HELLO
CONF:HELLO
make: Nothing to be done for 'all'.
```

现在我们把GCC的条件编译和make的`-e`参数，结合起来，给不同的人,生成不同的问候程序.

```c
#include <stdio.h>
int main(){
    printf("Hello %s\n",NAME);
    return 0;
}
```
```makefile
SRC=$(wildcard *.c)
NAME ?= Wrold
all:
    $(CC) -o '$(NAME)' -DNAME='"$(NAME)"' $(SRC)
```

```text
> make NMAE=Jake
cc -o Jake -DNAME='"Jake"' test.c
> ./Jake
Hello Jake
>
> make
cc -o World -DNAME='"World"' test.c
> ./World
Hello World

> make -e NAME="Jake Black"
cc -o 'Jake Black' -DNAME='"Jake Black"' test.c
> ./Jake\ Black
Hello Jake Black
```

特别提醒大家注意，上面的Makefile的最后一行，`'$(NAME)'`中的单引号可以换成双引号，目的是防止像最后一个例子那样在NAME中输入的是一个带有空格的字符串，用引号引起来，那么就还是把NAME的值作为一个字符传传递给C编译器的，后面的`'"$(NAME)"'`单引号就不能用双引号替换了，因为这里在字符J之前，k之后的引号，也是要传递给C编译器的值，也就是说，这里传递的是一个包含双引号的字符串。如果非要用双引号替换单引号，那么就必须使用转义来处理其中作为值的双引号。

所以要这样做，是因为，当我们在C的文件中，用宏定义一个字符串常量的时候的语法是这样的：

```c
#define NAME "Jake Black"
```
C编译器的`-D`参数，其实就是简单的把她参数转化成`#define`宏，也就是说，`-DKEY=VALUE`转化为宏`#define KEY VALUE`。想要这个转化符合C的语法，就必须把双引号作为值传递给C语言编译器。
