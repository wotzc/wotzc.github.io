---
title: Vue基础
date: 2021-08-31 12:55:19
tags:
- Vue
- Vue核心
- 前端
categories: Vue

---

# Vue.js 是什么

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。

# Vue的特点

1. 采用组件化模式，提高代码的复用率、且让代码更好维护。
2. 声明式编码，让编码人员无需直接操作DOM，提高开发效率。
3. 使用虚拟DOM+优秀DIff算法，尽量复用DOM结点。

# 模板语法

## 模板的理解

html 中包含了一些JS 语法代码，语法分为两种，分别为：

1. 插值语法（双大括号表达式）
2. 指令（以v-开头）

**插值语法**

功能: 用于解析**标签体**内容

语法: {{xxx}} ，xxxx 会作为js 表达式解析



**指令语法**

功能: 解析**标签属性、解析标签体内容、绑定事件**

举例：v-bind:href = 'xxxx' ，xxxx 会作为js 表达式被解析

-----------

# 数据绑定

## 单向数据绑定

语法：`v-bind`:href ="xxx" 或简写为:href

特点：数据只能从data 流向页面

> ```ABAP
> v- 前缀作为一种视觉提示，用来识别模板中 Vue 特定的 attribute。当你在使用 Vue.js 为现有标签添加动态行为 (dynamic behavior) 时，v- 前缀很有帮助，然而，对于一些频繁用到的指令来说，就会感到使用繁琐。同时，在构建由 Vue 管理所有模板的单页面应用程序 (SPA - single page application) 时，v- 前缀也变得没那么重要了。因此，Vue 为 v-bind 和 v-on 这两个最常用的指令，提供了特定简写：
> ```

### `v-bind` 缩写

```html
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a :[key]="url"> ... </a>
```

### `v-on` 缩写

```html
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>

<!-- 动态参数的缩写 (2.6.0+) -->
<a @[event]="doSomething"> ... </a>
```

## 双向数据绑定

语法：`v-mode`:value="xxx" 或简写为v-model="xxx"

特点：数据不仅能从data 流向页面，还能从页面流向data

> ```ABAP
> 1、双向绑定一般都应用在表单类元素上（如：input、select等）
> 2、v-mode:value 可以简写为v-model，因为v-model默认收集的就是value值
> ```

-------------------------

## el的两种写法

第一种写法

```javascript
new Vue({
    el: "#root",
    data() {
        name: "Vue"
    },
})
```

第二种写法

```javascript
v.$mount("#root");
```

## data的两种写法

第一种写法：对象式

```javascript
const v = new Vue({
    el: "#root",
    data: {
        name: "Vue"
    },
})
```

第二种写法：函数式

```javascript
const v = new Vue({
    el: "#root",
    data() {
        return {
            name: "Vue"
        }
    },
})
```

> ```ABAP
> 以后学习到组件时，data必须写成函数式
> ```

-----

# MVVM模型

**MVVM**（**Model–view–viewmodel**）是一种软件架构模式。

MVVM模式不同于MVC，在MVVM模式中，将ViewModel层绑定到View层后，它基本不使用点击事件，而是使用命令(Command)来控制。数据的显示也是不同于MVC，而是使用Binding来绑定相关数据。

值得一提的是，MVVM通常会使用属性更改通知，即数据驱动而不是事件驱动。在WPF中当数据发生改变时，会通过接口INotifyPropertyChanged通知到对应的组件绑定的数据，实现同步数据刷新。

**M**：模型(Model) ：对应data 中的数据

**V**：视图(View) ：模板

**VM**：视图模型(ViewModel) ： Vue 实例对象

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/viewmodel.png)

每个 Vue 应用都是通过用 `Vue` 函数创建一个新的 **Vue 实例**开始的：

```
var vm = new Vue({
  // 选项
})
```

虽然没有完全遵循 [MVVM 模型](https://zh.wikipedia.org/wiki/MVVM)，但是 Vue 的设计也受到了它的启发。因此在文档中经常会使用 `vm` (ViewModel 的缩写) 这个变量名表示 Vue 实例。

> ```ABAP
> vm身上的所有属性及Vue原型上的所有属性，在vue模板中都可以直接使用
> ```

------------------

# 事件处理

事件的基本使用：

- 使用v-on:xxx 或@xxx 绑定事件，其中xxx是事件名
- 事件的回调需要配置在methods对象中，最终会在vm上
- methods中配置的函数不要用箭头函数！否则this就不是vm了
- methods中配置的函数，都是被Vue所管理的函数，this指向是vm或组件实例化对象
- @click="demo"和@click="demo($event)"效果一致，但后者可以传参。

------------

## 事件修饰符

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。之前提过，修饰符是由点开头的指令后缀来表示的。

- `.stop`：阻止事件冒泡（常用）
- `.prevent`：阻止默认事件（常用）
- `.capture`：使用事件的捕获模式
- `.self`：只有event.target是当前操作的元素时才触发事件
- `.once`：事件只能触发一次（常用）
- `.passive`：事件的默认行为立即执行，无需等待事件回调执行完毕

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>
```

## 键盘事件

在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符：

```html
<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">
```

你可以直接将 `KeyboardEvent.key`暴露的任意有效按键名转换为 kebab-case 来作为修饰符。

```html
<input v-on:keyup.page-down="onPageDown">
```

在上述示例中，处理函数只会在 `$event.key` 等于 `PageDown` 时被调用。

Vue 提供了绝大多数常用的按键码的别名：

- `.enter`
- `.tab`（必须配合keydown使用）
- `.delete` (捕获“删除”和“退格”键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

**系统修饰键**

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

> ```css
> 注意：在 Mac 系统键盘上，meta 对应 command 键 (⌘)。在 Windows 系统键盘 meta 对应 Windows 徽标键 (⊞)。在 Sun 操作系统键盘上，meta 对应实心宝石键 (◆)。在其他特定键盘上，尤其在 MIT 和 Lisp 机器的键盘、以及其后继产品，比如 Knight 键盘、space-cadet 键盘，meta 被标记为“META”。在 Symbolics 键盘上，meta 被标记为“META”或者“Meta”。
> ```

1. 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才触发。
2. 配合keydown使用：正常触发

------------

# 计算属性

模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。例如：

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

在这个地方，模板不再是简单的声明式逻辑。你必须看一段时间才能意识到，这里是想要显示变量 `message` 的翻转字符串。当你想要在模板中的多处包含此翻转字符串时，就会更加难以处理。

所以，对于任何复杂逻辑，你都应当使用**计算属性**。

计算属性默认只有 getter，不过在需要时你也可以提供一个 setter：

```javascript
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

现在再运行 `vm.fullName = 'John Doe'` 时，setter 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。

1. **定义：要用的属性不存在要通过已有的属性计算得到。**
2. **原理：底层借助了Object.defineproperty方法提供的getter和setter。**
3. **get函数什么时候执行？**
   - **初次读取时会调用一次**
   - **当依赖的数据发生变化时会再次调用**
4. **优势：与methods实现相比，内部有缓存机制，效率更高，调试更方便**
5. **备注：**
   - **计算属性最终会出现在vm上，直接读取使用即可。**
   - **如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生变化**

简写形式：当计算属性只有getter时

```javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName() {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

等价于

```javascript
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

-----

# 侦听属性

先看一个小例子

```html
<div id="root">
    <h2>定个小目标，赚他{{amount}}个亿</h2>
    <button @click="amount++">提高小目标</button>
</div>
```

```javascript
const vm = new Vue({
    el: "#root",
    data: {
        amount: 1
    },
    watch: {
        amount:{
            immediate: true, //初始化时让handler调用一下
            // 当amount发生改变时调用handler
            handler(newValue, oldValue){
                console.log(newValue, oldValue)
            }
        }
    }
})
```

侦听属性简写形式如下:

```javascript
const vm = new Vue({
    el: "#root",
    data: {
        amount: 1
    },
    watch: {
        amount(newValue, oldValue){
            console.log(newValue, oldValue)
        }
    }
})
```

除了 `watch` 选项之外，您还可以使用命令式的 [vm.$watch API](https://cn.vuejs.org/v2/api/#vm-watch)。

```javascript
vm.$watch('amount',{
    // 当amount发生改变时调用handler
    immediate: true,
    handler(newValue, oldValue){
        console.log(newValue, oldValue)
    }
})
```

**深度侦听**

为了发现对象内部值的变化，可以在选项参数中指定 `deep: true`。注意监听数组的变更不需要这么做。

```html
<div id="root">
    <h3>a的值是：{{numbers.a}}</h3>
    <button @click="numbers.a++">a+1</button>
    <h3>b的值是：{{numbers.b}}</h3>
    <button @click="numbers.b++">a+1</button>
</div>
```

```javascript
const vm = new Vue({
    el: "#root",
    data: {
        numbers: {
            a: 1,
            b: 1
        }
    },
    watch: {
        numbers:{
            deep: true, //当deep为true时，a或b的值改变下面的handler会执行，否则不会执行
            handler(){
                console.log("numbers改变了")
            }
        }
    }
})
```

**计算属性 vs 侦听属性**

computed和watch之间的区别

1. computed能完成的功能，watch都可以完成。watch能完成的功能，computed不一定能完成，例如异步操作。
2. 虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

> ```asciiarmor
> 1、所有被Vue管理的函数最好写成普通函数，这样this的指向才是vm或组件实例对象
> 2、所有不背Vue管理的函数（定时器的回调函数、ajax的回调函数等），最好写成箭头函数，这样this的指向才是vm或组件实例对象
> ```

----------

# Class与Style的绑定

## Class的绑定

操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是 attribute，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将 `v-bind` 用于 `class` 和 `style` 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串之外，还可以是对象或数组。

### 字符串语法

```html
<div v-bind:class="danger"></div>
```

### 数组语法

我们可以把一个数组传给 `v-bind:class`，以应用一个 class 列表：

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```javascript
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

渲染为：

```html
<div class="active text-danger"></div>
```

### 对象语法

我们可以传给 `v-bind:class` 一个对象，以动态地切换 class：

```html
<div v-bind:class="{ active: isActive }"></div>
```

上面的语法表示 `active` 这个 class 存在与否将取决于数据 property `isActive` 的 [truthiness](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)。

------------

## Style的绑定

`v-bind:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```javascript
data: {
  activeColor: 'red',
  fontSize: 30
}
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div v-bind:style="styleObject"></div>
```

```javascript
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

`v-bind:style` 的数组语法可以将多个样式对象应用到同一个元素上：

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

------

# 条件渲染

## v-show

用于根据条件展示元素的选项是 `v-show` 指令。用法大致一样：

```html
<h1 v-show="ok">Hello!</h1>
```

不同的是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 CSS property `display`。

> ```asciiarmor
> 注意，v-show 不支持 <template> 元素，也不支持 v-else。
> ```

## v-if

`v-if` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 truthy 值的时候被渲染。

```html
<h1 v-if="awesome">Vue is awesome!</h1>
```

也可以用 `v-else` 添加一个“else 块”：

```html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

## `v-if`   `v-else-if`  `v-else`

`v-else-if`，顾名思义，充当 `v-if` 的“else-if 块”，可以连续使用：

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

##  `<template>`元素上使用-v-if-条件渲染分组

因为 `v-if` 是一个指令，所以必须将它添加到一个元素上。但是如果想切换多个元素呢？此时可以把一个 `<template>` 元素当做不可见的包裹元素，并在上面使用 `v-if`。最终的渲染结果将不包含 `<template>` 元素。

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

## `v-if` vs `v-show`

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

## 用 `key` 管理可复用的元素

Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。这么做除了使 Vue 变得非常快之外，还有其它一些好处。例如，如果你允许用户在不同的登录方式之间切换：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

那么在上面的代码中切换 `loginType` 将不会清除用户已经输入的内容。因为两个模板使用了相同的元素，`<input>` 不会被替换掉——仅仅是替换了它的 `placeholder`。

所以 Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 `key` attribute 即可：

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

现在，每次切换时，输入框都将被重新渲染。

-----------

# 列表渲染

##  `v-for`遍历 元素

我们可以用 `v-for` 指令基于一个数组来渲染一个列表。`v-for` 指令需要使用 `item in items` 形式的特殊语法，其中 `items` 是源数据数组，而 `item` 则是被迭代的数组元素的**别名**。

```html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

结果：

- Foo
- Bar

你也可以用 `of` 替代 `in` 作为分隔符，因为它更接近 JavaScript 迭代器的语法：

```html
<div v-for="item of items"></div>
```

在 `v-for` 块中，我们可以访问所有父作用域的 property。`v-for` 还支持一个可选的第二个参数，即当前项的索引。

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

> ```asciiarmor
> 为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 `key` attribute：
> ```

```html
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

建议尽可能在使用 `v-for` 时提供 `key` attribute，除非遍历输出的 DOM 内容非常简单，或者是刻意依赖默认行为以获取性能上的提升。

因为它是 Vue 识别节点的一个通用机制，`key` 并不仅与 `v-for` 特别关联。

> ```asciiarmor
> 不要使用对象或数组之类的非基本类型值作为 v-for 的 key。请用字符串或数值类型的值。
> ```

--------------

##  `v-for`遍历 对象

你也可以用 `v-for` 来遍历一个对象的 property。

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

```javascript
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

结果：

- How to do lists in Vue
- Jane Doe
- 2016-04-10

你也可以提供第二个的参数为 property 名称 (也就是键名)：

```html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

title: How to do lists in Vue

author: Jane Doe

publishedAt: 2016-04-10

还可以用第三个参数作为索引：

```html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

0. title: How to do lists in Vue

1. author: Jane Doe

2. publishedAt: 2016-04-10

-------------

## Key的原理

`key` 的特殊 attribute 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

有相同父元素的子元素必须有**独特的 key**。重复的 key 会造成渲染错误。

对比规则：
(1).旧虚拟DOM中找到了与新虚拟DOM相同的key：
			①.若虚拟DOM中内容没变, 直接使用之前的真实DOM！
			②.若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM。

(2).旧虚拟DOM中未找到与新虚拟DOM相同的key，创建新的真实DOM，随后渲染到到页面。

用index作为key可能会引发的问题：
   											1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作: 会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。

2. 如果结构中还包含输入类的DOM：会产生错误DOM更新 ==> 界面有问题。
4. 开发中如何选择key?:
1.最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
2.如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key是没有问题的。

------------------

## 显示过滤后的结果

有时，我们想要显示一个数组经过过滤或排序后的版本，而不实际变更或重置原始数据。在这种情况下，可以创建一个计算属性，来返回过滤或排序后的数组。

例如：

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```

```javascript
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## 显示排序后的结果

```javascript
computed: {
  evenNumbers() {
    const arr this.numbers.filter(function (number) {
      return number % 2 === 0
    })
    // 升序排列
    return arr.sort((prv,next)=> {
        return next - prv
    })
  }
}
```

----

# Vue监视数据的原理

**Vue监视数据的原理：**

1. vue会监视data中所有层次的数据。

**如何监测对象中的数据？**
通过**setter方法**实现监视，且要在new Vue时就传入要监测的数据。
(1).对象中后追加的属性，Vue默认不做响应式处理
(2).如需给后添加的属性做响应式，请使用如下API：

```
Vue.set(target，propertyName/index，value)
```

 或

```javascript
 vm.$set(target，propertyName/index，value)
```

- **参数**：
  - `{Object | Array} target`
  - `{string | number} propertyName/index`
  - `{any} value`

> ```asciiarmor
> 注意对象不能是 Vue 实例即vm，或者 Vue 实例的根数据对象vm.data。
> ```

3、**如何监测数组中的数据？**
通过包裹数组更新元素的方法实现，本质就是做了两件事：
(1).调用原生对应的方法对数组进行更新。
(2).重新解析模板，进而更新页面

4、**在Vue修改数组中的某个元素一定要用如下方法：**
1）.使用这些API:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

2）.Vue.set() 或 vm.$set()

> ```asciiarmor
> 特别注意：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象 添加属性！！！
> ```

-----------

# 表单输入绑定

## 文本

- text 和 textarea 元素使用 `value` property ，v-mode收集的是`value` 值

## 单选框

- <input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。

```html
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>
```

## 复选框

若：<input type="checkbox"/>
1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
2.配置input的value属性:
			(1) v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
			(2) v-model的初始值是数组，那么收集的的就是value组成的数组

## 修饰符

### `.lazy`

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了上述输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转为在 失去焦点 事件_之后_进行同步：

```html
<!-- 在input框失去焦点时更新 -->
<input v-model.lazy="msg">
```

### `.number`

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```html
<input v-model.number="age" type="number">
```

这通常很有用，因为即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 `parseFloat()` 解析，则会返回原始的值。

### `.trim`

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```html
<input v-model.trim="msg">
```

-------------------

# 过滤器

Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：**双花括号插值和 `v-bind` 表达式** (后者从 2.1.0+ 开始支持)。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示：

```html
<!-- 在双花括号中 -->
{{ message | capitalize }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

你可以在一个组件的选项中定义本地的过滤器：

```javascript
filters: {
  capitalize: function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
  }
}
```

或者在创建 Vue 实例之前**全局定义过滤器**：

```javascript
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
```

当全局过滤器和局部过滤器重名时，会采用局部过滤器。

**过滤器传参**

过滤器是 JavaScript 函数，因此可以接收参数：

```html
{{ message | filterA('arg1', arg2) }}
```

这里，`filterA` 被定义为接收三个参数的过滤器函数。其中 `message` 的值作为第一个参数，普通字符串 `'arg1'` 作为第二个参数，表达式 `arg2` 的值作为第三个参数。

**过滤器可以串联：**

```
{{ message | filterA | filterB }}
```

在这个例子中，`filterA` 被定义为接收单个参数的过滤器函数，表达式 `message` 的值将作为参数传入到函数中。然后继续调用同样被定义为接收单个参数的过滤器函数 `filterB`，将 `filterA` 的结果传递到 `filterB` 中。

> ```asciiarmor
> 过滤器不改变原有的数据，是产生新的数据
> ```

------------

# 其他指令

## v-text

- **预期**：`string`

- 更新元素的 `textContent`。如果要更新部分的 `textContent`，需要使用 `{{ Mustache }}` 插值。

- **示例**：

  ```html
  <span v-text="msg"></span>
  <!-- 和下面的一样 -->
  <span>{{msg}}</span>
  ```

## v-html

- **预期**：`string`

- **详细**：

  更新元素的 `innerHTML`。

- **示例**：

  ```html
  <div v-html="html"></div>
  ```

> ```asciiarmor
> 在网站上动态渲染任意 HTML 是非常危险的，因为容易导致 XSS 攻击。只在可信内容上使用 v-html，永不用在用户提交的内容上。
> ```

## v-cloak

- **不需要表达式**

- **用法**：

  这个指令保持在元素上直到关联实例结束编译。和 CSS 规则如 `[v-cloak] { display: none }` 一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。

- **示例**：

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  未解析的元素不会显示，直到编译结束，可以解决插值闪烁问题。

## v-once

- **不需要表达式**

- **详细**：

  只渲染元素和组件**一次**。随后的重新渲染，元素/组件及其所有的子节点将被视为静态内容并跳过。这可以用于优化更新性能。

  ```html
  <!-- 单个元素 -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- 有子元素 -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- 组件 -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for` 指令-->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

## v-pre

- **不需要表达式**

- **用法**：

  跳过这个元素和它的子元素的编译过程。

  可以利用它跳过没有使用指令语法、没有使用插值语法的结点。跳过大量没有指令的节点会加快编译。

- **示例**：

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

-------------------

# 自定义指令

有的情况下，你仍然需要对普通 DOM 元素进行底层操作，这时候就会用到自定义指令。举个聚焦输入框的例子，如下：

当页面加载时，该元素将获得焦点。事实上，只要你在打开这个页面后还没点击过任何内容，这个输入框就应当还是处于聚焦状态。现在让我们用指令来实现这个功能：

```javascript
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

如果想注册局部指令，组件中也接受一个 `directives` 的选项，

对象形式：

```javascript
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

自定义指令也可以写成函数形式，但是无法在钩子函数中做自定义的操作

```javascript
directives: {
    // 1.当指令与元素成功绑定时会调用
    // 2.指令所在的模板被重新解析时也会调用
  focus(element, binding){
    element.value = binding.value
  }
}
```

然后你可以在模板中任何元素上使用新的 `v-focus` property，如下：

```html
<input v-focus>
```

## 钩子函数

一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

## 钩子函数参数

指令钩子函数会被传入以下参数：

- `el`：指令所绑定的元素，可以用来直接操作 DOM。

- `binding`

  ：一个对象，包含以下 property：

  - `name`：指令名，不包括 `v-` 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。

> ```asciiarmor
> 自定义指定定义时不加v-，但是使用时要加v-
> 指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名
> ```

------------------------

# Vue生命周期函数

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，下图展示了实例的生命周期。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/lifecycle.png)

所有的生命周期钩子自动绑定 `this` 上下文到实例中，因此你可以访问数据，对 property 和方法进行运算。这意味着**你不能使用箭头函数来定义一个生命周期方法** (例如 `created: () => this.fetchTodos()`)。这是因为箭头函数绑定了父上下文，因此 `this` 与你期待的 Vue 实例不同，`this.fetchTodos` 的行为未定义。

## beforeCreate

在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

## created

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，property 和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el` property 目前尚不可用。

## beforeMount

在挂载开始之前被调用：相关的 `render` 函数首次被调用。

**该钩子在服务器端渲染期间不被调用。**

## mounted（重要）

实例被挂载后调用，这时 `el` 被新创建的 `vm.$el` 替换了。如果根实例挂载到了一个文档内的元素上，当 `mounted` 被调用时 `vm.$el` 也在文档内。

在此钩子函数中可以发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。

注意 `mounted` **不会**保证所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以在 `mounted` 内部使用 vm.$nextTick：

```javascript
mounted: function () {
  this.$nextTick(function () {
    // Code that will run only after the
    // entire view has been rendered
  })
}
```

## beforeUpdate

数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。

**该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行。**

## updated

由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用计算属性或 watcher 取而代之。

注意 `updated` **不会**保证所有的子组件也都一起被重绘。如果你希望等到整个视图都重绘完毕，可以在 `updated` 里使用 vm.$nextTick

## beforeDestroy（重要）

实例销毁之前调用。在这一步，实例仍然完全可用。

在此钩子函数中，可以清楚定时器、解绑自定义事件、取消订阅消息等【收尾工作】

> ```asciiarmor
> 一般不会在beforeDestroy操作数据，因为即便操作数据也不会再触发更新流程了
> ```

## destroyed

实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。

-----------------------------

# Vue组件化编程

## 非单文件组件

通常一个应用会以一棵嵌套的组件树的形式来组织：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vuecomponents.png)

例如，你可能会有页头、侧边栏、内容区等组件，每个组件又包含了其它的像导航链接、博文之类的组件。

为了能在模板中使用，这些组件必须先注册以便 Vue 能够识别。这里有两种组件的注册类型：**全局注册**和**局部注册**。

这里有一个 `Vue.component` **全局注册**的示例：

```javascript
// 定义一个名为 button-counter 的新组件
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

**局部注册**的示例:

```javascript
const counter = Vue.extend({
    data: function () {
        return {
            count: 0
        }
    },
    template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})
```

```javascript
new Vue({
    el: "#root",
    components: {
        'button-counter':counter
    }
})
```

组件是可复用的 Vue 实例，且带有一个名字：在这个例子中是 `<button-counter>`。我们可以在一个通过 `new Vue` 创建的 Vue 根实例中，把这个组件作为自定义元素来使用：

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

你可以将组件进行任意次数的复用：

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

使用`Vue.extend(options)`创建，其中`options`和`new Vue(options)`时传入的那个`options`几乎一样，但也有点区别；区别如下：

> ```asciiarmor
> Vue.extend(options)中el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
> ```

> ```asciiarmor
> 一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝，如果 Vue 没有这条规则，点击一个按钮就可能会像如下代码一样影响到其它所有实例：
> ```

几个注意点：

1. 关于组件名:

   - 一个单词组成：

     第一种写法(首字母小写)：school
     第二种写法(首字母大写)：School

   - 多个单词组成：	

     **使用 kebab-case**

     ```
     Vue.component('my-component-name', { /* ... */ })
     ```

     当使用 kebab-case (短横线分隔命名) 定义一个组件时，你也必须在引用这个自定义元素时使用 kebab-case，例如 `<my-component-name>`。

     **使用 PascalCase**

     ```
     Vue.component('MyComponentName', { /* ... */ })
     ```

     当使用 PascalCase (首字母大写命名) 定义一个组件时，你在引用这个自定义元素时两种命名法都可以使用。也就是说 `<my-component-name>` 和 `<MyComponentName>` 都是可接受的。注意，尽管如此，直接在 DOM (即非字符串的模板) 中使用时只有 kebab-case 是有效的。

2. 关于组件标签:

   第一种写法：<school></school>
   第二种写法：<school/>     (备注：不用使用脚手架时，<school/>会导致后续组件不能渲染。)

3. 一个简写方式：
   const school = Vue.extend(options) 可简写为：const school = options

> ```dylan
> 组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行。
> 可以使用name配置项指定组件在开发者工具中呈现的名字
> ```

-------------

## VueComponent

1. button-counter组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的。
2. 我们只需要写<button-counter></button-counter>，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：new VueComponent(options)。
3. 每次调用Vue.extend，返回的都是一个全新的VueComponent！
4. 关于this指向：
   - `.new Vue(options)`配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】
   - data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】

> ```asciiarmor
> 一个重要的内置关系：VueComponent.prototype.__proto__ === Vue.prototype
> 为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。
> ```

-------

## 单文件组件

在很多 Vue 项目中，我们使用 `Vue.component` 来定义全局组件，紧接着用 `new Vue({ el: '#container '})` 在每个页面内指定一个容器元素。

这种方式在很多中小规模的项目中运作的很好，在这些项目里 JavaScript 只被用来加强特定的视图。但当在更复杂的项目中，或者你的前端完全由 JavaScript 驱动的时候，下面这些缺点将变得非常明显：

- **全局定义 (Global definitions)** 强制要求每个 component 中的命名不得重复
- **字符串模板 (String templates)** 缺乏语法高亮，在 HTML 有多行的时候，需要用到丑陋的 `\`
- **不支持 CSS (No CSS support)** 意味着当 HTML 和 JavaScript 组件化时，CSS 明显被遗漏
- **没有构建步骤 (No build step)** 限制只能使用 HTML 和 ES5 JavaScript，而不能使用预处理器，如 Pug (formerly Jade) 和 Babel

文件扩展名为 `.vue` 的 **single-file components (单文件组件)** 为以上所有问题提供了解决方法，并且还可以使用 webpack 或 Browserify 等构建工具。

这是一个文件名为 `Hello.vue` 的简单实例：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Vue%E5%9F%BA%E7%A1%80/vue-component.png)

**一个.vue 文件的组成(3 个部分)**

1. 模板页面
```html
<template>
页面模板
</template>
```

2. JS 模块对象
```html
<script>
export default {
data() {return {}},
methods: {},
computed: {},
components: {}
}
</script>
```

3. 样式
```html
<style>
样式定义
</style>
```

