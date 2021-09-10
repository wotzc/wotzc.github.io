---
title: Vue Router
date: 2021-09-09 11:26:13
tags:
- Vue
- Vue Router
- 前端
categories: Vue
---

#  简介

Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：

- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于 Vue.js 过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的 CSS class 的链接
- HTML5 历史模式或 hash 模式，在 IE9 中自动降级
- 自定义的滚动条行为

-------------

# 安装

###  NPM

```bash
npm install vue-router
```

# 使用实例

## 引入插件并使用

在`main.js`中引入插件并使用

```js
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

## 编写router配置项:

```js
import VueRouter from 'vue-router'
import Chinese from "../pages/Chinese.vue"
import English from "../pages/English.vue"

export default new VueRouter({
    routes: [
        {
            path: '/chinese',
            component: Chinese
        },
        {
            path: '/english',
            component: English
        }
    ]
});
```

## 使用router

在`main.js`中引入`router`并使用

```js
import router from './router'

new Vue({
  render: h => h(App),
  router: router
}).$mount('#app')
```

## 实现路由页面切换

（active-class可配置高亮样式）

```html
<router-link class="list-group-item" active-class="active" to="/chinese">中文</router-link>
<router-link class="list-group-item" active-class="active" to="/english">英文</router-link>
```

指定组件渲染的位置

```html
<!-- 路由匹配到的组件将渲染在这里 -->
<router-view></router-view>
```

## 几个注意点

1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息。
4. 整个应用只有一个router，可以通过组件的```$router```属性获取到。

# 嵌套路由

实际生活中的应用界面，通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件。

配置路由：

```js
export default new VueRouter({
    routes: [
        {
            path: '/chinese',
            component: Chinese
        },
        {
            path: '/english',
            component: English,
            //通过children配置子级路由
            children: [
                {
                    path: 'book',
                    component: Book
                },
                {
                    path: 'author',
                    component: Author
                }
            ]
        }
    ]
});
```

路由跳转:

```html
<li>
  <router-link class="list-group-item" active-class="active" to="/english/book">Book</router-link>
</li>
<li>
  <router-link class="list-group-item" active-class="active" to="/english/author">Author</router-link>
</li>
```

-------------------

# 命名路由

有时候，通过一个名称来标识一个路由显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。你可以在创建 Router 实例的时候，在 `routes` 配置中给某个路由设置名称。

```js
export default new VueRouter({
    routes: [
        {
            name: 'chineseRouter',
            path: '/chinese',
            component: Chinese
        },
        {
            path: '/english',
            component: English,
            children: [
                {
                    name: 'bookRouter',
                    path: 'book',
                    component: Book
                },
                {
                    name: 'authorRouter',
                    path: 'author',
                    component: Author
                }
            ]
        }
    ]
});
```

路由跳转:

```js
<!--简化前，需要写完整的路径 -->
<router-link to="/english/book">Book</router-link>
<!--简化后，直接通过名字跳转 -->
<router-link :to="{name : 'bookRouter'}">Book</router-link>
```

-----------

# 路由的query参数

```js
<!-- 跳转路由并携带query参数，to的字符串写法 -->
<router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">{{m.title}}</router-link>&nbsp;&nbsp;

<!-- 跳转路由并携带query参数，to的对象写法 -->
<router-link :to="{
  path: '/english/author/info',
  query: {
      id: author.id,
      name: author.name,
      info: author.info
  }
}">
{{ author.name }}
</router-link>&nbsp;&nbsp;    
```

接收参数：

```html
<ul>
    <li>编号: {{$route.query.id}}</li>
    <li>作家: {{$route.query.name}}</li>
    <li>简介: {{$route.query.info}}</li>
</ul>
```

-------------

# 路由的param参数

在 `vue-router` 的路由路径中使用“动态路径参数”(dynamic segment) 

```js
children: [
    {
        name: 'bookRouter',
        path: 'book',
        component: Book
    },
    {
        name: 'authorRouter',
        path: 'author',
        component: Author,
        children: [
            {
                name: 'authorInfoRouter',
                path: 'info/:id/:name/:info',//使用占位符声明接收params参数
                component: AuthorInfo
            }
        ]
    }
]
```

路由跳转:

```html
<router-link :to="{
  name: 'authorInfoRouter',
  params: {
      id: author.id,
      name: author.name,
      info: author.info
  }
}">
{{ author.name }}
</router-link>&nbsp;&nbsp;
```

> <span style="color:red">特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！</span>

接受参数:

当匹配到一个路由时，参数值会被设置到 `this.$route.params`

```html
<ul>
    <li>编号: {{$route.params.id}}</li>
    <li>作家: {{$route.params.name}}</li>
    <li>简介: {{$route.params.info}}</li>
</ul>
```

------------

# 路由的props配置

在上面的例子中，我们读取参数需要不停重复的写 `this.$route.params` 前缀，很繁琐，使用props配置可以解决

## 布尔模式

如果 `props` 被设置为 `true`，`route.params` 将会被设置为组件属性。

若`props`为true，就会把路由组件收到的所有<span style="color:red">`params`</span>参数，以`props`的形式传给AuthorInfo组件

```js
children: [
    {
        name: 'authorInfoRouter',
        path: 'info/:id/:name/:info',
        component: AuthorInfo,
        props: true
    }
]
```

使用`props` 配置项接受参数：

```js
export default {
    components: {},
    props: ['id','name','info'],
    data() {
        //这里存放数据
        return {

        }
    }
},
```

使用参数

```html
<ul>
    <li>编号: {{id}}</li>
    <li>作家: {{name}}</li>
    <li>简介: {{info}}</li>
</ul>
```

## 函数模式

你可以创建一个函数返回 `props`。这样你便可以将参数转换成另一种类型，将静态值与基于路由的值结合等等。

```js
children: [
    {
        name: 'authorInfoRouter',
        path: 'info',
        component: AuthorInfo,
        props($route) {
            return {
                id: $route.query.id,
                name: $route.query.name,
                info: $route.query.info
            }
        }
    }
]
```

`props`函数的结构赋值形式:

```js
props({query: {id, name, info}}) {
    return {
        id: id,
        name: name,
        info: info
    }
}
```

# `<router-link>`的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为```push```和```replace```，```push```是追加历史记录（相当于向一个栈中添加元素），```replace```是替换当前记录（相当于替换掉栈顶元素）。路由跳转时候默认为```push```
3. 如何开启```replace```模式：```<router-link replace>News</router-link>```

# 编程式的导航

除了使用 `<router-link>` 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法`push`，通过编写代码来实现。

| 声明式                    | 编程式             |
| ------------------------- | ------------------ |
| `<router-link :to="...">` | `router.push(...)` |

**注意：在 Vue 实例内部，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`。**

`router.push(location, onComplete?, onAbort?)`

可选的在 `router.push` 或 `router.replace` 中提供 `onComplete` 和 `onAbort` 回调作为第二个和第三个参数。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后) 或终止 (导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由) 的时候进行相应的调用。

想要导航到不同的 URL，则使用 `router.push` 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

```js
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ 
    name: 'user', 
    params: { 
        userId: '123' 
    }
})

// 带查询参数，变成 /register?plan=private
router.push({ 
    path: 'register', 
    query: { 
        plan: 'private' 
    }
})
```

**注意：如果提供了 `path`，`params` 会被忽略，使用`params`就不用`path`，而是用`name`**

`router.replace(location, onComplete?, onAbort?)`

跟 `router.push` 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录，相当于替换掉栈顶元素。

| 声明式                            | 编程式                |
| --------------------------------- | --------------------- |
| `<router-link :to="..." replace>` | `router.replace(...)` |

```js
router.replace({ 
    path: 'register', 
    query: { 
        plan: 'private' 
    }
})
```

`forward`  、`back`  、`go`

```js
this.$router.forward() //前进
this.$router.back() //后退

// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
```

------------

# keep-alive

- **Props**：

  - `include` - 字符串或正则表达式。只有名称匹配的组件会被缓存。
  - `exclude` - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。
  - `max` - 数字。最多可以缓存多少组件实例。

- **用法**：

  `<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在组件的父组件链中。

  当组件在 `<keep-alive>` 内被切换，它的 `activated` 和 `deactivated` 这两个生命周期钩子函数将会被对应执行。

主要用于保留组件状态或避免重新渲染。

`include` 和 `exclude` prop 允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组（组件名）来表示：

```html
<keep-alive include="News"> 
    <router-view></router-view>
</keep-alive>
```

# 路由相关的两个生命周期钩子

## activated

- **类型**：`Function`

- **详细**：

  被 keep-alive 缓存的组件激活时调用。

- **代码**

```js
export default {
  //import 引入的组件需要注入到对象中才能使用
  name: "English",
  components: {},
  props: {},
  data() {
    //这里存放数据
    return {};
  },
  //计算属性类似于data 概念
  computed: {},
  activated() {}, // 被 keep-alive 缓存的组件激活时调用。
  deactivated() {} // 被 keep-alive 缓存的组件停用时调用。
};
```

## deactivated

- **类型**：`Function`

- **详细**：

  被 keep-alive 缓存的组件停用时调用。

----

# 路由守卫

作用：对路由进行权限控制

## 全局前置守卫

你可以使用 `router.beforeEach` 注册一个全局前置守卫：

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 **等待中**。

每个守卫方法接收三个参数：

- **`to: Route`**: 即将要进入的目标路由对象
- **`from: Route`**: 当前导航正要离开的路由
- **`next: Function`**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。
  - **`next()`**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
  - **`next(false)`**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
  - **`next('/')` 或者 `next({ path: '/' })`**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 `router-link` 的 `to` prop 或 `router.push`中的选项。
  - **`next(error)`**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 `router.onError()`注册过的回调。

> <span style="color:red">**确保 `next` 函数在任何给定的导航守卫中都被严格调用一次。它可以出现多于一次，但是只能在所有的逻辑路径都不重叠的情况下，否则钩子永远都不会被解析或报错**。</span>

```js
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```

## 全局后置钩子

你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

```js
router.afterEach((to, from) => {
  // ...
})
```

## 路由独享的守卫

你可以在路由配置上直接定义 `beforeEnter` 守卫：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

这些守卫与全局前置守卫的方法参数是一样的

##  组件内的守卫

最后，你可以在路由组件内直接定义以下路由导航守卫：

- `beforeRouteEnter`
- `beforeRouteUpdate` (2.2 新增)
- `beforeRouteLeave`

```js
export default {
  //import 引入的组件需要注入到对象中才能使用
  name: "English",
  components: {},
  props: {},
  data() {
    //这里存放数据
    return {};
  },
  beforeRouteEnter(to, from, next) {
    // 通过路由规则，进入该组件时被调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 通过路由规则，离开该组件时被调用
    // 可以访问组件实例 `this`
  }
```

`beforeRouteEnter` 守卫 **不能** 访问 `this`，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。

不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以**不支持**传递回调，因为没有必要了。

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

------------

## 路由元信息

定义路由的时候可以配置 `meta` 字段：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          // a meta field
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```

通过`to.meta.requiresAuth`访问