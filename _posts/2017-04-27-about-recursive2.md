---
layout: post
title: 关于递归的思考(2)
date: 2017-04-27 
tag: 编程
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgc4b83a4">1. 关于递归的思考&#x2013;续</a>
<ul>
<li><a href="#org586209c">1.1. 阶乘函数factorial的另一种算法</a></li>
</ul>
</li>
</ul>
</div>
</div>

<a id="orgc4b83a4"></a>

# 关于递归的思考(2)


<a id="org586209c"></a>

## 阶乘函数factorial的另一种算法

上一篇递归的文章提到factorial的方法.

    def factorial(x):
          if x == 1:
              return 1
          else:
              return x * factorial(x - 1)
    
    factorial(5)

我们来演算下它的过程,如下:

    factorial(5)
    5 * factorial(4)
    5 * (4 * factorial(3))
    5 * (4 * (3 * factorial(2)))
    5 * (4 * (3 * (2 * factorial(1))))
    5 * (4 * (3 * (2 * 1)))
    5 * (4 * (3 * 2))
    5 * (4 * 6)
    5 * 24
    120

现在我们把函数修改一下:

      def factorial_iterator(x, result):
          if x == 1:
              return result
    
          return factorial_iterator(x -1, x * result)
    
    factorial_iterator(5, 1)

演算下它的过程,如下:

    factorial_iterator(5, 1)
    factorial_iterator(4, 5)
    factorial_iterator(3, 20)
    factorial_iterator(2, 60)
    factorial_iterator(1, 120)
    120

思考下两种方法的差异&#x2026;

1.  可以看出factorial<sub>iterator</sub>(x, result) 每迭代一次, 都会把中间的结果存到 result中.

比如算到factorial<sub>iterator</sub>(3, 20)这里是, result = 20. 
想一想,假如计算机这时出现异常,导致前面两步:factorial<sub>iterator</sub>(4, 5), factorial<sub>iterator</sub>(5, 1)的压栈crash或其它异常丢失.
factorial<sub>iterator</sub>(3, 20)往下面步骤演算,依然能得到正确的结果.
而factorial(x)面对这种情况则无能为力了. 
所以如果编译器能优化的话, factorial<sub>iterator</sub>(x, result)每演算一步就可以release 前面的栈空间.栈空间只需一个, 不会增长, 防止多次调用时(试想下x = 10000)栈溢出问题. 

**总结**
两种方法的时间复杂度都是O(n).
方法1:factorial(x),思路清晰,当x值很大时,会产生栈空间溢出.
方法2:factorial<sub>iterator</sub>(x,result),进行了迭代改良，不会持续增长栈空间. 但笔者在python3.5的环境下测试x=1000时,
却也出现报错:

    RuntimeError: maximum recursion depth exceeded

估计编译器未做优化所致. 有兴趣的读者可以试试C语言版,不会有上述问题.

     #include <stdlib.h>
    
    long double fact(int x, long double result) {
       if (x == 1)
         return result;
       else{
         return fact(x - 1, x * result); 
       }
     }
    
     int main(void) {
     long double ret = fact(1000, 1); 
       printf("ret = %Lf \n", ret );
       return 0;
     }

Have fun!

