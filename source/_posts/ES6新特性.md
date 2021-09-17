---
title: ES6新特性
date: 2021-05-24 09:18:28
tags: [JavaScript,前端,JavaWeb]
---

#  基础语法

## let 关键字

let关键字来声明变量

## const 关键字

const 关键字用来声明常量， const声明有以下特点
1) 声明必须赋初始值
2) 标识符一般为大写
3) 不允许重复声明
4) 值不允许修改
5) 块儿级作用域
注意 : 对象属性修改和数组元素变化不会触发const错误

## 变量的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

### 数组的解构赋值

```js
const arr = ['张学友', '刘德华', '黎明', '郭富城']; 
let [zhang, liu, li, guo] = arr;
console.log(zhang,liu,li,guo)
// console.log: 张学友 刘德华 黎明 郭富城
```

### 对象的解构赋值

```js
const person = {
    name: 'zhangsan',
    hobbies: ['抽烟','喝酒','烫头'],
    selfIntroduction: function() {
        console.log("大家好，我是法外狂徒张三")
    }
}
let {name,hobbies,selfIntroduction} = person
console.log(name)  //console.log: zhangsan
console.log(hobbies) //console.log: ["抽烟", "喝酒", "烫头"]
selfIntroduction()  //console.log: 大家好，我是法外狂徒张三
```

### 默认值

```js
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```

--------------

## 模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

### 多行字符串

```js
let str = `Carpe diem. 
           Seize the day, boys. 
           Make your lives extraordinary.`
```

### 字符串中嵌入变量

```js
let star = '张三'; 
let result = `法外狂徒${star}`; //法外狂徒张三
```

## 对象的简化写法

ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

```js
let name = '张三'
function hello() {
    console.log("大家好，我是法外狂徒张三")
}
const Person = {
    name,
    hello
}
```

除了属性简写，方法也可以简写。

```javascript
const o = {
  method() {
    return "Hello!";
  }
};

// 等同于

const o = {
  method: function() {
    return "Hello!";
  }
};
```

## 箭头函数

箭头函数提供了一种更加简洁的函数书写方式。基本语法是：

**参数 => 函数体**

```js
/** * 1. 通用写法 */ 
let fn = (arg1, arg2, arg3) => { return arg1 + arg2 + arg3; }
```

函数体如果只有一条语句，则花括号可以省略的，返回值为该执行结果

```js
var sum = (num1, num2) =>  num1 + num2
console.log(sum(1,2)) // console.log: 3
```

如果形参只有一个，则小括号可以省略

```js
var double = arg => arg * 2
console.log(double(3)) // console.log: 6
```

箭头函数有几个使用注意点。

（1）箭头函数没有自己的<span style="color:#cc0000">this</span>对象，对于普通函数来说，内部的<span style="color:#cc0000">**this**</span>指向函数运行时所在的对象，但是这一点对箭头函数不成立。它没有自己的<span style="color:#cc0000">this</span>对象，内部的<span style="color:#cc0000">this</span>就是定义时上层作用域中的<span style="color:#cc0000">this</span>。

（2）不可以当作构造函数，也就是说，不可以对箭头函数使用<span style="color:#cc0000">new</span>命令，否则会抛出一个错误。

（3）不可以使用<span style="color:#cc0000">arguments</span>对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。

（4）不可以使用<span style="color:#cc0000">yield</span>命令，因此箭头函数不能用作 Generator 函数。

**不适用场合**

由于箭头函数使得<span style="color:#cc0000">this</span>从“动态”变成“静态”，下面两个场合不应该使用箭头函数。

第一个场合是定义对象的方法，且该方法内部包括`this`。

```javascript
const cat = {
  lives: 9,
  jumps: () => {
    this.lives--;
  }
}
```

上面代码中，`cat.jumps()`方法是一个箭头函数，这是错误的。调用`cat.jumps()`时，如果是普通函数，该方法内部的`this`指向`cat`；如果写成上面那样的箭头函数，使得`this`指向全局对象，因此不会得到预期结果。这是因为对象不构成单独的作用域，导致`jumps`箭头函数定义时的作用域就是全局作用域。

第二个场合是需要动态`this`的时候，也不应使用箭头函数。

```javascript
var button = document.getElementById('press');
button.addEventListener('click', () => {
  this.classList.toggle('on');
});
```

上面代码运行时，点击按钮会报错，因为`button`的监听函数是一个箭头函数，导致里面的`this`就是全局对象。如果改成普通函数，`this`就会动态指向被点击的按钮对象。 

另外，如果函数体很复杂，有许多行，或者函数内部有大量的读写操作，不单纯是为了计算值，这时也不应该使用箭头函数，而是要使用普通函数，这样可以提高代码可读性。

## rest 参数 

ES6 引入 rest 参数（形式为<span style="color:#cc00ff">...变量名</span>），用于获取函数的多余参数，这样就不需要使用<span style="color:#cc00ff">arguments</span>对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```js
/** * 作用与 arguments 类似 */ 
function add(...args) { 
    console.log(args)
} 
add(1, 2, 3, 4, 5)
```

> <span style="color:red">rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。</span>

## 扩展运算符

扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```js
let hobbies = ['抽烟','喝酒','烫头']
function fn() {
    console.log(arguments)
}
fn(...hobbies) //["抽烟", "喝酒", "烫头"]
```

### 数组的合并

```js
let hobbies = ['抽烟','喝酒','烫头']
let interests = ['钓鱼','摸鱼','划水']
let like = [...hobbies, ...interests]
console.log(like) // ["抽烟", "喝酒", "烫头", "钓鱼", "摸鱼", "划水"]
```

### 数组的克隆

```js
let hobbies = ['钓鱼','摸鱼','划水']
let interests = [...hobbies]
console.log(interests) // ["钓鱼", "摸鱼", "划水"]
```

-----------

# Symbol

ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入`Symbol`的原因。

ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，前六种是：`undefined`、`null`、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

Symbol 值通过`Symbol`函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

```javascript
let s = Symbol();

typeof s
// "symbol"
```

上面代码中，变量`s`就是一个独一无二的值。`typeof`运算符的结果，表明变量`s`是 Symbol 数据类型，而不是字符串之类的其他类型。

注意，`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。也就是说，由于 Symbol 值不是对象，所以不能添加属性。基本上，它是一种类似于字符串的数据类型。

`Symbol`函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。

## Symbol内置值

除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。

| 方法                      | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| Symbol.hasInstance        | 当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法 |
| Symbol.isConcatSpreadable | 对象的`Symbol.isConcatSpreadable`属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开。 |
| Symbol.species            | 对象的`Symbol.species`属性，指向一个构造函数。创建衍生对象时，会使用该属性。 |
| Symbol.match              | 对象的`Symbol.match`属性，指向一个函数。当执行`str.match(myObject)`时，如果该属性存在，会调用它，返回该方法的返回值。 |
| Symbol.replace            | 对象的`Symbol.replace`属性，指向一个方法，当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值。 |
| Symbol.search             | 对象的`Symbol.search`属性，指向一个方法，当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值。 |
| Symbol.split              | 对象的`Symbol.split`属性，指向一个方法，当该对象被`String.prototype.split`方法调用时，会返回该方法的返回值。 |
| Symbol.iterator           | 对象的`Symbol.iterator`属性，指向该对象的默认遍历器方法。    |
| Symbol.toPrimitive        | 对象的`Symbol.toPrimitive`属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。 |
| Symbol.toStringTag        | 对象的`Symbol.toStringTag`属性，指向一个方法。在该对象上面调用`Object.prototype.toString`方法时，如果这个属性存在，它的返回值会出现在`toString`方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个字符串。 |
| Symbol.unscopables        | 对象的`Symbol.unscopables`属性，指向一个对象。该对象指定了使用`with`关键字时，哪些属性会被`with`环境排除。 |

-------------------

# Iterator迭代器

JavaScript 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）。

Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

Iterator 的遍历过程是这样的。

（1）创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。

（2）第一次调用指针对象的`next`方法，可以将指针指向数据结构的第一个成员。

（3）第二次调用指针对象的`next`方法，指针就指向数据结构的第二个成员。

（4）不断调用指针对象的`next`方法，直到它指向数据结构的结束位置。

每一次调用`next`方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含`value`和`done`两个属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束。

下面是一个模拟`next`方法返回值的例子。

```javascript
var it = makeIterator(['a', 'b']);

it.next() // { value: "a", done: false }
it.next() // { value: "b", done: false }
it.next() // { value: undefined, done: true }

function makeIterator(array) {
  var nextIndex = 0;
  return {
    next: function() {
      return nextIndex < array.length ?
        {value: array[nextIndex++], done: false} :
        {value: undefined, done: true};
    }
  };
}
```

上面代码定义了一个`makeIterator`函数，它是一个遍历器生成函数，作用就是返回一个遍历器对象。对数组`['a', 'b']`执行这个函数，就会返回该数组的遍历器对象（即指针对象）`it`。

指针对象的`next`方法，用来移动指针。开始时，指针指向数组的开始位置。然后，每次调用`next`方法，指针就会指向数组的下一个成员。第一次调用，指向`a`；第二次调用，指向`b`。

`next`方法返回一个对象，表示当前数据成员的信息。这个对象具有`value`和`done`两个属性，`value`属性返回当前位置的成员，`done`属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用`next`方法。

总之，调用指针对象的`next`方法，就可以遍历事先给定的数据结构。

## 默认 Iterator 接口

Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环（详见下文）。当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。

一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）。

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名`Symbol.iterator`，它是一个表达式，返回`Symbol`对象的`iterator`属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

下面是另一个为对象添加 Iterator 接口的例子。

```js
let obj = {
    data: ['hello', 'world'],
    [Symbol.iterator]() {
        const self = this;
        let index = 0;
        return {
            next() {
                if (index < self.data.length) {
                    return {
                        value: self.data[index++],
                        done: false
                    };
                }
                return { value: undefined, done: true };
            }
        };
    }
};

for (const item of obj) {
    console.log(item)
}
// 输出
// hello
// world
```

-------------

# Generator 生成器

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同

Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

形式上，Generator 函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

```js
function* helloWorldGenerator() {
    yield 'hello';
    yield 'world';
    return 'ending';
}

var hw = helloWorldGenerator();
let next1 = hw.next()
console.log(next1) // { value: 'hello', done: false }

let next2 = hw.next()
console.log(next2) // { value: 'world', done: false }

let next3 = hw.next()
console.log(next3) // { value: 'ending', done: true }

let next4 = hw.next()
console.log(next4) // { value: undefined, done: true }
```

上面代码定义了一个 Generator 函数`helloWorldGenerator`，它内部有两个`yield`表达式（`hello`和`world`），即该函数有三个状态：hello，world 和 return 语句（结束执行）。

然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

下一步，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

上面代码一共调用了四次`next`方法。

第一次调用，Generator 函数开始执行，直到遇到第一个`yield`表达式为止。`next`方法返回一个对象，它的`value`属性就是当前`yield`表达式的值`hello`，`done`属性的值`false`，表示遍历还没有结束。

第二次调用，Generator 函数从上次`yield`表达式停下的地方，一直执行到下一个`yield`表达式。`next`方法返回的对象的`value`属性就是当前`yield`表达式的值`world`，`done`属性的值`false`，表示遍历还没有结束。

第三次调用，Generator 函数从上次`yield`表达式停下的地方，一直执行到`return`语句（如果没有`return`语句，就执行到函数结束）。`next`方法返回的对象的`value`属性，就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句，则`value`属性的值为`undefined`），`done`属性的值`true`，表示遍历已经结束。

第四次调用，此时 Generator 函数已经运行完毕，`next`方法返回对象的`value`属性为`undefined`，`done`属性为`true`。以后再调用`next`方法，返回的都是这个值。

总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象。`value`属性表示当前的内部状态的值，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束。

## next()函数的参数

一般情况下，next 方法不传入参数的时候，yield 表达式的返回值是 undefined 。**当 next 传入参数的时候，该参数会作为上一步yield的返回值。**

```js
function* sendParameter() {
    console.log("start");
    var x = yield '2';
    console.log("one:" + x);
    var y = yield '3';
    console.log("two:" + y);
    console.log("total:" + (x + y));
}
var sendp = sendParameter();
sendp.next(10);
// start
// {value: "2", done: false}
sendp.next(20);
// one:20
// {value: "3", done: false}
sendp.next(30);
// two:30
// total:50
// {value: undefined, done: true}
```

## Generator 函数的异步应用

异步编程对 JavaScript 语言太重要。JavaScript 语言的执行环境是“单线程”的，如果没有异步编程，根本没法用，非卡死不可。本章主要介绍 Generator 函数如何完成异步操作。

### 传统方法

ES6 诞生以前，异步编程的方法，大概有下面四种。

- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象

Generator 函数将 JavaScript 异步编程带入了一个全新的阶段。

### 基本概念

#### 异步

所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

#### 回调函数

JavaScript 语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。回调函数的英语名字`callback`，直译过来就是"重新调用"。

读取文件进行处理，是这样写的。

```javascript
fs.readFile('/etc/passwd', 'utf-8', function (err, data) {
  if (err) throw err;
  console.log(data);
});
```

上面代码中，`readFile`函数的第三个参数，就是回调函数，也就是任务的第二段。等到操作系统返回了`/etc/passwd`这个文件以后，回调函数才会执行。

一个有趣的问题是，为什么 Node 约定，回调函数的第一个参数，必须是错误对象`err`（如果没有错误，该参数就是`null`）？

原因是执行分成两段，第一段执行完以后，任务所在的上下文环境就已经结束了。在这以后抛出的错误，原来的上下文环境已经无法捕捉，只能当作参数，传入第二段。

#### Generator函数

下面看看如何使用 Generator 函数，执行一个真实的异步任务。

```javascript
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
```

上面代码中，Generator 函数封装了一个异步操作，该操作先读取一个远程接口，然后从 JSON 格式的数据解析信息。就像前面说过的，这段代码非常像同步操作，除了加上了`yield`命令。

执行这段代码的方法如下。

```javascript
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

上面代码中，首先执行 Generator 函数，获取遍历器对象，然后使用`next`方法（第二行），执行异步任务的第一阶段。由于`Fetch`模块返回的是一个 Promise 对象，因此要用`then`方法调用下一个`next`方法。

可以看到，虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。

----------

# promise对象

## Promise的含义

Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6将其写进了语言标准，统一了用法，原生提供了`Promise`对象。

所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise是一个对象，从它可以获取异步操作的消息。Promise提供统一的API，各种异步操作都可以用同样的方法进行处理。

`Promise`对象有以下两个特点。

（1）对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态：`Pending`（进行中）、`Resolved`（已完成，又称Fulfilled）和`Rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是`Promise`这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。`Promise`对象的状态改变，只有两种可能：从`Pending`变为`Resolved`和从`Pending`变为`Rejected`。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对`Promise`对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

有了`Promise`对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，`Promise`对象提供统一的接口，使得控制异步操作更加容易。

`Promise`也有一些缺点。首先，无法取消`Promise`，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，`Promise`内部抛出的错误，不会反应到外部。第三，当处于`Pending`状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

如果某些事件不断地反复发生，一般来说，使用stream模式是比部署`Promise`更好的选择。

## 基本用法

ES6规定，Promise对象是一个构造函数，用来生成Promise实例。

下面代码创造了一个Promise实例。

```javascript
var promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。它们是两个函数，由JavaScript引擎提供，不用自己部署。

`resolve`函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从Pending变为Resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；`reject`函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从Pending变为Rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise实例生成以后，可以用`then`方法分别指定`Resolved`状态和`Reject`状态的回调函数。

```javascript
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```

`then`方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为Resolved时调用，第二个回调函数是Promise对象的状态变为Reject时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

## Promise.prototype.then()

Promise实例具有`then`方法，也就是说，`then`方法是定义在原型对象Promise.prototype上的。它的作用是为Promise实例添加状态改变时的回调函数。前面说过，`then`方法的第一个参数是Resolved状态的回调函数，第二个参数（可选）是Rejected状态的回调函数。

`then`方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

```javascript
getJSON("/posts.json").then(function(json) {
  return json.post;
}).then(function(post) {
  // ...
});
```

上面的代码使用`then`方法，依次指定了两个回调函数。第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

## Promise.prototype.catch()

`Promise.prototype.catch`方法是`.then(null, rejection)`的别名，用于指定发生错误时的回调函数。

```javascript
getJSON("/posts.json").then(function(posts) {
  // ...
}).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});
```

Promise对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获。

```javascript
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```

上面代码中，一共有三个Promise对象：一个由`getJSON`产生，两个由`then`产生。它们之中任何一个抛出的错误，都会被最后一个`catch`捕获。

一般来说，不要在`then`方法里面定义Reject状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。

```javascript
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```

上面代码中，第二种写法要好于第一种写法，理由是第二种写法可以捕获前面`then`方法执行中的错误，也更接近同步的写法（`try/catch`）。因此，建议总是使用`catch`方法，而不使用`then`方法的第二个参数。

--------

# Set

ES6提供了新的数据结构Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

Set本身是一个构造函数，用来生成Set数据结构。

## 如何创建一个集合

您可以通过以下方式创建 JavaScript 集：

- 将数组传递给 `new Set()`
- 创建一个新的 Set 并用于`add()`添加值
- 创建一个新的 Set 并用于`add()`添加变量

```js
// Create a Set
const letters = new Set(["a","b","c"]);

// Create a Set
const letters = new Set();

// Add Values to the Set
letters.add("a");
letters.add("b");
letters.add("c");

// Create Variables
const a = "a";
const b = "b";
const c = "c";

// Create a Set
const letters = new Set();

// Add Variables to the Set
letters.add(a);
letters.add(b);
letters.add(c);
```

## Set 实例的属性和方法

Set 结构的实例有以下属性。

Set 结构的实例有以下属性。

- <span style="color:#dd0000">`Set.prototype.constructor`</span>：构造函数，默认就是<span style="color:#dd0000">`Set`</span>函数。
- <span style="color:#dd0000">`Set.prototype.size`</span>：返回`Set`实例的成员总数。

Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

- <span style="color:#dd0000">`add(value)`</span>：添加某个值，返回<span style="color:#dd0000"> Set</span> 结构本身。
- <span style="color:#dd0000">`delete(value)`</span>：删除某个值，返回一个布尔值，表示删除是否成功。
- <span style="color:#dd0000">`has(value)`</span>：返回一个布尔值，表示该值是否为<span style="color:#dd0000">`Set`</span>的成员。
- <span style="color:#dd0000">`clear()`</span>：清除所有成员，没有返回值。

Set 结构的实例有四个遍历方法，可以用于遍历成员。

- <span style="color:#dd0000">`Set.prototype.keys()`</span>：返回键名的遍历器
- <span style="color:#dd0000">`Set.prototype.values()`</span>：返回键值的遍历器
- <span style="color:#dd0000">`Set.prototype.entries()`</span>：返回键值对的遍历器
- <span style="color:#dd0000">`Set.prototype.forEach()`</span>：使用回调函数遍历每个成员

# Map

JavaScript的对象（Object），本质上是键值对的集合（Hash结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

ES6提供了Map数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object结构

提供了“字符串—值”的对应，Map结构提供了“值—值”的对应，是一种更完善的Hash结构实现。如果你需要“键值对”的数据结构，Map比Object更合适。

您可以通过以下方式创建 JavaScript Map：

- 将数组传递给 <span style="color:#dd0000">`new Map()`</span>
- 创建地图并使用 <span style="color:#dd0000">`Map.set()`</span>

您可以通过将 Array 传递给<span style="color:#dd0000">`new Map()`</span>构造函数来创建 Map ：

```js
// Create a Map
const fruits = new Map([
  ["apples", 500],
  ["bananas", 300],
  ["oranges", 200]
]);
```

您可以使用以下<span style="color:#dd0000">`set()`</span>方法向 Map 添加元素：

```js
// Create a Map
const fruits = new Map();

// Set Map Values
fruits.set("apples", 500);
fruits.set("bananas", 300);
fruits.set("oranges", 200);
```

该<span style="color:#dd0000">`set()`</span>方法还可用于更改现有 Map 值：

```js
fruits.set("apples", 500);
```

## Map Methods

| Method    | Description                                                  |
| :-------- | :----------------------------------------------------------- |
| new Map() | 创建一个新的 Map 对象                                        |
| set()     | `set`方法设置`key`所对应的键值，然后返回整个Map结构。如果`key`已经有值，则键值会被更新，否则就新生成该键。 |
| get()     | `get`方法读取`key`对应的键值，如果找不到`key`，返回`undefined`。 |
| clear()   | `clear`方法清除所有成员，没有返回值。                        |
| delete()  | `delete`方法删除某个键，返回true。如果删除失败，返回false。  |
| has()     | `has`方法返回一个布尔值，表示某个键是否在Map数据结构中。     |
| forEach() | 遍历Map的所有成员。                                          |
| entries() | 返回所有成员的遍历器。                                       |
| keys()    | 返回键名的遍历器。                                           |
| values()  | 返回键值的遍历器。                                           |

| Property | Description                     |
| :------- | :------------------------------ |
| size     | `size`属性返回Map结构的成员总数 |

demo:

```js
fruits.set("apples", 500);

fruits.get("apples");    // Returns 500

fruits.size;

fruits.delete("apples");

fruits.clear();

fruits.has("apples");

for (let key of fruits.keys()) {
    console.log(key);
}

for (let value of fruits.values()) {
    console.log(value);
}

for (let item of fruits.entries()) {
    console.log(item[0], item[1]);
}

var reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};

map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter);
```

-------------------------

# Class

## 简介

JavaScript语言的传统方法是通过构造函数，定义并生成新对象。下面是一个例子。

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```

上面这种写法跟传统的面向对象语言（比如C++和Java）差异很大，很容易让新学习这门语言的程序员感到困惑。

ES6提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。基本上，ES6的`class`可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。上面的代码用ES6的“类”改写，就是下面这样。

```js
//定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

上面代码定义了一个“类”，可以看到里面有一个`constructor`方法，这就是构造方法，而`this`关键字则代表实例对象。也就是说，ES5的构造函数`Point`，对应ES6的`Point`类的构造方法。

`Point`类除了构造方法，还定义了一个`toString`方法。注意，定义“类”的方法的时候，前面不需要加上`function`这个关键字，直接把函数定义放进去了就可以了。另外，方法之间不需要逗号分隔，加了会报错。

ES6的类，完全可以看作构造函数的另一种写法。

```javascript
class Point {
  // ...
}

typeof Point // "function"
Point === Point.prototype.constructor // true
```

上面代码表明，类的数据类型就是函数，类本身就指向构造函数。

使用的时候，也是直接对类使用`new`命令，跟构造函数的用法完全一致。

## 静态成员

类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

静态属性指的是Class本身的属性，即`Class.propname`，而不是定义在实例对象（`this`）上的属性。

```javascript
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```

上面的写法为`Foo`类定义了一个静态属性`prop`。

目前，只有这种写法可行，因为ES6明确规定，Class内部只有静态方法，没有静态属性。

```javascript
// 以下两种写法都无效
class Foo {
  // 写法一
  prop: 2

  // 写法二
  static prop: 2
}

Foo.prop // undefined
```

## Class的继承

Class之间可以通过`extends`关键字实现继承，这比ES5的通过修改原型链实现继承，要清晰和方便很多。

```javascript
class ColorPoint extends Point {}
```

上面代码定义了一个`ColorPoint`类，该类通过`extends`关键字，继承了`Point`类的所有属性和方法。但是由于没有部署任何代码，所以这两个类完全一样，等于复制了一个`Point`类。下面，我们在`ColorPoint`内部加上代码。

```javascript
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

上面代码中，`constructor`方法和`toString`方法之中，都出现了`super`关键字，它在这里表示父类的构造函数，用来新建父类的`this`对象。

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。

## getter和setter

与ES5一样，在Class内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```

----------------------------

# 数值的扩展

## Number.EPSILON

ES6在Number对象上面，新增一个极小的常量`Number.EPSILON`。

```javascript
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// '0.00000000000000022204'
```

引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。

但是如果这个误差能够小于`Number.EPSILON`，我们就可以认为得到了正确结果。

因此，`Number.EPSILON`的实质是一个可以接受的误差范围。

```javascript
function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON;
}
withinErrorMargin(0.1 + 0.2, 0.3)
// true
withinErrorMargin(0.2 + 0.2, 0.3)
// false
```

上面的代码为浮点数运算，部署了一个误差检查函数。

## 二进制和八进制

从ES5开始，在严格模式之中，八进制就不再允许使用前缀`0`表示，ES6进一步明确，要使用前缀`0o`表示。

如果要将`0b`和`0o`前缀的字符串数值转为十进制，要使用`Number`方法。

```javascript
Number('0b111')  // 7
Number('0o10')  // 8
```

## 新Number 属性

ES6 向 Number 对象添加了以下属性：

- <span style="color:#dd0000">`EPSILON`</span>   极小数
- <span style="color:#dd0000">`MIN_SAFE_INTEGER `</span>      JavaScript 能够准确表示的整数的最小值
- <span style="color:#dd0000">`MAX_SAFE_INTEGER`</span>       JavaScript 能够准确表示的整数的最大值

JavaScript 能够准确表示的整数范围在`-2^53`到`2^53`之间（不含两个端点），超过这个范围，无法精确表示这个值。

```javascript
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
// true
Number.MAX_SAFE_INTEGER === 9007199254740991
// true

Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
// true
Number.MIN_SAFE_INTEGER === -9007199254740991
// true
```

上面代码中，可以看到 JavaScript 能够精确表示的极限。

## 新Number 方法

| 方法                     | 作用                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `Number.isInteger()`     | `Number.isInteger()`用来判断一个值是否为整数。需要注意的是，在JavaScript内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值。 |
| `Number.isSafeInteger()` | `Number.isSafeInteger()`则是用来判断一个整数是否落在Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER之内。即在`-2^53`到`2^53`之间 |
| `Number.isFinite()`      | 用来检查一个数值是否为有限的（finite）。                     |
| `Number.isNaN()`         | 用来检查一个值是否为`NaN`。                                  |
| `parseInt()`             | 转为Int类型                                                  |
| `parseFloat()`           | 转为Float类型                                                |
| `Math.trunc`             | `Math.trunc`方法用于去除一个数的小数部分，返回整数部分。     |
| `Math.sign`              | 方法用来判断一个数到底是正数、负数、还是零。它会返回五种值。参数为正数，返回+1； 参数为负数，返回-1； 参数为0，返回0； 参数为-0，返回-0; 其他值，返回NaN。 |

------------------------

# 对象方法的扩展

## Object.is()

ES5比较两个值是否相等，只有两个运算符：相等运算符（`==`）和严格相等运算符（`===`）。它们都有缺点，前者会自动转换数据类型，后者的`NaN`不等于自身，以及`+0`等于`-0`。JavaScript缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。

ES6提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is`就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。

不同之处只有两个：一是`+0`不等于`-0`，二是`NaN`等于自身。

```javascript
+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

## Object.assign()

`Object.assign`方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。

```javascript
var target = { a: 1 };

var source1 = { b: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。

注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

### 注意点

`Object.assign`方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。

```javascript
var obj1 = {
    a: {
        b: 1
    }
};
var obj2 = Object.assign({}, obj1);

obj1.a.b = 2;
obj2.a.b // 2
```

上面代码中，源对象`obj1`的`a`属性的值是一个对象，`Object.assign`拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。

对于这种嵌套的对象，一旦遇到同名属性，`Object.assign`的处理方法是替换，而不是添加。

```javascript
var target = { 
    a: { 
        b: 'c', 
        d: 'e' 
    } 
}
var source = { 
    a: { 
        b: 'hello' 
    } 
}
Object.assign(target, source)
// { a: { b: 'hello' } }
```

### 常见用途

`Object.assign`方法有很多用处。

**（1）为对象添加属性**

```javascript
class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}
```

上面方法通过`Object.assign`方法，将`x`属性和`y`属性添加到`Point`类的对象实例。

**（2）为对象添加方法**

```javascript
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于下面的写法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

上面代码使用了对象属性的简洁表示法，直接将两个函数放在大括号中，再使用assign方法添加到SomeClass.prototype之中。

**（3）克隆对象**

```javascript
function clone(origin) {
  return Object.assign({}, origin);
}
```

上面代码将原始对象拷贝到一个空对象，就得到了原始对象的克隆。

不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```javascript
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

**（4）合并多个对象**

将多个对象合并到某个对象。

```javascript
const merge =
  (target, ...sources) => Object.assign(target, ...sources);
```

如果希望合并后返回一个新对象，可以改写上面函数，对一个空对象合并。

```javascript
const merge =
  (...sources) => Object.assign({}, ...sources);
```

**（5）为属性指定默认值**

```javascript
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
}
```

上面代码中，`DEFAULTS`对象是默认值，`options`对象是用户提供的参数。`Object.assign`方法将`DEFAULTS`和`options`合并成一个新对象，如果两者有同名属性，则`option`的属性值会覆盖`DEFAULTS`的属性值。

注意，由于存在深拷贝的问题，`DEFAULTS`对象和`options`对象的所有属性的值，都只能是简单类型，而不能指向另一个对象。否则，将导致`DEFAULTS`对象的该属性不起作用。

------------------

# 模块化

历史上，JavaScript 一直没有模块（module）体系，无法将一个大程序拆分成互相依赖的小文件，再用简单的方法拼装起来。其他语言都有这项功能，比如 Ruby 的`require`、Python 的`import`，甚至就连 CSS 都有`@import`，但是 JavaScript 任何这方面的支持都没有，这对开发大型的、复杂的项目形成了巨大障碍。

在 ES6 之前，社区制定了一些模块加载方案，最主要的有 CommonJS 和 AMD 两种。前者用于服务器，后者用于浏览器。ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代现有的 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

## export 命令

模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。下面是一个 JS 文件，里面使用`export`命令输出变量。

分别暴露

```js
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

统一暴露

```javascript
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export { firstName, lastName, year };
```

通常情况下，`export`输出的变量就是本来的名字，但是可以使用 <span style="color:#dd0000">`as`</span> 关键字重命名。

```javascript
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```

### export default 命令

从前面的例子可以看出，使用`import`命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到`export default`命令，为模块指定默认输出。

```javascript
// export-default.js
export default function () {
  console.log('foo');
}
```

上面代码是一个模块文件`export-default.js`，它的默认输出是一个函数。

其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字。

```javascript
// import-default.js
import customName from './export-default';
customName(); // 'foo'
```

上面代码的`import`命令，可以用任意名称指向`export-default.js`输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是，这时`import`命令后面，不使用大括号。

`export default`命令用在非匿名函数前，也是可以的。

```javascript
// export-default.js
export default function foo() {
  console.log('foo');
}

// 或者写成

function foo() {
  console.log('foo');
}

export default foo;
```

上面代码中，`foo`函数的函数名`foo`，在模块外部是无效的。加载的时候，视同匿名函数加载。

下面比较一下默认输出和正常输出。

```javascript
// 第一组
export default function crc32() { // 输出
  // ...
}

import crc32 from 'crc32'; // 输入

// 第二组
export function crc32() { // 输出
  // ...
};

import {crc32} from 'crc32'; // 输入
```

上面代码的两组写法，第一组是使用`export default`时，对应的`import`语句不需要使用大括号；第二组是不使用`export default`时，对应的`import`语句需要使用大括号。

`export default`命令用于指定模块的默认输出。显然，一个模块只能有一个默认输出，因此`export default`命令只能使用一次。所以，`import`命令后面才不用加大括号，因为只可能对应一个方法。

## import 命令

### 模块的整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

下面是一个`circle.js`文件，它输出两个方法`area`和`circumference`。

```javascript
// circle.js

export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}
```

现在，加载这个模块。

整体加载的写法如下。

```javascript
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

### 模块的选择加载

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

```javascript
// main.js
import {firstName, lastName} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

上面代码的`import`命令，用于加载`profile.js`文件，并从中输入变量。`import`命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，必须与被导入模块（`profile.js`）对外接口的名称相同。

如果想为输入的变量重新取一个名字，`import`命令要使用`as`关键字，将输入的变量重命名。

```javascript
import { lastName as surname } from './profile';
```

## 浏览器的模块加载

浏览器使用 ES6 模块的语法如下。

```html
<script type="module" src="foo.js"></script>
```

上面代码在网页中插入一个模块`foo.js`，由于`type`属性设为`module`，所以浏览器知道这是一个 ES6 模块。

浏览器对于带有`type="module"`的`<script>`，都是异步加载外部脚本，不会造成堵塞浏览器。

然后把模块引入语句写在foo.js文件里