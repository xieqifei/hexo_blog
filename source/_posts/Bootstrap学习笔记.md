---
title: Bootstrap学习笔记
top: false
cover: false
toc: true
mathjax: false
date: 2020-03-01 16:34:13
password:
summary: Bootstrap是一个非常优秀的CSS样式库，利用这个库，可以更少的关注网站样式，而更多的去专注于网站内容的安排。她同时还提供了非常不错的布局方案。响应式布局，灵活的容器修改。
tags: Bootstrap
categories: 学习笔记
---

# 0：入门例子

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Bootstrap</title>
    <link rel="stylesheet" href="css/bootstrap.min.css">
</head>
<body>
    <div class="container">
        <h1>网站天下</h1>
        <p>hello-world</p>
    </div>

</body>
</html>
```

`lang="zh-CN"`修改网站文字为中文文字。

`<meta name="viewport" content="width=device-width,initial-scale=1">`设置网站为自适应屏幕尺寸网站，缩放比例为不缩放。

`<link rel="stylesheet" href="css/bootstrap.min.css">`导入本地bootstrap.css样式。

# 1：容器

`.container`+`tab`=`<div class="container"></div>`固定宽度居中显示

`.container-fluid`全宽容器

变化容器中一行一共包含12个格子。建立一个列表，每三个小格（col-xl-3）为一大格，需要一共4个大格可将其填满。

```html
    <div class="container">
        <div class="row">
            <div class="col-xl-3" style="background-color: #0c5460">111</div>
            <div class="col-xl-3" style="background-color: #005cbf">222</div>
            <div class="col-xl-3" style="background-color: #1e7e34">333</div>
            <div class="col-xl-3" style="background-color: #6c757d">444</div>
        </div>
    </div>
```

**栅格等级：**`col`特小、`col-sm-*`小、`col=md-*`中、`col-lg-*`大、`col-xl-*`超宽。

各尺寸表示在不同尺寸屏幕上的显示方式，比如使用，`col-xl`则在屏幕宽度变为1000像素左右时，原来的栅格就换行显示，每个栅格占据一行。

若直接使用`col`来修饰，那么，即便在很小的屏幕上，栅格也不会换行。相当于网页直接缩放。不推荐使用。

栅格后的数字非必须。不添加数字则采用`flexbox`布局

- 设置一列宽度

`col-6`占6/12

`col-auto`可变宽度，随内容变化，变化的单位为1小格

- 混合布局

`col-sm-6 col-lg-3`大屏时每行四个，中屏每行2个，小屏每行一个。

- 对齐

垂直对齐`align-items-center`、`align-items-xl-end`

水平对齐`justify-content-start/center/end/around/between`

- 消除内边距

`<div class="row no-gutters">`

- 排序

`order-*`*值越小排序越前。默认为0

- 偏移

`offset-*`偏移*个格子，也可加断点

- margin移动

`mr-auto``ml-auto`

# 2：排版

```html
<h1>
    主标题<small> 小号字体</small>
</h1>

<div class="display-1">比标题字体大</div>

<div class="leader">变大强调</div>

<p><mark>高亮</mark></p>
<p>普通<span class="mark">高亮</span>文本</p>

<p><del>删除线</del></p>
<p><s>删除线</s></p>

<p><ins>下划线</ins></p>
<p><u>下划线</u></p>

<p><strong>粗体</strong></p>
<p><b>粗体</b></p>

<p><em>斜体</em></p>
<p><i>斜体</i></p>

<p><abbr title="请输入你的邮箱" class="initialism">email</abbr></p>

<blockquote class="blockquote">
    引用在此
</blockquote>

<code>代码块</code>

<var>变量斜体</var>

<kbd>用户输入，黑色背景白色字体</kbd>
```

# 3：图片和图片框

```html
<img src="../img/lg.img" alt="" class="img-fluid img-thumbnail w-100">

<div>
    <img src="" class="rounded float-left">//圆角
    <img src="" class="rounded float-right">
    <img src="" class="rounded d-block mx-auto">//转为块级元素，然后居中对齐
</div>

<picture>
	<source srcset="../images/lg.jpg" media="(min-width:992px)">
    <source srcset="../images/md.jpg" media="(min-width:576px)">
    <img src="../images/sm.jpg" alt="">
</picture>
<!--设置不同屏幕显示不同尺寸图片-->
```

# 4：表格

```html
<body>
    <div class="container"><br>
            <div class="table-responsive">
            <table class="table">
                <thead>
                <tr>
                    <th>HeadingHeadingHeading</th>
                    <th>HeadingHeadingHeading</th>
                    <th>HeadingHeadingHeading</th>
                </tr>
                </thead>
                <tbody>
                <tr>
                    <td>CellCellCell</td>
                    <td>CellCellCell</td>
                    <td>CellCellCell</td>
                </tr>
                </tbody>
            </table>
            </div>
    </div>
</body>
```

**条纹状表格：**使用`.table-striped`样式定义`<tbody>`可以产生逐行交替变化的背景色。同`.table-dark`结合使用改变变化背景色为黑夜主题。

**表格边框：**使用`。table-bordered`产生表格边框与间隙。`table-borderless`无边界表格。

**悬停效果：**`table-hover`悬停时，背景高亮。

**缩紧表格**：`table—sm`可以将表格padding值缩减为原来的一般。

**Caption表格辅助标题：**`<caption>`默认是隐藏的，协助屏幕阅读用户找到表格，了解表格内容，并决定是否阅读它。

**字体和背景色：**`table-dark、table-primary、table-secondary、table-warnung、table-success、table-danger、table-info`为浅色字体色，将`table`替换为`bg`则改为背景色。能加在tr或td上。

**响应式滚动条：**在`table`上层添加`<div class="table-responsive">`

# 5：边框

```html
<span class="border-top border-secondary rounded-top"></span>
<span class="rounded-circle">圆形或椭圆边框</span>
<span class="rounded-pill">圆形或胶囊型</span>
```

`border-top-0`清除边框。

# 6：颜色—Display显示属性

```html
<div class="d-inline"></div>
<div class="d-xl-none">断点，当屏幕过大时不显示</div>
```

