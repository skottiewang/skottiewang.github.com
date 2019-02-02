---
id: 329
title: Img与background-image区别实例
date: 2018-02-05T18:16:20+00:00
author: skottiewang
layout: post
guid: http://skottiewang.com/?p=329
permalink: '/2018/02/05/img%e4%b8%8ebackground-image%e5%8c%ba%e5%88%ab%e5%ae%9e%e4%be%8b/'
categories:
  - Code
---
在ImagePicker组件中，需要实现图片的上传与预览。

  * 容器宽度为屏幕宽度减去margin再除4。
  * 高度等于宽度，保持正方形。

下图所示：

<!--more-->

![](http://skottiewang.com/wp-content/uploads/2018/02/%E5%9B%BE1.jpg)

如果使用img标签来做，在移动端遇到横拍的图片，会出现下图所示情况，图片不能占满外部容器。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-html"> &lt;img :src="colitem.url"/&gt;
</code></pre>

![](http://skottiewang.com/wp-content/uploads/2018/02/%E5%9B%BE2.jpg)

如果使用background-image来做：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-html"> &lt;div :style="`background-image:url(${colitem.url})`"&gt;&lt;/div&gt;
</code></pre>

![](http://skottiewang.com/wp-content/uploads/2018/02/%E5%9B%BE3.jpg)

### 浅析

  * background-image会对外部容器做宽高的适配，img只是在容器内把图片读取出来。
  * img是html标签，img去掉css后依然可以完整展示内容；background-image是css方法，需要依靠css样式来修饰。
  * img适合用作不做任何修饰的图片，如广告图，产品列表图；background-image适合做修饰性的背景图，如加了标签，按钮，外部容器的产品列表图。

### 补充

  * http://www.cnblogs.com/ivy-xu/p/6638459.html
  * https://segmentfault.com/q/1010000003064116