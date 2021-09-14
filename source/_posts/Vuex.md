---
title: Vuex
date: 2021-09-08 09:38:03
tags:
- Vue
- Vuex
- 前端
categories: Vue
---

#  什么是Vuex?

Vuex是专门在Vue 中实现集中式状态（数据）管理的一个Vue 插件，对vue 应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

当我们的应用遇到**多个组件共享状态**时，单向数据流的简洁性很容易被破坏：

- 多个视图依赖于同一状态。
- 来自不同视图的行为需要变更同一状态。

对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

因此，我们为什么不把组件的共享状态抽取出来，以一个全局单例模式管理呢？在这种模式下，我们的组件树构成了一个巨大的“视图”，不管在树的哪个位置，任何组件都能获取状态或者触发行为！

通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。这就是 Vuex 背后的基本思想.

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vuex.png)

# 什么情况下我应该使用 Vuex？

Vuex 可以帮助我们管理共享状态，并附带了更多的概念和框架。这需要对短期和长期效益进行权衡。

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。但是，如果您需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。引用 Redux 的作者 Dan Abramov 的话说就是：

> Flux 架构就像眼镜：您自会知道什么时候需要它。

--------------

# 使用

## 安装vuex

`NPM`

```bash
npm install vuex 
```

## 使用Store

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用

1. 创建文件：```src/store/index.js```

   ```js
   
   import Vue from 'vue'
   import Vuex from 'vuex'
   
   Vue.use(Vuex)
   
   const store = new Vuex.Store({
       actions: {
   
       },
       state: {
           count: 0
       },
       mutations: {
           increment(state) {
               state.count++
           }
       }
   })
   ```

2. 在```main.js```中创建vm时传入```store```配置项

   为了在 Vue 组件中访问 `this.$store` property，你需要为 Vue 实例提供创建好的 store。Vuex 提供了一个从根组件向所有子组件，以 `store` 选项的方式“注入”该 store 的机制：

   ```js
   new Vue({
     el: '#app',
     store: store,
   })
   ```

   > 如果使用 ES6，你也可以以 ES6 对象的 property 简写 (用在对象某个 property 的 key 和被传入的变量同名时)：

   ```js
   new Vue({
     el: '#app',
     store
   })
   ```

3. 在子组件<span style="color:red">HelloWorld</span>中绑定一个方法，用来改变store中的数据

   ```html
   <div class="container">
        <h1>当前的值是:{{$store.state.count}}</h1>
        <button @click="increase">+1</button>
   </div>
   ```

   在组件中使用 `this.$store.dispatch('xxx')` 分发 action

   ```js
   methods: {
       increase() {
         this.$store.dispatch('increment',1)
       }
   }
   ```

4. 完善```src/store/index.js```中的方法

   ```js
   import Vue from 'vue'
   import Vuex from 'vuex'
   
   Vue.use(Vuex)
   
   const store = new Vuex.Store({
       actions: {
           //  Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 		   			context.state 和 context.getters 来获取 state 和 getters。
           // 参数value值是通过dispatch方法传过来的参数
           increment(context, value) {
               context.commit("increment",value)
           }
       },
       state: {
           count: 0
       },
       mutations: {
           increment(state,value) {
               state.count += value
           }
       }
   })
   
   export default store
   ```

   

5. 效果

   点击按钮前：

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vuexchangebefore.png)

   点击按钮5下后

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vuexchangeafter.png)

------------------------

## Getter

Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。

Getter 接受 state 作为其第一个参数：

```js
const store = new Vuex.Store({
    actions: {
        increase(context, value) {
            context.commit("increase",value)
        }
    },
    state: {
        dollar: 0,
        rate: 7
    },
    getters: {
        rmb(state){
            return state.dollar * state.rate
        }
    },
    mutations: {
        increase(state,value) {
            state.dollar += value
        }
    }
})
export default store
```

Getter 会暴露为 `store.getters` 对象，你可以以属性的形式访问这些值：

```js
$store.getters.rmb 
```

## mapGetters 辅助函数

先引入mapGetters 

```js
import { mapGetters } from 'vuex'
```

数组写法

```js
computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
```

如果你想将一个 getter 属性另取一个名字，使用对象形式：

```js
...mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

## mapState 辅助函数

先引入mapState 

```js
import { mapState } from 'vuex'
```

当一个组件需要获取多个状态的时候，将这些状态都声明为计算属性会有些重复和冗余。为了解决这个问题，我们可以使用 `mapState` 辅助函数帮助我们生成计算属性，让你少按几次键：

当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 `mapState` 传一个字符串数组。

```js
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

对象写法

```js
computed: mapState([
  // 映射 this.count 为 store.state.count
  count:'count'
])
```

## mapActions 辅助函数

先导入mapActions 

```js
import { mapActions } from 'vuex'
```

数组写法：

```js
 methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
```

对象写法:

```js
...mapActions({
  add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
})
```

## mapMutations 辅助函数

先导入mapActions 

```js
import { mapMutations } from 'vuex'
```

数组写法：

```js
methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
]),
```

对象写法

```js
...mapMutations({
  add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
})
```

#  Module模块化

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割。

默认情况下，模块内部的 action、mutation 和 getter 是注册在**全局命名空间**的——这样使得多个模块能够对同一 mutation 或 action 作出响应。

如果希望你的模块具有更高的封装度和复用性，你可以通过添加 `namespaced: true` 的方式使其成为带命名空间的模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。

```js
const moduleA = {
  namespaced:true,//开启命名空间
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  namespaced:true,//开启命名空间
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
```

**在组件中对数据进行读取:**

读取<span style="color:red">`state`</span>数据：

```js
//第一个参数空间名称字符串，第二个参数是空间名称字符串的中的参数
computed: {
    ...mapState('currency',['dollar','rate'])
  }
```

读取<span style="color:red">`getters`</span>数据：

```js
//第一个参数空间名称字符串
computed: {
    ...mapGetters('currency',['rmb'])
  }
```

组件中调用<span style="color:red">`dispatch`</span>：

```js
methods: {
    //方式一：自己直接dispatch
    this.$store.dispatch('currency/increase',1),
    //方式二：借助mapGetters读取：
    ...mapActions('currency',['increase'])
},

```

组件中调用<span style="color:red">`commit`</span>：

```js
methods: {
    addOnePerson() {
    	//方式一：自己直接dispatch
        this.$store.commit('person/addOnePerson',newPerson)
    },
    //方式二：借助mapGetters读取：
    ...mapActions('person',['addOnePerson'])
},
```

