---
id: 288
title: Promise实战-基于VUE的Modal组件
categories:
  - 前端
---
##### 希望实现的效果

Modal弹出后点击取消则Modal立刻消失，点击OK，弹出Toast的轻提示组件，1秒延迟后，Toast与Modal同时消失。

![](http://skottiewang.com/wp-content/uploads/2018/01/QQ20180118-1@2x.png)

<!--more-->

##### 实现思路

用户在parent中通过Modal.alert传入title,message,btnGroup三个参数，其中btnGroup是包含了按钮text与按钮点击事件onPress的对象数组。Toast是我写好的组件


```javascript
      promiseModal () {
        Modal.alert('Delete',
          'Are you sure',
          [
            {text: 'Cancel'},
            {text: 'Ok',
              onPress: () => new Promise((resolve) => {
                Toast({
                  message: '操作成功',
                  duration: 1000
                })
                setTimeout(() => {
                  resolve()
                }, 1000)
              })
            }
          ]
        )
      },
```


在Modal组件内部，v-for btnGroup中的item

```javascript

<Button :class="wrapClass"
		@click="handleAction(item)"
        v-for="(item, index, text) in btnGroup">
        	{{ btnGroup[index].text }}
 </Button>
```


##### 按钮点击事件

  * 上面定义了onPress事件，那么先执行onPress事件中的Toast,再执行setTimeout，一秒后Promise请求执行成功。
  * 执行promise.then方法，Modal被关闭。


```javascript

 handleAction (btn) {
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
```
##### 思考：

能否在onpress中不使用promise？

试验如下，parent中：

```javascript
 
  Modal.alert(
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
```
组件内部

 
```javascript

handleAction (btn) {
	btn.onPress()
    this.visible = false
  }
```
这时Modal框将被立刻关闭，如果要实现延迟，就需要在this,visible=false外面套一个setTimeout，这时延迟的数值将被作为参数传入，这样也可以实现，但是在外部的代码书写会比较奇怪。

##### 小结

Promise的异步操作并不会被立即执行，而是当Promise执行完成后通过.then()方法来执行。这使得用户可以将Modal的关闭延迟书写在组件外部的Promise中。