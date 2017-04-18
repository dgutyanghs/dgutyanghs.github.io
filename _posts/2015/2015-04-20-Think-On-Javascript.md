---
title : 我对Javascript的观察和思考
layout: default
tags: [编程]
---


# 我对Javascript的观察和思考 #

## 1. 引子 ##
最近,图样突森破忽然流行开来,什么叫图样突森破呢？如果你问度娘，度娘会会告诉你：

> 图样图森破，是一个流行于百度贴吧、微博、人人的一个热门网络词语。为英文 Too young,too simple的中文谐音。意思为：太年轻，很傻很天真。

不能说这个解释不正确，但是如果我们进一步的发问，为啥偏偏这一句短语流行起来了呢？它的始作俑者有是谁呢？网络流行语，很多的确是莫名奇妙的就流行起来了，但是更多的其实是能够考据出它是从什么地方第一次被使用的。例如,`打酱油`、`屌丝`、`给力` 等等。
那么这个最近忽然就火起来的*图样突森破* 它是什么路子呢？是民众自主自发的呢？还是像`给力`宫廷御用，扩散民间的？如果从百度的解释来看，好像更像第一种情况，而且好像始作俑者不可考据的样子。但是实际上，这个*图样突森破* 和 `给力`的性质更加接近，它最初是一个`大老虎`在被记者问道，香港特首董建华的连任是不是其钦定的，而恼羞成怒，大骂记者的话，其中就有，`You are so native.` `Too young too simple.`
这个事情过去多年了，近来在谣传，这个大老虎要被拿下的时候，这个词忽然流行起来，而且像《澎湃》新闻,这样的官方媒体都毫不避讳的使用，可能是在一点程度上证实谣传。
我们说 *图样突森破*, 就是它的原意------ `Too young,too sample`.我不是在黑Javascript，所以这里的我把Young和Sample作褒义使用。
那么问题就来了，Javascirpt真的是`Too young Too sample` 吗？

## 2. Is Javascript  Young?##
对这个问题，有Javascript 的历史有些了解的人可能立马就说了，Javascript在编程语言中，虽然算不得*Old*，但是也绝对不*Young*。Javascript中1995年被发明，到现在已经整整20个年头了。
的确是这样的。如果单单从发明的时间来计算Javascript可以算得上程序语言中的爷爷辈的了。但是我们仍然可以说Javascript是Young的。
我说Javascript是Young，有两个含义，第一、Javascript的版本不停的更新，Javascript这个语言本身的是不断更新的。这一点我们可以和C语言相比，C语言，他的标准很久不更新一次，只是这段时间才开始有从新的活跃起来，但是它的更新，更多的是对库函数的添加，在语法上并没有什么新的内容，或者很少。而且语言标准的更新和语言的实现还不一样,C语言,他最新的版本是C11,但是GCC的默认版本现在依旧还是C86,马上发布的GCC5,才把默认的支持改为了C11.但Javascript不一样,他的版本在保持先后兼容的同时，有在不停的添加新的语法。举个例子来说，Javascript的最新的ES6标准添加了对class的支持，如果说这个功能熟悉其他面向对象的语言，你还能马上理解的话，那么**变量的解构赋值**就是一个全新的内容了。
常规的Javascript赋值是这样的：

```javascript
var a=1;
var a=1,b=2;
```
解构赋值的语法是这样的：

```javascript
var [a,b]=[1,2]
var { foo,bar }={foo:"hello",bar:"world",...} //(*)
```
这两个语法在ES5中是完全错误的语法，尤其是（*）标记的这个对象的解构赋值语法，在Javascript中，`{}`原来是构造一个对象的直接量的语法，现在这里的表示，象这样:

```javascript
{
   foo:"hello",
   bar:"world",
   other_key:other_value,
    ...
}
```

但是我们看到在（\*）标记的对象解构赋值语句中，只有对象的key的内容，没有value，第一次看到这样的代码，如果刚好赋值语句后面的对象有不是一个对象的直接量，那就比较难以理解。
从Javascript的实现来说也是一样,基本上Javascript的实现走在他的规范的前面.几乎都是各家浏览器厂商实现了新的功能之后,才推动新规范的产生.所以如果,我们从Javascript语言的版本演化来说,Javascript是Young的,是朝气十足的,是活力四射的.
从Javascript可以在那些地方被使用来说，Javascript也是Young。Javascirpt用在Web页面，操作DOM节点，这固然有20年的时间了，但是Javascirpt一直在不停的扩展其使用的领域。例如，在服务器端最成功的Node.js，Node.js 和WebKit结合起来写PC程序，也已经很成熟了，各种使用Javascript写移动端应用的尝试,也在如火如荼的发展。
而且，现在,在Web页面，流行的HTML5技术，其核心就是浏览器扩大对Javascirpt API的支持，如果HTML5没有Javascirpt的支持，那么很难想象它还能不能像现在这样的玄酷。Javascirpt现在已经打通了前端、后端，PC，Mobile。这些新的努力，在使得Javascirpt扩大了其使用范围，同时也给Javascirpt增加了新的活力.
有些努力中的一些是功能的扩展，例如现在的各种JS框架，像JQuery之类;有些甚至添加了新的语法和代码组织的方法，例如Node.js遵循的Command.Js的规范，来对模块提供支持。因此，从这一角度来说，我们也可以说Javascript是Young的。套用儒家的说法,可以说Javascript是**“苟日新，日日新，又日新"**的。
第3个角度，从编程语言的高度来看，Javascript也是Young的，原来Javascript只是一个语言，最多是有多个方言，多个公司对同一个标准的不同实现，或者是按照自己的需要对这个语言作一定的改造。对计算机语言来说，还是一套语法。就像现在Java已经不在只是一个编程语言，而成了一个编程语言的运行平台，Javascript也在变成一个编程语言的运行平台。Jython，Groovy等等运行在JVM上。TypeScript,CaffeScript等语言最后的目标语言是Javascript。编程语言的高度来说，Javascript已经成为了一个平台，从这一方面来说，我们也可以说Javascript是Young的。

## 3. Is Javascript Simple?##
Javascript 简单吗？从设计的初衷来说，Javascript最初就是为设计人员准备的一个语言，这也就意味着，这个语言不能很复杂，不能很难学，或者生产效率很低。现在我们承认了Javascript是**Too young,Too simple**，那么它是不是也是**so native**呢？不是的.Javascript从来都不是实验室中的模型,或玩具语言,而是大量用在实践中的,而且可以作很多工作的语言.从本质上来说，Javascript是一个通用型的计算机语言，所以，其他语言能作的事情，基本上Javascript也可以做。虽然，Javascript 不是解决问题的最后的银弹，但是可以说Javascipt也绝对不是编程语言中的*银样蜡枪头*。
那么Javascript有会不会像LISP语言那样，虽然语法简单，入门容易，但是却很难完全掌握呢？ 也不是的，Javascript绝对不像LISP那样***艳春白雪，曲高和寡***。Javascript现在很受欢迎，刚刚赢得了2014年的年度编程语言。在2015年1月流行语言排行榜中，Javascript是第7位。这个榜单是根据人们对一个编程语言的搜索兴趣得到的。如果根据Githup上提交的代码来看，Javascript早就是TOP1了，具体的情况，我们可以到[GITHUB INFO](http://githut.info/) 上看看。而且我一直绝的Javascript所以不想Java,C/C++那样排在编程语言排行榜单上的前3甲,重要的原因是Javascript too simple,所以大家不用搜索就能解决问题,所以搜索的热度就不如C/C++,Java.
那么回到我们的问题上来，对这个问题的回答，我们的答案是，***Javascirpt is Sample and Powerful !***.

维基百科如此对Javascript进行定义：

> JavaScript is classified as a **prototype-based** **scripting** language with **dynamic typing** and **first-class functions**. This mix of features makes it a **multi-paradigm** language, supporting **object-oriented**, **imperative**, and **functional** programming styles.

从上面的高亮的关键字中，可以知道，Javascript是一个基于原型的，动态类型，函数一等公民的脚本语言。支持多种编程的范式：面向对象，命令式和函数式编程。其中这一段对Javascript的特性归纳的很完整，只有一点没有提到，那就是Javascript是弱类型的语言。
Javascript这么多的特性，我们可以叫做它的**性**，Javascript的性**使得**它**Young and simple**，所以我们可以说**Young and simple**是Javascript的**命**。命就是说是必然的。因为Javascript语言本身的这些特性，使得Javascirpt必然的Young and simple。当然了，这是相对于其他语言，或者说是相对于其他没有Javascript这些特点的语言来说的。

## 4. Javascript的势 ##
首先我们对Javascript的历史作一个分期。这里我们借用地质年代的名词对Javascript的发展作一个概括，没什么特别的依据，只是我自己对Javascript使用的一点体会。

1. 古生代------Javascript语言自身发展的时期
  这是Javascript创建以来的最初阶段，这个阶段***Javascript is simple and weak !***，自身的功能有限，可操作的对象有限，写起来也麻烦。在Javascript最初的版本中，甚至连正则表达式都没有，更不要期望什么函数库了。这种状态持续了很久，但是不是说这种状态下Javascript没有进化，这种状态下Javascript的进化是以Javascript语言自身的进化来体现的。比如后来的版本中引入真则表达式等等内容。
2. 中生代------类库混战的时期
   中生代，Javascript出现了一些类库，使用这些类库来在WEB端写Javascript，大家觉的，真是太幸福了。现在我们能知道的Javascript的类库有那些呢？我想大家脱口而出的可能就是jQuery. jQuery是Javascript DOM类库中的优胜者，曾经还有其他的DOM 类库,比如现在几乎已经销声匿迹的Prototype.js . 我清楚的记得在《Javascript权威指南》这本书的第5版中还同时的介绍jQuery和Prototype这两个框架，那个时候还没有jQuery和Prototype等等几个框架还没有显出谁更有优势。但这本书的第6版的时候，就没有Prototype等其他类库的介绍了，反而对jQuery类库作了更多的描述。
3. 古近级------Browser War II
   我们知道有两次世界大战，在IT史上也有两次的浏览器大战（Browser war）。第一次比较出名，以IE最后把领航者（Navigator）干掉而结束。然后开始了IE6的漫长的独裁时期。这个时候IE成了上网的标识。一直到现在很多人还只把IE作为浏览器。
   第二次浏览器大战从什么时候开始，就不好界定了，第一次浏览器大战只有交战的上方。那个时候比的是谁支持更多的新的tag。在第二次浏览器大战的时候，变成了多国混战，在IE长时间坐吃山空之后，最先发力的就是火狐，然后陆续的有出现了Safari、 Opera以及Chrome。所以，如果以市场上出现新的竞争者来作为第二次浏览器大战的开始的话，那么会很难界定。但是如果关注这次浏览器大战的特点，我们会发现，这次浏览器大战，是以***效率***作为关键字来竞争的。尤其是Javascript的执行效率。如果从这点来说，那么Chrome，以及它使用的V8引擎无疑这次大战的主角，也是最珍贵的遗产。
4. 新近纪 ------ Javascript全面爆发的时期
   Javascript全面爆发从Node.js的开始。Node.js使用V8引擎，使得Javascript脱离WEB环境运行。Javascript脱离WEB环境运行，就这一点来说，不是Node.js的首创，但是直到Node.js的时候，这种方式才大行其道，处理Node.js自身的原因外，还和V8的超高者执行效率有关。

网络用语中有个吐槽神句，叫 ***You can You up, No can no BB*** 。 对Javascript 我们**BB**了这么多，但是大家还是对Javascript到底有多**Can**，还没有一个直观的映像，现在我们就从Node.js开始，看看Javascript到底有多***Can***。

## 5. Javascript的技##

### 5.1 Node.js###
首先我们看看Node.js官网上是如何介绍它自己的：

> Node.js® is a platform **built on Chrome's JavaScript runtime** for easily building ***fast***, ***scalable*** network applications. Node.js uses an **event-driven**, **non-blocking I/O** model that makes it ***lightweight*** and ***efficient***, perfect for ***data-intensive real-time*** applications that run across distributed devices.

第一句说出了Node.js的靠山------V8.第二句则是说出了Node.js的模型和强项。一个事物要宣传自己，难免会放大自己的优点，如果使用者对这种放大失望的话，就会有俗话说的：吹破牛皮,顶破天。现在我们看看Node.js有没有吹牛皮,看看我们斜体加粗的关键字，在多大程度上得到了兑现。

* fast & efficient
  这是速度和效率.在判断一个语言和一个框架的时候,当是这是首先要考量的内容。但是通常我们讲的速度和效率指的是**1）程序在机器上的运行效率**，这是速度和效率的重要的一个方面，但是不是全部的内容。从速度和效率上考量一个语言或框架好坏，还有一个重要的方面就是，使用它的**2）程序员的生产效率**。也就是通常说的写的快不快的问题。
  1. 对于执行效率，语言或框架本身的特性以及程序员都会对其造成影响。**通常来说低级的语言写的程序执行效率比高级语言要高，编译性语言的执行效率要比解释性语言的效率要高。**无论那种语言，好的算法总是要比坏的算法的效率要高。也就是说，在执行效率上，**人的因素，是在于程序员是否有足够的经验，在语言或框架已提供的结构基础上写出适合的算法**。
  所以，执行效率，更多的是语言或框架本身的编程器，解释器的实现的问题。
  2.  对于生产效率的问题刚好和执行相率的问题相反。越高级的语言，生产效率越高，产生错误的概率越小。动态语言的效率高于静态的，解释性的高于编译性的。当然，对一个语言经验丰富的程序要当然要比一个新手生产效率高。但是对某个单独的程序员来说，当他同时熟悉了两种语言的时候，就会发现其中有一个语言解决问题更加快捷。这是因为语言或框架本身在语法或预构建的结构上的不同造成的。
  所以，生产效率的问题，是语言或框架语法或提供的预定义结构的问题。
  3. Node.js 的效率问题
  因为Node.js使用的是V8，所以第一个问题，就是V8的执行效率的问题。我们可以通过[juila主页](http://julialang.org/)中的数据来看以下。
  对于生产效率的问题，那么我们看一下，我们看一个Node.js官网上提供的例子，看看用Node.js 写一个http服务的情况：

  ```javascript
    var http = require('http');
    http.createServer(function (req, res) {
        res.writeHead(200, {'Content-Type': 'text/plain'});
        res.end('Hello World\n');
    }).listen(1337, '127.0.0.1');
    console.log('Server running at http://127.0.0.1:1337/');
  ```
  可以看到也就数行代码，如果我们用C语言为apache或Ngix写一个mod呢？同样是实现最最基本的helloworld的功能，代码行数应该是Node.js的十倍左右。

* lightweight
 使用动态语言的的开发效率来作页面的开发，这个本身不是什么新鲜事。比如常见的黄金搭档LAMP中P之的就是PHP或者Python。但是这个不是lightweight的，因为其实PHP或Python的最后还是Apache的一个mod中执行。但是Node.js不用，它可以自己单独的执行，不用Apache或Nginx助力。
* data-intensive real-time
  这是Node.js的目标，或者是说限制，因为对于计算密集型的程序，Node.js是不适合的。那么什么是数据密集呢？其实就是数据的转移。在实际的编程实践中，绝大多数的任务都是数据的转移，比如网页:静态的内容的话，也是要把文本中的数据，首相转移到内存，然后在转移到网络。动态的内容，可能需要数据从数据库中转移到内存，然后在转移到网络。和数据密集对应的是计算密集。比如要计算PI的$10^{30}$位。那就必须很长时间的运算从能完成，而不是数据的挪移了。
* scalable
  我的理解，这是代码组织的问题。脚本语言,最初是为了快速的解决小问题而存在的,脚本往往都是***用完立即扔***的,所以很多脚本语言并没有为大规模代码的组织提供解决方法.这里面突出的例子有Shell,和Javascript.在Shell和Javascript自身的语法中,没有其他语言的include或者import等类似的指令.没有这样的指令,结果就是无法作对代码作模块使用.如果要作复杂的程序,就可能引起命名冲突的问题.Node.js对ES5作了扩展,为Javascirpt引入了模块的概念.Node.js基本上遵循CommonJS的规范.从[NPM的官网](https://www.npmjs.com/)上我们可以查找NodeJS中可以使用的公开第3方模块,几乎涵盖了编程的每一个邻域.NPM上模块的数目是很多的一个数字,而且是持续的增长的.

我今天第一次看到是139,007,3个小时候之后,这个数字变成了139,034.从这个数字的增长也可以看出Node.js生态系统的蓬勃发展.

想用Nodejs作什么呢?

写爬虫?那么可以使用node-crawler.

想在Node.js中用jquery来分析文档?没问题,看看cheerio.

啥啥啥?Nodejs不能作图像识别的工作?那么可以看看npm中的opencv.

想在Nodejs中允许自己写的C库有不想自己写Wrab?看看ffi.

... ...

Node.js可以说是Javascript在服务器端的成功案例.那么在桌面呢?下面我们看看在桌面上Javascript有啥作为.

### 5.2 Javascript Run on desktop ###


#### 5.2.1 失败的HTA ####

在HTML取得成功之后,很快就有人想到用HTML+Javascript来作应用程序.早在1999年,微软就提出了HTML Application的概念.只要把HTML写好,后缀名改成.hta,那么页面就变成了一个独立的应用程序了.更多的细节,查看wikipedia中[HTA](http://en.wikipedia.org/wiki/HTML_Application)的介绍.就我搜集到的资料来看,这应该是使用WEB技术作桌面应用程序的最早的尝试.但是HTA没有推广开来.所谓**时势造英雄**,就是说的这种情况,有时候不是你看不清趋势,而是没有认清现实环境------也就是**时势**.

#### 5.2.2 来自开源圣地的Atom-Shell####

上节我们说到使用WEB技术作桌面开发,第一个螃蟹的微软失败了,原因我们归结为时势不到.那么到Node.js红起来之后的现实环境是不是允许WEB技术来作桌面开发呢?答案是肯定的.而且这次是百花齐放.使用WEB技术开发桌面应用程序的有: Atom-Shell , NW.js, heX,Chrome Apps, Titanium, TideSDK,... ...
其中比较有代表性的有Atom-Shell,来自Github社区开发,Github自己开源的IDE的[Atom](https://atom.io/)就是基于Atom-Shell来构建的.最近的Facebook开源的IDE工具Nuclide也是基于Atom-Shell的.

![reactnative IDE](http://nuclide.io/images/screenshot.png "ReactNative IDE-- Nuclide")

#### 5.2.3 NW.js ####

NW.js要比Atom-Shell还要早一些,最初叫Node-webkit,这个是来自英特尔的工程师作的框架,使用的Chromium 和 node.js来写桌面程序.这个也有一批拥趸者.而且也有不少的优秀的程序是基于NW.js来做的比如:

IDE LightTable<br>
![Lighttable](http://lighttable.com/images/screens.png "LightTable")

视频客户端Popcorn Time<br>
![Popcorn Time](https://i.imgur.com/MdZR313.gif "Popcon Time")

更多的应用见NW.js的[宣传页](https://github.com/nwjs/nw.js/wiki/List-of-apps-and-companies-using-nw.js)


#### 5.2.4 heX ####

heX是网易开源出来的解决方案.网易自己用他作了,有道词典的客户端.<br>
![有道词典](http://cidian.youdao.com/pics/index-2.png "有道词典")

## 5.3 移动端呢? ##
上面我们看到Javascript已经在服务器端和PC段都获得了不菲的成绩,那么Javascript在移动端的发展如何呢?
Javascript向移动进发的步骤比PC甚至更早.前几年Facebook力挺HTML5,尝试使用混杂的形式来开发Facebook的手机端的应用.但是后来因为HTML5效率问题,体验不佳,最后宣布失败.关于混杂开发,有几个框架[PhoneGap](http://phonegap.com/),[Apache Cordova](https://cordova.apache.org/)等,他们的思路基本上和Facebook早期的思路一致,都是使用HTML和CSS做界面,Javascript作动作响应,然后把移动端的WEB,加一个Native的壳.还有一个叫[NativeScript](https://www.nativescript.org/),虽然他说使用XML+CSS+Javascript来开发native应用.但是本质上还是没有脱离上面的思路.只是增强了Javascript的功能.但是允许效率还是不高. 那么是不是说Javascript在移动端没有前途呢?现在还不好说.最近Faceboook做了第二次的努力,开源了自己的解决方案叫[ React Native](http://www.reactnative.com/). 这个解决方案开源不到一个月,就获得了11000+赞.
其实其他的解决方案,效率不高的问题主要是对DOM的操作的问题.前面的几个解决方案,虽然各有优缺点,但是都没有解决这个痛点,所以虽然这些解决方法能够提高开发的效率,依然少有人使用.
从社区的反馈来说,React Native对与程序的效率问题解决的比较成功.现在,Facebook只开源了React Native的iOS的解决方案.但是从官网上看,Android也会公布,等Facebook把把这个的解决方案也公布出来的时候,我想安卓的app将会迎来app的大爆发.为啥这样说呢?想想iOS的开发,开发者除了付出人力成本外,还要做硬件的投入,Mac机器,iPhone,开发者ID,等等,安卓的开发成本,基本上只有程序员的人力成本.等React Native的安卓解决方案也开源了,那么人力的成本也大大的降低了.
