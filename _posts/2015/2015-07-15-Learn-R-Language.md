---
layout: default
title: R語言學習（5）——對R語言中的一些疑惑的理解
tags: [编程,读书]
---

# 關於貝努力分布#

rbinom(n,size,pro)
這個函數按照是貝努利來參數隨機數的函數。要描述一個貝努力分布，需要兩個參數，實驗
的次數，每次實驗室成功的概率pro。這裏的n是用來表示要生成多少個隨機數。也就是說使
用size和pro描述的隨機密度曲線來產生n的隨機數。有size和pro描述的貝努力分布的隨機
變量取值範圍是[0,size]。如果pro是一數，那麼n個書記數都是按照一樣的密度曲線來生成
的，如果pro是一個向量，那麼n個隨機數使用不同的曲線來生成。第i個隨機數使用
binom(size,pro[i])來生成。

# tapply(v,f,func)#
這個函數的意義是，使用參數f對限量v進行分組，然後對每一組都使用func來進行運算。

# 泛型函數和類#
在R語言中提供了一種泛型函數,他們可以接受不同類的對象,調用這個對象合適的方法.泛型函數自身不
做計算,它為對象查找合適的函數,並調用找到的函數.例如泛型函數`print(x)`,在內部print首先找到
x的class屬性(一個字符串向量),然後把print和class屬性中的每個元素,通過"."連接起來,如果找到
其中的一個函數,那麼就調用這個函數.具體來說,如果x的class屬性是c("foo","bar"),那麼
`print()`首先,查找print.foo() 是否存在,如果不存在繼續查找print.bar(),如果print.foo或
print.bar存在,那麼就調用首先找到的那個,如果都不存在,調用print.default()函數.如果
print.default也不存在就出出錯.下面我們用代碼做個實驗:

```R
vdata <- 1:5
print(vdata)# [1] 1 2 3 4 5
class(vdata) <- "vdata" # set class attributes
print.vdata <- function(vdata)
{
    print(vdata[1])
}
print.vdata(vdata) #[1] 1
print(vdata) # [1] 1
```

第1行我們定義了一個數據vdata,它是數值向量.第2行,我們打印這個數據項.第3行,我們把vdata的
class屬性設置為"vdata".第4~7行,我們定義了一個叫做`print.vdata()`的函數.第8行我們直接調
用`print.vdata()`,第9行,調用`print()`泛型函數,第9行的結果和第8行一樣,和第2行不一樣.也就
是說,泛型函數print(x),如果x的類屬性是vdata的話,其內部調用了print.vdata函數.

可以使用`methods(class="classname")`來查看類classname可用的泛型函數.

`methods(func)`可以用來查看函數func支持的類型.

對於一個函數,在交互控制臺中,可以通過輸入函數名來看到源代碼,例如`print.vdata`顯示出來的就
是我們上面代碼中的第4~7行中的內容,但是`print`顯示的卻是如下的內容:

```
function (x, ...)
UseMethod("print")
<bytecode: 0x000000000b06f3f0>
<environment: namespace:base>
```

UseMethod()表明了print方法是一個泛型方法.現在我們看看`method(print)`輸出的內容:

```
[1] print.acf*
[2] print.anova*
[3] print.aov*
...
[183] print.vdata
```

對與print.vdata我們知道如何獲取其內容,但是`print.acf*`我們不能直接通過函數名來獲取其中代
碼,函數`getAnyWhere("name_of_objec")`可以獲得有通用符號的函數.例如`getAnywhere("print.acf")`得到的結果是:

```
A single object matching 'print.acf' was found
It was found in the following places
  registered S3 method for print from namespace stats
  namespace:stats
with value

function (x, digits = 3L, ...)
{
    type <- match(x$type, c("correlation", "covariance", "partial"))
    msg <- c("Autocorrelations", "Autocovariances", "Partial autocorrelations")
    cat("\n", msg[type], " of series ", sQuote(x$series), ", by lag\n\n",
        sep = "")
    nser <- ncol(x$lag)
    if (type != 2)
        x$acf <- round(x$acf, digits)
    if (nser == 1) {
        acfs <- setNames(drop(x$acf), format(drop(x$lag), digits = 3L))
        print(acfs, digits = digits, ...)
    }
    else {
        acfs <- format(x$acf, ...)
        lags <- format(x$lag, digits = 3L)
        acfs <- array(paste0(acfs, " (", lags, ")"), dim = dim(x$acf))
        dimnames(acfs) <- list(rep("", nrow(x$lag)), x$snames,
            x$snames)
        print(acfs, quote = FALSE, ...)
    }
    invisible(x)
}
<bytecode: 0x000000000ac42e10>
<environment: namespace:stats>
```


可以看到這是一個S3方法,通過函數`getS3method("print","acf")`可以獲得print.acf方法的代
碼:

```
function (x, digits = 3L, ...)
{
    type <- match(x$type, c("correlation", "covariance", "partial"))
    msg <- c("Autocorrelations", "Autocovariances", "Partial autocorrelations")
    cat("\n", msg[type], " of series ", sQuote(x$series), ", by lag\n\n",
        sep = "")
    nser <- ncol(x$lag)
    if (type != 2)
        x$acf <- round(x$acf, digits)
    if (nser == 1) {
        acfs <- setNames(drop(x$acf), format(drop(x$lag), digits = 3L))
        print(acfs, digits = digits, ...)
    }
    else {
        acfs <- format(x$acf, ...)
        lags <- format(x$lag, digits = 3L)
        acfs <- array(paste0(acfs, " (", lags, ")"), dim = dim(x$acf))
        dimnames(acfs) <- list(rep("", nrow(x$lag)), x$snames,
            x$snames)
        print(acfs, quote = FALSE, ...)
    }
    invisible(x)
}
<bytecode: 0x000000000ac42e10>
<environment: namespace:stats>
```

另外,通过getAnyWhere()函数查到的信息中,我们能看到print.acf的名称空间为stats,
那么可以直接通过加上名字空间直接来查看它的函数定义,名称空间使用"::"来处理,和C++中的一样,
如果"::"不起作用的话,就使用":::".

# S4类#
R语言是函数式编程语言,本身没有面向对象的功能,在面向对象的编程方法兴盛之后,R语言也添加了面向对象的功能.
更糟糕的是R中还2次添加了面向对象的功能,S3类,是第一次向R中添加的面向对象的机制.
S4类是第二次向R中添加的面向对象的机制.S4类通过下面的几个函数来完成面向对象的编程.

1. setClass()    
setClass 用来创建一个对象,它包含如下的参数,其中大部分是可选的参数.
    Class,字符串,用来表示这个类的类名.这个不是可选的.
    + slots 是一个命名向量或列表,其中名称是类的成员名,值是成员的类型名.如果从不同的包中导入的类,
    有相同的名字,这个时候成员的类型就需要有一个"package"的属性来区分这个成员的类型是什么.
    slots也允许使用一个没有名字的字符串向量,每个字符串作为一个成员名,成员的类型都是"ANY".
    + contains  这个类的父类的名字.如果父类是"VIRTUAL",那么将创建一个虚类.
    + prototype 为这个类的成员(slots)提供默认值的对象.默认情况下,使用每个父类的prototype对象,
    来作为这个类的prototype.
    + validity 如果提供的话,应该是这个类的一个方法(如果参数对这个类是合法的话,就返回TRUE),
    这个函数的参数,可以看作是python中的slef或Java中的this.
    + sealed 如果值为TRUE的话,这个类定义被冻结,在这个类名上调用其他的setClass方法会失败
    + package 这个类的可选的包名.一个类的的包名是这个类定义的位置.(目录?)
setClass以不可见的形式返回的是一个生产函数.这个函数用来产生对象.对这个函数的调用,将调用这个类的
[new]( #new)方法.传给这个函数的所有的参数,将会传给initialize方法.
如果这个类或他的父类没有定义initalize方法,默认的方法期望参数的名字和solts的名字一样.如果一个类是一个虚类,
那么无论使用生产函数,还是使用new方法,都会返回一个错误.

2. setGeneric()
这个函数通过给定的名字来用来创建一个泛型函数.泛型函数,也就是说,它根据参数的类型调用其他的函数.
它有如下的参数:
    + name 这个泛型函数的名字.

2. setMethod(f,signature=character(),definition,...) removeMethod    
这两个函数用来为一个类添加去掉一个方法.
其中参数为:
    + f 通用函数或者是这个函数的字符串形式的名字.
    + signature f函数的形式参数匹配名,包括参数名和参数的类型.
    + definition 一个函数定义,如果对f的调用匹配了这个类的signature的话,就会有一个方法调用.
    + sealed 默认值是FALSE,如果为TRUE,这样定义的方法不能在被其他人通过调用setMethod函数修改.


# 繪圖#
绘图函数分为3种:
1. 高级: 在一个新的图形上绘制内容
2. 低级: 在已经存在的图形上绘制内容
1. 交互: 允许使用交互的向图形上添加内容,尤其是和信息格式相关的内容

## 1.1 plot()函數##
1. plot(x) 如果x是時間序列,那么自變量時間,应变量是x的值.如果x是向量,自變量是下標,應變量是元素值.
2. plot(x,y) 或plot(list(x=x,y=y)) 如果x,y是向量,這個形式畫出x,y的散點圖,其中y是縱軸,x是橫軸.
3. plot(f) 如果f是一個因子對象,那麼畫出這個因子對象的直方圖
4. plot(f,v)如果f是因子對象,v是數值向量,那麼畫出v的數據中每個f水平的盒子圖.
5. plot(df) df是數據框,這個圖給出的是在數據框中的變量分佈圖.
6. plot(~expre) 命名對象expre的分佈圖
7. plot(y~expre) y是隨著expre命名對象變化圖像.

我們使用下面的代碼來看看這些格式的不同:

```R
x <- seq(0,2*pi,length=100)
y <- sin(x)
jpeg(file="%d.jpg",bg="white")
plot(x) #1.jpg
plot(x,y)#2.jpg
plot(list(x=x,y=y))#3.jpg
plot(~x)# 4.jpg
plot(y~x)#5.jpg
```

結果為:

|---
| 函数     | 图形          | 备注
|----------|--------------|------
| plot(x)  | ![plot(x)]({{site.baseurl}}/ref/2015-07-15/1.jpg "plot(x)" ) | x向量中的值是随着小标变化而变化.
| plot(x,y) | ![plot(x,y)]({{site.baseurl}}/ref/2015-07-15/2.jpg "plot(x,y)") | y与x的散点图
| plot(list(x=x,y=y)) | <img atr="plot(list(x=x,y=y)" src="{{site.baseurl}}/ref/2015-07-15/3.jpg" name="plot(list(x=x,y=y))"/> |列表中的y与x散点图
| plot(~x)           | <img atr="plot(~x)" src="{{site.baseurl}}/ref/2015-07-15/4.jpg" name="plot(~x)"/> | x的分布,因为x是[0,2&pi;]中<br>均匀分布的100个点,<br>图中显示的基本也是密度均匀的
| plot(y~x)          | <img atr="plot(y~x)" src="{{site.baseurl}}/ref/2015-07-15/5.jpg" name="plot(y~x)"/> | y 随x的分布图
|---

## 1.2 绘制多个数据##

pairs(X) X为矩阵或数据框.

coplot(x,y,z);如果z是factor,那么实际是按照z对x,y分组,然后对每个组绘制x,y散点图.
如果z是连续的数据,那么把z分成等距的间隔,在每个间隔中绘制x,y的散点图.x,y,z的长度必须一样.

## 1.3 数据显示##

hist(x) 绘制x的直方图
hist(x,nclass=m),建议把数据分成m个矩形

## 1.4 高级绘图函数的参数##

高级绘图函数大部分都支持一下几个参数

|---
| 参数 | 释义
|-|-
|  add=TRUE | 把高级绘制函数强制变为低级绘图函数,<br>也就是说不打开一个新的绘图设备,<br>而是在当前的设备上绘制
|  axes=FALS | 不绘制坐标轴
|  log= "x" <br> log="y"<br>log="xy" | x,y 或 xy轴为对数坐标
|  type= "l"   |  画线
| type= "o" | 画点
| type= "b"  | 线点都画(both)
| col= "blue" | 设置绘图的颜色
|---


# 低级绘图函数#

lines(x,y)根据x,y绘制线条

poins(x,y)根据x,y绘制点
