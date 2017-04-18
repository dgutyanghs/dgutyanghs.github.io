---
title: CSS Flex 布局
tags: [日志, 编程]
---

Flex box 布局的问题，是CSS3中的一部分。在去年的时候，我看一本关于CSS和HTML5的书的时候就看到相关的内容，但是因为当时初学CSS和HTML5，所以对书中那些是重点，那些不是重点，还不知道。也许是因为当初的时候，各大浏览器对Flex的支持还没有现在这样的完备，所以书中也只是一带而过。现在Flex已经被几乎所有的现代浏览器所支持了，而且现在native-react 也是用Flex来布局的（这也是我最初深入了解Flex的原因）。看了阮一峰的 [博客](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html "Flex 布局教程：语法篇")之后，自己也用Flex布局改造了本站。

现在自己虽然可以通过查资料来完成Flex的布局，但是还是没有形成自己的体系，很多时候，还是需要对照资料才能达到自己想要的布局。所以，我觉得有必要，对这个知识做个整理。

Flex布局的基本思想是这样的，对一个内容进行布局，其实就是要指定一个节点，其中的子节点按照什么顺序排列，如何处理子节占用空间外的剩余空间。对于子节点自己来说，需要特别的指定自己和别的节点有什么不一样的地方。如果有这些信息了，那么就可以对一个节点其中的子节点进行布局了。因此，可以把Flex的属性分成两部分，一部分是用来指定使用Flex布局的节点的子节点是如何排列的属性，我们称这样的节点为**容器节点**；第二部分是，排列的子节点，这些节点，称为**项目**（item）。

## 0. 使用Flex布局##
要在容器节点上使用Flex布局，那么就把**容器节点**的display属性设置为flex：

```CSS
.container {
    dispaly:-webkit-flex;//
    dispaly: flex;
}
```
所以有第一行，是为了对webkit内核的浏览器进行兼容，现在基本上所有的浏览器都支持flex了，所以第一行，越来越没有必要了。不过，留着也没有关系，最多不过是被第二行的给覆盖。

## 1. 容器节点上的Flex属性##

在容器节点上，可以设置如下的属性：`justify-content`、`align-items`,`align-conten`、`flex-warp`、`flex-flow`和`flex-direction`共六个属性.下面分别介绍着两个属性代表什么含义。

### 1.2. `flex-direction` 属性###

可以想象容器就是一张白纸，我们要在白纸上写字，这里字就是项目节点。对文字的排列，可以是横排，从左向右，然后从上向下的排列，这是现在大部分的文字使用的排列方式；也可以是从上向下，从右向左，比如我国古代的书籍就是用的这种排版方式。

`flex-direction`用来指定主轴的方向的，也就是说安行排列和安列排列的问题。


`flex-direction`有四个值可供选择，`[row|row-reverse|column|column-reverse]`.

+ row 默认值，项目从左向右排列 (&rightarrow;)
+ row-reverse 项目从右向左排列 (&leftarrow;)
+ column 从上向下排列 (&downarrow;)
+ column-reverse 从下向上排列 (&uparrow;)

`flex-direction:row` (&rightarrow;)示例：
```html
<div style="display:flex;flex-direction:row; border:solid red">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
```

效果如下：
<div style="display:flex;flex-direction:row; border:solid red">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>

<br>

`flex-direction:row-reverse` (&leftarrow;)示例：

```html
<div style="display:flex;flex-direction:row-reverse; border:solid red">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
```

效果如下：
<div style="display:flex;flex-direction:row-reverse; border:solid red">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>

<br>

`flex-direction:column`(&downarrow;)示例：

```html
<div style="display:flex;height:120px;border:solid red;flex-direction:column;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
```
效果如下：

<div style="display:flex;height:120px;border:solid red;flex-direction:column;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>

<br>

`flex-direction:column-reverse`(&uparrow;)示例：

```html
<div style="display:flex;height:120px;border:solid red;flex-direction:column;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
```
效果如下：

<div style="display:flex;height:120px;border:solid red;flex-direction:column-reverse;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>

### 1.3. `justify-content` 属性###

我们知道`flex-direction`,是用来指定排版的主轴的，那么现在我们看看对于一行的内容，改如如何处理项目剩余的空白的问题。也就是这一小节要讲的`justify-content`属性的作用。

`justify-content`属性可取的值有一下几个：`[flex-start|flex-end|center|space-around|space-between]`。 他们的意义如下：

+ `flex-start`     表示**把项目_集中_到主轴_起点_**的位置，空白留在主轴结束的位置。对于`flex-direction:row`来说就是从最左边开始排列，剩余的空间积攒到最右边。对`flex-direction:row-reverse`来说，就是从最右端开始排列第一个项目。空白放在最左端。对`flex-direction:column`来说，就是从最上开始排列。等等一次类推。

+ `flex-end`    
正好和`flex-end`相反，**把项目_集中到_主轴的_末端_**，而把空白集中到主轴的开始位置。对于不同的主轴的效果分别如下：    
    0. `row`(&rightarrow;)
        ```html
<div style="display:flex;border:solid red;flex-direction:row;justify-content:flex-end;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
        ```
        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row;justify-content:flex-end;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `row-reverse`(&leftarrow;)
        ```html
    <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:flex-end;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
        <div style="width:30px;height:30px;border:solid #222">3</div>
    </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:flex-end;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column`(&downarrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:flex-end;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:flex-end;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column-reverse`(&uparrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:flex-end;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:flex-end;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

+ `center`    
`center`**把项目_集中_到主轴的_中间_**，主轴的两端，放置空白。对于不同的主轴的效果分别如下：    
    0. `row`(&rightarrow;)
        ```html
<div style="display:flex;border:solid red;flex-direction:row;justify-content:center;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
        ```
        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row;justify-content:center;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `row-reverse`(&leftarrow;)
        ```html
    <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:center;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
        <div style="width:30px;height:30px;border:solid #222">3</div>
    </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:center;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column`(&downarrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:center;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:center;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column-reverse`(&uparrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:center;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:center;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

----

+ `space-around`    
`space-around` 名如其实，**用空白_包围_项目**。对于不同的主轴的效果分别如下：    
    0. `row`(&rightarrow;)
        ```html
<div style="display:flex;border:solid red;flex-direction:row;justify-content:space-around;">
    <div style="width:30px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
        ```
        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row;justify-content:space-around;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `row-reverse`(&leftarrow;)
        ```html
    <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:space-around;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
        <div style="width:30px;height:30px;border:solid #222">3</div>
    </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:space-around;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column`(&downarrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:space-around;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:space-around;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column-reverse`(&uparrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:space-around;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:space-around">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

-----

+ `space-between`    
`space-between` 名如其实，**用空白_隔开_项目**。对于不同的主轴的效果分别如下：    
    0. `row`(&rightarrow;)
        ```html
<div style="display:flex;border:solid red;flex-direction:row;justify-content:space-between;">
    <div style="width:70px;height:30px;border:solid #000">1</div>
    <div style="width:30px;height:30px;border:solid #111">2</div>
    <div style="width:30px;height:30px;border:solid #222">3</div>
</div>
        ```
        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row;justify-content:space-between;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `row-reverse`(&leftarrow;)
        ```html
    <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:space-between;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
        <div style="width:30px;height:30px;border:solid #222">3</div>
    </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;flex-direction:row-reverse;justify-content:space-between;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column`(&downarrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:space-between;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column;justify-content:space-between;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. `column-reverse`(&uparrow;)
        ```html
            <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:space-between;">
                <div style="width:30px;height:30px;border:solid #000">1</div>
                <div style="width:30px;height:30px;border:solid #111">2</div>
                <div style="width:30px;height:30px;border:solid #222">3</div>
            </div>
        ```

        效果如下：
        <div style="display:flex;border:solid red;height:120px;flex-direction:column-reverse;justify-content:space-between">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

## 1.4 `align-items`属性##

我们已经知道如何设置排版的方向，每行中空白的处理方法，但是我们还是没有办法排出我国古代书籍的样式，那种样式是从上向下，从右向左的。我们可以达到从上像下的排列了，但是多行的时候，第一行是从最左边开始的，如果主轴是`column`的话，要想达到古代书籍的排版样式，就需要这个小节介绍的这个属性`align-items`的帮忙。

`align-items`表示的是副轴的排版方式。它有如下几个值可取:`[flex-start|flex-end|center| baseline | stretch]`
下面分别介绍这几个值得意义。

1. `align-items:flex-start`表示**项目集中在_副轴的前端_**，空白累积到末端。这也是默认的值其效果，见上面。

2. `align-items:flex-end`表示**项目集中到_副轴末端_**,空白累积到前端。

    1. 在横排(`flex-direction:row`&rightarrow;)时的效果:    
        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:row;align-items:flex-end;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>
    2. 在横排反序中(`flex-direction:row-reverse`&leftarrow;)时的效果:    
        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:row-reverse;align-items:flex-end;flex-wrap:wrap;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    3. 在竖排(`flex-direction:column`&downarrow;)时的效果:

        <div style="display:flex;border:solid red;height:120px;height:120px;width:120px;flex-direction:column;align-items:flex-end;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>
    4. 在竖排反序中(`flex-direction:column-reverse` &uparrow;)的效果:

        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:column-reverse;align-items:flex-end;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    可以看出,对竖排中使用`align-items:flex-end`就可以达到中国古籍排版的效果。

0. `align-items:center`表示**项目集中到_副轴中间_**,空白分散到两端。

    1. 在横排(`flex-direction:row`&rightarrow;)时的效果:

        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:row;align-items:center;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    2. 在横排反序中(`flex-direction:row-reverse`&leftarrow;)时的效果:

        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:row-reverse;align-items:center;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    3. 在竖排(`flex-direction:column`&downarrow;)时的效果:

        <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:column;align-items:center;flex-wrap:wrap;">
            <div style="width:30px;height:30px;border:solid #000">1</div>
            <div style="width:30px;height:30px;border:solid #111">2</div>
            <div style="width:30px;height:30px;border:solid #222">3</div>
        </div>

    0. 在竖排反序中(`flex-direction:column-reverse` &uparrow;)的效果:

    <div style="display:flex;border:solid red;height:120px;width:120px;flex-direction:column-reverse;align-items:center;flex-wrap:wrap;">
        <div style="width:30px;height:30px;border:solid #000">1</div>
        <div style="width:30px;height:30px;border:solid #111">2</div>
        <div style="width:30px;height:30px;border:solid #222">3</div>
    </div>
