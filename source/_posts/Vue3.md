---
title: Vue3
date: 2021-09-11 19:52:10
tags: 
- Vue
- Vue3
- 前端
categories: Vue
---

#  介绍

2020年9月18日Vue.js 3.0“海贼王”正式发布。该框架的这个新主要版本提供了改进的性能、更小的包大小、更好的 TypeScript 集成、用于处理大规模用例的新 API，以及该框架未来长期迭代的坚实基础。

特点：

- 随着时间的推移，它演变成我们所说的“渐进式框架”：一个可以逐渐学习和采用的框架，同时随着用户处理越来越多的要求的场景提供持续的支持。
- Vue 3.0 核心仍然可以通过一个简单的`<script>`标签使用，但它的内部已经从头开始重写为[一组解耦模块](https://github.com/vuejs/vue-next/tree/master/packages)。新架构提供了更好的可维护性，并允许最终用户通过 tree-shaking 将运行时大小减少一半。
- 2.x 基于对象的 API 在 Vue 3 中基本保持不变。但是，3.0 还引入了[Composition API——](https://v3.vuejs.org/guide/composition-api-introduction.html)一组新的 API，旨在解决 Vue 在大规模应用程序中使用的痛点。
- 性能改进
- Vue 3 的代码库是用 TypeScript 编写的，具有自动生成、测试和捆绑的类型定义，因此它们始终是最新的

# 快速上手

## 使用 vue-cli 创建Vue3.0工程

先查看@vue/cli版本，确保@vue/cli版本在4.5.0以上

```bash
vue --version
```

如果版本过低，就需要安装或者升级你的@vue/cli

```bash
npm install -g @vue/cli
```

运行以下命令来创建一个新项目：

```sh
vue create hello-world
```

启动项目

```sh
cd hello-world
npm run serve
```

# 常用 Composition API

## `setup`

使用 (`data`、`computed`、`methods`、`watch`) 组件选项来组织逻辑通常都很有效。然而，当我们的组件开始变得更大时，**逻辑关注点**的列表也会增长。尤其对于那些一开始没有编写这些组件的人来说，这会导致组件难以阅读和理解。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/options-api.png)

这是一个大型组件的示例，其中**逻辑关注点**按颜色进行分组。

这种碎片化使得理解和维护复杂组件变得困难。选项的分离掩盖了潜在的逻辑问题。此外，在处理单个逻辑关注点时，我们必须不断地“跳转”相关代码的选项块。

如果能够将同一个逻辑关注点相关代码收集在一起会更好。

<span style="color:red">**setup的设计是为了使用组合式api，使相关逻辑的代码集中在一起.**</span>

### `setup` 组件选项

新的 `setup` 选项在**组件创建之前**执行，一旦 `props` 被解析，就将作为组合式 API 的入口。

> <span style="color:orange">在 `setup` 中你应该避免使用 `this`，因为它不会找到组件实例。`setup` 的调用发生在 `data` property、`computed` property 或 `methods` 被解析之前，所以它们无法在 `setup` 中被获取。</span>

`setup` 选项是一个接收 `props` 和 `context` 的函数，此外，我们将 `setup` 返回的所有内容都暴露给组件的其余部分 (计算属性、方法、生命周期钩子等等) 以及组件的模板。

```js
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup(prop,context) {
    console.log("prop:",prop)
    console.log("context:",context)
    
    return {} // 这里返回的任何内容都可以用于组件的其余部分
  }
}
```

使用 `setup` 函数时，它将接收两个参数：

1. `props`
2. `context`

### Props

`setup` 函数中的第一个参数是 `props`。正如在一个标准组件中所期望的那样，`setup` 函数中的 `props` 是响应式的，当传入新的 prop 时，它将被更新。

```js
// MyBook.vue

export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```

> <span style="color:orange">但是，因为 `props` 是响应式的，你**不能使用 ES6 解构**，它会消除 prop 的响应性。</span>

如果需要解构 prop，可以在 `setup` 函数中使用 [`toRefs`](https://v3.cn.vuejs.org/guide/reactivity-fundamentals.html#响应式状态解构) 函数来完成此操作：

```js
// MyBook.vue

import { toRefs } from 'vue'

setup(props) {
  const { title } = toRefs(props)

  console.log(title.value)
}
```

如果 `title` 是可选的 prop，则传入的 `props` 中可能没有 `title` 。在这种情况下，`toRefs` 将不会为 `title` 创建一个 ref 。你需要使用 `toRef` 替代它：

```js
// MyBook.vue
import { toRef } from 'vue'
setup(props) {
  const title = toRef(props, 'title')
  console.log(title.value)
}
```

`props`在浏览器的控制台输出如下图：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/vue3props.png)

### Context

传递给 `setup` 函数的第二个参数是 `context`。`context` 是一个普通的 JavaScript 对象，它暴露组件的三个 property：

的三个 property：

```js
// MyBook.vue

export default {
  setup(props, context) {
    // Attribute (非响应式对象)
    console.log(context.attrs)

    // 插槽 (非响应式对象)
    console.log(context.slots)

    // 触发事件 (方法)
    console.log(context.emit)
  }
}
```

`context` 是一个普通的 JavaScript 对象，也就是说，它不是响应式的，这意味着你可以安全地对 `context` 使用 ES6 解构。

```js
// MyBook.vue
export default {
  setup(props, { attrs, slots, emit }) {
    ...
  }
}
```

`attrs` 和 `slots` 是有状态的对象，它们总是会随组件本身的更新而更新。这意味着你应该避免对它们进行解构，并始终以 `attrs.x` 或 `slots.x` 的方式引用 property。

`context`在浏览器的控制台输出如下图：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/vue3context.png)

`setup`注意点：

1. 尽量不要与Vue2.x配置混用
   - Vue2.x配置（data、methos、computed...）中<strong style="color:#DD5145">可以访问到</strong>setup中的属性、方法。
   - 但在setup中<strong style="color:#DD5145">不能访问到</strong>Vue2.x配置（data、methos、computed...）。
   - 如果有重名, setup优先。
2. setup不能是一个async函数，因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要Suspense和异步组件的配合）
3. **在 `setup()` 内部，`this` 不是该活跃实例的引用**

执行 `setup` 时，组件实例尚未被创建。因此，你只能访问以下 property：

- `props`
- `attrs`
- `slots`
- `emit`

换句话说，你**将无法访问**以下组件选项：

- `data`
- `computed`
- `methods`

-------------------

## ref

接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象具有指向内部值的单个 property `.value`。

**示例：**

```js
setup() {
  const count = ref(0)
  function countAdd() {
    count.value++
  }

  return {
    count,
    countAdd
  }
}
```

如果将对象分配为 ref 值，则通过 [reactive](https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive) 方法使该对象具有高度的响应式。

使用数据

```html
<h2>count: {{count}}</h2>
<button @click="countAdd">count++</button>
```

- 接收的数据可以是：基本类型、也可以是对象类型。
- 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的。
- 对象类型的数据：内部 使用了Vue3.0中的一个新函数—— ```reactive```函数。

-------------------------

## reactive函数

返回对象的响应式副本，Proxy的实例对象

```js
const obj = reactive({ count: 0 })
```

> <span style="color:green">`reactive` 将解包所有深层的 [refs](https://v3.cn.vuejs.org/api/refs-api.html#ref)，同时维持 ref 的响应性。</span>

```js
const count = ref(1)
const obj = reactive({ count })

// ref 会被解包
console.log(obj.count === count.value) // true

// 它会更新 `obj.count`
count.value++
console.log(count.value) // 2
console.log(obj.count) // 2

// 它也会更新 `count` ref
obj.count++
console.log(obj.count) // 3
console.log(count.value) // 3
```

## Vue3.0中的响应式原理

### vue2.x的响应式

- 实现原理：

  - 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）。

  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

    ```js
    Object.defineProperty(data, 'count', {
        get () {}, 
        set () {}
    })
    ```

- 存在问题：

  - 新增属性、删除属性, 界面不会更新。
  - 直接通过下标修改数组, 界面不会自动更新。

### Vue3.0的响应式

- 实现原理: 
  - 通过Proxy（代理）:  **Proxy 是一个对象，它包装了另一个对象，并允许你拦截对该对象的任何交互。**
  - 通过Reflect（反射）:  使用 Proxy 的一个难点是 `this` 绑定。我们希望任何方法都绑定到这个 Proxy，而不是目标对象，这样我们也可以拦截它们。值得庆幸的是，ES6 引入了另一个名为 `Reflect` 的新特性，它允许我们以最小的代价消除了这个问题：
  - MDN文档中描述的Proxy与Reflect：
    - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

    - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect


```js
new Proxy(data, {
	// 拦截读取属性值
    get (target, prop) {
    	return Reflect.get(target, prop)
    },
    // 拦截设置属性值或添加新属性
    set (target, prop, value) {
    	return Reflect.set(target, prop, value)
    },
    // 拦截删除属性
    deleteProperty (target, prop) {
    	return Reflect.deleteProperty(target, prop)
    }
})

proxy.name = 'tom'   
```

## 计算属性和侦听器

### 计算属性

有时我们需要依赖于其他状态的状态——在 Vue 中，这是用组件[计算属性](https://v3.cn.vuejs.org/guide/computed.html#计算属性和侦听器)处理的，以直接创建计算值，我们可以使用 `computed` 方法：它接受 getter 函数并为 getter 返回的值返回一个不可变的响应式 [ref](https://v3.cn.vuejs.org/guide/reactivity-fundamentals.html#创建独立的响应式值作为-refs) 对象。

```js
setup() {
  const person = reactive({
    firstName: '',
    lastName: ''
  })

  person.fullName = computed(() => {
    return person.firstName + ' ' + person.lastName
  })

 return {
   person
 }
}
```

使用数据

```html
<template>
  <div class="hello">
    <h1>请输入姓名</h1>
    <h3>firstName</h3>
    <input type="text" v-model="person.firstName" />
    <h3>lastName</h3>
    <input type="text" v-model="person.lastName" />
    <h3>fullName</h3>
    <h3>{{ person.fullName }}</h3>
  </div>
</template>
```

效果

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/vue3computed.png" style="zoom: 80%;" />

或者，它可以使用一个带有 `get` 和 `set` 函数的对象来创建一个可写的 ref 对象。

```js
setup() {
    const person = reactive({
      firstName: "",
      lastName: "",
    });

    person.fullName = computed({
      get() {
        return person.firstName + " " + person.lastName;
      },
      set(newValue) {
        const names = newValue.split(" ");
        person.firstName = names[0];
        person.lastName = names[names.length - 1];
      },
    });

    return {
      person,
    };
},
```

使用数据

```html
<template>
  <div class="hello">
    <h1>请输入姓名</h1>
    <h3>fullName</h3>
    <input type="text" v-model="person.fullName" />
    <h3>firstName</h3>
    <h3>{{ person.firstName }}</h3>
    <h3>lastName</h3>
    <h3>{{ person.lastName }}</h3>
  </div>
</template>
```

效果

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/vue3computed1.png" style="zoom:80%;" />

---------------------------------------------

### Watch

`watch` 需要侦听特定的数据源，并在回调函数中执行副作用。默认情况下，它也是惰性的，即只有当被侦听的源发生变化时才执行回调。

#### 侦听单个数据源

侦听器数据源可以是返回值的 getter 函数，也可以直接是 `ref`：

```js
// 侦听一个 getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

// 直接侦听ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```

#### 侦听多个数据源

侦听器还可以使用数组同时侦听多个源：

```js
const firstName = ref("zhang");
const lastName = ref("san");

watch([firstName, lastName], (newValues, prevValues) => {
  console.log(newValues, prevValues);
});

function changeFirstName() {
  firstName.value = "Steve"; // logs: ["Steve", "san"] ["zhang", "san"]
}

function changeLastName() {
  lastName.value = "Jobs";  // logs: ["zhang", "Jobs"] ["zhang", "san"]
}
```

尽管如此，如果你在同一个方法里同时改变这些被侦听的来源，侦听器仍只会执行一次：

```js
function changeFullName() {
  firstName.value = "Steve";
  lastName.value = "Jobs";
  // 打印 ["Steve", "Jobs"] ["zhang", "san"]
}
```

注意多个同步更改只会触发一次侦听器。

两个小“坑”：

- 监视reactive定义的响应式数据时：oldValue无法正确获取、默认已经强制开启了深度监视（deep配置失效）。
- 监视reactive定义的响应式数据中某个属性时：deep配置有效。

-----------------

## watchEffect

- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。

- watchEffect有点像computed：

  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。

  - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

    ```js
    //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
    watchEffect(()=>{
        const x1 = sum.value
        const x2 = person.age
        console.log('watchEffect配置的回调执行了')
    })
    ```

-----------------------

## 生命周期

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/lifecycle.svg"  />

下表包含如何在 [setup ()](https://v3.cn.vuejs.org/guide/composition-api-setup.html) 内部调用生命周期钩子：

| 选项式 API        | Hook inside `setup` |
| ----------------- | ------------------- |
| `beforeCreate`    | Not needed*         |
| `created`         | Not needed*         |
| `beforeMount`     | `onBeforeMount`     |
| `mounted`         | `onMounted`         |
| `beforeUpdate`    | `onBeforeUpdate`    |
| `updated`         | `onUpdated`         |
| `beforeUnmount`   | `onBeforeUnmount`   |
| `unmounted`       | `onUnmounted`       |
| `errorCaptured`   | `onErrorCaptured`   |
| `renderTracked`   | `onRenderTracked`   |
| `renderTriggered` | `onRenderTriggered` |
| `activated`       | `onActivated`       |
| `deactivated`     | `onDeactivated`     |

> <span style="color:green">因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写。</span>

## `toRef`

可以用来为源响应式对象上的某个 property 新创建一个 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。然后，ref 可以被传递，它会保持对其源 property 的响应式连接。

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

fooRef.value++
console.log(state.foo) // 2

state.foo++
console.log(fooRef.value) // 3
```

## `toRefs`

将响应式对象转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的 [`ref`](https://v3.cn.vuejs.org/api/refs-api.html#ref)。

当从组合式函数返回响应式对象时，`toRefs` 非常有用，这样消费组件就可以在不丢失响应性的情况下对返回的对象进行分解/扩散：

```js
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // 操作 state 的逻辑

  // 返回时转换为ref
  return toRefs(state)
}

export default {
  setup() {
    // 可以在不失去响应性的情况下解构
    const { foo, bar } = useFeatureX()

    return {
      foo,
      bar
    }
  }
}
```

`toRefs` 只会为源对象中包含的 property 生成 ref。如果要为特定的 property 创建 ref，则应当使用 `toRef`

-----------------------

# 其他 Composition API

## shallowReactive 与 shallowRef

- `shallowReactive`：创建一个响应式代理，它跟踪其自身 property 的响应性，但不执行嵌套对象的深层响应式转换

  ```js
  const state = shallowReactive({
    foo: 1,
    nested: {
      bar: 2
    }
  })
  
  // 改变 state 本身的性质是响应式的
  state.foo++
  // ...但是不转换嵌套对象
  isReactive(state.nested) // false
  state.nested.bar++ // 非响应式
  ```

  

- `shallowRef`：只处理基本数据类型的响应式, 不进行对象的响应式处理。

  ```js
  const foo = shallowRef({})
  // 改变 ref 的值是响应式的
  foo.value = {}
  // 但是这个值不会被转换。
  isReactive(foo.value) // false
  ```

- 什么时候使用?

  -  如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> `shallowReactive`。
  -  如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> `shallowRef`。

## readonly 与 shallowReadonly

- `readonly`: 接受一个对象 (响应式或纯对象) 或 [ref](https://v3.cn.vuejs.org/api/refs-api.html#ref) 并返回原始对象的只读代理。只读代理是深层的：任何被访问的嵌套 property 也是只读的。

  ```js
  const original = reactive({ count: 0 })
  
  const copy = readonly(original)
  
  watchEffect(() => {
    // 用于响应性追踪
    console.log(copy.count)
  })
  
  // 变更 original 会触发依赖于副本的侦听器
  original.count++
  
  // 变更副本将失败并导致警告
  copy.count++ // 警告!
  ```

- `shallowReadonly`：创建一个 proxy，使其自身的 property 为只读，但不执行嵌套对象的深度只读转换 (暴露原始值)。

  ```js
  const state = shallowReadonly({
    foo: 1,
    nested: {
      bar: 2
    }
  })
  
  // 改变 state 本身的 property 将失败
  state.foo++
  // ...但适用于嵌套对象
  isReadonly(state.nested) // false
  state.nested.bar++ // 适用
  ```

- 应用场景: 不希望数据被修改时。

## toRaw 与 markRaw

- toRaw：

  - 作用：将一个由```reactive```生成的<strong style="color:orange">响应式对象</strong>转为<strong style="color:orange">普通对象</strong>。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。

- markRaw：

  - 作用：标记一个对象，使其永远不会再成为响应式对象。返回对象本身。

    ```js
    const foo = markRaw({})
    console.log(isReactive(reactive(foo))) // false
    
    // 嵌套在其他响应式对象中时也可以使用
    const bar = reactive({ foo })
    console.log(isReactive(bar.foo)) // false
    ```

    > <span style="color:red">有些值不应该是响应式的，例如复杂的第三方类实例或 Vue 组件对象</span>
    >
    > <span style="color:red">当渲染具有不可变数据源的大列表时，跳过 proxy 转换可以提高性能。</span>

## customRef

创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。它需要一个工厂函数，该函数接收 `track` 和 `trigger` 函数作为参数，并且应该返回一个带有 `get` 和 `set` 的对象。

自定义customRef配合自定义hook函数使用。

什么是hook？—— 本质是一个函数，把setup函数中使用的Composition API进行了封装。类似于vue2.x中的mixin。自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂。

在项目中新建hooks文件夹，在hooks文件夹下新建useDebounce.js文件

```js
import { customRef } from "vue"

export default function(value, delay = 1000) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }
```

使用自定义 ref 通过 `v-model` 实现 debounce 的示例：

```html
<input v-model="text" />
<h3>{{text}}</h3>
```

```js
import useDebouncedRef from '../hooks/useDebounce'

export default {
  setup() {
    return {
      text: useDebouncedRef('hello')
    }
  }
}
```

---------------------

## provide 与 inject

通常，当我们需要从父组件向子组件传递数据时，我们使用 [props](https://v3.cn.vuejs.org/guide/component-props.html)。想象一下这样的结构：有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。

对于这种情况，我们可以使用一对 `provide` 和 `inject`。无论组件层次结构有多深，父组件都可以作为其所有子组件的依赖提供者。这个特性有两个部分：父组件有一个 `provide` 选项来提供数据，子组件有一个 `inject` 选项来开始使用这些数据。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/vue3/components_provide.png)

用法:

父组件中提供：

```js
setup(){
	......
    let car = reactive({name:'奔驰',price:'40万'})
    provide('car',car)
    ......
}
```

子组件中使用：

```js
setup(props,context){
	......
    const car = inject('car')
    return {car}
	......
}
```

## 响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

---------------

# 新的组件

Teleport 提供了一种干净的方法，允许我们控制在 DOM 中哪个父节点下渲染了 HTML，而不必求助于全局状态或将其拆分为两个组件。

让我们修改 `modal-button` 以使用 `<teleport>`，并告诉 Vue “**Teleport** 这个 HTML **到**该‘**body**’标签”。

```js
app.component('modal-button', {
  template: `
    <button @click="modalOpen = true">
        Open full screen modal! (With teleport!)
    </button>

    <teleport to="body">
      <div v-if="modalOpen" class="modal">
        <div>
          I'm a teleported modal! 
          (My parent is "body")
          <button @click="modalOpen = false">
            Close
          </button>
        </div>
      </div>
    </teleport>
  `,
  data() {
    return { 
      modalOpen: false
    }
  }
})
```

# 其他

## 全局API的转移

- Vue 2.x 有许多全局 API 和配置。

  - 例如：注册全局组件、注册全局指令等。

    ```js
    //注册全局组件
    Vue.component('MyButton', {
      data: () => ({
        count: 0
      }),
      template: '<button @click="count++">Clicked {{ count }} times.</button>'
    })
    
    //注册全局指令
    Vue.directive('focus', {
      inserted: el => el.focus()
    }
    ```

- Vue3.0中对这些API做出了调整：

  - 将全局的API，即：```Vue.xxx```调整到应用实例（```app```）上

    | 2.x 全局 API（```Vue```） | 3.x 实例 API (`app`)                        |
    | ------------------------- | ------------------------------------------- |
    | Vue.config.xxxx           | app.config.xxxx                             |
    | Vue.config.productionTip  | <strong style="color:#DD5145">移除</strong> |
    | Vue.component             | app.component                               |
    | Vue.directive             | app.directive                               |
    | Vue.mixin                 | app.mixin                                   |
    | Vue.use                   | app.use                                     |
    | Vue.prototype             | app.config.globalProperties                 |

## 其他改变

- data选项应始终被声明为一个函数。

- 过度类名的更改：

  - Vue2.x写法

    ```css
    .v-enter,
    .v-leave-to {
      opacity: 0;
    }
    .v-leave,
    .v-enter-to {
      opacity: 1;
    }
    ```

  - Vue3.x写法

    ```css
    .v-enter-from,
    .v-leave-to {
      opacity: 0;
    }
    
    .v-leave-from,
    .v-enter-to {
      opacity: 1;
    }
    ```

- <strong style="color:#DD5145">移除</strong>keyCode作为 v-on 的修饰符，同时也不再支持```config.keyCodes```

- <strong style="color:#DD5145">移除</strong>```v-on.native```修饰符

  - 父组件中绑定事件

    ```vue
    <my-component
      v-on:close="handleComponentEvent"
      v-on:click="handleNativeClickEvent"
    />
    ```

  - 子组件中声明自定义事件

    ```vue
    <script>
      export default {
        emits: ['close']
      }
    </script>
    ```

- <strong style="color:#DD5145">移除</strong>过滤器（filter）

  > 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。