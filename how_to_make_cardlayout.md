# 如何实现卡片式布局

卡片式布局有两种：一种是每个卡片的尺寸相同，卡片的排列非常标准；还有一种是使用不同尺寸的卡片组成的布局，卡片间没有固定的排序。这里只讨论前者布局的实现方式。

我们先来看下flutter官网中的卡片式布局

![flutter](.\images\flutter.jpg)

我们先总结一下这个卡片式布局有什么特点：

1. 卡片的背景颜色与卡片之外的背景颜色不同，感觉卡片颜色要比外面的背景颜色更亮一点
2. 每个卡片之间的间距相同
3. 卡片边框有阴影，感觉浮在背景上
4. 卡片边框有一点圆角

我们先来实现一下卡片的布局，主要考虑两点，相等的间距和较暗的背景颜色。

假设我们要做一个3行4列的卡片式布局。其实有好多种做法，可以用div，浮动，table，grid布局，flex布局等。最简单的应该是grid布局，听说grid布局兼容性不太好，我特意查了下就IE不支持，那就无所谓了，我们先拿grid开搞。grid布局大家可能用得比较少，所以我会带着大家一点点学会这个布局怎么用。

## 用grid布局实现

grid，顾名思义，这是一个二维网格布局，它有一些基本概念如下（瞄一眼就行，现在不要细究，待会用到了再去了解）

![grid基本概念](.\images\grid基本概念.jpg)

首先，给容器声明其display属性为grid或inline-grid，容器内部就会变成grid布局，如果不给grid设置其内部细节的属性，那么默认align-content: normal，justify-content: normal，align-items: normal，justify-items: normal；现在看不懂这些属性没关系，后面一点点看，现在知道是个什么效果就行了。

我们在html中写点结构：

```html
<div id="container">
        <div>1</div>
        <div>2</div>
        <div>3</div>
        <div>4</div>
        <div>5</div>
        <div>6</div>
</div>
```

容器样式

```html
<style>
#container{
    width: 600px;
    height: 600px;
    border:1px solid black;
    display: grid;
}
</style>
```

可以看到效果：

<img src=".\images\grid1.jpg" alt="grid1" style="zoom:50%;" />

至于为什么是这个效果，后面在讲解了grid的一些细节属性后再来解释。我们先研究用法。grid可以通过grid-template-columns设置有多少列，每列宽度占多少，比如，我们想要网格布局按四列排列，行数自适应，那么可以设置grid-template-columns:150px 150px 150px 150px，当然这个150是我们计算出来的。可以看到效果：

<img src=".\images\grid2.jpg" alt="grid2" style="zoom:50%;" />

如果不想要人工计算，可以把所有的150px换成1fr，fr单位是grid布局中的一个特殊单位，它类似flex布局中给item设置的flex占比值，它会根据你给其设置的占比值自动分配宽度，比如，我这里改成2fr 1fr 1fr，那么效果就是第一列占容器一半，第二列和第三列平分另外的容器的一半，如图所示：

<img src=".\images\grid3.jpg" alt="grid3" style="zoom:50%;" />

有grid-template-columns，那么聪明的同学应该能猜到就有grid-template-rows，没错，grid-template-rows就是设置有多少行，每行的高度是多少，设置规则同grid-template-rows。其实学到这里，就可以做出3行4列的布局了。

在html结构中，多添加6个子div，然后在style中修改代码如下所示

```
#container{
    width: 600px;
    height: 600px;
    border:1px solid black;
    display: grid;
    grid-template-columns: 1fr 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr 1fr;
}
```

效果如下

<img src=".\images\grid4.jpg" alt="grid4" style="zoom:50%;" />

但是明显还有缺陷，就是每个单位之间的间距(基本概念图中的gap)，这个怎么设置呢？可以通过column-gap和row-gap设置，比如，我设置column-gap:10px，row-gap:10px，那么效果如下：

<img src=".\images\grid5.jpg" alt="grid5" style="zoom:50%;" />

至此，一个3行4列的卡片式布局就完成了，虽然grid布局还有很多技巧没讲，但这不是本节重点，剩下的就是把之前的一个坑填一下。

经过我的研究，align-items和justify-items都是对单元内部的子元素进行对齐方向上的设置，align是纵向上的设置，justify是横向上的设置，如设置align-items:center，数字就会垂直居中；设置justify-items，数字就会水平居中。

而align-content和justify-content是对网格进行设置，实际上网格不一定就是贴合父容器的，比如我们设置

```
#container{
    width: 600px;
    height: 600px;
    border:1px solid black;
    display: grid;
    grid-template-columns: 150px 150px 150px;
    grid-template-rows: 150px 150px;
}
```

那么可以看到，网格大小是小于容器的

<img src=".\images\grid6.jpg" alt="grid6" style="zoom:50%;" />

此时再对align-content和justify-content就有效果，例如，我们设置align-content:center就是让整个网格垂直居中，justify-content就是水平居中。

<img src=".\images\grid7.jpg" alt="grid7" style="zoom:50%;" />

justify-content默认值为flex-start，align-content默认值为stretch，justify-items默认值为start，align-items默认值应该是baseline，那个坑是container里放置了6个无样式的div，container仅设置了固定宽高和grid布局，这里grid应该是自动根据div数量设置了网格，好吧，似乎这个坑目前是填不上，那么放在以后在填吧。那么布局和间距的问题就解决了，接下来搞一下边框的圆角和阴影。

实现卡片的边框效果

圆角就不用讲了，直接border-radius。主要讲下box-shadow，这玩意我之前几乎没用过，box-shadow第一个参数是水平方向上的阴影偏移量，如果是负值就是是左侧边框的阴影，反之就是右侧边框阴影；第二个参数是竖直方向上的阴影偏移量，如果是负值就是上侧边框阴影，反之就是下册边框阴影。第三个(可选)参数是模糊程度，数值越大越模糊，第四个(可选)参数是阴影大小，数值越大阴影越大，第五个(可选)参数是阴影颜色。需要注意的是，boxShadow 属性把一个或多个下拉阴影添加到框上。该属性是一个**用逗号分隔阴影的列表**，所以你可以用下述形式同时给不同方向的边框添加阴影

```css
.card{
    width: 100%;
    height: 100%;
    background-color: #ccc;
    border-radius: 4px;
    box-shadow: -1px -1px 5px #888888,1px 1px 5px #888888;
}
```

可以看到效果：

<img src=".\images\card.jpg" alt="card" style="zoom: 80%;" />

是不是有一种浮上来的感觉了呢。

最后，是颜色搭配，经过试验我发现只要颜色色差不是特别大，都还可以接受；而且就算卡片颜色和外部背景颜色相同，因为阴影效果在就能看出来这是一个卡片，所以没必要在这里做文章。最后放一张整体效果图！

<img src=".\images\卡片布局整体效果图.jpg" alt="卡片布局整体效果图" style="zoom:50%;" />

后来我在看sof的时候，注意到，随着布局的放大和缩小，他的grid能够自适应。举个例子，比如当前布局大小为500X500，里面有5X5个格子，后来我动态地将布局的宽度拉到450*500，那么列数就会少一列，行数会多出一行，这就是自适应。那么我们来想想它是怎么实现的呢？

首先，要把当前网页的窗口作为响应式父容器，对其样式设置：

```css
html,body{
    background-color: #F5F5F7;
    height: 100%;
    margin:0;
    padding: 0;
    overflow: hidden;
}
```

再设置grid容器的width和height，这里我们给width设置40%，height设置60%。这样，当我们弹出F12的开发者工具并且拉动时，就可以看到grid大小也随之发生变化。

但此时，grid中的格子也会随之放大或缩小，这不是我们想要的效果，格子的大小应当是固定不变的，当外部布局变化时，我只想改变列数，而不是让格子大小发生变化。我找到的解决方案是这样的：
在repeat中使用auto-fit或auto-fill参数来让列数根据容器宽度自适应(参考[这篇文章](https://blog.csdn.net/weixin_42190844/article/details/109579915))  
这样的做法有点瑕疵就是容器右边可能会有留白，如果想要把这部分留白平均分给每一列，可以使用minmax函数。具体看index.html