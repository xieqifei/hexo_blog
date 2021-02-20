---
title: Volantis主题ReadMore自动截取文章内容
top: false
cover: false
toc: true
mathjax: false
date: 2020-06-18 15:56:09
password:
summary: Volantis自带的Readmore需要在文章内容里添加一个<!-- more -->标签或者在md文件的description里写内容。才能在主页显示摘要。如果不添加more标签或者描述，就会显示全文。本文介绍如何添加自动截取摘要的办法。
tags: Volantis
categories: 网站编程
---

## 1：在主题配置文件里添加键值对

添加键值对的目的是，控制截取长度和自动截取功能是否开启。在你的博客目录`\themes\volantis\_config.yml`下添加

```yaml
# Excerpt 
## Auto creat excerpt with not <!--more-->
## Enable will truncate auto_excerpt.lines rows in post head to replace excerpt.
auto_excerpt:
    enable: true
    lines: 5
```

## 2：修改文章代码

修改`\themes\volantis\layout\_partial`文件内容为：

```ejs
<article class="post white-box <%- theme.style.body.effect.join(' ') %> reveal <%= (post.title) ? "" : "no-title" %>">
  <%- partial('meta',{post:post, position:'header'}) %>
  <section class="article typo">
    <div class="article-entry" itemprop="articleBody">
      <% var show_all_content = true %>
      <% if (post.excerpt) { %>
        <% show_all_content = false %>
        <%- post.excerpt %>
      <% }  else if (post.summary) { %>
        <% show_all_content = false %>
        <p><%- post.summary %></p>
      <% } else if (theme.auto_excerpt.enable) { %>
        <% var br_position = 0 %>
        <% for (var br_count = 0; br_count < theme.auto_excerpt.lines; br_count++) { %>
            <% br_position = post.content.indexOf('\n',br_position + 1) %>
            <% if(br_position < 0) { break } %>
        <% } %>
        <% if(br_position > 0) { %>
            <% show_all_content = false %>
            <p><%- post.content.substring(0, br_position + 1) %><p>
        <% } %>
      <% } %>
      <% if (show_all_content) { %>
        <%- post.content %>
        <% } %>
    </div>
    <% if (theme.layout.on_list.readmore) { %>
      <% if ((post.readmore != false) && (show_all_content != true)) { %>
        <div class="button readmore">
          <a href="<%- url_for(post.link || post.path) %>">
            <%- theme.layout.on_list.readmore %>
          </a>
        </div>
      <% } %>
    <% } %>
  </section>
</article>

```

> 上面的代码逻辑，是，如果有more标签，则使用more标签前的文字作为摘要，非则使用md文章scaffolds里的summary内容作为摘要，主题原来用的scaffolds是description。如果需要用description，可以自行替换上面的代码。
>
> 在`scaffolds\post.md`里可以修改新建md文件生成的scaffold。

> 重要！！这种方法是直接提取的文章前5行。如果提取到的内容里，包含代码块的一部分，那么由于渲染问题布局将会出现问题。建议如果你的文章前面里有代码，自行在summary里写摘要，或者more标签前写摘要。

## 3：参考文献

《[Hexo 自动给博文添加 ReadMore](https://blog.zthxxx.me/post/hexo-automatic-add-readmore/)》

