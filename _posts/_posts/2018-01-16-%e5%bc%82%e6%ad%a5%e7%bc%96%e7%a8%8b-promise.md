---
id: 264
title: 异步编程-Promise
date: 2018-01-16T16:08:49+00:00
author: skottiewang
layout: post
guid: http://skottiewang.com/?p=264
permalink: '/2018/01/16/%e5%bc%82%e6%ad%a5%e7%bc%96%e7%a8%8b-promise/'
categories:
  - Code
tags:
  - 前端
  - 编程
---
创建一个Promise构造函数来实现promise模式:

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript"> var Promise = function () {
          this.callbacks = []
        }

        Promise.prototype = {
          construct: Promise,
          resolve: function (result) {
            this.complete('resolve', result)
          },

          reject: function (result) {
            this.complete('reject', result)
          },

          complete: function (type, result) {
            while (this.callbacks[0]) {
              this.callbacks.shift()[type](result)
            }
          },

          then: function (successHandler, failedHandler) {
            this.callbacks.push({
              resolve: successHandler,
              reject: failedHandler
            })
            return this
          }
        }
</code></pre>

<!--more-->

  * callbacks: 用于管理回调函数
  * resolve: 请求成功时执行的方法
  * reject:请求失败时执行的方法
  * complete: 执行回调
  * then：绑定回调函数

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript">// 调用promise
var promise = new Promise()

        var delay1 = function () {
          console.log('delay1-start')
          setTimeout(function () {
            promise.resolve('数据1')
          }, 1000)
          console.log(222)
          return promise
        }

        var callback = function (re) {
          re = re + '数据2'
          console.log('callback', re)
          promise.resolve(re)
        }
        delay1().then(callback)
</code></pre>

### 分析：

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript">// 第一步
var delay1 = function () {
          console.log('delay1-start')
          setTimeout(function () {
            promise.resolve('数据1')
          }, 1000)
          console.log(222)
          return promise
        }
</code></pre>

  * 执行delay1，这个方法返回一个promise对象，并将在一秒后异步执行promise.resolve('数据1')

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript"> var callback = function (re) {
          re = re + '数据2'
          console.log('callback', re)
          promise.resolve(re)
        }
</code></pre>

  * 执行顺序：delay1()\--->return promise\--->1秒后执行setTimeout\--->setTimeout的结果将被指定为callback的参数\--->执行callback函数。
  * then方法将为delay1()中setTimeout的异步执行操作指定回调函数
  * 这个then方法还将返回一个promise对象。可以继续调用，如下～

<pre class="line-numbers prism-highlight" data-start="1"><code class="language-javascript">delay1().then(callback1).then(callback2);
</code></pre>

打印结果：
  
delay1-start
  
222
  
// 一秒后
  
callback1 数据1数据2