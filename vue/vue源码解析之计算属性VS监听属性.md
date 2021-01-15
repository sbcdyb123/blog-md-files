# VUE源码系列四：计算属性和监听属性，到底该用谁？

# 前言

上一篇我们分析了Vue的响应式原理([juejin.im/post/684490…](https://juejin.im/post/6844904045694418951))，今天我们来搞一下，Vue的计算属性和监听属性的实现原理，以让我们更清楚什么时候该使用computed，什么时候该使用watch，以及为什么官方不建议使用watch？

# 正文

还记得我们在data渲染视图([juejin.im/post/684490…](https://juejin.im/post/6844904033459634184))中讲的，New Vue()会发生什么么？这其中有一段源代码：

```
/*  初始化状态 */
export function initState (vm: Component) {
  // ...
  /*初始化computed*/
  if (opts.computed) initComputed(vm, opts.computed)
  /*初始化watchers*/
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
复制代码
```

可以看出我们在new Vue()之后，会执行initState方法，该方法去初始化initComputed（计算）和initWatch（监听），我们首先看计算属性；

## computed

先看initComputed
源码：src/core/instance/state.js

```
/* 为了在属性值不变的情况下get()只执行一次而设置的标志位，下边会讲的 */
const computedWatcherOptions = { lazy: true }
/* 初始化computed */
function initComputed (vm: Component, computed: Object) {
  const watchers = vm._computedWatchers = Object.create(null)
  /* 循环计算属性，给每个属性添加Watcher监听，不知道Watcher干什么的可以去看https://juejin.im/post/6844904045694418951#heading-13 */
  for (const key in computed) {
    const userDef = computed[key]
    /* 拿get方法 */
    const getter = typeof userDef === 'function' ? userDef : userDef.get
      /* 添加watcher监听 */
      watchers[key] = new Watcher(
        vm,
        getter || noop,
        noop,
        computedWatcherOptions
      )
    
    if (!(key in vm)) {
      defineComputed(vm, key, userDef)
    } else if (process.env.NODE_ENV !== 'production') {
      /* 如果定义的计算属性在data或者props中已经被定义过了，会报警告 */
      if (key in vm.$data) {
        warn(`The computed property "${key}" is already defined in data.`, vm)
      } else if (vm.$options.props && key in vm.$options.props) {
        warn(`The computed property "${key}" is already defined as a prop.`, vm)
      }
    }
  }
}
复制代码
```

那么我们重点看一下defineComputed的实现

```
/**
 * 定义计算属性
 * @param     {Object | Function}    userDef     计算属性的值
 */
export function defineComputed (
  target: any,
  key: string,
  userDef: Object | Function
) {
  /* 非服务端渲染，执行createComputedGetter */
  const shouldCache = !isServerRendering()
  /* 计算属性是函数时 */
  if (typeof userDef === 'function') {
    sharedPropertyDefinition.get = shouldCache
      ? createComputedGetter(key)
      : createGetterInvoker(userDef)
      /* noop是一个空函数，Vue中定义的工具函数 */
    sharedPropertyDefinition.set = noop
  } else {
    /* 计算属性是对象时 */
    sharedPropertyDefinition.get = userDef.get
      ? shouldCache && userDef.cache !== false
        ? createComputedGetter(key)
        : createGetterInvoker(userDef.get)
      : noop
    sharedPropertyDefinition.set = userDef.set || noop
  }
  // ...
  Object.defineProperty(target, key, sharedPropertyDefinition)
}
复制代码
```

可以看出，本质上就是利用Object.defineProperty去给属性添加setter和getter，并且无论计算属性是函数还是对象，都会去执行createComputedGetter方法，并传入属性键。

```
function createComputedGetter (key) {
  /* 返回一个函数，即对应的getter */
  return function computedGetter () {
    /* this._computedWatchers是在initComputed方法中定义的一个空对象 */
    const watcher = this._computedWatchers && this._computedWatchers[key]
    if (watcher) {
      /*****
      Watcher中有evaluate这么一个方法，当取到get()值以后，将dirty置为false，那么下次再去取这个计算属性值的时候因为dirty已经变为false了，就不会再去执行get()方法了，而是用的之前的取的值，这就是computed的缓存机制
      evaluate () {
        this.value = this.get()
        this.dirty = false
      }
      ******/
      
      if (watcher.dirty) {
        watcher.evaluate()
      }
      /* 为了避免重新渲染的时候，计算属性渲染的部分不被重新渲染，因此进行依赖收集 */
      if (Dep.target) {
        watcher.depend()
      }
      /* 返回属性值 */
      return watcher.value
    }
  }
}
复制代码
```

createComputedGetter方法返回一个函数，即对应的是getter方法，该方法主要是返回watcher的值，也就是getter的值，看Watcher的源码我们可以发现dirty的值就是lazy, 而上边说的const computedWatcherOptions = { lazy: true }，lazy初始值为true,并在上边initComputed方法中合并给Watcher了，因此计算属性在属性值不变的情况下，只会去执行一次get()方法取值，这也就是为什么Vue的计算属性有缓存作用。

------

我们举个例子看一下computed和watch的不同，我们知道computed也会对数据尽心监听，下边我们把计算属性的监听暂且叫做computed watcher

```
var vm = new Vue({
  data: {
    firstName: 'yang',
    lastName: 'bo'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
复制代码
```

当初始化fullName时，我们会执行到Watcher
源码：/src/core/observer/watcher.js

```
constructor () {
    /* 这一步是给computed watcher设置的，计算属性并不会去立刻求值 */
    this.value = this.lazy
      ? undefined
      : this.get()
  }
复制代码
```

然后当render函数访问到this.fullName的时候，就会触发计算属性的getter,它会拿到计算属性对应的watcher,然后执行watcher.depend()进行依赖收集。

```
depend () {
    let i = this.deps.length
    while (i--) {
      this.deps[i].depend()
    }
}
复制代码
```

然后还执行了watcher.evaluate()

```
evaluate () {
    this.value = this.get()
    this.dirty = false
}
复制代码
```

这个方法我们上边已经讲了，就不啰嗦了。我们在看Watcher中的get方法

```
get () {
    /* 收集Watcher实例，也就是Dep.target */
    pushTarget(this)
    let value
    const vm = this.vm
    try {
      value = this.getter.call(vm, vm)
    }
    return value
  }
复制代码
```

get执行了getter方法，也就是我们例子中的

```
this.firstName + ' ' + this.lastName
复制代码
```

然后拿到计算属性最后的value值。

## watch

watch初始化也是在initState方法中，上边已经讲到了。

```
if (opts.watch && opts.watch !== nativeWatch) {
  initWatch(vm, opts.watch)
}
复制代码
```

来看一下 initWatch 的实现
源码：src/core/instance/state.js

```
/**
 * 初始化侦听
 */
function initWatch (vm: Component, watch: Object) {
  for (const key in watch) {
    const handler = watch[key]
    if (Array.isArray(handler)) {
      for (let i = 0; i < handler.length; i++) {
        createWatcher(vm, key, handler[i])
      }
    } else {
      createWatcher(vm, key, handler)
    }
  }
}
复制代码
```

这里就是对 watch 对象做遍历，拿到每一个 handler，因为 Vue 是支持 watch 的同一个 key 对应多个 handler，所以如果 handler 是一个数组，则遍历这个数组，调用 createWatcher 方法，否则直接调用 createWatcher

```
function createWatcher (
  vm: Component,
  expOrFn: string | Function,
  handler: any,
  options?: Object
) {
  if (isPlainObject(handler)) {
    options = handler
    handler = handler.handler
  }
  if (typeof handler === 'string') {
    handler = vm[handler]
  }
  return vm.$watch(expOrFn, handler, options)
}
复制代码
```

首先对 hanlder 的类型做判断，拿到它最终的回调函数，最后调用 `vm.$watch(keyOrFn, handler, options)` 函数，$watch 是 Vue 原型上的方法，它是在执行 stateMixin 的时候定义的

```
export function stateMixin (Vue: Class<Component>) {
  // ...
  Vue.prototype.$watch = function (
    expOrFn: string | Function,
    cb: any,
    options?: Object
  ): Function {
    const vm: Component = this
    if (isPlainObject(cb)) {
      return createWatcher(vm, expOrFn, cb, options)
    }
    options = options || {}
    /* 用户自定义watch */
    options.user = true
    const watcher = new Watcher(vm, expOrFn, cb, options)
    if (options.immediate) {
      try {
        cb.call(vm, watcher.value)
      }
    }
    /* 返回卸载watcher的方法 */
    return function unwatchFn () {
      watcher.teardown()
    }
  }
}
复制代码
```

侦听属性 watch 最终会调用 `$watch` 方法，这个方法首先判断 cb 如果是一个对象，则调用 createWatcher 方法，这是因为 $watch 方法是用户可以直接调用的，它可以传递一个对象，也可以传递函数。接着执行 const watcher = new Watcher(vm, expOrFn, cb, options) 实例化了一个 watcher，这里需要注意一点这是一个 user watcher，因为 options.user = true。通过实例化 watcher 的方式，一旦我们 watch 的数据发生变化，它最终会执行 watcher 的 run 方法，执行回调函数 cb，并且如果我们设置了 immediate 为 true，则直接会执行回调函数 cb。最后返回了一个 unwatchFn 方法，它会调用 teardown 方法去移除这个 watcher
所以本质上侦听属性也是基于 Watcher 实现的，它是一个 user watcher。其实 Watcher 支持了不同的类型，下面我们梳理一下它有哪些类型以及它们的作用。

## Watcher Options

```
if (options) {
  this.deep = !!options.deep // 深度监听
  this.user = !!options.user // 在对 watcher 求值以及在执行回调函数的时候，会处理一下错误
  this.lazy = !!options.lazy // 惰性求值，赋值给this.dirty，计算属性的时候用到的
  this.sync = !!options.sync // 在当前 Tick 中同步执行 watcher 的回调函数，否则响应式数据发生变化之后，watcher回调会在nextTick后执行；
} 
复制代码
```

所以 watcher 总共有 4 种类型，我们来一一分析它们，看看不同的类型执行的逻辑有哪些差别

### deep watcher

也就是我们通常说的深度监听，看一下我们如果将一个对象进行深度监听会发生什么：

```
get () {
  if (this.deep) {
    traverse(value)
  }
  return value
}
复制代码
```

看一下traverse源码：src/core/observer/traverse.js

```
export function traverse (val: any) {
  _traverse(val, seenObjects)
  seenObjects.clear()
}

function _traverse (val: any, seen: SimpleSet) {
  let i, keys
  const isA = Array.isArray(val)
  if ((!isA && !isObject(val)) || Object.isFrozen(val) || val instanceof VNode) {
    return
  }
  if (isA) {
    i = val.length
    while (i--) _traverse(val[i], seen)
  } else {
    keys = Object.keys(val)
    i = keys.length
    while (i--) _traverse(val[keys[i]], seen)
  }
}
复制代码
```

很清晰，对传入对watch对象进行递归遍历，因为递归有一定对性能开销，因此，我们一定要在合适的场景去设置deep。

### user watcher

就是用户手写的watch监听，前面讲过了，略过。

### computed watcher

为计算属性量身定制的监听，具有“缓存”功效，前面讲过了，略过。

### sync watcher

在我们之前对 setter 的分析过程知道，当响应式数据发送变化后，触发了 watcher.update()，只是把这个 watcher 推送到一个队列中，在 nextTick 后才会真正执行 watcher 的回调函数。而一旦我们设置了 sync，就可以在当前 Tick 中同步执行 watcher 的回调函数。

```
update () {
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      /* 执行Watcher回调，触发视图更新 */
      this.run()
    } else {
      queueWatcher(this)
    }
}
复制代码
```

因此只有当我们需要 watch 的值的变化到执行 watcher 的回调函数是一个同步过程的时候才会去设置该属性为 true。

# 总结

计算属性和监听属性都是通过Watcher这个类去实现当，本身都具有监听数据的能力。
**计算属性**：计算属性本质上是 computed watcher，计算属性适合用在模板渲染中，某个值是依赖了其它的响应式对象甚至是计算属性计算而来，它具有缓存能力，当依赖的值没有变化甚至是计算结果没有发生变化，触发更新的回调则不会执行；
**监听属性**：侦听属性本质上是 user watcher，适用于观测某个值的变化去完成一段复杂的业务逻辑，当新老值相同，也不会去触发更新回调。