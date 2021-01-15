# Vue源码解析之nextTick

>  目标是写给自己看的所以就写的稍微简单一点.

在vue中 , 有一种情况 : 

```vue
<template>
  <div>
    <div ref="name">{{ name }}</div>
    <button @click="updateName">updateName</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: 'test1'
    }
  },
  methods: {
    updateName() {
      this.name = 'test2'
      console.log(this.$refs.name.textContent)//test1
      this.$nextTick(()=>{
          console.log(this.$refs.name.textContent)//test2
      })
    }
  }
}
</script>
```

如上代码 : 

页面中显示了一个插值表达式`{{name}}`,按钮绑定了一个`updateName`的函数,当点击按钮,会将`name`改为`test2`,这时候打印`ref`的`textContent`,打印出的值为`test1`.

那我们想打印的肯定是`vue`响应式更新后的`dom`,理所当然的以为是`test2 ` , 那这到底是为什么呢 ? 



## 响应式

这里简单介绍下vue的响应式 :

当我们定义了一个组件时,就相应的创建了一个`Watcher `, 在组件里定义了一个`data`,这时候vue会对data里的数据进行拦截(通过`Object.defineProperty`),设置`get`和`set`函数 , 

`get`函数:在读取的时候 , 创建一个dep对象,并且在dep对象下的sub对象上`push` `watcher`对象，产生依赖，同时在`watcher`添加dep的相关信息.

`set`函数:在设置的时候,改变值的同时执行`notify`,通知dep中所有的`watcher`执行`update`更新dom. 接下来虚拟`dom`这方面的知识.这里先不做拓展.只要知道,`vue`会将`oldVNode`和newVNode进行`diff`比对,算出哪里变了,然后就进行更新`dom`.

这里着重来看下`watcher`的`update`方法.

```js
  update () {
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      this.run()
    } else {
      queueWatcher(this)
    }
  }
```

很显然这里的`queueWatcher`就是核心函数,我们继续深入

```javascript
export function queueWatcher (watcher: Watcher) {
  const id = watcher.id
  if (has[id] == null) {
    has[id] = true
    if (!flushing) {
      queue.push(watcher)
    } else {
      // if already flushing, splice the watcher based on its id
      // if already past its id, it will be run next immediately.
      let i = queue.length - 1
      while (i > index && queue[i].id > watcher.id) {
        i--
      }
      queue.splice(i + 1, 0, watcher)
    }
    // queue the flush
    if (!waiting) {
      waiting = true

      if (process.env.NODE_ENV !== 'production' && !config.async) {
        flushSchedulerQueue()
        return
      }
      nextTick(flushSchedulerQueue)
    }
  }
}
```

如上源码, 我们从第一句代码执行过来, 首先获取该 `id = watcher.id`; 然后判断该id是否存在 `if (has[id] == null) {}` , 如果已经存在则直接跳过,不存在则执行if语句内部代码, 并且标记哈希表has[id] = true; 

用于下次检验。如果 `flushing` 为false的话, 则把该`watcher`对象push到队列中, 考虑到一些情况, 比如正在更新队列中的`watcher`时, 又有事件塞入进来怎么处理? 因此这边加了一个flushing来表示队列的更新状态。

如果加入队列到更新状态时，又分为两种情况:

1. 这个`watcher`还没有处理, 就找到这个`watcher`在队列中的位置, 并且把新的放在后面, 比如如下代码:

```javascript
if (!flushing) {
  queue.push(watcher)
}
```

2. 如果`watcher`已经更新过了, 就把这个`watcher`再放到当前执行的下一位, 当前的`watcher`处理完成后, 立即会处理这个最新的。如下代码:

```javascript
else {
  // if already flushing, splice the watcher based on its id
  // if already past its id, it will be run next immediately.
  let i = queue.length - 1
  while (i > index && queue[i].id > watcher.id) {
    i--
  }
  queue.splice(i + 1, 0, watcher)
}
```

3. `waiting` 为false, 等待下一个tick时, 会执行刷新队列。 如果不是正式环境的话, 会直接 调用该函数 `flushSchedulerQueue`; (源码在: vue/src/core/observer/scheduler.js) 中。否则的话, 把该函数放入 nextTick 函数延迟处理。

```javascript
if (!waiting) {
  waiting = true

  if (process.env.NODE_ENV !== 'production' && !config.async) {
    flushSchedulerQueue()
    return
  }
  nextTick(flushSchedulerQueue)
}
```

这里我们看到vue进行响应式更新的时候也是将更新函数放到`nextTick`中去执行 .

关于`nextTick`的原理 , 和JS事件循环的宏任务/微任务相关.如果对这一块不熟悉的朋友,建议先去恶补相关知识.(看别人的文章就可以).

## nextTick源码

```javascript
/* @flow */
/* globals MutationObserver */

import { noop } from 'shared/util'
import { handleError } from './error'
import { isIE, isIOS, isNative } from './env'

export let isUsingMicroTask = false

const callbacks = []
let pending = false

function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}

// Here we have async deferring wrappers using microtasks.
// In 2.5 we used (macro) tasks (in combination with microtasks).
// However, it has subtle problems when state is changed right before repaint
// (e.g. #6813, out-in transitions).
// Also, using (macro) tasks in event handler would cause some weird behaviors
// that cannot be circumvented (e.g. #7109, #7153, #7546, #7834, #8109).
// So we now use microtasks everywhere, again.
// A major drawback of this tradeoff is that there are some scenarios
// where microtasks have too high a priority and fire in between supposedly
// sequential events (e.g. #4521, #6690, which have workarounds)
// or even between bubbling of the same event (#6566).
let timerFunc

// The nextTick behavior leverages the microtask queue, which can be accessed
// via either native Promise.then or MutationObserver.
// MutationObserver has wider support, however it is seriously bugged in
// UIWebView in iOS >= 9.3.3 when triggered in touch event handlers. It
// completely stops working after triggering a few times... so, if native
// Promise is available, we will use it:
/* istanbul ignore next, $flow-disable-line */
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    // In problematic UIWebViews, Promise.then doesn't completely break, but
    // it can get stuck in a weird state where callbacks are pushed into the
    // microtask queue but the queue isn't being flushed, until the browser
    // needs to do some other work, e.g. handle a timer. Therefore we can
    // "force" the microtask queue to be flushed by adding an empty timer.
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
} else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  // PhantomJS and iOS 7.x
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  // Use MutationObserver where native Promise is not available,
  // e.g. PhantomJS, iOS7, Android 4.4
  // (#6466 MutationObserver is unreliable in IE11)
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // Fallback to setImmediate.
  // Technically it leverages the (macro) task queue,
  // but it is still a better choice than setTimeout.
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else {
  // Fallback to setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}

```

如上代码，我们从上往下看，首先定义变量 `callbacks = []`; 该变量的作用是: 用来存储所有需要执行的回调函数。`let pending = false`; 该变量的作用是表示状态，判断是否有正在执行的回调函数。
也可以理解为，如果代码中 `timerFunc` 函数被推送到任务队列中去则不需要重复推送。

`flushCallbacks()` 函数，该函数的作用是用来执行`callbacks`里面存储的所有回调函数。如下代码:

`timerFunc`: 保存需要被执行的函数。

继续看接下来的代码，我们上面讲解过，在Vue中使用了几种情况来延迟调用该函数。

**1. promise.then 延迟调用,** 基本代码如下: 

```javascript
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  timerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
  isUsingMicroTask = true
}
```

如上代码的含义是: 如果我们的设备(或叫浏览器)支持Promise, 那么我们就使用 Promise.then的方式来延迟函数的调用。Promise.then会将函数延迟到调用栈的最末端，从而会做到延迟。

**2. MutationObserver 监听,** 基本代码如下:

```javascript
else if (!isIE && typeof MutationObserver !== 'undefined' && (
  isNative(MutationObserver) ||
  // PhantomJS and iOS 7.x
  MutationObserver.toString() === '[object MutationObserverConstructor]'
)) {
  // Use MutationObserver where native Promise is not available,
  // e.g. PhantomJS, iOS7, Android 4.4
  // (#6466 MutationObserver is unreliable in IE11)
  let counter = 1
  const observer = new MutationObserver(flushCallbacks)
  const textNode = document.createTextNode(String(counter))
  observer.observe(textNode, {
    characterData: true
  })
  timerFunc = () => {
    counter = (counter + 1) % 2
    textNode.data = String(counter)
  }
  isUsingMicroTask = true
}
```

如上代码，首先也是判断我们的设备是否支持 MutationObserver 对象, 如果支持的话，我们就会创建一个MutationObserver构造函数, 并且把flushCallbacks函数当做callback的回调, 然后我们会创建一个文本节点, 之后会使用MutationObserver对象的observe来监听该文本节点, 如果文本节点的内容有任何变动的话，它就会触发 flushCallbacks 回调函数。那么要怎么样触发呢? 在该代码内有一个 timerFunc 函数, 如果我们触发该函数, 会导致文本节点的数据发生改变，进而触发MutationObserver构造函数。

**3. setImmediate 监听,** 基本代码如下:

```javascript
else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // Fallback to setImmediate.
  // Techinically it leverages the (macro) task queue,
  // but it is still a better choice than setTimeout.
  timerFunc = () => {
    setImmediate(flushCallbacks)
  }
}
```

如果上面的 Promise 和 MutationObserver 都不支持的话, 我们继续会判断设备是否支持 setImmediate, 我们上面分析过, 他属于 macrotasks(宏任务)的。该任务会在一个宏任务里执行回调队列。

**4. 使用setTimeout 做降级处理**

如果我们上面三种情况, 设备都不支持的话, 我们会使用 setTimeout 来做降级处理, 实现延迟效果。如下基本代码:

```javascript
else {
  // Fallback to setTimeout.
  timerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
```

现在我们的源码继续往下看, 会看到我们的nextTick函数被export了，如下基本代码:

```javascript
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    timerFunc()
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

如上代码, nextTick 函数接收2个参数，cb 是一个回调函数, ctx 是一个上下文。 首先会把它存入callbacks函数数组里面去, 在函数内部会判断cb是否是一个函数，如果是一个函数，就调用执行该函数，当然它会在callbacks函数数组遍历的时候才会被执行。其次 如果cb不是一个函数的话, 那么会判断是否有_resolve值, 有该值就使用Promise.then() 这样的方式来调用。比如: this.$nextTick().then(cb) 这样的使用方式。因此在下面的if语句内会判断赋值给_resolve：

```javascript
if (!cb && typeof Promise !== 'undefined') {
  return new Promise(resolve => {
    _resolve = resolve
  })
}
```

使用Promise返回了一个 fulfilled 的Promise。赋值给 _resolve; 然后在callbacks.push 中会执行如下:

```
_resolve(ctx);
```



以上就可以解释为什么

```javascript
this.name = 'test2'
console.log(this.$refs.name.textContent)//test1
this.$nextTick(()=>{
   console.log(this.$refs.name.textContent)//test2
})
```

1. 当我们执行script,主体代码的时候(主体代码属于宏任务),执行了`this.name = 'test2'`

2. 这时候触发了更新，update将watcher的callback1推到了nextTick中，也就是微任务队列中
3. 接下来执行`console.log(this.$refs.name.textContent)`这时候并没有执行到微任务，所以dom对象并没有改变，所以拿到的`textNode`还是`test1`.
4. 执行`this.$nextTick()`，将callback2，继续推入微任务队列。
5. 执行微任务队列。callback1 改变dom对象(页面渲染要在一次事件循环结束后，也就是微任务队列执行完毕后UI渲染刷新)，callback2拿到修改后的dom对象，理所当然`textContent`为`test2`.（此时页面没有重新渲染刷新，分清时间点）
6. 微任务队列执行完毕一次事件循环结束，页面重新渲染刷新.

宏任务: 主体代码  

微任务: callback1 =>  callback2