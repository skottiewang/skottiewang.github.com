---
id: 288
title: Promise实战-基于VUE的Modal组件
date: 2018-01-18T10:29:57+00:00
author: skottiewang
layout: post
guid: http://skottiewang.com/?p=288
permalink: '/2018/01/18/promise%e5%ae%9e%e6%88%98-%e5%9f%ba%e4%ba%8evue%e7%9a%84modal%e7%bb%84%e4%bb%b6/'
categories:
  - Code
---
##### 希望实现的效果

Modal弹出后点击取消则Modal立刻消失，点击OK，弹出Toast的轻提示组件，1秒延迟后，Toast与Modal同时消失。
  
![](http://skottiewang.com/wp-content/uploads/2018/01/QQ20180118-1@2x.png)

<!--more-->

##### 实现思路

用户在parent中通过Modal.alert传入title,message,btnGroup三个参数，其中btnGroup是包含了按钮text与按钮点击事件onPress的对象数组。Toast是我写好的组件

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript">Modal.alert(
          'Delete',
          'Are you sure',
          [
            {text: 'Cancel', onPress: () =&gt; console.log('第1个按钮被点击了')},
            {text: 'Ok',
              onPress: () =&gt; new Promise((resolve) =&gt; {
                Toast({
                  message: '操作成功',
                  duration: 1000
                })
                setTimeout(() =&gt; {
                  resolve()
                }, 1000)
              })
            }
          ]
        )
</code></pre>

在Modal组件内部，v-for btnGroup中的item

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-html">&lt;button :class="wrapClass"
                  @click="handleAction(item)"
                  v-for="(item, index, text) in btnGroup"&gt;
            {{ btnGroup[index].text }}
          &lt;/button&gt;
</code></pre>

##### 按钮点击事件

  * 上面定义了onPress事件，那么先执行onPress事件中的Toast,再执行setTimeout，一秒后Promise请求执行成功。
  * 执行promise.then方法，Modal被关闭。

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript"> handleAction (btn) {
        if (btn.onPress && typeof btn.onPress === 'function') {
          const promise = btn.onPress()
          if (promise) {
            promise.then(() =&gt; {
              this.visible = false
            })
          } else {
            this.visible = false
          }
        } else {
          this.visible = false
        }
      }
</code></pre>

##### 思考：

能否在onpress中不使用promise？
  
试验如下，parent中：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript"> Modal.alert(
          'Delete',
          'Are you sure',
          [
            {text: 'Cancel'},
            {text: 'Ok',
              onPress: () =&gt; {
                Toast({
                  iconClass: 'check',
                  message: '操作成功'
                })
              }
            }
          ]
        )
</code></pre>

组件内部

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript"> handleAction (btn) {
        btn.onPress()
        this.visible = false
 }
</code></pre>

这时Modal框将被立刻关闭，如果要实现延迟，就需要在this,visible=false外面套一个setTimeout，这时延迟的数值将被作为参数传入，这样也可以实现，但是在外部的代码书写会比较奇怪。

##### 小结

Promise的异步操作并不会被立即执行，而是当Promise执行完成后通过.then()方法来执行。这使得用户可以将Modal的关闭延迟书写在组件外部的Promise中。