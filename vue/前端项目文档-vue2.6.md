# 前端项目文档

## 目录结构

```
├── public                     // 不打包资源
├── src                        // 源代码
│   ├── api                    // 所有请求
│   ├── assets                 // 主题 字体等静态资源
│   ├── components             // 全局公用组件
│   ├── directive              // 全局指令
│   ├── filtres                // 全局 filter
│   ├── icons                  // 项目所有 svg icons
│   ├── Layout                 // 项目的布局结构
│   ├── router                 // 路由
│   ├── store                  // 全局 store管理
│   ├── styles                 // 全局样式
│   ├── utils                  // 全局公用方法
│   ├── vendor                 // 公用vendor
│   ├── views                   // view
│   ├── App.vue                // 入口页面
│   ├── main.js                // 入口 加载组件 初始化等
│   └── permission.js          // 权限管理
└── package.json               // package.json
```



## vscode 配置 ESLint

因为本项目使用ESLint , 为了提高编码体验 , 需要进行一些编辑器的配置.

推荐编辑器: `vscode`

1.首先在安装 eslint 插件

![0ZXZWD.png](https://s1.ax1x.com/2020/09/29/0ZXZWD.png)

2.安装完成 ESLint 后，我们继续回到 VSCode 进行扩展设置，依次点击 文件 > 首选项 > 设置 打开 VSCode 配置文件,添加如下配置

```json
{
  "files.autoSave": "off",
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue-html",
    {
      "language": "vue",
      "autoFix": true
    }
  ],
  "eslint.run": "onSave",
  "eslint.autoFixOnSave": true
}
```

这样每次保存的时候就可以根据根目录下`.eslintrc.js` 你配置的 `eslint` 规则来检查和做一些简单的 代码格式化 . 减少`eslint`莫名其妙的报错.同时提高项目的代码规范.

## 路径别名(alias)

`src`目录对应`@` . 比如:`import {upload} from "@/components/upload"`

不需要用``import {upload} from "../../components/upload"`这中形式来导入模块或者组件. 

同样的别的src下的目录也可以这样导入.



## icon图标

### svg

如果你没有在本项目中找到需要的图标，可以到 [iconfont.cn](http://iconfont.cn/) 上选择并生成自己的业务图标库，再进行使用。或者其它 svg 图标网站，下载 svg 并放到文件夹之中就可以了

#### 修改颜色和大小

当你在iconfont或者其他网站下载下来的svg文件,是带有初始颜色的

```xml
<?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg t="1601276528962" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="8658" xmlns:xlink="http://www.w3.org/1999/xlink" width="200" height="200">
    <defs>
        <style type="text/css"></style>
    </defs>
    <path d="M328.7552 884.5312c-18.3296 0-36.5056-5.7344-52.0704-17.0496-27.4944-19.968-41.0112-53.248-35.2768-86.7328l22.4256-130.6624c2.7648-16.1792-2.56-32.7168-14.336-44.1856l-94.9248-92.5696c-24.3712-23.7568-32.9728-58.5728-22.4256-90.9312s37.9392-55.4496 71.5776-60.3648L334.8992 343.04a50.01216 50.01216 0 0 0 37.5808-27.2896L431.104 196.8128c15.0528-30.5152 45.5168-49.4592 79.5648-49.4592s64.4608 18.944 79.5136 49.4592l58.6752 118.8864a49.80224 49.80224 0 0 0 37.5808 27.2896l131.1744 19.0464c33.6384 4.9152 61.0816 28.0064 71.5776 60.3648s1.8944 67.1744-22.4256 90.9312l-94.9248 92.5696a49.7408 49.7408 0 0 0-14.336 44.1856l22.4256 130.6624c5.7344 33.536-7.7824 66.7648-35.2768 86.7328-27.4944 19.968-63.2832 22.5792-93.3888 6.7584l-117.3504-61.696a49.9712 49.9712 0 0 0-46.4384 0l-117.3504 61.696a89.1648 89.1648 0 0 1-41.3696 10.2912zM510.6688 208.8448c-5.12 0-17.664 1.4848-24.4224 15.2064L427.5712 342.8864a111.37024 111.37024 0 0 1-83.8144 60.928l-131.2256 19.0464c-15.104 2.2016-20.4288 13.7216-22.016 18.5344s-4.0448 17.2544 6.912 27.9552l94.9248 92.5184c26.2656 25.6 38.1952 62.4128 32 98.56l-22.4256 130.6624c-2.6112 15.0528 6.7072 23.6544 10.8544 26.6752s15.1552 9.1648 28.7232 2.0992l117.3504-61.696c32.4608-17.0496 71.168-17.0496 103.6288 0l117.3504 61.696c13.5168 7.1168 24.576 0.9216 28.7232-2.0992s13.4144-11.5712 10.8544-26.6752L696.9344 660.48a111.4112 111.4112 0 0 1 32-98.56l94.9248-92.5184c10.9568-10.6496 8.448-23.0912 6.912-27.9552s-6.8608-16.3328-22.016-18.5344l-131.1744-19.0464a111.2832 111.2832 0 0 1-83.8144-60.928l-58.6752-118.8864c-6.7584-13.7216-19.3536-15.2064-24.4224-15.2064z" fill="#474747" p-id="8659"></path>
    <path d="M659.2 512.3072c-1.536 0-3.1232-0.1024-4.7104-0.3584l-56.9856-8.7552a95.872 95.872 0 0 1-69.632-48.4352l-28.8768-51.8656a30.73024 30.73024 0 0 1 11.8784-41.7792c14.848-8.2432 33.536-2.9184 41.7792 11.8784l28.8768 51.8656a35.01568 35.01568 0 0 0 25.2928 17.6128l56.9856 8.7552c16.7936 2.56 28.2624 18.2784 25.7024 35.0208a30.72 30.72 0 0 1-30.3104 26.0608z" fill="#45CFFF" p-id="8660"></path>
</svg>
```

上面的`fill=#474747`就是初始颜色 , 如果想要修改他的颜色 , 只要修改该颜色即可.或者删除`fill`属性.

自定义css颜色.

```vue
<svg-icon icon-class="shouchang" class-name="icon" />
```

```css
.icon{
  width: 50px;
  height: 50px;
  color:red
}
.icon{
  font-size: 60px;
  color:red
}
```

> 注意需要用`class-name`来指定class属性.当然css也可以指定svg的大小 . 
>
> font-size和width&height都可以修改大小

### element自带的icon

也可以使用elementUI自带的图标.具体使用方法自己看[官方文档.](https://element.eleme.cn/#/zh-CN)

## 环境变量

本项目是基于 `vue-cli`来进行构建，所以所有的环境变量配置都是基于`vue-cli`来实现和控制的。

```text
.env                # 在所有的环境中被载入
.env.[mode]         # 只在指定的模式中被载入
```

一个环境文件只包含环境变量的“键=值”对：

```text
FOO=bar
VUE_APP_SECRET=secret
```

> 注意！！！
>
> 环境变量必须以`VUE_APP_`为开头。如:`VUE_APP_API`、`VUE_APP_TITLE`
>
> 你在代码中可以通过如下方式获取:
>
> ```js
> console.log(process.env.VUE_APP_xxxx)
> ```

除了 `VUE_APP_*` 变量之外，在你的应用代码中始终可用的还有两个特殊的变量：

- `NODE_ENV` - 会是 "development"、"production" 或 "test" 中的一个。具体的值取决于应用运行的模式。
- `BASE_URL` - 会和 `vue.config.js` 中的 `publicPath` 选项相符，即你的应用会部署到的基础路径。

本项目的环境变量主要有:

`.env.development`

```reStructuredText
# just a flag
ENV = 'development'

# base api
VUE_APP_BASE_API = 'http://127.0.0.1:8002'

# vue-cli uses the VUE_CLI_BABEL_TRANSPILE_MODULES environment variable,
# to control whether the babel-plugin-dynamic-import-node plugin is enabled.
# It only does one thing by converting all import() to require().
# This configuration can significantly increase the speed of hot updates,
# when you have a large number of pages.
# Detail:  https://github.com/vuejs/vue-cli/blob/dev/packages/@vue/babel-preset-app/index.js

VUE_CLI_BABEL_TRANSPILE_MODULES = true

```

`.env.production`

```reStructuredText
# just a flag
ENV = 'production'

# base api
VUE_APP_BASE_API = 'http://fdevops.com:8001'
```

## 引入外部模块

### 引入依赖

在终端输入下面的命令完成安装：

```bash
$ npm install moment --save
```

> 加上 `--save` 参数会自动添加依赖到 package.json 中去。

### 建议方式

在 Vuejs 项目中使用 JavaScript 库的一个优雅方式是将其代理到 Vue 的原型对象上去. 按照这种方式, 我们引入 Moment 库:

**main.js**

```js
import moment from 'moment'
Object.defineProperty(Vue.prototype, '$moment', { value: moment })
```

**MyNewComponent.vue**

```js
export default {
  created() {
    console.log('The time is ', this.$moment().format('HH:mm'))
  }
}
```

> 这种方式只适合,该第三方库在很多components&views中都有用到才建议这样导入.

在诸多 Vue.js 应用中, Lodash, Moment, Axios, Async 等都是一些非常有用的 JavaScript 库. 但随着项目越来越复杂, 可能会采取组件化和模块化的方式来组织代码, 还可能要使应用支持不同环境下的服务端渲染. 除非你找到了一个简单而又健壮的方式来引入这些库供不同的组件和模块使用, 不然, 这些第三方库的管理会给你带来一些麻烦,这里来介绍一下 Vue.js 中使用第三方库的方式详情见该 [blog](https://github.com/dwqs/blog/issues/51)。

## 和服务端进行交互

### 一个请求文章列表页的例子：

```js
// api/article.js
import request from '../utils/request';
export function fetchList(query) {
  return request({
    url: '/article/list',
    method: 'get',
    params: query
  })
}


// views/example/list
import { fetchList } from '@/api/article'
export default {
  data() {
    list: null,
    listLoading: true
  },
  methods: {
    fetchData() {
      this.listLoading = true
      fetchList().then(response => {
        this.list = response.data.items
        this.listLoading = false
      })
    }
  }
}
```

### 设置多个 baseURL

我们可以通过环境变量设置多个`baseURL`，从而请求不同的 api 地址。

```bash
# .env.development
VUE_APP_BASE_API = '/dev-api' #注入本地 api 的根路径
VUE_APP_BASE_API2 = '/dev-api2' #注入本地 api 的根路径
```

之后根据环境变量创建`axios`实例，让它具有不同的`baseURL`。 @/utils/request.js

```js
// create an axios instance
const service = axios.create({
  baseURL: process.env.BASE_API, // api 的 base_url
  timeout: 5000 // request timeout
})

const service2 = axios.create({
  baseURL: process.env.BASE_API2, // api 的 base_url
  timeout: 5000 // request timeout
})
```

或者

```js
export function fetchList(query) {
  return request({
    url: '/article/list',
    method: 'get',
    params: query,
    baseURL: 'xxxx' // 直接通过覆盖的方式
  })
}
```

## 样式(css)

### CSS Modules

在样式开发过程中，有两个问题比较突出：

- 全局污染 —— CSS 文件中的选择器是全局生效的，不同文件中的同名选择器，根据 build 后生成文件中的先后顺序，后面的样式会将前面的覆盖；
- 选择器复杂 —— 为了避免上面的问题，我们在编写样式的时候不得不小心翼翼，类名里会带上限制范围的标示，变得越来越长，多人开发时还很容易导致命名风格混乱，一个元素上使用的选择器个数也可能越来越多，最终导致难以维护。

好在 vue 为我们提供了 [scoped](https://vue-loader.vuejs.org/guide/scoped-css.html#mixing-local-and-global-styles) 可以很方便的解决上述问题。 它顾名思义给 css 加了一个域的概念。

```css
/* 编译前 */
.example {
  color: red;
}

/* 编译后 */
.example[_v-f3f3eg9] {
  color: red;
}
```

### 目录结构

vue-element-admin 所有全局样式都在 `@/styles` 下设置

```bash
├── styles
│   ├── btn.scss                 # 按钮样式
│   ├── element-ui.scss          # 全局自定义 element-ui 样式
│   ├── index.scss               # 全局通用样式
│   ├── mixin.scss               # 全局mixin
│   ├── sidebar.scss             # sidebar css
│   ├── transition.scss          # vue transition 动画
│   └── variables.scss           # 全局变量
```

常见的工作流程是，全局样式都写在 `src/styles` 目录下，每个页面自己对应的样式都写在自己的 `.vue` 文件之中

```css
<style>
/* global styles */
</style>

<style scoped>
/* local styles */
</style>
```

### 自定义 element-ui 样式

现在我们来说说怎么覆盖 element-ui 样式。由于 element-ui 的样式我们是在全局引入的，所以你想在某个页面里面覆盖它的样式就不能加 scoped，但你又想只覆盖这个页面的 element 样式，你就可在它的父级加一个 class，用命名空间来解决问题。

```css
.article-page {
  /* 你的命名空间 */
  .el-tag {
    /* element-ui 元素*/
    margin-right: 0px;
  }
}
```

**当然也可以使用深度作用选择器 下文会介绍**

### 父组件改变子组件样式-深度选择器

当你子组件使用了 `scoped` 但在父组件又想修改子组件的样式可以 通过 `>>>` 或者来实现：

```css
<style scoped>
.a >>> .b { /* ... */ }
</style>
```

将会编译成

```css
.a[data-v-f3f3eg9] .b {
  /* ... */
}
```

如果你使用了一些预处理的东西，如 `sass` 你可以通过 `/deep/` 来代替 `>>>` 实现想要的效果。

所以你想覆盖某个特定页面 `element` 的 button 的样式，你可以这样做：

```css
.xxx-container >>> .el-button{
  xxxx
}
```

[官方文档](https://vue-loader.vuejs.org/en/features/scoped-css.html)

### Mixin

本项目没有设置自动注入 sass 的 mixin 到全局，所以需要在使用的地方手动引入 mixin

```scss
<style rel="stylesheet/scss" lang="scss">
  @import "src/styles/mixin.scss";
</style>
```

## 新增`views`|`api`|`组件`|`样式`

### **新增`views`**

新增完路由之后不要忘记在 `@/views` 文件下 创建对应的文件夹，一般性一个路由对应一个文件，该模块下的功能组件或者方法就建议在本文件夹下创建一个`utils`或`components`文件夹，各个功能模块维护自己的`utils`或`components`组件。如：

![img](https://panjiachen.gitee.io/gitee-cdn/vue-element-admin-site/8ca55a30-c22c-4143-aa8d-2a0d3e04fc33.png)

### 新增 `api`

![img](https://user-gold-cdn.xitu.io/2017/5/3/c185f7d37a268a1ff4044ff60f5341c0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如 article 模块下放的都是文章相关的 api，这样不管项目怎么累加，api和views的维护还是清晰的，当然也有一些全区公用的api模块，如upload，remoteSearch等等，这些单独放置就行。

### 新增组件

在全局的 `@/components` 只会写一些全局的组件，如富文本，各种搜索组件，封装的日期组件等等能被公用的组件。每个页面或者模块特定的业务组件则会写在当前 views 下面。如：`@/views/article/components/xxx.vue`。这样拆分大大减轻了维护成本。

**请记住拆分组件最大的好处不是公用而是可维护性！**

### 新增样式

页面的样式和组件是一个道理，全局的 `@/style` 放置一下全局公用的样式，每一个页面的样式就写在当前 `views`下面，请记住加上`scoped` 或者命名空间，避免造成全局的样式污染。

```css
<style>
/* global styles */
</style>

<style scoped>
/* local styles */
.xxx-container{
  /* name scoped */
  xxx
}
</style>
```

## 代码风格

### Component

所有的`Component`文件都是以大写开头 (PascalCase)，这也是官方所[推荐的](https://cn.vuejs.org/v2/style-guide/index.html#单文件组件文件的大小写-强烈推荐).

但除了 `index.vue`。

例子：

- `@/components/BackToTop/index.vue`
- `@/components/Charts/Line.vue`
- `@/views/example/components/Button.vue`

### JS 文件

所有的`.js`文件都遵循横线连接 (kebab-case)。

例子：

- `@/utils/open-window.js`
- `@/views/svg-icons/require-icons.js`
- `@/components/MarkdownEditor/default-options.js`

### Views

在`views`文件下，代表路由的`.vue`文件都使用横线连接 (kebab-case)，代表路由的文件夹也是使用同样的规则。

例子：

- `@/views/svg-icons/index.vue`
- `@/views/svg-icons/require-icons.js`

使用横线连接 (kebab-case)来命名`views`主要是出于以下几个考虑。

- 横线连接 (kebab-case) 也是官方推荐的命名规范之一 [文档](https://cn.vuejs.org/v2/style-guide/index.html#单文件组件文件的大小写-强烈推荐)
- `views`下的`.vue`文件代表的是一个路由，所以它需要和`component`进行区分(component 都是大写开头)
- 页面的`url` 也都是横线连接的，比如`https://www.xxx.admin/export-excel`，所以路由对应的`view`应该要保持统一
- 没有大小写敏感问题



# 项目注意事项

## 集成ES2020部分实用新特性

### [可选的链接操作](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)

有时候为了访问深层嵌套的属性，我们需要写一个很长的&&链去检查每个属性是否存在，代码如下：

```js
var price = result && result.body && result.body.data && result.body.data.price;
```

事实上在日常开发中我们经常要这么做，而且如果我们不这么做，那么很可能导致程序异常。为了简化上述的代码，于是乎新增了可选链运算符，代码如下：

```js
var price = result?.body?.data?.price;
```

可选运算符不仅能作用到属性上，也能作用到方法。

```js
const result = {}; 
const price = result?.body?.data?.price;  

const user = {}; 
const name = user?.getInfo?.().getName?.();  

const respone = {}; 
const firstProduct = respone?.body?.data?.products?.[0];
```



### [合并空运算符](https://babeljs.io/docs/en/babel-plugin-proposal-nullish-coalescing-operator)

获取对象的属性的时候，我们经常需要为 null/undefined 的属性设置默认值。目前正常的情况下我们可以使用 || 运算符，例如：

```js
var user = {}; 
var name = user.name || 'p9';
```

但是，这么做会导致false，0，空字符串等属性会被覆盖掉，这是我们不愿意看到的，这时候就可以使用空值合并运算符来避免。例如:

```js
const response = {
  settings: {
    nullValue: null,
    height: 400,
    animationDuration: 0,
    headerText: '',
    showSplashScreen: false
  }
};

const undefinedValue = response.settings.undefinedValue ?? 'some other default'; 
// result: 'some other default'

const nullValue = response.settings.nullValue ?? 'some other default'; 
// result: 'some other default'

const headerText = response.settings.headerText ?? 'Hello, world!'; 
// result: ''

const animationDuration = response.settings.animationDuration ?? 300; 
// result: 0

const showSplashScreen = response.settings.showSplashScreen ?? true; 
// result: false标准化的全局对象 - globalThis
```

具体来说就是大大降低程序出错的概率.多用用新特性.

## 第三方库

### loadsh

[loadsh是一个一致性、模块化、高性能的 JavaScript 实用工具库。](https://www.lodashjs.com/)

### moment

[JavaScript 日期处理类库](http://momentjs.cn/)