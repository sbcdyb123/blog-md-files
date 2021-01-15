后台项目文档以及注意事项

# 目录结构

```shell
├── build                      // 构建相关  
├── config                     // 配置相关
├── src                        // 源代码
│   ├── api                    // 所有请求
│   ├── assets                 // 主题 字体等静态资源
│   ├── components             // 全局公用组件
│   ├── directive              // 全局指令
│   ├── filtres                // 全局 filter
│   ├── icons                  // 项目所有 svg icons
│   ├── lang                   // 国际化 language
│   ├── mock                   // 项目mock 模拟数据
│   ├── router                 // 路由
│   ├── store                  // 全局 store管理
│   ├── styles                 // 全局样式
│   ├── utils                  // 全局公用方法
│   ├── vendor                 // 公用vendor
│   ├── views                   // view
│   ├── App.vue                // 入口页面
│   ├── main.js                // 入口 加载组件 初始化等
│   └── permission.js          // 权限管理
├── static                     // 第三方不打包资源
│   └── Tinymce                // 富文本
├── .babelrc                   // babel-loader 配置
├── eslintrc.js                // eslint 配置项
├── .gitignore                 // git 忽略项
├── favicon.ico                // favicon图标
├── index.html                 // html模板
└── package.json               // package.json

```

## api 和 views

![](https://user-gold-cdn.xitu.io/2017/5/3/591e388437a8aea99794b175b1098389?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如图可见模块有很多，而且随着业务的迭代，模块还会会越来越多。 所以这里建议根据业务模块来划分 views，并且 将views 和 `api` 两个模块一一对应，从而方便维护。如下图：

![](https://user-gold-cdn.xitu.io/2017/5/3/c185f7d37a268a1ff4044ff60f5341c0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如 article 模块下放的都是文章相关的 `api`，这样不管项目怎么累加，`api`和views的维护还是清晰的，当然也有一些全区公用的`api`模块，如七牛upload，`remoteSearch`等等，这些单独放置就行。

## components

这里的 components 放置的都是全局公用的一些组件，如上传组件，富文本等等。一些页面级的组件建议还是放在各自views文件下，方便管理。如图：

![](https://user-gold-cdn.xitu.io/2017/5/3/a355aa4081709f7d9fecf6dfaf08129d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

## store

这里我个人建议不要为了用 `vuex` 而用 `vuex`。每个页面里存放自己的 data 就行。当然有些数据还是需要用 `vuex` 来统一管理的，如登录token,用户信息，或者是一些全局个人偏好设置等，还是用`vuex`管理更加的方便，具体当然还是要结合自己的业务场景的。总之还是那句话，不要为了用`vuex`而用`vuex`！

# `webpack`

### `alias`(`vue.config.js`中修改)

当项目逐渐变大之后，文件与文件直接的引用关系会很复杂，这时候就需要使用[alias](https://webpack.js.org/configuration/resolve/) 了。 有的人喜欢alias 指向`src`目录下，再使用相对路径找文件

```javascript
resolve: {
  alias: {
    '~': resolve(__dirname, 'src')
  }
}

//使用
import stickTop from '~/components/stickTop'
```

或者也可以

```javascript
alias: {
  'src': path.resolve(__dirname, '../src'),
  'components': path.resolve(__dirname, '../src/components'),
  'api': path.resolve(__dirname, '../src/api'),
  'utils': path.resolve(__dirname, '../src/utils'),
  'store': path.resolve(__dirname, '../src/store'),
  'router': path.resolve(__dirname, '../src/router')
}

//使用
import stickTop from 'components/stickTop'
import getArticle from 'api/article'

```

# `ESLint`

不管是多人合作还是个人项目，代码规范是很重要的。这样做不仅可以很大程度地避免基本语法错误，也保证了代码的可读性。这所谓工欲善其事，必先利其器，个人推荐 eslint+`vscode` 来写 `vue`，绝对有种飞一般的感觉。效果如图：

![](https://user-gold-cdn.xitu.io/2017/5/3/709326a6df695ff7b92ba4c4d6ff7d71?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

每次保存，vscode就能标红不符合eslint规则的地方，同时还会做一些简单的自我修正。安装步骤如下：

首先安装eslint插件

![eslint1.png](https://user-gold-cdn.xitu.io/2017/5/3/2c4a6510d1a0c8a4086ea99daccf9b2d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

安装并配置完成 ESLint 后，我们继续回到 VSCode 进行扩展设置，依次点击 文件 > 首选项 > 设置 打开 VSCode 配置文件,添加如下配置

```javascript
"files.autoSave":"off",
"eslint.validate": [
   "javascript",
   "javascriptreact",
   "html",
   { "language": "vue", "autoFix": true }
 ],
 "eslint.options": {
    "plugins": ["html"]
 }
```
这样每次保存的时候就可以根据根目录下.eslintrc.js你配置的eslint规则来检查和做一些简单的fix。这里提供了一份我平时的eslint规则[地址](https://github.com/PanJiaChen/vue-element-admin/blob/master/.eslintrc.js)，都简单写上了注释。每个人和团队都有自己的代码规范，统一就好了，去打造一份属于自己的eslint 规则上传到npm吧，如饿了么团队的 [config](https://www.npmjs.com/package/eslint-config-elemefe)，vue的 [config](https://github.com/vuejs/eslint-config-vue)。

[vscode 插件和配置推荐](https://github.com/varHarrie/Dawn-Blossoms/issues/10)

# 封装 axios

我们经常遇到一些线上 的bug，但测试环境很难模拟。其实可以通过简单的配置就可以在本地调试线上环境。 这里结合业务封装了axios ，[线上代码](https://github.com/PanJiaChen/vue-element-admin/blob/master/src/utils/request.js)

```javascript
import axios from 'axios'
import { Message } from 'element-ui'
import store from '@/store'
import { getToken } from '@/utils/auth'

// 创建axios实例
const service = axios.create({
  baseURL: process.env.BASE_API, // api的base_url
  timeout: 5000 // 请求超时时间
})

// request拦截器
service.interceptors.request.use(config => {
  // Do something before request is sent
  if (store.getters.token) {
    config.headers['X-Token'] = getToken() // 让每个请求携带token--['X-Token']为自定义key 请根据实际情况自行修改
  }
  return config
}, error => {
  // Do something with request error
  console.log(error) // for debug
  Promise.reject(error)
})

// respone拦截器
service.interceptors.response.use(
  response => response,
  /**
  * 下面的注释为通过response自定义code来标示请求状态，当code返回如下情况为权限有问题，登出并返回到登录页
  * 如通过xmlhttprequest 状态码标识 逻辑可写在下面error中
  */
  //  const res = response.data;
  //     if (res.code !== 20000) {
  //       Message({
  //         message: res.message,
  //         type: 'error',
  //         duration: 5 * 1000
  //       });
  //       // 50008:非法的token; 50012:其他客户端登录了;  50014:Token 过期了;
  //       if (res.code === 50008 || res.code === 50012 || res.code === 50014) {
  //         MessageBox.confirm('你已被登出，可以取消继续留在该页面，或者重新登录', '确定登出', {
  //           confirmButtonText: '重新登录',
  //           cancelButtonText: '取消',
  //           type: 'warning'
  //         }).then(() => {
  //           store.dispatch('FedLogOut').then(() => {
  //             location.reload();// 为了重新实例化vue-router对象 避免bug
  //           });
  //         })
  //       }
  //       return Promise.reject('error');
  //     } else {
  //       return response.data;
  //     }
  error => {
    console.log('err' + error)// for debug
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  })

export default service


```

```javascript
import request from '@/utils/request'

//使用
export function getInfo(params) {
  return request({
    url: '/user/info',
    method: 'get',
    params
  });
}
```

比如后台项目，每一个请求都是要带 token 来验证权限的，这样封装以下的话我们就不用每个请求都手动来塞 token，或者来做一些统一的异常处理，一劳永逸。 而且因为我们的 api 是根据 `env` 环境变量动态切换的，如果以后线上出现了bug，我们只需配置一下 `@/config/dev.env.js` 再重启一下服务，就能在本地模拟线上的环境了。

```javascript
module.exports = {
    NODE_ENV: '"development"',
    BASE_API: '"https://api-dev"', //修改为'"https://api-prod"'就行了
    APP_ORIGIN: '"https://wallstreetcn.com"' //为公司打个广告 pc站为vue+ssr
}
```

**妈妈再也不用担心我调试线上bug了。** 当然这里只是简单举了个例子，axios还可以执行多个并发请求，拦截器什么的，大家自行去研究吧。

# 多环境

vue-cli 默认只提供了`dev`和`prod`两种环境。但其实正真的开发流程可能还会多一个`sit`或者`stage`环境，就是所谓的测试环境和预发布环境。所以我们就要简单的修改一下代码。其实很简单就是设置不同的环境变量

```
"build:prod": "NODE_ENV=production node build/build.js",
"build:sit": "NODE_ENV=sit node build/build.js",
```

之后在代码里自行判断，想干就干啥

```
var env = process.env.NODE_ENV === 'production' ? config.build.prodEnv : config.build.sitEnv
```

新版的 vue-cli 也内置了 `webpack-bundle-analyzer` 一个模块分析的东西，相当的好用。使用方法也很简单，和之前一样封装一个 npm script 就可以。

```javascript
//package.json
 "build:sit-preview": "cross-env NODE_ENV=production env_config=sit npm_config_preview=true  npm_config_report=true node build/build.js"

//之后通过process.env.npm_config_report来判断是否来启用webpack-bundle-analyzer

var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
webpackConfig.plugins.push(new BundleAnalyzerPlugin())

```

# 前后端交互

## 跨域问题

首先前后端交互不可避免的就会遇到跨域问题，我司现在全是用 `cors`来解决的，如果你司后端嫌麻烦不肯配置的话，dev环境也可以通过 `webpack-dev-server`的`proxy`来解决，开发环境用`nginx`反代理一下就好了，具体配置这里就不展开了。

## 前后端的交互问题

其实大家也知道，平时的开发中交流成本占据了我们很大一部分时间，但前后端如果有一个好的协作方式的话能解决很多时间。我司开发流程都是前后端和产品一起开会讨论项目，之后后端根据需求，首先定义数据格式和api，然后 mock api 生成好文档，我们前端才是对接接口的。这里推荐一个文档生成器 [swagger](http://swagger.io/)。 **swagger**是一个REST APIs文档生成工具，可以在许多不同的平台上从代码注释中自动生成，开源，支持大部分语言，社区好，总之就是一个强大，如下图的api 文档(swagger自动生成，ui忽略)

## iconfont

详细具体的使用可以见文章 [手摸手，带你优雅的使用 icon](https://juejin.im/post/59bb864b5188257e7a427c09)

# router-view

![router-view.png](https://user-gold-cdn.xitu.io/2017/5/3/ed2de15673673276b00e205c042048e4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

我创建和编辑的页面使用的是同一个component,默认情况下当这两个页面切换时并不会触发vue的created或者mounted钩子，官方说你可以通过watch $route的变化来做处理，但其实说真的还是蛮麻烦的。后来发现其实可以简单的在 router-view上加上一个唯一的key，来保证路由切换时都会重新渲染触发钩子了。这样简单的多了。

```javascript
<router-view :key="key"></router-view>

computed: {
    key() {
        return this.$route.name !== undefined? this.$route.name + +new Date(): this.$route + +new Date()
    }
 }
```

# 优化





## Table 常见坑

通过dialog来编辑，新建，删除table的元素这种业务场景相对于前面说的两种更加的常见。而且也有不少的小坑。 首先我们要明确一个点 vue 是一个MVVM框架，我们传统写代码是命令式编程，拿到table这个dom之后就是命令式对dom增删改。而我们现在用声明式编程，只用关注data的变化就好了，所以我们这里的增删改都是基于list这个数组来的。这里我们还要明确一点[vue 列表渲染注意事项](https://cn.vuejs.org/v2/guide/list.html#注意事项)

> 由于 JavaScript 的限制， Vue 不能检测以下变动的数组： * 当你利用索引直接设置一个项时，例如： vm.items[indexOfItem] = newValue

所以我们想改变table中第一条数据的值，通过`this.list[0]=newValue`这样是不会生效的。

```javascript
解决方案：
// Array.prototype.splice`
example1.items.splice(indexOfItem, 1, newValue)

```

所以我们可以通过

```javascript
//添加数据
this.list.unshift(this.temp);

//删除数据 
const index = this.list.indexOf(row); //找到要删除数据在list中的位置
this.list.splice(index, 1); //通过splice 删除数据

//修改数据
const index = this.list.indexOf(row); //找到修改的数据在list中的位置
this.list.splice(index, 1,this.updatedData); //通过splice 替换数据 触发视图更新

```

这样我们就完成了对table的增删改操作，列表view也自动响应发生了变化。这里在修改数据的时候还有一个小坑**需要主要**。 当我们拿到需要修改行的数据时候不能直接将它直接赋值给dialog，不然会发生下面的问题。

![img](https://lc-gold-cdn.xitu.io/a7d80d91001f15b7f5be.gif?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如上图所示，我们在dialog里面改变状态的时候，遮罩下面的table里面该行的状态也在那里跟着一只变化着。原因想必大家都猜到了。赋值的数据是一个objec引用类型共享一个内存区域的。所以我们就不能直接连等复制，需要重新指向一个新的引用，方案如下：

```javascript
//赋值对象是一个obj
this.objData=Object.assign({}, row) //这样就不会共用同一个对象

//数组我们也有一个巧妙的防范
newArray = oldArray.slice(); //slice会clone返回一个新数组

```

## Tabs

tab在后台项目中也比较常用的。假设我们有四个tab选项，每个tab都会向后端请求数据，但我们希望一开始只会请求当前的tab数据，而且tab来回切换的时候不会重复请求，只会实例化一次。首先我们想到的就是用`v-if` 这样的确能做到一开始不会挂载后面的tab，但有一个问题，每次点击这个tab组件都会重新挂载一次，这是我们不想看到的，这时候我们就可以用到`<keep-alive>`了。

> keep-alive 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。 它是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

所以我们就可以这样写tabs了

```html
<el-tabs v-model="activeTab">
  <el-tab-pane label="简介及公告" name="announcement">
    <announcement />
  </el-tab-pane>
  <el-tab-pane label="资讯" name="information">
    <keep-alive>
      <information v-if="activeTab=='information'" />
    </keep-alive>
  </el-tab-pane>
  <el-tab-pane label="直播流配置" name="stream">
    <keep-alive>
      <stream v-if="activeTab=='stream'" />
    </keep-alive>
  </el-tab-pane>
</el-tabs>

```

# 命名规范

## Component

所有的`Component`文件都是以大写开头 (PascalCase)，这也是官方所 [推荐的](https://cn.vuejs.org/v2/style-guide/index.html#单文件组件文件的大小写-强烈推荐)。

但除了 `index.vue`。

例子：

- `@/src/components/BackToTop/index.vue`
- `@/src/components/Charts/Line.vue`
- `@/src/views/example/components/Button.vue`

## JS 文件

所有的`.js`文件都遵循横线连接 (kebab-case)。

例子：

- `@/src/utils/open-window.js`
- `@/src/views/svg-icons/require-icons.js`
- `@/src/components/MarkdownEditor/default-options.js`



## Views

在`views`文件下，代表路由的`.vue`文件都使用横线连接 (kebab-case)，代表路由的文件夹也是使用同样的规则。

例子：

- `@/src/views/svg-icons/index.vue`
- `@/src/views/svg-icons/require-icons.js`

使用横线连接 (kebab-case)来命名`views`主要是出于以下几个考虑。

- 横线连接 (kebab-case) 也是官方推荐的命名规范之一 [文档](https://cn.vuejs.org/v2/style-guide/index.html#单文件组件文件的大小写-强烈推荐)
- `views`下的`.vue`文件代表的是一个路由，所以它需要和`component`进行区分(component 都是大写开头)
- 页面的`url` 也都是横线连接的，比如`https://www.xxx.admin/export-excel`，所以路由对应的`view`应该要保持统一
- 没有大小写敏感问题



# 小技巧与建议

## Watch immediate

这个已经算是一个比较常见的技巧了，这里就简单说一下。当 watch 一个变量的时候，初始化时并不会执行，如下面的例子，你需要在`created`的时候手动调用一次。

```javascript
// bad
created() {
  this.fetchUserList();
},
watch: {
  searchText: 'fetchUserList',
}

```

你可以添加`immediate`属性，这样初始化的时候也会触发，然后上面的代码就能简化为：

```javascript
// good
watch: {
  searchText: {
    handler: 'fetchUserList',
    immediate: true,
  }
}

```

ps: watch 还有一个容易被大家忽略的属性`deep`。当设置为`true`时，它会进行深度监听。简而言之就是你有一个 `const obj={a:1,b:2}`，里面任意一个 key 的 value 发生变化的时候都会触发`watch`。应用场景：比如我有一个列表，它有一堆`query`筛选项，这时候你就能`deep watch`它，只有任何一个筛序项改变的时候，就自动请求新的数据。或者你可以`deep watch`一个 form 表单，当任何一个字段内容发生变化的时候，你就帮它做自动保存等等。

## Attrs 和 Listeners

这两个属性是 `vue 2.4` 版本之后提供的，它简直是二次封装组件或者说写高阶组件的神器。在我们平时写业务的时候免不了需要对一些第三方组件进行二次封装。比如我们需要基于`el-select`分装一个带有业务特性的组件，根据输入的 name 搜索用户，并将一些业务逻辑分装在其中。但`el-select`这个第三方组件支持几十个配置参数，我们当然可以适当的挑选几个参数通过 props 来传递，但万一哪天别人用你的业务组件的时候觉得你的参数少了，那你只能改你封装的组件了，亦或是哪天第三方组件加入了新参数，你该怎么办？

其实我们的这个组件只是基于`el-select`做了一些业务的封装，比如添加了默认的`placeholder`，封装了远程 ajax 搜索请求等等，总的来说它就是一个中间人组件，只负责传递数据而已。

这时候我们就可以使用`v-bind="$attrs"`：传递所有属性、`v-on="$listeners"`传递所有方法。如下图所示：

![img](https://user-gold-cdn.xitu.io/2019/4/24/16a4e82b28f2b24c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这样，我们没有在`$props`中声明的方法和属性，会通过`$attrs`、`$listeners`直接传递下去。这两个属性在我们平时分装第三方组件的时候非常有用！

## .sync

这个也是 `vue 2.3` 之后新加的一个语法糖。这也是平时在分装组件的时候很好用的一个语法糖，它的实现机制和`v-model`是一样的。

![img](https://user-gold-cdn.xitu.io/2019/5/10/16aa06bad379efff?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

当你有需要在子组件修改父组件值的时候这个方法很好用。 线上[例子](https://github.com/PanJiaChen/vue-element-admin/blob/master/src/components/Pagination/index.vue)

## Computed 的 get 和 set

`computed` 大家肯定都用过，它除了可以缓存计算属性外，它在处理传入数据和目标数据格式不一致的时候也是很有用的。set、get [文档](https://cn.vuejs.org/v2/guide/computed.html#计算属性的-setter)

上面说的可能还是是有点抽象，举一个简单的的例子：我们有一个 form 表单，from 里面有一个记录创建时间的字段`create_at`。我们知道前端的时间戳都是 13 位的，但很多后端默认时间戳是 10 位的，这就很蛋疼了。前端和后端的时间戳位数不一致。最常见的做法如下：

![img](https://user-gold-cdn.xitu.io/2019/5/7/16a91387762c26d2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

上面的代码主要做的是：在拿到数据的时候将后端 10 位时间戳转化为 13 位时间戳，之后再向服务端发送数据的时候再转化回 10 位时间戳传给后端。目前这种做法当然是可行的，但之后可能不仅只有创建接口，还有更新接口的时候，你还需要在`update`的接口里在做一遍同样数据转化的操作么？而且这只是一个最简单的例子，真实的 form 表单会复杂的多，需要处理的数据也更为的多。这时候代码就会变得很难维护。

这时候就可以使用 computed 的 set 和 get 方法了。

![img](https://user-gold-cdn.xitu.io/2019/5/9/16a9b629e9827115?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)