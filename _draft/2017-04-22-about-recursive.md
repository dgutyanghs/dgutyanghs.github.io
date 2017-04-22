<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgb4ffe7c">1. 关于递归的思考</a>
<ul>
<li><a href="#org27c2008">1.1. 大事化小</a></li>
<li><a href="#org734e133">1.2. 怎样把大象放进冰箱？</a></li>
<li><a href="#orgf2f1412">1.3. 总结</a></li>
</ul>
</li>
</ul>
</div>
</div>

<a id="orgb4ffe7c"></a>

# 关于递归的思考


<a id="org27c2008"></a>

## 大事化小

把一个大的难题演化, 变成相同的难题，但规模缩小。如此重复，直到最后可解。
如, 求阶乘factorial(5):

    def factorial(x):
          if x == 1:
              return 1
          else:
              return x * factorial(x - 1)
    
    
    factorial(5)

如上面的代码 factorial(5)先规模缩小:

    3次循环后的结果：
    return 4 * factorial(4 -1)
    return 3 * factorial(3 -1)
    return 2 * factorial(2 -1)
    再次运行时：factorial(1)
    return 1

规模缩小到factorial(1)，问题解决;


<a id="org734e133"></a>

## 怎样把大象放进冰箱？

怎样把大象放进冰箱？这是小品里，宋丹丹问赵本山的脑筋急转弯。
答案出奇简单，是分三步：

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-right">1</th>
<th scope="col" class="org-left">把冰箱门打开</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-right">2</td>
<td class="org-left">把大象放进去</td>
</tr>
</tbody>

<tbody>
<tr>
<td class="org-right">3</td>
<td class="org-left">把门关上</td>
</tr>
</tbody>
</table>

让我们回到大学里数据结构的经典问题“[汉诺塔](http://baike.baidu.com/item/%25E6%25B1%2589%25E8%25AF%25BA%25E5%25A1%2594/3468295)".
64个大小不一大黄金盘子，从A柱子移动到C柱子。
解决步骤：

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-right" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-right">1</th>
<th scope="col" class="org-left">把A柱上面63个盘子看作一个整体，先移动B柱子</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-right">2</td>
<td class="org-left">把A柱第64个盘子移动到C柱</td>
</tr>
</tbody>

<tbody>
<tr>
<td class="org-right">3</td>
<td class="org-left">把B柱的63个盘子一同移到C柱</td>
</tr>
</tbody>
</table>

    def hanoi(n, a='A', b='B', c='C'): 
        if n == 1:              #2.把A柱第64个盘子移动到C柱
            print('move', a, '-->', c) 
            return
    
        hanoi(n-1, a, c, b)  #1.把A柱上面63个盘子，先移动B柱子
        print('move', a, '-->', c)
        hanoi(n-1, b, a, c) #3.把B柱的63个盘子一同移到C柱
    
    print(hanoi(64))

我们把难题缩到最小， 如果不是64个盘子，而是只有2个，大家在去看上面的代码hanoi输出:

    move A ---> B //先把上面的盘子移动B柱
    move A ---> C //再把剩下的一个盘子移动到C柱子。
    move B ---> C //然后把B柱上的盘子移动C柱。

没有问题， 大家应该能想清楚。现在大家的疑问是：当盘子是64个时，

**把A柱上面63个盘子先移动B柱子** 行的通吗？
我觉得可以这样想，要把63盘子移动到B柱子， 先要把前面的62个移动开。问题又回来了， 要把第62个盘子移开，先要把前面的61个移动开&#x2026;.
我们已经知道只有2个盘子的情况，是可行的。 那么数学归纳法应该可以证明的，可惜笔者的数学能力有限。


<a id="orgf2f1412"></a>

## 总结

大事化小，把一个大的问题规模缩小，直至最后能求解，是递归的重要思考方式，各位在日常的编程中可多试试， 几行代码就能把问题解决，简洁优美。

