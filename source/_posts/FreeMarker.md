---
title: FreeMarker
date: 2021-07-12 11:24:47
tags:
- 前端框架
- Java
categories: 前端
---

#  FreeMarker

## 1.相关概念

### 1）FreeMarker

FreeMarker 是一款 *模板引擎*： 即一种基于模板和要改变的数据， 并用来生成输出文本(HTML网页，电子邮件，配置文件，源代码等)的通用工具。 它不是面向最终用户的，而是一个Java类库，是一款程序员可以嵌入他们所开发产品的组件。

模板编写为FreeMarker Template Language (FTL)。它是简单的，专用的语言， *不是* 像PHP那样成熟的编程语言。 那就意味着要准备数据在真实编程语言中来显示，比如数据库查询和业务运算， 之后模板显示已经准备好的数据。在模板中，你可以专注于如何展现数据， 而在模板之外可以专注于要展示什么数据。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/FreeMarker/overview.png"  />

这种方式通常被称为 MVC (模型 视图 控制器) 模式，对于动态网页来说，是一种特别流行的模式。 它帮助从开发人员(Java 程序员)中分离出网页设计师(HTML设计师)。设计师无需面对模板中的复杂逻辑， 在没有程序员来修改或重新编译代码时，也可以修改页面的样式。

而FreeMarker最初的设计，是被用来在MVC模式的Web开发框架中生成HTML页面的，它没有被绑定到 Servlet或HTML或任意Web相关的东西上。它也可以用于非Web应用环境中。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/FreeMarker/template.png)

### 2）模板

假设在一个在线商店的应用系统中需要一个HTML页面，和下面这个页面类似：

`output`

```
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>Welcome Big Doe!</h1>
  <p>Our latest product:
  <a href="products/greenmouse.html">green mouse</a>!
</body>
</html>
```

这里的用户名(上面的"Big Joe")，应该是登录这个网页的访问者的名字， 并且最新产品的数据应该来自于数据库，这样它才能随时更新。那么不能直接在HTML页面中输入它们， 不能使用静态的HTML代码。此时，可以使用要求输出的 **模板**。 模板和静态HTML是相同的，只是它会包含一些 FreeMarker 将它们变成动态内容的指令：

`template`

```
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>Welcome ${user}!</h1>
  <p>Our latest product:
  <a href="${latestProduct.url}">${latestProduct.name}</a>!
</body>
</html>
```

模板文件存放在Web服务器上，就像通常存放静态HTML页面那样。当有人来访问这个页面， FreeMarker将会介入执行，然后动态转换模板，用最新的数据内容替换模板中 `${*...*}` 的部分， 之后将结果发送到访问者的Web浏览器中。访问者的Web浏览器就会接收到例如第一个HTML示例那样的内容 (也就是没有FreeMarker指令的HTML代码)，访问者也不会察觉到服务器端使用的FreeMarker。 (当然，存储在Web服务器端的模板文件是不会被修改的；替换也仅仅出现在Web服务器的响应中。)

请注意，模板并没有包含程序逻辑来查找当前的访问者是谁，或者去查询数据库获取最新的产品。 显示的数据是在 FreeMarker 之外准备的，通常是一些 "真正的" 编程语言(比如Java) 所编写的代码。模板作者无需知道这些值是如何计算出的。事实上，这些值的计算方式可以完全被修改， 而模板可以保持不变，而且页面的样式也可以完全被修改而无需改动模板。 当模板作者(设计师)和程序员不是同一人时，显示逻辑和业务逻辑相分离的做法是非常有用的， 即便模板作者和程序员是一个人，这么来做也会帮助管理应用程序的复杂性。 保证模板专注于显示问题(视觉设计，布局和格式化)是高效使用模板引擎的关键。

### 3）模型

为模板准备的数据整体被称作为 **数据模型**。 模板作者要关心的是，数据模型是树形结构(就像硬盘上的文件夹和文件)，在视觉效果上， 数据模型可以是：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/FreeMarker/datamodal.png)

```
Note:
上面只是一个形象化显示；数据模型不是文本格式，它来自于Java对象。 对于Java程序员来说，root就像一个有 getUser() 和 getLatestProduct() 方法的Java对象， 也可以有 "user" 和 "latestProducts" 键值的Java Map对象。相似地，latestProduct 就像是有 getUrl() 和 getName() 方法的Java对象。
```

正如已经看到的，数据模型的基本结构是树状的。 这棵树可以很复杂，并且可以有很大的深度，比如：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/FreeMarker/modaltree.png)

上图中的变量扮演目录的角色(比如 root, `animals`, `mouse`, `elephant`, `python`, `misc`) 被称为 **hashes** (哈希表或哈希，译者注)。哈希表存储其他变量(被称为 *子变量*)， 它们可以通过名称来查找(比如 "animals", "mouse" 或 "price")。

存储单值的变量 (`size`, `price`, `message` 和 `foo`) 称为 **scalars** (标量，译者注)。

如果要在模板中使用子变量， 那应该从根root开始指定它的路径，每级之间用点来分隔开。要访问 `mouse` 的 `price` ，要从root开始，首先进入到 `animals` ，之后访问 `mouse` ，最后访问 `price` 。就可以这样来写 `animals.mouse.price`。

另外一种很重要的变量是 **sequences** (序列，译者注)。 它们像哈希表那样存储子变量，但是子变量没有名字，它们只是列表中的项。 比如，在下面这个数据模型中， `animals` 和 `misc.fruits` 就是序列：

`Data Model`

```
(root)
  |
  +- animals
  |   |
  |   +- (1st)
  |   |   |
  |   |   +- name = "mouse"
  |   |   |
  |   |   +- size = "small"
  |   |   |
  |   |   +- price = 50
  |   |
  |   +- (2nd)
  |   |   |
  |   |   +- name = "elephant"
  |   |   |
  |   |   +- size = "large"
  |   |   |
  |   |   +- price = 5000
  |   |
  |   +- (3rd)
  |       |
  |       +- name = "python"
  |       |
  |       +- size = "medium"
  |       |
  |       +- price = 4999
  |
  +- misc
      |
      +- fruits
          |
          +- (1st) = "orange"
          |
          +- (2nd) = "banana"
```

要访问序列的子变量，可以使用方括号形式的数字索引下标。 索引下标从0开始(从0开始也是程序员的传统)，那么第一项的索引就是0， 第二项的索引就是1等等。要得到第一个动物的名称的话，可以这么来写代码 `animals[0].name`。要得到 `misc.fruits` 中的第二项(字符串`"banana"`)可以这么来写 `misc.fruits[1]`。(实践中，通常按顺序遍历序列，而不用关心索引， 这点会在 [后续介绍](http://freemarker.foofun.cn/dgui_quickstart_template.html#topic.tutorial.list)。)

标量类型可以分为如下的类别：

- 字符串：就是文本，也就是任意的字符序列，比如上面提到的 ''m'', ''o'', ''u'', ''s'', ''e''。比如 `name` 和 `size` 也是字符串。
- 数字：这是数值类型，就像上面的 `price`。 在FreeMarker中，字符串 `"50"` 和数字 `50` 是两种完全不同的东西。前者是两个字符的序列 (这恰好是人们可以读的一个数字)，而后者则是可以在数学运算中直接被使用的数值。
- 日期/时间: 可以是日期-时间格式(存储某一天的日期和时间)， 或者是日期(只有日期，没有时间)，或者是时间(只有时间，没有日期)。
- 布尔值：对应着对/错(是/否，开/关等值)类似的值。 比如动物可以有一个 `protected` (受保护的，译者注) 的子变量， 该变量存储这个动物是否被保护起来的值。

总结：

- 数据模型可以被看成是树形结构。
- 标量用于存储单一的值。这种类型的值可以是字符串，数字，日期/时间或者是布尔值。
- 哈希表是一种存储变量及其相关且有唯一标识名称的容器。
- 序列是存储有序变量的容器。存储的变量可以通过数字索引来检索，索引通常从0开始。

-----

## 2.模板一览

### 1) interpolation 插值

`${...}`： FreeMarker将会输出真实的值来替换大括号内的表达式，这样的表达式被称为 **interpolation**插值

### 2) FTL标签

- **FTL 标签** (FreeMarker模板的语言标签)： FTL标签和HTML标签有一些相似之处，但是它们是FreeMarker的指令，是不会在输出中打印的。 这些标签的名字以 `#` 开头。(用户自定义的FTL标签则需要使用 `@` 来代替 `#`，但这属于更高级的话题了。
- FTL标签也被称为 **指令**。 这些指令在HTML的标签 (比如： `<table>` 和 `</table>`) 和HTML元素 (比如： `table` 元素) 中的关系是相同的。(如果现在还没有感觉到它们的不同， 那么把“FTL标签”和“指令”看做是同义词即可。)

### 3) 注释

- **注释：** 注释和HTML的注释也很相似， 但是它们使用 `<#--` and `-->` 来标识。 不像HTML注释那样，FTL注释不会出现在输出中(不出现在访问者的页面中)， 因为 FreeMarker会跳过它们。

### 4) if 指令

使用 `if` 指令可以有条件地跳过模板的一些片段, 比如，假设在 最初的示例中， 想向你的老板Big Joe特别地问好，可其他人不同：

`Template`

```
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>
    Welcome ${user}<#if user == "Big Joe">, our beloved leader</#if>!
  </h1>
  <p>Our latest product:
  <a href="${latestProduct.url}">${latestProduct.name}</a>!
</body>
</html>
```

此时，告诉 FreeMarker，当和 `"Big Joe"` 相同时 ", our beloved leader" (我们最尊敬的领导，译者注) 才是if条件中那唯一的 `user` 变量的值。 通常来讲，如果 `*condition*` 是false(布尔值)，那么介于 `<#if *condition*>` 和 `</#if>` 标签中的内容会被略过。

我们来详细说说 `*condition*` 的使用： `==` 是用来判断它两侧的值是否相等的操作符， 比较的结果是布尔值，也就是true或者false。在 `==` 的左侧，是被引用的变量， 我们很熟悉这样的语法结构；最终它会被变量的值所替代。通常来说， 在指令或插值中没有被引号标注的内容都被视为变量的引用。右侧则是指定的字符串， 在模板中的字符串 *只能* 放在引号内。

if-else if-else示例

```
<#if animals.python.price < animals.elephant.price>
  Pythons are cheaper than elephants today.
<#elseif animals.elephant.price < animals.python.price>
  Elephants are cheaper than pythons today.
<#else>
  Elephants and pythons cost the same today.
</#if>
```

### 5）list 指令

当需要列表显示内容时，list指令是必须的。

```
TEMPLATE
<p>We have these animals:
<table border=1>
  <#list animals as animal>
    <tr><td>${animal.name}<td>${animal.price} Euros
  </#list>
</table>
```

那么输出结果将会是这样的：

```
OUTPUT
<p>We have these animals:
<table border=1>
    <tr><td>mouse<td>50 Euros
    <tr><td>elephant<td>5000 Euros
    <tr><td>python<td>4999 Euros
</table>
```

`list` 指令的一般格式为： `<#list sequence as loopVariable*repeatThis</#list>`。 `repeatThis` 部分将会在给定的 `sequence` 遍历时在每一项中重复， 从第一项开始，一个接着一个。在所有的重复中， `loopVariable` 将持有当前遍历项的值。 这个变量仅存在于 `<#list ...>` 和 `</#list>` 标签内。

### 6）include 指令

使用 `include` 指令， 我们可以在模板中插入其他文件的内容。

假设要在一些页面中显示版权声明的信息。那么可以创建一个文件来单独包含这些版权声明， 之后在需要它的地方插入即可。比方说，我们可以将版权信息单独存放在页面文件 `copyright_footer.html` 中：

```
TEMPLATE
<hr>
<i>
Copyright (c) 2000 <a href="http://www.acmee.com">Acmee Inc</a>,
<br>
All Rights Reserved.
</i>
```

当需要用到这个文件时，可以使用 `include` 指令来插入：

```
TEMPLATE
<html>
<head>
  <title>Test page</title>
</head>
<body>
  <h1>Test page</h1>
  <p>Blah blah...
  <#include "/copyright_footer.html">
</body>
</html>
```

此时，输出的内容为：

```
OUTPUT
<html>
<head>
  <title>Test page</title>
</head>
<body>
  <h1>Test page</h1>
  <p>Blah blah...
<hr>
<i>
Copyright (c) 2000 <a href="http://www.acmee.com">Acmee Inc</a>,
<br>
All Rights Reserved.
</i>
</body>
</html>
```

### 7）使用内建函数

内建函数很像子变量(如果了解Java术语的话，也可以说像方法)， 它们并不是数据模型中的东西，是 FreeMarker 在数值上添加的。 为了清晰子变量是哪部分，使用 `?`(问号)代替 `.`(点)来访问它们。常用内建函数的示例：

- `user?html` 给出 `user` 的HTML转义版本， 比如 `&` 会由 `&` 来代替。
- `user?upper_case` 给出 `user` 值的大写版本 (比如 "JOHN DOE" 来替代 "John Doe")
- `animal.name?cap_first` 给出 `animal.name` 的首字母大写版本(比如 "Mouse" 来替代 "mouse")
- `user?length` 给出 `user` 值中 *字符*的数量(对于 "John Doe" 来说就是8)
- `animals?size` 给出 `animals` 序列中 *项目* 的个数(我们示例数据模型中是3个)
- 如果在 `<#list animals as animal>` 和对应的 `</#list>` 标签中：
  - `animal?index` 给出了在 `animals` 中基于0开始的 `animal`的索引值
  - `animal?counter` 也像 `index`， 但是给出的是基于1的索引值
  - `animal?item_parity` 基于当前计数的奇偶性，给出字符串 "odd" 或 "even"。在给不同行着色时非常有用，比如在 `<td class="${animal?item_parity}Row">`中。

一些内建函数需要参数来指定行为，比如：

- `animal.protected?string("Y", "N")` 基于 `animal.protected` 的布尔值来返回字符串 "Y" 或 "N"。
- `animal?item_cycle('lightRow','darkRow')` 是之前介绍的 `item_parity` 更为常用的变体形式。
- `fruits?join(", ")` 通过连接所有项，将列表转换为字符串， 在每个项之间插入参数分隔符(比如 "orange,banana")
- `user?starts_with("J")` 根据 `user` 的首字母是否是 "J" 返回布尔值true或false。

内建函数应用可以链式操作，比如`user?upper_case?html` 会先转换用户名到大写形式，之后再进行HTML转义。(这就像可以链式使用 `.`(点)一样)

可以阅读 [全部内建函数参考](http://freemarker.foofun.cn/ref_builtins.html)。

### 8) 处理不存在的变量

数据模型中经常会有可选的变量(也就是说有时并不存在)。 除了一些典型的人为原因导致失误外，FreeMarker 绝不能容忍引用不存在的变量， 除非明确地告诉它当变量不存在时如何处理。这里来介绍两种典型的处理方法。

*这部分对程序员而言: 一个不存在的变量和一个是 `null` 值的变量， 对于FreeMarker来说是一样的，所以这里所指的"丢失"包含这两种情况。*

不论在哪里引用变量，都可以指定一个默认值来避免变量丢失这种情况， 通过在变量名后面跟着一个 `!`(叹号，译者注)和默认值。 就像下面的这个例子，当 `user` 不存在于数据模型时, 模板将会将 `user` 的值表示为字符串 `"visitor"`。(当 `user` 存在时， 模板就会表现出 `${user}` 的值)：

```
<h1>Welcome ${user!"visitor"}!</h1>
```

也可以在变量名后面通过放置 `??` 来询问一个变量是否存在。将它和 `if` 指令合并， 那么如果 `user` 变量不存在的话将会忽略整个问候的代码段：

```
<#if user??><h1>Welcome ${user}!</h1></#if>
```

关于多级访问的变量，比如 `animals.python.price`， 书写代码：`animals.python.price!0` 当且仅当 `animals.python` 永远存在， 而仅仅最后一个子变量 `price` 可能不存在时是正确的 (这种情况下我们假设价格是 `0`)。 如果 `animals` 或 `python` 不存在， 那么模板处理过程将会以"未定义的变量"错误而停止。为了防止这种情况的发生， 可以如下这样来编写代码 `(animals.python.price)!0`。 这种情况就是说 `animals` 或 `python` 不存在时， 表达式的结果是 `0`。对于 `??` 也是同样用来的处理这种逻辑的; 将 `animals.python.price??` 对比 `(animals.python.price)??`来看。

-----

## 3.快速浏览(备忘单)

这里给已经了解 FreeMarker 的人或有经验的程序员的提个醒：

- 直接指定值

  - 字符串： `"Foo"` 或者 `'Foo'` 或者 `"It's \"quoted\""` 或者 `'It\'s "quoted"'` 或者 `r"C:\raw\string"`
  - 数字： `123.45`
  - 布尔值： `true`， `false`
  - 序列： `["foo", "bar", 123.45]`； 值域： `0..9`, `0..<10` (或 `0..!10`), `0..`
  - 哈希表： `{"name":"green mouse", "price":150}`

- 检索变量

  - 顶层变量： `user`
  - 从哈希表中检索数据： `user.name`， `user["name"]`
  - 从序列中检索数据： `products[5]`
  - 特殊变量： `.main`

- 字符串操作

  - 插值(或连接)： `"Hello ${user}!"` (或 `"Hello " + user + "!"`)
  - 获取一个字符： `name[0]`
  - 字符串切分： 包含结尾： `name[0..4]`，不包含结尾： `name[0..<5]`，基于长度(宽容处理)： `name[0..*5]`，去除开头： `name[5..]`

- 序列操作

  - 连接： `users + ["guest"]`
  - 序列切分：包含结尾： `products[20..29]`， 不包含结尾： `products[20..<30]`，基于长度(宽容处理)： `products[20..*10]`，去除开头： `products[20..]`

- 哈希表操作

  - 连接： `passwords + { "joe": "secret42" }`

- 算术运算： `(x * 1.5 + 10) / 2 - y % 100`

- 比较运算： `x == y`， `x != y`， `x < y`， `x > y`， `x >= y`， `x <= y`， `x lt y`， `x lte y`， `x gt y`， `x gte y`， 等等。。。。。。

- 逻辑操作： `!registered && (firstVisit || fromEurope)`

- 内建函数： `name?upper_case`, `path?ensure_starts_with('/')`

- 方法调用： `repeat("What", 3)`

- 处理不存在的值

  ：

  - 默认值： `name!"unknown"` 或者 `(user.name)!"unknown"` 或者 `name!` 或者 `(user.name)!`
  - 检测不存在的值： `name??` 或者 `(user.name)??`

- 赋值操作： `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `++`, `--`

请参考： [运算符优先级](http://freemarker.foofun.cn/dgui_template_exp.html#dgui_template_exp_precedence)

------

## 4.转义

| 转义序列 | 含义                            |
| :------- | :------------------------------ |
| `\"`     | 引号 (u0022)                    |
| `\'`     | 单引号(又称为撇号) (u0027)      |
| `\{`     | 起始花括号：`{`                 |
| `\\`     | 反斜杠 (u005C)                  |
| `\n`     | 换行符 (u000A)                  |
| `\r`     | 回车 (u000D)                    |
| `\t`     | 水平制表符(又称为tab) (u0009)   |
| `\b`     | 退格 (u0008)                    |
| `\f`     | 换页 (u000C)                    |
| `\l`     | 小于号：`<`                     |
| `\g`     | 大于号：`>`                     |
| `\a`     | &符：`&`                        |
| `\xCode` | 字符的16进制 Unicode 码 (UCS码) |

在 `\x` 之后的 `*Code*` 是1-4位的16进制码。下面这个示例中都是在字符串中放置版权符号： `"\xA9 1999-2001"`， `"\x0A9 1999-2001"`， `"\x00A9 1999-2001"`。 如果紧跟16进制码后一位的字符也能解释成16进制码时， 就必须把4位补全，否则FreeMarker就会误解你的意图。

原生字符串是一种特殊的字符串。在原生字符串中， 反斜杠和 `${` 没有特殊含义， 它们被视为普通的字符。为了表明字符串是原生字符串， 在开始的引号或单引号之前放置字母`r`，例如：

```
TEMPLATE
${r"${foo}"}
${r"C:\foo\bar"}
```

将会输出：

```
OUTPUT
${foo}
C:\foo\bar
```

## 5.值域

值域也是序列，但它们由指定包含的数字范围所创建， 而不需指定序列中每一项。比如： `0..<m`，这里假定 `m` 变量的值是5，那么这个序列就包含 `[0, 1, 2, 3, 4]`。值域的主要作用有：使用 `<#list...>` 来迭代一定范围内的数字，序列切分和 字符串切分。

值域表达式的通用形式是( `start` 和 `end` 可以是任意的结果为数字表达式)：

- `start..end`： 包含结尾的值域。比如 `1..4` 就是 `[1, 2, 3, 4]`， 而 `4..1` 就是 `[4, 3, 2, 1]`。当心一点， 包含结尾的值域不会是一个空序列，所以 `0..length-1` 就是 *错误的*，因为当长度是 `0` 时， 序列就成了 `[0, -1]`。

- `start..<end` 或 `start..!end`： 不包含结尾的值域。比如 `1..<4` 就是 `[1, 2, 3]`，`4..<1` 就是 `[4, 3, 2]`, 而 `1..<1` 表示 `[]`。请注意最后一个示例； 结果可以是空序列，和 `..<` 和 `..!` 没有区别; 最后这种形式在应用程序中使用了 `<` 字符而引发问题(如HTML编辑器等)。

- `start..length`： 限定长度的值域，比如 `10..4` 就是 `[10, 11, 12, 13]`，`10..-4` 就是 `[10, 9, 8, 7]`，而 `10..0` 表示 `[]`。当这些值域被用来切分时， 如果切分后的序列或者字符串结尾在指定值域长度之前，则切分不会有问题；请参考 [序列切分](http://freemarker.foofun.cn/dgui_template_exp.html#dgui_template_exp_seqenceop_slice) 来获取更多信息。

- `start..`： 无右边界值域。这和限制长度的值域很像，只是长度是无限的。 比如 `1..` 就是 `[1, 2, 3, 4, 5, 6, ... ]`，直到无穷大。 但是处理(比如列表显示)这种值域时要万分小心，处理所有项时， 会花费很长时间，直到内存溢出应用程序崩溃。 和限定长度的值域一样，当它们被切分时， 遇到切分后的序列或字符串结尾时，切分就结束了。

  **Warning!**

  无右边界值域在 FreeMarker 2.3.21 版本以前只能用于切分， 若用于其它用途，它就像空序列一样了。要使用新的特性， 使用 FreeMarker 2.3.21 版本是不够的，程序员要设置 `incompatible_improvements` 至少到2.3.21版本。

值域的进一步注意事项：

- 值域表达式本身并没有方括号，比如这样编写代码 `<#assign myRange = 0..<x>`， 而不是 `<#assign myRange = [0..<x]>`。 后者会创建一个包含值域的序列。方括号是切分语法的一部分，就像 `*seq*[*myRange*]`。
- 可以在 `..` 的两侧编写算术表达式而不需要圆括号， 就像 `n + 1 ..< m / 2 - 1`。
- `..`，`..<`， `..!` 和 `..*` 是运算符， 所以它们中间不能有空格。就像 `n .. <m` 这样是错误的，但是 `n ..< m` 这样就可以。
- 无右边界值域的定义大小是2147483647 (如果 `incompatible_improvements` 低于2.3.21版本，那么就是0)， 这是由于技术上的限制(32位)。但当列表显示它们的时候，实际的长度是无穷大。
- 值域并不存储它们包含的数字，那么对于 `0..1` 和 `0..100000000` 来说，创建速度都是一样的， 并且占用的内存也是一样的。

