---
layout: default
title: Benighted on the Ben.
excerpt: An unplanned bivouac on Ben Nevis.
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org29993a4">1. 纯代码解决UIScrollView 的 AutoLayout</a>
<ul>
<li><a href="#org8cffbd7">1.1. UIScrollView的特殊性</a></li>
<li><a href="#org5d7a118">1.2. 解决思路</a>
<ul>
<li><a href="#org5cfb007">1.2.1. step 1</a></li>
<li><a href="#org703033b">1.2.2. step 2</a></li>
<li><a href="#org77c8a42">1.2.3. step 3</a></li>
<li><a href="#org1a05b71">1.2.4. 结语</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>


<a id="org29993a4"></a>

# 纯代码解决UIScrollView 的 AutoLayout


<a id="org8cffbd7"></a>

## UIScrollView的特殊性

   相对于普通的View来说, UIScrollView 的AutoLayout 比较特殊.
因为它的 left/right/top/bottom space 是相对于 UIScrollView的 contentSize 而不是 bounds 来确定的.
如果你尝试用 UIScrollView和它 subview 的left/right/top/bottom 来互相决定大小的时候，
系统会警告你"Has ambiguous scrollable content width/height".


<a id="org5d7a118"></a>

## 解决思路


<a id="org5cfb007"></a>

### step 1

  在scrollView和它的subviews之间,先添加一个 containView,
对齐scrollview的\*top\* & **leading\*边界,
 scrollView的\*width** & **height** 对齐containView,
从而使scrollView 的 \*contentSize\*确定下来. (contentSize的大小等于containView.size)
见以下代码:(使用了[PureLayout开源lib)](https:https://github.com/PureLayout/PureLayout)

    [containView autoPinEdge:ALEdgeLeading toEdge:ALEdgeLeading ofView:scrollView];
    [containView autoPinEdge:ALEdgeTop  toEdge:ALEdgeTop  ofView:scrollView];
    [containView autoPinEdge:ALEdgeTrailing toEdge:ALEdgeTrailing ofView:scrollView];
    [containView autoPinEdge:ALEdgeBottom toEdge:ALEdgeBottom ofView:scrollView];
    [containView autoMatchDimension:ALDimensionHeight toDimension:ALDimensionHeight ofView:scrollView];


<a id="org703033b"></a>

### step 2

保存下面的NSLayoutConstraint,如果scrollView的宽度变化(如:网络请求回来的内容增加了),则需要更新此约束.

    NSLayoutConstraint *contentWidthConstraint = [containView autoMatchDimension:ALDimensionWidth toDimension:ALDimensionWidth ofView:scrollView];
    self.contentWidthConstraint = contentWidthConstraint;


<a id="org77c8a42"></a>

### step 3

添加scrollView 和它的superView 约束. 此处的superView为self.view

    [scrollView autoPinEdge:ALEdgeTrailing toEdge:ALEdgeTrailing ofView:self.view];
    [scrollView autoPinToBottomLayoutGuideOfViewController:self withInset:0];
    [scrollView autoPinEdge:ALEdgeTop toEdge:ALEdgeTop ofView:self.view];
    [scrollView autoPinEdge:ALEdgeLeading toEdge:ALEdgeLeading ofView:self.view];

ok, 大功告成!


<a id="org1a05b71"></a>

### 结语

"Talk is cheap, show me the code  &#x2013; Linus Torvalds "
使用storyboard来实现的例子,已有方案在网上了.请自行google.
考虑到大公司里,多人的iOS团队都要求用纯代码开发,OK, 我们不使用storyboard, 用纯代码实现它
例子代码:<https://github.com/dgutyanghs/UIScrollView-AutoLayout-NotUse-Storyboard>
Have fun!

