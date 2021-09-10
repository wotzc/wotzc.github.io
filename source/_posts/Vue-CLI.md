---
title: Vue CLI
date: 2021-09-03 19:05:48
tags:
- Vue
- Vue-CLI
- 前端
categories: Vue
---

# Vue-CLI的使用

##  介绍

vue脚手架的作用是用来自动一键生成vue+webpack的项目模版，包括依赖库，免去你手动安装各种插件，寻找各种cdn并一个个引入的麻烦。

vue脚手架指的是vue-cli，它是一个专门为单页面应用快速搭建繁杂的脚手架，它可以轻松的创建新的应用程序而且可用于自动生成vue和webpack的项目模板。

vue-cli是有Vue提供的一个官方cli，专门为单页面应用快速搭建繁杂的脚手架。它是用于自动生成vue.js+webpack的项目模板，是为现代前端工作流提供了 batteries-included 的构建设置。只需要几分钟的时间就可以运行起来并带有热重载，保存时 lint 校验，以及生产环境可用的构建版本

## 安装

Node 版本要求

Vue CLI 4.x 需要 [Node.js](https://nodejs.org/) v8.9 或更高版本 (推荐 v10 以上)。你可以使用 [n](https://github.com/tj/n)，[nvm](https://github.com/creationix/nvm) 或 [nvm-windows](https://github.com/coreybutler/nvm-windows) 在同一台电脑中管理多个 Node 版本。

可以使用下列任一命令安装这个新的包：

```sh
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

安装之后，你就可以在命令行中访问 `vue` 命令。你可以通过简单运行 `vue`，看看是否展示出了一份所有可用命令的帮助信息，来验证它是否安装成功。

你还可以用这个命令来检查其版本是否正确：

```sh
vue --version
```

### 升级

如需升级全局的 Vue CLI 包，请运行：

```sh
npm update -g @vue/cli

# 或者
yarn global upgrade --latest @vue/cli
```

## 创建项目

切换到你要创建项目的目录，然后运行以下命令来创建一个新项目：

```sh
vue create hello-world
```

你会被提示选取一个 preset。你可以选默认的包含了基本的 Babel + ESLint 设置的 Vue2或者Vue3，也可以选“手动选择特性”来选取需要的特性。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vueprojectinit.png)

**使用图形化界面**

你也可以通过 `vue ui` 命令以图形化界面创建和管理项目：

```sh
vue ui
```

上述命令会打开一个浏览器窗口，并以图形化界面将你引导至项目创建的流程。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/initprojectinweb.png)

## 启动项目

```sh
npm run serve
```

> ```asciiarmor
> 如出现下载缓慢请配置npm 淘宝镜像：npm config set registry https://registry.npm.taobao.org
> ```

> ```dylan
> Vue 脚手架隐藏了所有webpack 相关的配置，若想查看具体的webpakc 配置，请执行：vue inspect > output.js
> ```

##  vue.config.js

`vue.config.js` 是一个可选的配置文件，如果项目的 (和 `package.json` 同级的) 根目录中存在这个文件，那么它会被 `@vue/cli-service` 自动加载。你也可以使用 `package.json` 中的 `vue` 字段，但是注意这种写法需要你严格遵照 JSON 的格式来写。

这个文件应该导出一个包含了选项的对象：

```js
// vue.config.js

/**
 * @type {import('@vue/cli-service').ProjectOptions}
 */
module.exports = {
  // 选项...
}
```

## 模板项目的结构

```
├── node_modules 
├── public
│   ├── favicon.ico: 页签图标
│   └── index.html: 主页面
├── src
│   ├── assets: 存放静态资源
│   │   └── logo.png
│   │── component: 存放组件
│   │   └── HelloWorld.vue
│   │── App.vue: 汇总所有组件
│   │── main.js: 入口文件
├── .gitignore: git版本管制忽略的配置
├── babel.config.js: babel的配置文件
├── package.json: 应用包配置文件 
├── README.md: 应用描述文件
├── package-lock.json：包版本控制文件
```

------------

# ref属性

`ref` 被用来给元素或子组件注册引用信息。

- 引用信息将会注册在父组件的 `$refs` 对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素（id的替代者）；
- 如果用在子组件上，引用就指向组件实例`VueComponent`：

```html
<!-- `vm.$refs.p` will be the DOM node -->
<p ref="p">hello</p>

<!-- `vm.$refs.child` will be the child component instance -->
<child-component ref="child"></child-component>
```

当 `v-for` 用于元素或组件的时候，引用信息将是包含 DOM 节点或组件实例的数组。

获取：```this.$refs.xxx```

-------------

# props

props 可以是数组或对象，用于接收来自父组件的数据。

**三种读取方式**

- **示例**：

  ```javascript
  // 简单语法
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })
  
  // 指定名称和类型
  Vue.component('props-demo-simple', {
    props: {
      title: String,
      likes: Number,
      isPublished: Boolean,
      commentIds: Array,
      author: Object,
      callback: Function,
      contactsPromise: Promise // or any other constructor
   }
  })
  
  // 对象语法，提供验证
  Vue.component('props-demo-advanced', {
    props: {
      // 检测类型
      height: Number,
      // 检测类型 + 其他验证
      age: {
        type: Number,
        default: 0, // 默认值
        required: true,
        validator: function (value) {
          return value >= 0
        }
      }
    }
  })
  ```

> ```asciiarmor
> 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。
> ```

--------------

# mixin

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。

当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。

```javascript
// 定义一个混入对象
var mixin = {
  created: function () { console.log(1) }
}
```

混入对象的局部使用

```javascript
// 定义一个使用混入对象的组件
var vm = new Vue({
  created: function () { console.log(2) },
  mixins: [mixin]
})

// => 1
// => 2
```

如果你的混入包含一个 created 钩子，而创建组件本身也有一个，那么两个函数都会被调用。

Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用。

全局使用

全局混入：`Vue.mixin(xxx)`

全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为。**不推荐在应用代码中使用**。

----

# 插件

插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：

1. 添加全局方法或者 property。如：vue-custom-element
2. 添加全局资源：指令/过滤器/过渡等。如 vue-touch
3. 通过全局混入来添加一些组件选项。如 vue-router
4. 添加 Vue 实例方法，通过把它们添加到 `Vue.prototype` 上实现。
5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能。如 vue-router

## 开发插件

Vue.js 的插件应该暴露一个 `install` 方法。这个方法的第一个参数是 `Vue` 构造器，第二个参数是一个可选的选项对象：

```javascript
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或 property
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```

## 使用插件

通过全局方法 `Vue.use()` 使用插件。它需要在你调用 `new Vue()` 启动应用之前完成：

```javascript
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

new Vue({
  // ...组件选项
})
```

# scoped样式

1. 作用：让样式在局部生效，防止冲突。

2. 写法：

   ```html
   <style scoped>
     @media (min-width: 250px) {
       .list-container:hover {
         background: orange;
       }
     }
   </style>
   ```

这个可选 `scoped` attribute 会自动添加一个唯一的 attribute (比如 `data-v-21e5b78`) 为组件内 CSS 指定作用域，编译的时候 `.list-container:hover` 会被编译成类似 `.list-container[data-v-21e5b78]:hover`。

-----------

# 组件的自定义事件

组件的自定义事件是一种组件间通信方式，适用于：子组件给父组件传递数据。

不同于组件和 prop，事件名不存在任何自动化的大小写转换。而是触发的事件名需要完全匹配监听这个事件所用的名称。

不同于组件和 prop，事件名不会被用作一个 JavaScript 变量名或 property 名，所以就没有理由使用 camelCase 或 PascalCase 了。并且 `v-on` 事件监听器在 DOM 模板中会被自动转换为全小写 (因为 HTML 是大小写不敏感的)，所以 `v-on:myEvent` 将会变成 `v-on:myevent`——导致 `myEvent` 不可能被监听到。

因此，我们推荐你**始终使用 kebab-case 的事件名**。

**步骤：**

先在父组件中给子组件`TodoFooter`绑定事件:

```html
<TodoFooter @custom-event="doSomething"></TodoFooter>
```

并在`methods`中定义doSomething回调方法:

```javascript
doSomething(msg) {
  console.log(msg)
}
```

在子组件中绑定一个按钮来触发自定义事件:

```html
<button @click="triggerCustomEvent">触发自定义事件</button>
```

触发自定义事件:

```javascript
triggerCustomEvent() {
  this.$emit("custom-event","doSomething...")
}
```

上面绑定自定义事件还有第二种方式：

```javascript
<TodoFooter ref="todofooter"></TodoFooter>
......
mounted() {
	this.$refs.todofooter.$on("custom-event",this.doSomething)
},
```

**解绑自定义事件：**

**vm.$off( [event, callback\] )**

- **参数**：

  - `{string | Array<string>} event` (只在 2.2.2+ 支持数组)
  - `{Function} [callback]`

- **用法**：

  移除自定义事件监听器。

  - 如果没有提供参数，则移除所有的事件监听器；
  - 如果只提供了事件，则移除该事件所有的监听器；
  - 如果同时提供了事件与回调，则只移除这个回调的监听器。

```javascript
this.$off("custom-event")
```

**将原生事件绑定到组件**

你可能有很多次想要在一个组件的根元素上直接监听一个原生事件。这时，你可以使用 `v-on` 的 `.native` 修饰符：

```html
<TodoFooter @click.native="show"></TodoFooter>
```

-----------------

# 全局事件总线

vue组件中的数据传递最最常见的就是父子组件之间的传递。父传子通过props向下传递数据给子组件；子传父通过$emit发送事件，并携带数据给父组件。而有时两个组件之间毫无关系，或者他们之间的结构复杂，如何传递数据呢？这时就要用到 vue 中的事件总线 EventBus的概念。

## 使用 EventBus

- 第一种方式: 可以在 main.js中，初始化 EventBus

```javascript
new Vue({
  render: h => h(App),
  beforeCreate() {
    Vue.prototype.$bus = this
  }
}).$mount('#app')
```

X组件想接收数据，则在X组件中给`$bus`绑定自定义事件，事件的<span style="color:red">回调留在A组件自身。</span>

```javascript
methods(){
  demo(data){
  }
}
......
mounted() {
  this.$bus.$on('xxxx',this.demo)
}
```

Y组件想要给X组件传递数据，则在Y组件中发送事件

```javascript
this.$bus.$emit('xxxx',param)
```

在beforeDestroy钩子中，用$off去解绑<span style="color:red">当前组件所用到的</span>事件

语法：`this.$EventBus.$off(要移除监听的事件名)`

-----------------------

# $nextTick

### vm.$nextTick( [callback\] )

- **参数**：

  - `{Function} [callback]`

- **用法**：

  将回调延迟到下次 DOM 更新循环之后执行。

- **示例**：

  ```javascript
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // 修改数据
        this.message = 'changed'
        // DOM 还没有更新
        this.$nextTick(function () {
          // DOM 现在更新了
          // `this` 绑定到当前实例
          this.doSomethingElse()
        })
      }
    }
  })
  ```

--------------

# 过渡&动画

## 单元素/组件的过渡

Vue 提供了 `transition` 的封装组件，在下列情形中，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

这里是一个典型的例子：

```html
<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```

```javascript
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
```

```css
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
```

在进入/离开的过渡中，会有 6 个 class 切换。

1. `v-enter`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
2. `v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
3. `v-enter-to`：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 `v-enter` 被移除)，在过渡/动画完成之后移除。
4. `v-leave`：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
5. `v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
6. `v-leave-to`：定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 `v-leave` 被删除)，在过渡/动画完成之后移除。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/transition.png)

对于这些在过渡中切换的类名来说，如果你使用一个没有名字的 `<transition>`，则 `v-` 是这些类名的默认前缀。如果你使用了 `<transition name="my-transition">`，那么 `v-enter` 会替换为 `my-transition-enter`。

`v-enter-active` 和 `v-leave-active` 可以控制进入/离开过渡的不同的缓和曲线。

## CSS 过渡

常用的过渡都是使用 CSS 过渡。

下面是一个简单例子：

```html
<div id="example-1">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```

```javascript
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
```

```css
/* 可以设置不同的进入和离开动画 */
/* 设置持续时间和动画函数 */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
```

## 第三方 CSS 动画库

我们可以通过以下 attribute 来自定义过渡类名：

- `enter-class`
- `enter-active-class`
- `enter-to-class`
- `leave-class`
- `leave-active-class`
- `leave-to-class`

他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 [Animate.css](https://daneden.github.io/animate.css/) 结合使用十分有用。

Install with npm:

```shell
$ npm install animate.css
```

Import it into your file:

```js
import "animate.css"
```

```html
<div id="demo">
    <button @click="show = !show">Toggle render</button>
    <transition
      name="animate__animated animate__bounce"
      enter-active-class="animate__bounce"
      leave-active-class="animate__backOutDown"
    >
      <h1 v-if="show">An animated element</h1>
    </transition>
  </div>
```

那么怎么同时渲染整个列表，比如使用 `v-for`？在这种场景中，使用 `<transition-group>` 组件。在我们深入例子之前，先了解关于这个组件的几个特点：

- 不同于 `<transition>`，它会以一个真实元素呈现：默认为一个 `<span>`。你也可以通过 `tag` attribute 更换为其他元素。
- 过渡模式不可用，因为我们不再相互切换特有的元素。
- 内部元素**总是需要**提供唯一的 `key` attribute 值。
- CSS 过渡的类将会应用在内部的元素中，而不是这个组/容器本身。

--------

# Vue-CLI配置代理

如果你的前端应用和后端 API 服务器没有运行在同一个主机上，你需要在开发环境下将 API 请求代理到 API 服务器。这个问题可以通过 `vue.config.js` 中的 `devServer.proxy` 选项来配置。

resolve `CORS policy` `Access-Control-Allow-Origin`

- `devServer.proxy` 可以是一个指向开发环境 API 服务器的字符串：

```js
module.exports = {
  devServer: {
    proxy: 'http://localhost:4000'
  }
}
```

这会告诉开发服务器将任何未知请求 (没有匹配到静态文件的请求) 代理到`http://localhost:4000`。

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （优先匹配前端资源）

**配置多个代理**

```js
module.exports = {
	devServer: {
      proxy: {
      '/api1': {// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api1': ''}
      },
      '/api2': {// 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api2': ''}
      }
    }
  }
}
/*
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
2. 缺点：配置略微繁琐，请求资源时必须加前缀。

-------------

# 插槽(slot)

Slot 通俗的理解就是“占坑”，在组件模板中占好了位置，当使用该组件标签时候，组件标签里面的内容就会自动填坑（替换组件模板中<span style="color:red">`slot`</span>位置）
并且可以作为承载分发内容的出口

因为在2.6.0中，**具名插槽** 和 **作用域插槽** 引入了一个新的统一的语法 (即<span style="color:red">`v-slot`</span> 指令)。它取代了 <span style="color:red">`slot`</span> 和 <span style="color:red">`slot-scope`</span>

## 插槽内容

Vue 实现了一套内容分发的 API，这套 API 的设计灵感源自 [Web Components 规范草案](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)，将 `<slot>` 元素作为承载分发内容的出口。

它允许你像这样合成组件：

```html
<HelloWorld>
  Hello World
</HelloWorld>
```

然后你在 `HelloWorld` 组件中可能会写为：

```html
<div class="hello">
	<slot></slot>
</div>
```

当组件渲染的时候，`<slot></slot>` 将会被替换为“Hello World”。插槽内可以包含任何模板代码，包括 HTML。

如果`HelloWorld` 组件 的 `template` 中**没有**包含一个 `<slot>` 元素，则该组件起始标签<span style="color:red">`<HelloWorld>`</span>和结束标签 <span style="color:red">`</HelloWorld>`</span>之间的任何内容都会被抛弃。

## 具名插槽

对于这样的情况，`<slot>` 元素有一个特殊的 attribute：`name`

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

一个不带 `name` 的 `<slot>` 出口会带有隐含的名字“default”。

在向具名插槽提供内容的时候，我们可以在一个 `<template>` 元素上使用 `v-slot` 指令，并以 `v-slot` 的参数的形式提供其名称：

```html
<HelloWorld>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</HelloWorld>
```

现在 <span style="color:red">`<template>`</span>元素中的所有内容都将会被传入相应的插槽。任何没有被包裹在带有<span style="color:red">`v-solt`</span>的 <span style="color:red">`<template>`</span> 中的内容都会被视为默认插槽的内容。

然而，如果你希望更明确一些，仍然可以在一个 <span style="color:red">`<template>`</span> 中包裹默认插槽的内容：<span style="color:red">`<template>`</span>

```html
<HelloWorld>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</HelloWorld>
```

任何一种写法都会渲染出：

```html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

> ```asciiarmor
> 注意 v-slot 只能添加在 <template> 上
> ```

## 具名插槽的缩写

跟 `v-on` 和 `v-bind` 一样，`v-slot` 也有缩写，即把参数之前的所有内容 (`v-slot:`) 替换为字符 `#`。例如 `v-slot:header` 可以被重写为 `#header`：

```html
<HelloWorld>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</HelloWorld>
```

## 作用域插槽

有时让插槽内容能够访问子组件中才有的数据是很有用的。例如，父组件访问子组件中才有的数据

在子组件<span style="color:red">`HelloWorld`</span>中先定义数据

```js
data() {
    return {
      user: {
        firstName : '张',
        lastName : '三'
      }
    }
}
```

为了让 `user` 在父级的插槽内容中可用，我们可以将 `user` 作为 `<slot>` 元素的一个 attribute 绑定上去：

```html
<span>
  <slot :user="user"></slot>
</span>
```

在父组件中使用子组件的数据：

```html
<HelloWorld>
  <template slot-scope="{user}">
    <h1>法外狂徒{{ user.firstName }}{{ user.lastName }}</h1>
  </template>
</HelloWorld>
```

效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/slotshow.png)

--------------

