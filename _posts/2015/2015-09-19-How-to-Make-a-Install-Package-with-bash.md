---
title: 用Bash做软件安装包
layout: default
tags: [编程]

---

在linux下安装JDK的时候，Sun公司为JDK6的linux版本提供了一个shell的安装包，用起来特别的好用，基本上和在Windows下安装软件没什么两样，shell文件执行之后，几乎一切都系都设置好了，不用我们自己再动手设置PATH和JAVA_HOME，可是一个shell文件中是如何把二进制代码包含进来的呢？

再例如淘宝为linux开发的淘宝插件，其实也是一个shell文件，但是执行这个shell文件之后，会安装很多二进制的东西，那么问题来了，Shell只是文本文件，其中的二进制是怎么来包含进来的呢？

从文本文件转换出可执行文件，通过编译器把源程序编程成可执行程序当然是可以的，但是前面提到的哪两种情况都不是这样做的。原因有两个，第一对于大的项目来说，编译需要的时间比较长，环境比较复杂;第二，更加重要的是，这样做其实和从源代码编译程序没什么两样，对于不想让用户看到自己的源码的商业软件来说，这显然是不可取的。

我们看看一般的程序是如何发布他们的二进制包的。一般的程序，无非是把二进制和必要的文档压缩到一个压缩文件中，然后通过README文档的解释程序的运行依赖什么样的假设，然后，你就可以把程序自行的移动到你想移动的地方去了。很多时候，我们把程序放到任何地方都是可以运行的，但是程序可以运行，并不是说我们已经完成了程序的安装，举个例子来说，如果我们解压JDK的二进制包之后，直接把程序移动到一个地方，然后把对应的bin目录添加到PATH中就可以执行JDK提供的一系列工具了。但是如果我们安装其他的依赖与JDK的程序的时候，比如TOMCAT，那么就会有问题。因为我们只是在PATH中加入了JDK的bin目录而没有制定JAVA_HOME这个环境变量，所以TOMCAT很可能会不能运行。再比如我们使用man命令来查看一个程序的手册，一般情况下二进制包中也会包含man文档的，但是如果我们只是把解压的二进制包的路径添加到了PATH中，还不能在man中找到对应的文档。也正是因为这样的原因吧，所以很多的二进制包的发布是使用deb或者rpm包来发布。安装的时候少了很多的烦恼。要制做deb或者rpm包当然是需要学习成本的，而且deb和rpm也只能在对应的linux发行版中使用，如果想要为所有的Linux发行版都提供一个安装文件，那么使用shell文件来做无疑是最好的办法。

Shell的学习成本低，而且对linux平台来说有通用，那么是如何做到的呢？

想想我们在手动安装的情形，无非是把压缩的二进制包解压，移动到特定的目录下，在PATH变量中添加二进制包的可执行文件的路径等等工作。首先我们把二进制包压缩文件和shell文件分开。这样一来，shell中只要完成解压，然后把解压后的目录移动到指定的目录中去，设置各种各样的环境变量然后就完成了工作了。但是我们在如何把压缩文件和这个shell解压之后要执行的命令的shell文件一起放到一个shell文件中呢？要做到这一点，首先这个shell文件中，要有可以解压的二进制内容，其次，这个shell要做的工作就是，把二进制内容，解压，然后把原来手动做的工作在这个shell中用命令完成。在用shell写个脚本完成一些手动完成的工作，这个任务比较容易，所以制作shell安装包的难点就是如何在其中包含二进制内容了。如何在一个文本文件中记录二进制的内容呢？这个问题早就被解决了。答案就是使用Base64编码。在linux下就有base64 这个命令程序就是来做这个工作的。base64可以把文件进行base64的编码，输出的标准输出中去或者把文件中的Base64编码的内容解码。命令base64除了可以对文件的内容做Base64的编解码外，也可以对标准输入中的数据进行Base64编辑码。有了这些预备的知识，那么我们就可以看看具体的如何来做shell的二进制发布包了。

首先假设我们要发布的文件都放在名为test的当前文档中。

1. 把要发布的文件打包

    ```bash
    tar zcf test.tar ./test
    ```
1.  对打好的二进制包做Base64编码

    ```bash
    base64 ./test.tar > test_base64.txt
    ```

1. 准备安装文件的shell文件

    ```bash
    test_base64="";#test_base64中的所有内容
    echo $test_base64|base64 -d >test.tar
    tar zxf test.tar
    rm test.tar
    # 其他的安装代码
    ```

写到这里，我们已经把道理将明白了。但是可不可以写一个shell程序，专门来生成这样的发布包软件呢？当然可以。下面是我写的这个打包程序的源代码。

```bash
function mkpackage(){
    target_dir=$1
    felow_install_shell_command_file=$2
    echo "tar -zcf ._test_dir.tar.gz $target_dir"
    tar -zcf ._test_dir.tar.gz $target_dir
    base64 ._test_dir.tar.gz >._base64
    rm ._test_dir.tar.gz
    printf "test_base64=\"">install.sh
    while IFS='' read -r line || [[ -n "$line" ]]; do
    printf "$line\\" >>install.sh
    printf "n" >>install.sh
    done <./._base64
    rm ._base64
    echo "\"" >>install.sh
    echo 'printf $test_base64|base64 -d >._temp.tar.gz;'>>install.sh
    echo 'tar zxf ._temp.tar.gz' >> install.sh
    echo 'rm ._temp.tar.gz' >>install.sh
    if [[ -e $fellow_install_shell_command_file ]]; then
        cat $fellow_install_shell_command_file >>install.sh
    fi
    chmod +x install.sh
}
function usage(){
   echo "usage:"
   echo "    $1 test_dir [the_command.sh]"
}
if [[ $# != 0 ]]
then
    mkpackage $1 $2
else
   usage $0
fi
```

这个程序接受两个输入参数，第一个表示要打的程序包目录，第二个是解压后，安装动作的shell脚本文件。最后这个程序生成一个名为 install.sh 的文件。执行 install.sh 之后，会首先得到加压的程序包内容，然后执行第二个参数指定的 shell 脚本的内容。这样我们就做了用Shell发布二进制文件的一个打包程序。当然了，在最后发布之前，还需要把这个shell压缩一下。以便消除因为Base64编码而带来的文件长度的变大的影响。


---------------

更新日期 2016-7-13

----------------

一个 Bash 编写的可以执行的安装脚本, 最困难的部分就是如何自己解压自己. 其实这早就有更好的解决方案了, 也就是如何做一个 **自解压文件** 而已. 在Linux 下做一个自解压文件, 其实是很简单的事情, 怎么做呢? 本质就是利用文件追加功能, 以及 Linux 下最常见的管道 `|` 的功能.

在 Linux 最常用的压缩, 解压工具是 `tar`. 在 Linux 下一切都是文件, 标准的输入输出, 当然也是文件, 自然的 `tar` 工具是可以压缩,解压标准输入中的内容. 实际上, 使用 `tar` 的时候, 如果不使用参数 `-f`, 那么 `tar` 命令就是作用在标准输入上的. 也就是说对标准输入做压缩, 或解压.

所以, 假设你已经有一个压缩文件了, 叫做`install.tar.gz` 放在当前目录下, 现在你想要做一个自解压程序, 这个程序自行之后, 自动的解压出来 install.tar.gz 中的内容, 并然后做一些操作, 然后把解压出来的文件删除. 这个 Shell 脚本应该是一下这个样子的.

```shell
TMPDIR=`mktemp -d /tmp/selfextract.XXXXXX`
LINE=9 # the total line of this file add one
tail -n +${LINE}  $0 | tar zx -C ${TMPDIR}
# now your have a dir ${TMPDIR}, and install.tar.gz have unzip into it.
# your can write your code here

# delete the tmp dir
rm -rf ${TMPDIR}
exit
```

假如这个脚本在文件 install.sh 文件中, 那么现在, 使用下面的命令, 把 install.tar.gz 的内容追加到这个文件的末尾.

```shell
cat install.tar.gz >> install.sh
chmod +x install.sh
```
现在 install.sh 就是自解压包了.

现在解释一下上面的脚本. 第二行 `LINE` 应该是这个脚本总行数加1的内容. 它是下一行的一个重要的参数.

第三行是整个脚本的最关键的一行. 这一行以管道符 `|` 为界限分成两个部分.

第一部分是 `tail -n +${LINE} $0` 这个命令是说把这个脚本的第`${LINE}` 行, 以及后面的内容输出的标准输出中去.

第二部分 `tar zx -C ${TMPDIR}` 的作用是, 解压标准输入的内容到 `${TMPDIR}` 目录中去.

`|` 的作用就是把左边的标准输出的内容, 作为右边的标准输入. 所以, 第三行整体就是说把这个脚本文件, 第`$LINE` 行, 及其后的内容, 解压到 `$TMPDIR` 中.

最后一行的 `exit` 非常重要, 不能省略. 为什呢? 因为我们把压缩文件追加到了这个脚本文件的后面, 如果没有这一行, 脚本执行到这一行后, 发现还有内容, 就会继续的按照 bash 来执行. 因为后面的是二进制的内容, 所以, 就难以预料会发生什么事情了. 好的情况是, bash 提示错误, 并退出, 坏的情况, 也许会把你的系统给弄崩溃. 如果加上了 `exit` , bash 执行到最后一行, 就退出了, 就不会有处理后面追加到后面的二进制内容了.
