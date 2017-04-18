---
title: 《HTML5:Up and Running》笔记
layout: default
tags: [读书,编程]
---

# 1. HTML5的文档结构#
## 1.1 文档类型##
在HTML页面的第一行写如下的内容：

```HTML
<!DOCTYPE html>
```
注意，这行内容的前面不能有空格，否则浏览器不认为是文档类型声明。这样的文档类型声明的就是HTML5文档。
## 1.2 根节点##
一般根节点html通过属性lang指定页面使用自然语言。

```html
<html lang="cn">
```
## 1.3 <head> 节点##

<head> 节点一般是<html>节点的第一个子节点。其中包含的是页面的元信息。例如这个页面的编码方式，这个页面标题等等。
## 1.4 <meta>标签##
在HTML5文件中指定编码使用<meta>标签,有两种方式：
* 传统HTML文档的方式:

    ```html
    <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
    ```
* HTML5文档简化格式：

    ```html
<meta charset="utf-8">
    ```
## 1.5 <link>标签#
常规的连接(`<a href>`)只是简单的指向其他的页面。而<link>标签是用来解释为什么这个页面要指向其他页面的。HTML5把连接关系分成了两类：**连到外部的资源**;**超连接**--连接到其他的页面。

连接的行为由连接的关系决定。例如：

```html
<link rel="stylesheet" href="url">
<linke ref="alternate" title="" href="url">
```
`rel="stylesheet"`表示是外部资源。
`ref="alternate"` 表示连接到一个其他的资源，这个资源的内容和这个页面一样，只是格式不一样，例如到一个ATOM订阅的连接或到一个PDF版本。

其他的连接关系：
* ref="archives"    
暗示应用的文档描述了一个记录，文档或其他相关历史资料的集合。一个博客的索引页，可以通过ref="arachives"来连到这个博客的所有提交的索引去。
* ref="author"    
用来连到关于作者的页面上。也可是以是个`mailto:`地址。
* rel="external"    
这暗示了这个连接，连到站外。
* ref="start",ref="prev",ref="next"    
如果两个页面属于一个系列的或，用来定义两个页面之间的关系。
* ref="icon"    
这个用来指定这个页面对应的ICON文件。

# HTML5#
