---
title: avalon.js入门教程
date: 2021-07-13 08:53:29
tags: [前端框架,JavaWeb,avalon]
categories: 前端
---

## 关于AvalonJs

avalon是一个简单易用迷你的MVVM框架，它最早发布于2012.09.15，为解决同一业务逻辑存在各种视图呈现而开发出来的。 事实上，这问题其实也可以简单地利用一般的前端模板加jQuery 事件委托 搞定，但随着业务的膨胀， 代码就充满了各种选择器与事件回调，难以维护。因此彻底的将业务与逻辑分离，就只能求助于架构。 最初想到的是MVC，尝试过backbone，但代码不降反升，很偶尔的机会，碰上微软的WPF， 优雅的MVVM架构立即吸引住了作者，作者觉得这就是他一直追求的解决之道。

MVVM将所有前端代码彻底分成两部分，视图的处理通过绑定实现（angular有个更炫酷的名词叫指令）， 业务逻辑则集中在一个个叫VM的对象中处理。我们只要操作VM的数据，它就自然而然地神奇地同步到视图。

我们从一个完整的例子开始认识 avalon ：

```html
<!DOCTYPE html>
<html>
    <head>
        <title></title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <script src="avalon.js"></script>
    </head>
    <body>
        <div ms-controller="box">
            <div style=" background: #a9ea00;" ms-css-width="w" ms-css-height="h"  ms-click="click"></div>
            <p>{{ w }} x {{ h }}</p>
            <p>W: <input type="text" ms-duplex="w" data-duplex-event="change"/></p>
            <p>H: <input type="text" ms-duplex="h" /></p>
        </div>
        <script>
              var vm = avalon.define({
                 $id: "box",//告诉avalon这个Model是作用于哪个ms-controller的
                  w: 100,
                  h: 100,
                  click: function() {
                    vm.w = parseFloat(vm.w) + 10;
                    vm.h = parseFloat(vm.h) + 10;
                  }
              })
        </script>
    </body>
</html>
```

上面的代码中，我们可以看到在JS中，没有任何一行操作DOM的代码，也没有选择器，非常干净。在HTML中， 我们发现就是多了一些以ms-开始的属性与`{% raw %} {{ }} {% endraw %}`标记，有的是用于渲染样式， 有的是用于绑定事件。这些属性或标记，实质就是avalon的**绑定系统**的一部分。绑定（有的框架也将之称为指令）， 负责帮我们完成视图的各种操作，相当于一个隐形的**jQuery**。正因为有了绑定，我们就可以在JS代码专注业务逻辑本身， 写得更易维护的代码！

------

## 视图模型

视图模型，ViewModel，也经常被略写成VM，是通过avalon.define方法进行定义。生成的对象会默认放到avalon.vmodels对象上。 每个VM在定义时必须指定$id。如果你有某些属性不想监听，可以直接将此属性名放到$skipArray数组中。

接着我们说一些重要的概念：

- $id， 每个VM都有$id，VM的ID，方便在avalon.vmodels里查找到它，或用在ms-controller、ms-important上。如果VM的某一个属性是对象（并且它是可监控的），也会转换为一个VM，这个子VM也会默认加上一个$id。 但只有用户添加的那个最外面的$id会注册到avalon.vmodels对象上。
- 监控属性，一般地，VM中的属性都会转换为此种属性，当我们以vm.aaa = yyy这种形式更改其值时，就会同步到视图上的对应位置上。
- 计算属性，定义时为一个对象，并且只存在set,get两个函数或只有一个get一个函数。它是监控属性的高级形式，表示它的值是通过函数计算出来的，是依赖于其他属性合成出来的。
- 监控数组，定义时为一个数组，它会添加了许多新方法，但一般情况下与普通数组无异，但调用它的push, unshift, remove, pop等方法会同步视图。
- 非监控属性，这包括框架添加的$id属性，以$开头的属性，放在$skipArray数组中的属性，值为函数、元素节点、文本节点的属性，总之，改变它们的值不会产生同步视图的效果。

$skipArray 是一个字符串数组，只能放当前对象的直接属性名，想禁止子对象的某个属性的监听，在那个子对象上再添加一个$skipAray数组就行了。

视图里面，我们可以使用ms-controller, ms-important指定一个VM的作用域。

此外，在ms-each, ms-with，ms-repeat绑定属性中，它们会创建一个临时的VM，我们称之为代理VM， 用于放置$key, $val, $index, $last, $first, $remove等变量或方法。

另外，avalon不允许在VM定义之后，再追加新属性与方法，比如下面的方式是错误的：

```js
var vm = avalon.define({
    $id:   "test", 
    test1: "点击测试按钮没反应 绑定失败";
});
vm.one = function() {
    vm.test1 = "绑定成功";
};
```

也不允许在define里面直接调用方法或ajax。

**我们再看看如何更新VM中的属性(重点)：**

```js
<script>
var model : avalon.define({
     $id:  "update", 
     aaa : "str",
     bbb : false,
     ccc : 1223,
     time : new Date,
     simpleArray : [1, 2, 3, 4],
     objectArray : [{name: "a"}, {name: "b"}, {name: "c"}, {name: "d"}],
     object : {
         o1: "k1",
         o2: "k2",
         o3: "k3"
     },
     simpleArray : [1, 2, 3, 4],
     objectArray : [{name: "a", value: "aa"}, {name: "b", value: "bb"}, {name: "c", value: "cc"}, {name: "d", value: "dd"}],
     object : {
         o1: "k1",
         o2: "k2",
         o3: "k3"
     }
 })
 
       setTimeout(function() {
           //如果是更新简单数据类型（string, boolean, number）或Date类型
           model.aaa = "这是字符串"
           model.bbb = true
           model.ccc = 999999999999
           var date = new Date
           model.time = new Date(date.setFullYear(2005))
       }, 2000)
 
       setTimeout(function() {
           //如果是数组，注意保证它们的元素的类型是一致的
           //只能全是字符串，或是全是布尔，不能有一些是这种类型，另一些是其他类型
           //这时我们可以使用set方法来更新（它有两个参数，第一个是index，第2个是新值）
           model.simpleArray.set(0, 1000)
           model.simpleArray.set(2, 3000)
           model.objectArray.set(0, {name: "xxxxxxxxxxxxxxxx", value: "xxx"})
       }, 2500)
       setTimeout(function() {
           model.objectArray[1].name = "5555"
       }, 3000)
       setTimeout(function() {
           //如果要更新对象，直接赋给它一个对象，注意不能将一个VM赋给它，可以到VM的$model赋给它（要不会在IE6-8中报错）
           model.object = {
               aaaa: "aaaa",
               bbbb: "bbbb",
               cccc: "cccc",
               dddd: "dddd"
           }
       }, 3000)
   </script>
```

```html
 <div ms-controller="update">
   <div>{{aaa}}</div>
   <div>{{bbb}}</div>
   <div>{{ccc}}</div>
   <div>{{time | date("yyyy - MM - dd mm:ss")}}</div>
   <ul ms-each="simpleArray">
       <li>{{el}}</li>
   </ul>
   <div>  <select ms-each="objectArray">
           <option ms-value="el.value">{{el.name}}</option>
       </select>
   </div>
   <ol ms-with="object">
       <li>{{$key}}                {{$val}}</li>
   </ol>
</div>
```

----------

## 绑定

avalon的绑定（或指令），拥有以下三种类型：

- `{% raw %} {{ }} {% endraw %}`插值表达式， 这是开标签与闭标签间，换言之，也是位于文本节点中，innerText里。`{% raw %} {{ }} {% endraw %}`里面可以添加各种过滤器（以|进行标识）。值得注意的是`{% raw %} {{ }} {% endraw %}`实际是文本绑定(ms-text)的一种形式。
- ms-*绑定属性， 这是位于开标签的内部， 95%的绑定都以这种形式存在。 它们的格式大概是这样划分的*"ms" + type + "-" + param1 + "-" + param1 + "-" + param2 + ... + number = value

```
ms-skip                //这个绑定属性没有值
ms-controller="expr"   //这个绑定属性没有参数
ms-if="expr"           //这个绑定属性没有参数
ms-if-loop="expr"       //这个绑定属性有一个参数
ms-repeat-el="array"    //这个绑定属性有一个参数
ms-attr-href="xxxx"    //这个绑定属性有一个参数
ms-attr-src="xxx/{{a}}/yyy/{{b}}"   //这个绑定属性的值包含插值表达式，注意只有少部分表示字符串类型的属性可以使用插值表达式
ms-click-1="fn"       //这个绑定属性的名字最后有数字，这是方便我们绑定更多点击事件 ms-click-2="fn"  ms-click-3="fn"  
ms-on-click="fn"     //只有表示事件与类名的绑定属性的可以加数字，如这个也可以写成  ms-on-click-0="fn"    
ms-class-1="xxx" ms-class-2="yyy" ms-class-3="xxx" //数字还表示绑定的次序
ms-css-background-color="xxx" //这个绑定属性有两个参数，但在css绑定里，相当于一个，会内部转换为backgroundColor 
ms-duplex-aaa-bbb-string="xxx"//这个绑定属性有三个参数，表示三种不同的拦截操作
```

- data-xxx-yyy="xxx"，辅助指令，比如ms-duplex的某一个辅助指令为data-duplex-event="change"，ms-repeat的某一个辅助指令为data-repeat-rendered="yyy"

------

## 指令

avalon的指令是一个非常重要的东西,它用来引入一些新的HTML语法, 使元素拥有特定的行为。 举例来说，静态的HTML不知道如何来创建和展现一个日期选择器控件。 让HTML能识别这个语法，我们需要使用指令。 指令通过某种方法来创建一个能够支持日期选择的元素。

指令一共拥有3种形式

1. 插值表达式
2. 自定义标签
3. 绑定属性

其中`绑定属性`的种类是最多的，它们都位置于元素节点中，以ms-开头或以:开头(avalon2.1.7新增)

绑定属性的属性名是以-分成几段 其中第二个就是指令的名字, 如ms-css, ms-attr, ms-html, ms-text, ms-on都是来源于jQuery同名方法名, 简单好记.

```html
<p ms-on-click="@clickFn" ms-if="@toggle">{{@name}}</p>
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/avalon/avalon01.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/avalon/avalon02.jpg)

------

###  插值表达式

位于文本节点中的双重花括号,当然这个可以配置.此指令其中文本ms-text指令的简单形式.

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: 'aaa',
            bbb: 'bbb'
        })

    </script>
    <p>{{@aaa}}{{@bbb}} 这个性能差些</p>
    <p>{{@aaa+@bbb}} 这个性能好些</p>
    <p>{{@aaa+@bbb  | uppercase}} 选择器必须放在表达值的后端</p>
</body>
```

`{% raw %} {{ }} {% endraw %}`里面可以添加各种过滤器（以|进行标识）。值得注意的是`{% raw %} {{ }} {% endraw %}`实际是文本绑定(ms-text)的一种形式。

###  忽略扫描绑定(ms-skip)

这是ms-skip负责。只要元素定义了这个属性，无论它的值是什么，它都不会扫描其他属性及它的子孙节点了。

```html
<div ms-controller="test" ms-skip>
    <p
        ms-repeat-num="cd" 
        ms-attr-name="num"
        ms-data-xxx="$index">
        {{$index}} - {{num}}
    </p>
    A：<div ms-each="arr">{{yy}}</div>
</div>
```

### ms-controller

这个指令是用于圈定某个VM的作用域范围(换言之,这个元素的outerHTML会被扫描编译,所有ms-*及双花括号替换成vm中的内容),ms-controller的属性值只能是某个VM的$id

ms-controller的元素节点下面的其他节点也可以使用ms-controller

每个VM的$id可以在页面上出现一次, 因此不要在ms-for内使用ms-controller.

```html
<script>
  avalon.define({
      $id: "AAA",
      name: "liger",
      color: "green"　　
  });
  avalon.define({
      $id: "BBB",
      name: "sphinx",
      color: "red"　　
  });　　
  avalon.define({
      $id: "CCC",
      name: "dragon" //不存在color

  });
  avalon.define({
      $id: "DDD",
      name: "sirenia" //不存在color

  });　
</script>
<div ms-controller="AAA">
    <div>{{@name}} : {{@color}}</div>
    <div ms-controller="BBB">
        <div>{{@name}} : {{@color}}</div>
        <div ms-controller="CCC">
            <div>{{@name}} : {{@color}}</div>
        </div>
        <div ms-important="DDD">
            <div>{{@name}} : {{@color}}</div>
        </div>
    </div>
</div>
```

当avalon的扫描引擎打描到ms-controller/ms-important所在元素时, 会尝试移除ms-controller类名.因此基于此特性,我们可以在首页渲染页面时, 想挡住双花括号乱码问题,可以尝试这样干(与avalon1有点不一样):

```css
  .ms-controller{
     visibility: hidden;
  }
```

-------

### ms-important

这个指令是用于圈定某个VM的作用域范围(换言之,这个元素的outerHTML会被扫描编译,所有`ms-*`及双花括号替换成vm中的内容),ms-important的属性值只能是某个VM的$id

ms-important的元素节点下面的其他节点也可以使用[ms-controller](http://www.itprobie.com/jc/avalon/avalon_html/ms-controller.html)或ms-important

> 与ms-controller不一同的是,当某个属性在ms-important的VM找不到时,就不会所上寻找

 

> 不要在ms-for内使用ms-important.

ms-important这特性有利协作开发,每个人的VM都不会影响其他人,并能大大提高性能

ms-important只能用于ms-controller的元素里面

```html
  <div ms-important='aaa'>
      <div ms-controller='ccc'>
           <div ms-important='ddd'>

          </div>
          </div>
      <div ms-controller='bbb'>

      </div>
  </div>
```

-------

### ms-attr

属性绑定用于为元素节点添加一组属性, 因此要求属性值为对象或数组形式. 数组最后也会合并成一个对象.然后取此对象的键名为属性名, 键值为属性值为元素添加属性

如果键名如果为for, char这样的关键字,请务必在两边加上引号

如果键名如果带横杠,请务必转换为驼峰风格或两边加上引号

注意,不能在ms-attr中设置style属性

```html
<p ms-attr="{style:'width:20px'}">这样写是错的,需要用ms-css指令!!</p>
```

示例:

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            obj: {title: '普通 ', algin: 'left'},
            active: {title: '激活'},
            width: 111,
            height: 222,
            arr: [{img: 'aaa'}, {img: 'bbb'}, {img: 'ccc'}],
            path: '../aaa/image.jpg',
            toggle: false,
            array: [{width: 1}, {height: 2}]
        })

    </script>
    <span ms-attr="@obj">直接引用对象</span>
    <img ms-attr="{src: @path}" />
    <ul>
        <li ms-for="el in @arr"><a ms-attr="{href:'http://www.ccc.xxx/ddd/'+ el.img}">下载</a></li>
    </ul>
    <span :attr="{width: @width, height: @height}">使用对象字面量</span><br/>
    <span :attr="@array">直接引用数组</span><br/>
    <span :attr="[@obj, @toggle && @active ]" :click="@toggle = !@toggle">选择性添加多余属性或重写已有属性</span>
</body>
```

----

### ms-css

CSS绑定用于为元素节点添加一组样式, 因此要求属性值为对象或数组形式. 数组最后也会合并成一个对象.然后取此对象的键名为样式名, 键值为样式值为元素添加样式

> 如果键名为表示长宽,字体大小这样的样式, 那么键值不需要加单位,会自动加上px
>
> 如果键名如果为float,请务必在两边加上引号
>
> 如果键名如果为font-size,请务必转换为驼峰风格或两边加上引号

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            obj: {backgroundColor: '#3bb0d0',width:300, height:50, 'text-align': 'center'},//属性名带-,必须用引号括起
            active: {color: 'red'},
            width:  300,
            height: 60,
            toggle: true,
            array: [{width:100},{height:50},{border: '1px solid #5cb85c'}]
        })

    </script>
  <div ms-css="@obj">直接引用对象</div>
  <div :css="{width: @width, height: @height,background: 'pink'}">使用对象字面量</div>
  <div :css="@array">直接引用数组</div>
  <div :css="[@obj, @toggle && @active ]" :click="@toggle = !@toggle">选择性添加多余属性或重写已有属性</div>
</body>
```

需要注意的是 设置背景图片是比较复杂

```html
<span :css="{background: 'url('+@imageUrl + ') no-repeat center center;'}">图片</span>
<span :css="{backgroundImage: 'url('+@imageUrl + ')'}">图片</span>
```

----

### ms-text

文本绑定是最简单的绑定,它其实是双花括号插值表达式的一种形式

它要求VM对应的属性的类型为字符串, 数值及布尔, 如果是null, undefined将会被转换为空字符串

```html
<span ms-text="@aaa">不使用过滤器</span>
<span ms-text="@aaa | uppercase">使用过滤器</span>
```

-------

### ms-html

HTML绑定类似于文本绑定,能将一个元素清空,填上你需要的内容

它要求VM对应的属性的类型为字符串

```html
<span ms-html="@aaa">不使用过滤器</span>
<span ms-html="@aaa | uppercase">使用过滤器</span>
```

我们可以通过ms-html异步加载大片内容。

```html
<body :controller="test">
<script>
var vm = avalon.define({
  $id: "test",
  aaa: "loading..."
})
jQuery.ajax({
   url:'action.do',
   success: function(data){
      vm.aaa = data.html
   }
})
</script>
<div ms-html="@aaa"></div>
</body>
```

----

### ms-duplex

双工绑定是MVVM框架中最强大的指令.react推崇单向数据流,没有双工绑定, 那么需要rudex等额外的库来实现相同的功能.

双工绑定只要用于表单元素上.或当一个div设置了contenteditable为true,也可以用ms-duplex指令.

注意：ms-duplex与ms-checked不能在同时使用于一个元素节点上。

注意：如果表单元素同时绑定了ms-duplex=xxx与ms-click或ms-change，而事件回调要立即得到这个vm.xxx的值，input[type=radio]是存在问题，它不能立即得到当前值，而是之前的值，需要在回调里面加个setTimeout。

**各个表单元素的用法**

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: 'aaa',
            bbb: 'bbb',
            ccc: 'ccc'
        })

    </script>

    <input ms-duplex="@aaa"/>{{@aaa}}
    <input ms-duplex="@bbb" type="password"/>{{@bbb}}
    <textarea ms-duplex="@ccc" /></textarea>{{@ccc}}
</body>
```

上面有三个控件,text, password, textarea它们都是属于输入型控件, 只要每为控件敲入一个字符, 后面的文本都会立即变化.那是因为它们默认是绑定**oninput**事件,如果想控件全部输入好,失去焦点时 才同步,那么可以使用`change`过滤器

```html
<input ms-duplex="@aaa | change"/>{{@aaa}}
```

如果你是做智能提示, 控件是绑定了一个AJAX请求与后端不断交互, 使用oninput事件会太频繁, 使用onchange事件会太迟钝,那么我们可以使用`debounce`过滤器

```html
<input ms-duplex="@aaa | debounce(300)"/>{{@aaa}}
```

300ms同步一次.

另外,可编辑元素的用法与过滤器与上面三种控件一样.

```html
<div contenteditable="true" ms-duplex="@aaa | debounce(300)"/></div>
<p>{{@aaa}}</p>
```

> 这两个过滤器只能适用于上面的情况.

此外, 控件还有许多种, 像checkbox, radio,它们的同步机制也不一样.

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: '33',
            bbb: ['22']
        })

    </script>

    <input type="radio" value="11"  ms-duplex="@aaa"/>
    <input type="radio" value="22"  ms-duplex="@aaa"/>
    <input type="radio" value="33"  ms-duplex="@aaa"/>
    <input type="checkbox" value="11"  ms-duplex="@bbb"/>
    <input type="checkbox" value="22"  ms-duplex="@bbb"/>
    <input type="checkbox" value="33"  ms-duplex="@bbb"/>
    <p>radio: {{@aaa}}; checkbox:{{@bbb}}</p>
</body>
```

checkbox与radio是一点击就会更新.radio要求在vm中为一个简单数据类型数据,字符串,数字或布尔. 而checkbox则要求是一个数组.并且在最开始时,ms-duplex会令radio钩上其value值等vm属性的控件, checkbox则可以勾选多个.如此一来,vm中的属性些总是等于radio与checkbox的属性值.但我们也可以让 vm的属性值等于此控件的勾选状态,这时需要用上`ms-duplex-checked`转换器.

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: false,
            bbb: false
        })

    </script>
    <input type="radio"  ms-duplex-checked="@aaa"/>
    <input type="checkbox"  ms-duplex-checked="@bbb"/>
    <p>radio: {{@aaa}}; checkbox:{{@bbb}}</p>
</body>
```

最后表单元素还有select控件,它根据其multiple属性分为单选下拉框与复选下拉框, 其在vm中的值与radio,checkbox一样.即单选时,必须是一个简单数据类型, 复选时为一个数组. 在最开始时, 当option元素的value值或innerText(不在value值)与数据相同,它们就会被选上.

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: 'bbb'
            bbb: ['bbb','ccc'],
        })

    </script>
    <select :duplex="@aaa"><option>aaa</option><option>bbb</option><option>ccc</option></select>
    <select multiple="true" :duplex="@bbb"><option>aaa</option><option>bbb</option><option>ccc</option></select>
</body>
```

| 控件                               |          触发时机          |           数据 |
| :--------------------------------- | :------------------------: | -------------: |
| text,password,textarea及可编辑元表 | oninput,onchange, debounce |       简单数据 |
| radio,checkbox                     |          onclick           | 简单数据或数组 |
| select                             |          onchange          | 简单数据或数组 |

**数据转换**

上面我们已经提到一个数据转换器ms-duplex-checked了.那只能用于checkbox与radio.

为什么会有这种东西呢?因为无论我们原来的数据类型是什么,跑到表单中都会变成字符串,然后我们通过事件取出来 它们也是字符串,不会主动变回`原来的类型`.我们需要一种机制保持数据原来的类型,这就是数据转换器.

avalon内置了4种过滤器

1. ms-duplex-string="@aaa"
2. ms-duplex-number="@aaa"
3. ms-duplex-boolean="@aaa"
4. ms-duplex-checked="@aaa"

前三个是将元素的value值转换成string, number, boolean（只有为'false'时转换为false）

最后是根据当前元素（它只能是radio或checkbox）的checked属性值转换为vm对应属性的值。

它们都是放在属性名上。当数据从元素节点往vmodel同步时，转换成预期的数据。

```html
<input value="11"  ms-duplex-number="@aaa"/>
```

**数据格式化**

一般来说,数据格式化是由过滤器实现的,如

```html
<input value="11"  ms-duplex="@aaa | uppercase"/>
```

但这里有一个隐患,可能导致死循环, 因此建议放在事件回调中实现.

```html
<body ms-controller="test">
    <script>
    var vm = avalon.define({
        $id: 'test',
        aaa: '111',
        bbb: '222',
        format1: function(e){//只能输入数字
           vm.aaa = e.target.value.replace(/\D/g,'')
        },
        format1: function(e){//只能输入数字
           vm.bbb = avalon.filter.date(e.target.value, 'yyyy-MM-dd')
        }
    })

    </script>

    <input :duplex="@aaa" :on-input="@format1"/>{{@aaa}}
    <input :duplex="@bbb" :on-change="@format2"/>{{@bbb}}
</body>
```

数据格式化是放在属性值时，以过滤器形式存在，如

```javascript
ms-duplex='@aaa | uppercase'
ms-duplex='@aaa | date('yyyy:MM:dd')'
```

**数据验证**

这必须在所有表单元素的上方form元素加上ms-validate指令, 当前元素加上ms-rules才会生效

```html
<form ms-validate="@validation">
<input ms-duplex='@aaa' 
       ms-rules='require,email,maxlength' 
       data-maxlength='4' 
       data-maxlength-message='太长了' >
</form>
```

详见[ms-rules](http://www.itprobie.com/jc/avalon/avalon_html/ms-rules.html)指令

**同步后的回调**

ms-duplex还有一个回调，data-duplex-changed，用于与事件绑定一样， 默认第一个参数为事件对象。如果传入多个参数，那么使用$event为事件对象占位。

```html
<input value="11"  ms-duplex-number="@aaa" data-duplex-changed="@fn"/>
```

**示例**

现在我们来一些实际的例子!

**全选与非全选**

```javascript
var vm = avalon.define({
    $id: "duplex1",
    data: [{checked: false}, {checked: false}, {checked: false}],
    allchecked: false,
    checkAll: function (e) {
        var checked = e.target.checked
        vm.data.forEach(function (el) {
            el.checked = checked
        })
    },
    checkOne: function (e) {
        var checked = e.target.checked
        if (checked === false) {
            vm.allchecked = false
        } else {//avalon已经为数组添加了ecma262v5的一些新方法
            vm.allchecked = vm.data.every(function (el) {
                return el.checked
            })
        }
    }
})

<table ms-controller=" duplex1" border="1">
    <tr>
        <td><input type="checkbox" 
                   ms-duplex-checked="@allchecked" 
                   data-duplex-changed="@checkAll"/>全选</td>
    </tr>
    <tr ms-for="($index, el) in @data">
        <td><input type="checkbox" ms-duplex-checked="el.checked" data-duplex-changed="@checkOne" />{{$index}}::{{el.checked}}</td>
    </tr>
</table>
```

我们仔细分析其源码，allchecked是用来控制最上面的复选框的打勾情况， 数组中的checked是用来控制下面每个复选框的下勾情况。由于是使用ms-duplex，因此会监听用户行为， 当复选框的状态发生改变时，就会触发data-duplex-changed回调，将当前值传给回调。 但这里我们不需要用它的value值，只用它的checked值。

最上面的复选框对应的回调是checkAll，它是用来更新数组的每个元素的checked属性，因此一个forEach循环赋值就是。

下面的复选框对应的checkOne，它们是用来同步最上面的复选框，只要它们有一个为false上面的复选框就不能打勾， 当它们被打勾了，它们就得循环整个数组，检查是否所有元素都为true，是才给上面的checkall属性置为true。

现在我们学了循环指令，结合它来做一个表格看看。现在有了强大无比的orderBy, limitBy, filterBy, selectBy。 我们做高性能的大表格是得心应手的！

```javascript
 if (!Date.now) {//fix 旧式IE
    Date.now = function() {
        return new Date - 0;
    }
}
avalon.define({
    $id: "duplex2",
    selected: "name",
    options: ["name", "size", "date"],
    trend: 1,
    data: [
        {name: "aaa", size: 213, date: Date.now() + 20},
        {name: "bbb", size: 4576, date:Date.now() - 4},
        {name: "ccc", size: 563, date: Date.now() - 7},
        {name: "eee", size: 3713, date: Date.now() + 9},
        {name: "555", size: 389, date: Date.now() - 20}
    ]
})
<div ms-controller=" duplex2">
<div style="color:red">
    <p>本例子用于显示如何做一个简单的表格排序</p>
</div>
<p>
    <select ms-duplex="@selected">
        <option  ms-for="el in @options">{{el}}</option>
    </select>
    <select ms-duplex-number="@trend">
        <option value="1">up</option>
        <option value="-1">down</option>
    </select>
</p>
<table width="500px" border="1">
    <tbody >
        <tr ms-for="el in @data | orderBy(@selected, @trend)">
            <td>{{el.name}}</td> <td>{{el.size}}</td> <td>{{el.date}}</td>
        </tr>
    </tbody>
</table>
</div>
```

我们再来一个文本域与下拉框的联动例子，它只用到ms-duplex，不过两个控件都是绑定同一个属性。

```html
avalon.define({
    $id: "fruit",
    options: ["苹果", "香蕉", "桃子", "雪梨", "葡萄",
        "哈蜜瓜", "橙子", "火龙果", "荔技", "黄皮"],
    selected: "桃子"
})
<div ms-controller=" fruit">
    <h3>文本域与下拉框的联动</h3>
    <input  ms-duplex="@selected" />
    <select ms-duplex="@selected" >
        <option ms-for="el in @options" ms-attr="{value: el}" >
        {{el}}
        </option>
    </select>
</div>
```

**下拉框三级联动**

```javascript
var map = {
    "中国": ["江南四大才子", "初唐四杰", "战国四君子"],
    "日本": ["日本武将", "日本城堡", "幕府时代"],
    "欧美": ["三大骑士团", "三大魔幻小说", "七大奇迹"],
    "江南四大才子": ["祝枝山", "文征明", "唐伯虎", "周文宾"],
    "初唐四杰": ["王勃", "杨炯", "卢照邻", "骆宾王"],
    "战国四君子": ["楚国春申君黄歇", "齐国孟尝君田文", "赵国平原君赵胜", "魏国信陵君魏无忌"],
    "日本武将": ["织田信长", "德川家康", "丰臣秀吉"],
    "日本城堡": ["安土城", "熊本城", "大坂城", "姬路城"],
    "幕府时代": ["镰仓", "室町", "丰臣", "江户"],
    "三大骑士团": ["圣殿骑士团", "医院骑士团", "条顿骑士团"],
    "三大魔幻小说": ["冰与火之歌", "时光之轮", "荆刺与白骨之王国"],
    "七大奇迹": ["埃及胡夫金字塔", "奥林匹亚宙斯巨像", "阿尔忒弥斯月神殿", "摩索拉斯陵墓", "亚历山大港灯塔", "巴比伦空中花园", "罗德岛太阳神巨像"]
}
var vm = avalon.define({
    $id: 'linkage',
    first: ["中国", "日本", "欧美"],
    second: map['日本'].concat(),
    third: map['日本武将'].concat(),
    firstSelected: "日本",
    secondSelected: "日本武将",
    thirdSelected: "织田信长"
})


vm.$watch("firstSelected", function (a) {
    vm.second = map[a].concat()
    vm.secondSelected = vm.second[0]
})
vm.$watch("secondSelected", function (a) {
    vm.third = map[a].concat()
    vm.thirdSelected = vm.third[0]
})
<div ms-controller=" linkage">
<h3>下拉框三级联动</h3>
<select ms-duplex="@firstSelected" >
    <option  ms-for="el in @first" ms-attr="{value:el}" >{{el}}</option>
</select>
<select ms-duplex="@secondSelected" >
    <option  ms-for="el in @second" ms-attr="{value:el}" >{{el}}</option>
</select>
<select ms-duplex="@thirdSelected" >
    <option  ms-for="el in @third" ms-attr="{value:el}" >{{el}}</option>
</select>
</div>
```

这里的技巧在于使用$watch回调来同步下一级的数组与选中项。注意，使用concat方法来复制数组。

-----

### ms-for

avalon2.0的ms-for绑定集齐了ms-repeat, ms-each, ms-with的所有功能, 并且更好用, 性能提升七八倍

ms-for可以同时循环对象与数组

```html
<ul>
    <li ms-for="el in @aaa">{{el}}</li>
</ul>
```

现在采用类似angular的语法, in前面如果只有一个变量,那么它就是数组元素或对象的属性名

```javascript
vm.aaa = ['aaa','bbb','ccc']
vm.bbb = {a: 1, b: 2, c: 3}
<ul>
    <li ms-for="(aaa, el) in @aaa">{{aaa}}-{{el}}</li>
</ul
<ul>
    <li ms-for="(k, v) in @bbb">{{k}}-{{v}}</li>
</ul>
```

依次输出的LI元素内容为0-aaa,1-bbb,2-ccc, a-1,b-2,c-3

in 前面有两个变量, 它们需要放在小括号里,以逗号隔开, 那么分别代表数组有索引值与元素, 或对象的键名与键值, 这个与jQuery或avalon的each方法的回调参数一致。

> 小括号里面的变量是随便起的,主要能符合JS变量命名规范就行,当然,也不要与window, this这样变量冲突.

```html
 <li ms-for="($index, el) in @arr">{{$index}}-{{el}}</li>
 <li ms-for="($key, $val) in @obj">{{$key}}-{{$val}}</li>
```

写成这样,就与avalon1.*很相像了

ms-for还可以配套**data-for-rendered**回调,当列表渲染好时执行此方法

```html
<ul>
   <li ms-for="el in @arr" data-for-rendered='@fn'>{{el}}</li>
<ul>
```

fn为vm中的一个函数，用法与`ms-on-*`差不多，如果不传参，默认第一个参数为事件对象，类型type为`rendered`， target为当前循环区域的父节点，这里为｀ul`元素。并且回调中的this指向vm。

```javascript
{type: 'rendered', target: ULElement }
```

你也可以在回调里面传入其他东西，使用`$event`代表事件对象

```html
<ul>
   <li ms-for="el in @arr" data-for-rendered="@fn('xxx',$event)">{{el}}</li>
<ul>
```

如果你想截取数组的一部分出来单独循环,可以用limitBy过滤器, 使用as来引用新数组

```html
<ul>
    <li ms-for="el in @aaa | limitBy(10) as items">{{el}}</li>
</ul>
```

上例是显示数组的前10个元素, 并且将这10个元素存放在items数组中, 以保存过滤或排序结果

使用注释节点实现循环,解决同时循环多个元素的问题

```html
<!DOCTYPE html>
<html>
    <head>
        <title>TODO supply a title</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <script src="../dist/avalon.js"></script>
        <script>
            vm = avalon.define({
                $id: 'for4',
                arr: [1, 2, 3, 4]
            })
        </script>
    </head>
    <body>
        <div ms-controller='for4'  >
            <!--ms-for: el in @arr-->
            <p>{{el}}</p>
            <p>{{el}}</p>
            <!--ms-for-end:-->
        </div>
    </body>
</html>
```

avalon 不需要像angular那样要求用户指定trace by或像react 那样使用key属性来提高性能,内部帮你搞定一切

如果你只想循环输出数组的其中一部分,请使用filterBy,只想循环输出对象某一些键值并设置默认值,则用selectBy. 不要在同一个元素上使用ms-for与ms-if,因为这样做会在页面上生成大量的注释节点,影响页面性能

可用于ms-for中的过滤器有limitBy, filterBy, selectby, orderBy

> ms-for支持下面的元素节点继续使用ms-for,形成双重循环与多级循环, 但要求双重循环对应的二维数组.几维循环对应几维数组

```javascript
vm.array = [{arr: [111,222, 333]},{arr: [111,222, 333]},{arr: [111,222, 333]}]
<p>array的元素里面有子数组,形成2维数组</p>        
<ul>
    <li ms-for="el in @array"><div ms-for='elem in el.arr'>{{elem}}</div></li>
</ul>
```

**如何双向绑定ms-for中生成的变量?**

由于 循环生成的变量前面不带@, 因此就找不到其对应的属性,需要特别处理一下

```html
<div ms-controller="test">
<div ms-for="(key,el) in @styles">
        <label>{{ key }}::{{ el }}</label>
        <input type="text" ms-duplex="@styles[key]" >
        <!--不能ms-duplex="el"-->
    </div>
</div>

<script type="text/javascript">
    var root = avalon.define({
        $id: "test",
        styles: {
            width: 200,
            height: 200,
            borderWidth: 1,
            borderColor: "red",
            borderStyle: "solid",
            backgroundColor: "gray"
        }
    })
</script>
```

-----

### ms-class

**类名绑定**

属性绑定用于为元素节点添加几个类名, 因此要求属性值为字符串或字符串数组.

字符串形式下,可以使用空格隔开多个类名

字符串数组形下, 可以在里面使用三元运算符或与或号

```html
<body ms-controller="test">
    <script>
        avalon.define({
            $id: 'test',
            aaa: "aaa bbb ccc",
            bbb: 'ddd',
            ccc: ['xxx', 'yyy', 'zzz'],
            ddd: 'eee',
            toggle: true,
            toggle2: false
        })

    </script>

    <span :class="@aaa">直接引用字符串</span>
    <span :class="@ccc">直接引用数组</span>
    <span :class="[@aaa, @bbb]">使用数组字面量</span>
    <span :class="['aaa', 'bbb',(@toggle? 'ccc':'ddd')]">选择性添加类名</span>
    <span :class="[@toggle && 'aaa']">选择性添加类名</span>
    <span :class="[ @ddd + 4]">动态生成类名</span>
</body>
```

----

### ms-if

通过属性值决定是否渲染目标元素, 为否时原位置上变成一个注释节点

> `avalon1.*`中ms-if-loop指令已经被废掉,请使用limitBy, selectBy, filterBy过滤器代替相应功能

```html
<body :controller="test">
<script>
var vm = avalon.define({
  $id: "test",
  aaa: "这是被隐藏的内容"
  toggle: false
})
</script>
<p><button type="button" :click='@toggle = !@toggle'>点我</span></button></p>
<div :if="@toggle">{{@aaa}}</div>
</body>
```

> 注意,有许多人喜欢用ms-if做非空处理,这是不对的,因此ms-if只是决定它是否插入DOM树与否,ms-if里面的 **ms-**指令还是会执行.

```javascript
avalon.define({
  $id: 'test',
  aaa: {}
})
<div ms-controller="test">
   <div ms-if="@aaa.bbb">
     {{@aaa.bbb.ccc}}这里肯定会出错
   </div>
</div>
```

正确的做法是,当你知道这里面有非空判定,需要用方法包起来

```javascript
avalon.define({
  $id: 'test',
  aaa: {},
  show: function(aaa, bbb, ccc){
     var obj = aaa[bbb]
     if(obj){
        return obj[ccc]
     }
     return ''
  }
})
<div ms-controller="test">
   <div ms-if="@aaa.bbb">
     {{@show(@aaa, 'bbb', 'ccc')}}
   </div>
</div>
```

----

### ms-visible

这是通过修改元素的style.display值改变元素的可见性, 要求属性值对应一个布尔，如果不是布尔， avalon会自动转换值为布尔。

```html
<body :controller="test">
<script>
var vm = avalon.define({
  $id: "test",
  aaa: "这是被隐藏的内容"
  toggle: false
})
</script>
<p><button type="button" :click='@toggle = !@toggle'>点我</span></p>
<div :visible="@toggle">{{@aaa}}</div>
</body>
```

------

### ms-on

**事件绑定**

此绑定为元素添加交互功能，对用户行为作出响应. `ms-on-*="xxx"`是其使用形式, `*`代表click, mouseover, touchstart等事件名，只能与小写形式定义， xxx是事件回调本身，可以是方法名，或表达式。 默认,事件回调的第一个参数是事件对象,并进行标准化处理. 如果你是用`ms-on-click="@fn(el, 1)"`这样的传参方式，第一个传参被你占用， 而你又想用事件对象，可以使用**$event**标识符，即`ms-on-click="@fn(el, 1, $event)"` 那么第三个参数就是事件对象。

如果你想绑定多个点击事件,可以用`ms-on-click-1="@fn(el)", ms-on-click-2="@fn2(el)",ms-on-click-3="@fn3(el)"`来添加。

并且,avalon对常用的事件,还做了快捷处理,你可以省掉中间的on。

avalon默认对以下事件做快捷处理:

```
animationend、 blur、 change、 input、 click、 dblclick、 focus、 keydown、 keypress、 keyup、 mousedown、 mouseenter、 mouseleave、 mousemove、 mouseout、 mouseover、 mouseup、 scroll、 submit
```

此外,avalon2相对avalon1，还做了以下强化:

以前`ms-on-*`的值只能是vm中的一个函数名`ms-on-click="fnName"`, 现在其值可以是表达式,如`ms-on-click="el.open = !el.open"`, 与原生的onclick定义方式更相近. 以前`ms-on-*`的函数,this是指向绑定事件的元素本身,现在this是指向vm, 元素本身可以直接从e.target中取得.

`ms-on-*`会优先考虑使用事件代理方式绑定事件,将事件绑在根节点上!这会带来极大的性能优化! `ms-on-*`的值转换为函数后,如果发现其内部不存在ms-for动态生成的变量,框架会将它们缓存起来! 添加了一系列针对事件的过滤器 对按键进行限制的过滤器esc，tab，enter，space，del，up，left，right，down 对事件方法stopPropagation, preventDefault进行简化的过滤器stop, prevent

```javascript
var vm = avalon.define({
    $id: "test",
    firstName: "司徒",
    array: ["aaa", "bbb", "ccc"],
    argsClick: function(e, a, b) {
        alert([].slice.call(arguments).join(" "))
    },
    loopClick: function(a, e) {
        alert(a + "  " + e.type)
    },
    status: "",
    callback: function(e) {
        vm.status = e.type
    },
    field: "",
    check: function(e) {
        vm.field = e.target.value + "  " + e.type
    },
    submit: function() {
        var data = vm.$model
        if (window.JSON) {
            setTimeout(function() {
                alert(JSON.stringify(data))
            })
        }
    }
})
<fieldset ms-controller="test">
    <legend>有关事件回调传参</legend>
    <div ms-mouseenter="@callback" ms-mouseleave="@callback">{{@status}}<br/>
        <input ms-on-input="@check"/>{{@field}}
    </div>
    <div ms-click="@argsClick($event, 100, @firstName)">点我</div>
    <div ms-for="el in @array" >
        <p ms-click="@loopClick(el, $event)">{{el}}</p>
    </div>
    <button ms-click="@submit" type="button">点我</button>
</fieldset>
```

绑定多个同种事件的例子：

```javascript
var count = 0
var model = avalon.define({
    $id: "multi-click",
    str1: "1",
    str2: "2",
    str3: "3",
    click0: function() {
        model.str1 = "xxxxxxxxx" + (count++)
    },
    click1: function() {
        model.str2 = "xxxxxxxxx" + (count++)
    },
    click2: function() {
        model.str3 = "xxxxxxxxx" + (count++)
    }
})
<fieldset>
    <legend>一个元素绑定多个同种事件的回调</legend>
    <div ms-controller="multi-click">
        <div ms-click="@click0" ms-click-1="@click1" ms-click-2="@click2" >请点我</div>
        <div>{{@str1}}</div>
        <div>{{@str2}}</div>
        <div>{{@str3}}</div>
    </div>
</fieldset>
```

回调执行顺序的例子：

```javascript
 avalon.define({
      $id: "xxx",
      fn: function() {
          console.log("11111111")
      },
      fn1: function() {
          console.log("2222222")
      },
      fn2: function() {
          console.log("3333333")
      }
  })
<div ms-controller="xxx" 
    ms-on-mouseenter-3="@fn"
    ms-on-mouseenter-2="@fn1"
    ms-on-mouseenter-1="@fn2"
    style="width:100px;height:100px;background: red;"
    >
</div>
```

avalon已经对ms-mouseenter, ms-mouseleave进行修复，可以在这里与这里了解这两个事件。 到chrome30时，所有浏览器都原生支持这两个事件。

````javascript
avalon.define({
    $id: "test",
    text: "",
    fn1: function (e) {
       this.text = e.target.className + " "+ e.type
    },
    fn2: function (e) {
       this.text = e.target.className + " "+ e.type
    }
})
.bbb{
    background: #1ba9ba;
    width:200px;
    height: 200px;
    padding:20px;
    box-sizing:content-box;
}
.ccc{
    background: #168795;
    width:160px;
    text-align: center;
    line-height: 160px;
    height: 160px;
    margin:20px;
    box-sizing:content-box;
}
    <div class="aaa" ms-controller="test">
      <div class="bbb" ms-mouseenter="@fn1" ms-mouseleave="@fn2">
          <div class="ccc" >
              {{@text}}
          </div>
      </div>
    </div>
```

最后是mousewheel事件的修改，主要问题是出现firefox上， 它死活也不愿意支持mousewheel，在avalon里是用DOMMouseScroll或wheel实现模拟的。 我们在事件对象通过wheelDelta属性是否为正数判定它在向上滚动。

```javascript
 avalon.define({
      $id: "event4",
      text: "",
      callback: function(e) {
          this.text = e.wheelDelta + "  " + e.type
      }
  })
<div ms-controller="event4">
    <div ms-on-mousewheel="@callback" id="aaa" style="background: #1ba9ba;width:200px;height: 200px;">
        {{@text}}
    </div>
</div>
```

此外avalon还对input，animationend事件进行修复，大家也可以直接用avalon.bind, avalon.fn.bind来绑定这些事件。但建议都用ms-on绑定来处理。

------

### ms-rules

**验证规则绑定**

avalon2砍掉了不少功能（如`ms-include`,`ms-data`），腾出空间加了其他更有用的功能。 数据验证就是其中之一。现在avalon2内置的验证指令是参考之前的oniui验证框架与jquery validation。

此指令只能用于添加ms-duplex指令的表单元素上。

avalon内置验证规则有

| 规则                 | 描述                                                         |
| :------------------- | :----------------------------------------------------------- |
| required(true)       | 必须输入的字段                                               |
| norequired(true)     | 不是必填的字段                                               |
| email(true)          | 必须输入正确格式的电子邮件                                   |
| url(true)            | 必须输入正确格式的网址                                       |
| date(true或正则)     | 必须输入正确格式的日期。默认是要求YYYY-MM-dd这样的格式       |
| number(true)         | 必须输入合法的数字（负数，小数）                             |
| digits(true)         | 必须输入整数                                                 |
| pattern(正则或true） | 让输入数据匹配给定的正则，如果没有指定，那么会到元素上找pattern属性转换成正则再匹配 |
| equalto(ID名）       | 输入值必须和 #id 元素的value 相同                            |
| maxlength：5         | 输入长度最多是 5 的字符串（汉字算一个字符）                  |
| minlength：10        | 输入长度最小是 10 的字符串（汉字算一个字符）                 |
| chs（true）          | 要求输入全部是中文                                           |
| max:5                | 输入值不能大于 5                                             |
| min:10               | 输入值不能小于 10                                            |

这些验证规则要求使用ms-rules指令表示，要求为一个普通的JS对象。

此外要求验征框架能动起来，还必须在所有表单元素外包一个form元素，在form元素上加ms-validate指令。

```javascript
 var vm = avalon.define({
        $id: "validate1",
        aaa: "",
        bbb: '',
        ccc: '',
        validate: {
            onError: function (reasons) {
                reasons.forEach(function (reason) {
                    console.log(reason.getMessage())
                })
            },
            onValidateAll: function (reasons) {
                if (reasons.length) {
                    console.log('有表单没有通过')
                } else {
                    console.log('全部通过')
                }
            }
        }
    })
<div ms-controller="validate1">
  <form ms-validate="@validate">
      <p><input ms-duplex="@aaa" placeholder="username"
                ms-rules='{required:true,chs:true}' >{{@aaa}}</p>
      <p><input type="password" id="pw" placeholder="password"
                ms-rules='{required:true}' 
                ms-duplex="@bbb" /></p>
      <p><input type="password" 
             ms-rules="{required:true,equalto:'pw'}" placeholder="再填一次"
             ms-duplex="@ccc | change" /></p>
      <p><input type="submit" value="submit"/></p>
  </form>
</div>
```

因此，要运行起avalon2的内置验证框架，必须同时使用三个指令。ms-validate用于定义各种回调与全局的配置项（如什么时候进行验证）。[ms-duplex](http://www.itprobie.com/jc/avalon/avalon_html/ms-duplex.html)用于将单个表单元素及相关信息组成一个Field对象，放到ms-validater指令的fields数组中。ms-rules用于定义验证规则。如果验证规则不满足你，你可以自行在**avalon.validators**对象上添加。

现在我们可以一下ms-validate的用法。其对应一个对象。

| 配置项                   | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| fields                   | 框架自行添加，用户不用写。为一个数组，放置ms-duplex生成的Field对象。 |
| onSuccess                | 空函数，单个验证成功时触发，this指向被验证元素this指向被验证元素，传参为一个对象数组外加一个可能存在的事件对象。 |
| onError                  | 空函数，单个验证失败时触发，this与传参情况同上               |
| onComplete               | 空函数，单个验证无论成功与否都触发，this与传参情况同上。     |
| onValidateAll            | 空函数，整体验证后或调用了validateAll方法后触发；有了这东西你就不需要在form元素上ms-on-submit="submitForm"，直接将提交逻辑写在onValidateAll回调上 |
| onReset                  | 空函数，表单元素获取焦点时触发，this指向被验证元素，大家可以在这里清理className、value |
| validateInBlur           | true，在blur事件中进行验证,触发onSuccess, onError, onComplete回调 |
| validateInKeyup          | true, 在keyup事件中进行验证,触发onSuccess, onError, onComplete回调。当用户在ms-duplex中使用change debounce过滤器时会失效 |
| validateAllInSubmit      | true，在submit事件中执行onValidateAll回调                    |
| resetInFocus             | true，在focus事件中执行onReset回调                           |
| deduplicateInValidateAll | false，在validateAll回调中对reason数组根据元素节点进行去重   |

我们看一下如何自定义验证规则.

比如说我们有一个变态的需求,一个字段可以不填，但如果要填的话一定要是合法的数字,并且大于零. 这就需要自定义规则了.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            avalon.validators.aaa = {
                message: '必须数字并大于0',
                get: function (value, field, next) {
                   //想知道它们三个参数是什么,可以console.log(value, field,next)
                    var ok = (value === '' || (Number(value) > 0))
                    next(ok)
                    return value
                }
            }
            var vm = avalon.define({
                $id: "test",
                aaa: '',
                validate: {
                    onError: function (reasons) {
                        reasons.forEach(function (reason) {
                            console.log(reason.getMessage())
                        })
                    },
                    onValidateAll: function (reasons) {
                        if (reasons.length) {
                            console.log('有表单没有通过')
                        } else {
                            console.log('全部通过')
                        }
                    }
                }
            })
        </script>
    </head>

    <body ms-controller="test">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>自定义规则</legend>
                <p>
                    <input 
                        ms-duplex="@aaa"
                        ms-rules="{aaa: true}" 
                        >
                </p>
            </fieldset>
            <p>
                <input class="submit" type="submit" value="提交">
            </p>
        </fieldset>
    </form>
</body>
</html>
```

在上表还有一个没有提到的东西是如何显示错误信息，这个avalon不帮你处理。但提示信息会帮你拼好，如果你没有写，直接用验证规则的message，否则在元素上找data-message或data-required-message这样的属性。

```html
 <!DOCTYPE html>
<html>
    <head>
        <title>ms-validate</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
        <script src="../dist/avalon.js"></script>
        <script>
            var vm = avalon.define({
                $id: "test",
                rules:{required:true,email:true},
                email:'',
                validate: {
                    onError: function (reasons) {
                        reasons.forEach(function (reason) {
                            console.log(reason.getMessage())
                        })
                    },
                    onValidateAll: function (reasons) {
                        if (reasons.length) {
                            console.log('有表单没有通过')
                        } else {
                            console.log('全部通过')
                        }
                    }
                }
            })
        </script>
    </head>

    <body ms-controller="test">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>验证完整的表单</legend>
                <p>
                    <label for="email">Email</label>
                    <input id="email" 
                           name="email" 
                           type="email"
                           ms-duplex="@email"
                           ms-rules="@rules" 
                           data-required-message="请输入"
                           data-email-message="请输入一个正确的邮箱"
                           >
                </p>
                </fieldset>
                <p>
                    <input class="submit" type="submit" value="提交">
                </p>
            </fieldset>
        </form>
    </body>
</html>
```

最后给一个复杂的例子：

```html
<script>
    var vm = avalon.define({
        $id: "validate2",
        firstname: '司徒正美',
        lastname: '',
        username: '',
        password: '',
        confirm_password: '',
        email: '',
        agree: false,
        topic: [],
        toggle: false,
        validate: {
            onError: function (reasons) {
                reasons.forEach(function (reason) {
                    console.log(reason.getMessage())
                })
            },
            onValidateAll: function (reasons) {
                if (reasons.length) {
                    console.log('有表单没有通过')
                } else {
                    console.log('全部通过')
                }
            }
        }
    })
    avalon.validators.checked = {
        message: '必须扣上',
        get: function (value, field, next) {
            next(value)
            return value
        }
    }
    avalon.validators.selecttwo = {
        message: '至少选择两个',
        get: function (value, field, next) {
            next(!vm.toggle || value.length >= 2)
            return value
        }
    }
   </script>

    <div ms-controller="validate2">
        <form class="cmxform" ms-validate="@validate" >
            <fieldset>
                <legend>验证完整的表单</legend>
                <p>
                    <label for="firstname">名字</label>
                    <input id="firstname" 
                           name="firstname" 
                           ms-duplex="@firstname"
                           ms-rules="{required:true, pattern: /[\u4e00-\u9fa5a-z]{2-8}/i }" 
                           data-required-message="必须是中文或字母(3-8个字符)" >
                </p>
                <p>
                    <label for="lastname">姓氏</label>
                    <input id="lastname" 
                           name="lastname"
                           ms-duplex="@lastname"
                           ms-rules="{required:true}" 
                           data-required-message="请输入您的姓氏"
                           >
                </p>
                <p>
                    <label for="username">用户名</label>
                    <input id="username" 
                           name="username"
                           ms-duplex="@username | change"
                           ms-rules="{required:true, minlength:2}" 
                           >
                </p>
                <p>
                    <label for="password">密码</label>
                    <input id="password" 
                           name="password" 
                           type="password"
                           ms-duplex="@password"
                           ms-rules="{required:true,minlength:5}" 
                           data-required-message="请输入密码"
                           data-required-message="密码长度不能小于 5 个字母"

                           >
                </p>
                <p>
                    <label for="confirm_password">验证密码</label>
                    <input id="confirm_password" 
                           name="confirm_password" 
                           type="password"
                           ms-duplex="@confirm_password | change"
                           ms-rules="{required:true,equalto:'password'}" 
                           data-equalto-message="两次密码输入不一致"
                           >
                </p>
                <p>
                    <label for="email">Email</label>
                    <input id="email" 
                           name="email" 
                           type="email"
                           ms-duplex="@email"
                           ms-rules="{email:true}" 
                           data-email-message="请输入一个正确的邮箱"
                           >
                </p>
                <p>
                    <label for="agree">请同意我们的声明</label>
                    <input type="checkbox" class="checkbox" id="agree" name="agree"
                           ms-duplex-checked="@agree"
                           ms-rules="{checked:true}" 
                           >
                </p>
                <p>
                    <label for="newsletter">我乐意接收新信息</label>
                    <input type="checkbox" class="checkbox" 
                           id="newsletter" 
                           name="newsletter"
                           ms-duplex-checked="@toggle"
                           >
                </p>
                <fieldset id="newsletter_topics" ms-visible="@toggle" >
                    <legend>主题 (至少选择两个) </legend>
                    <label for="topic_marketflash">
                        <input type="checkbox" 
                               id="topic_marketflash" 
                               value="marketflash" 
                               name="topic[]" 
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Marketflash
                    </label>
                    <label for="topic_fuzz">
                        <input type="checkbox"
                               id="topic_fuzz"
                               value="fuzz"
                               name="topic[]"
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Latest fuzz
                    </label>
                    <label for="topic_digester">
                        <input type="checkbox" 
                               id="topic_digester"
                               value="digester"
                               name="topic[]"
                               ms-duplex="@topic"
                               ms-rules="{selecttwo:true}"
                               >Mailing list digester
                    </label>
                    <label for="topic" class="error" style="display:none">至少选择两个</label>
                </fieldset>
                <p>
                    <input class="submit" type="submit" value="提交">
                </p>
            </fieldset>
        </form>
    </div>
```

-----

## 过滤器

### 格式化过滤器

用于处理数字或字符串，多用于``{% raw %} {{ }} {% endraw %}``或ms-attr 或ms-class

> 注意: avalon的过滤器与ng的过滤器在传参上有点不一样,需要用()括起来

### uppercase

将字符串全部大写

```javascript
vm.aaa = "aaa"

<div>{{@aaa | uppercase}}</div>
```

### lowercase

将字符串全部小写

```javascript
vm.aaa = "AAA"

<div>{{@aaa | lowercase}}</div>
```

### truncate

对长字符串进行截短，有两个可选参数

number，最后返回的字符串的长度，已经将truncation的长度包含在内，默认为30。 truncation，告知用户它已经被截短的一个结尾标识，默认为"..."

```javascript
vm.aaa = "121323234324324"

<div>{{@aaa | truncate(10,'...')}}</div>
```

### camelize

驼峰化处理， 如"aaa-bbb"变成"aaaBBB"

### escape

对类似于HTML格式的字符串进行转义，如将<、 >转换为<、 >

### sanitize

对用户输入的字符串进行反XSS处理，去掉onclick, `javascript:alert`，`<script>`等危险属性与标签。

### number

对需要处理的数字的整数部分插入千分号（每三个数字插入一个逗号），有一个参数fractionSize，用于保留小数点的后几位。

fractionSize:小数部分的精度，默认为3。

```html
<!DOCTYPE html>
<html>
<head>
    <title>TODO supply a title</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <script src="avalon.js"></script>
    <script>
        avalon.define({
            $id: "number",
            aaa: 1234.56789
        })
    </script>
</head>

<body>
    <div ms-controller="number">
        <p>输入数字:
            <input ms-duplex="@aaa">
        </p>
        <p>不处理：{{@aaa}}</p>
        <p>不传参：{{@aaa|number}}</p>
        <p>不保留小数：{{@aaa|number(0)}}</p>
        <p>负数:{{-@aaa|number(4)}}</p>
    </div>
</body>

</html>
```

### currency

用于格式化货币，类似于number过滤器（即插入千分号），但前面加了一个货币符号，默认使用人民币符号`\uFFE5`

symbol, 货币符号，默认是`\uFFE5` fractionSize，小数点后保留多少数，默认是2

### date

对日期进行格式化，date(formats), 目标可能是符合一定格式的字符串，数值，或Date对象。

```html
<!DOCTYPE html>
<html>
<head>
    <title>TODO supply a title</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <script src="avalon.js"></script>
    <script>
        avalon.define({
            $id: 'testtest',
            name: "大跃进右",
            d1: new Date,
            d2: "2011/07/08",
            d3: "2011-07-08",
            d4: "01-01-2000",
            d5: "03 04,2000",
            d6: "3 4,2000",
            d7: 1373021259229,
            d8: "1373021259229",
            d9: "2014-12-07T22:50:58+08:00",
            d10: "\/Date(1373021259229)\/"

        })
    </script>
</head>
<body>
    <div ms-controller="testtest">
        <p>生成于{{ @d1 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d2 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d3 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d4 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d5 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d6 | date("yyyy MM dd")}}</p>
        <p>生成于{{ @d7 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d8 | date("yyyy MM dd:HH:mm:ss")}}</p>
        <p>生成于{{ @d9 | date("yyyy MM dd:HH:mm:ss")}} //这是ISO8601的日期格式</p>
        <p>生成于{{ @d10| date("yyyy MM dd:HH:mm:ss")}} //这是ASP.NET输出的JSON数据的日期格式</p>
    </div>
</body>

</html>
```

| 标记       | 说明                                                         |
| :--------- | :----------------------------------------------------------- |
| yyyy       | 将当前的年份以4位数输出，如果那一年为300，则补足为0300       |
| yy         | 将当前的年份截取最后两位数输出，如2014变成14， 1999变成99， 2001变成01 |
| y          | 将当前的年份原样输出，如2014变成2014， 399变成399， 1变成1   |
| MMMM       | 在中文中，MMMM与MMM是没有区别，都是"1月"，"2月"……英语则为该月份的单词全拼 |
| MMM        | 在中文中，MMMM与MMM是没有区别，都是"1月"，"2月"……英语则为该月份的单词缩写(前三个字母) |
| MM         | 将月份以01-12的形式输出(即不到两位数，前面补0)               |
| M          | 将月份以1-12的形式输出                                       |
| dd         | 以日期以01-31的形式输出(即不到两位数，前面补0)               |
| d          | 以日期以1-31的形式输出                                       |
| EEEE       | 将当前天的星期几以“星期一”，“星期二”，“星期日”的形式输出，英语则Sunday-Saturday |
| EEE        | 将当前天的星期几以“周一”，“周二”，“周日”的形式输出，英语则Sun-Sat |
| HH         | 将当前小时数以00-23的形式输出                                |
| H          | 将当前小时数以0-23的形式输出                                 |
| hh         | 将当前小时数以01-12的形式输出                                |
| h          | 将当前小时数以0-12的形式输出                                 |
| mm         | 将当前分钟数以00-59的形式输出                                |
| m          | 将当前分钟数以0-59的形式输出                                 |
| ss         | 将当前秒数以00-59的形式输出                                  |
| s          | 将当前秒数以0-59的形式输出                                   |
| a          | 将当前时间是以“上午”，“下午”的形式输出                       |
| Z          | 将当前时间的时区以-1200-+1200的形式输出                      |
| fullDate   | 相当于y年M月d日EEEE 2014年12月31日星期三                     |
| longDate   | 相当于y年M月d日EEEE 2014年12月31日                           |
| medium     | 相当于yyyy-M-d H:mm:ss 2014-12-31 19:02:44                   |
| mediumDate | 相当于yyyy-M-d 2014-12-31                                    |
| mediumTime | 相当于H:mm:ss 19:02:44                                       |
| short      | 相当于yy-M-d ah:mm 14-12-31 下午7:02                         |
| shortDate  | 相当于yy-M-d 14-12-31                                        |
| shortTime  | 相当于ah:mm 下午7:02                                         |

------

### 循环过滤器

用于ms-for指令中

### limitBy

只能用于ms-for循环,对数组与对象都有效, 限制输出到页面的个数, 有两个参数

1. limit: 最大个数,必须是数字或字符, 当个数超出数组长或键值对总数时, 等于后面
2. begin: 开始循环的个数, 可选,默认0

```html
<script>
    avalon.define({
        $id: "limitBy",
        array: [1, 2, 3, 4, 5, 6],
        object: {a: 1, b: 2, c: 3, d: 4, e: 5},
        num: 3
    })
</script>
<div ms-controller='limitBy'>
    <select ms-duplex-number='@num'>
        <option>2</option>
        <option>3</option>
        <option>4</option>
        <option>5</option>
    </select>
    <ul>
        <li ms-for='el in @array | limitBy(@num)'>{{el}}</li>
    </ul>
    <ul>
        <li ms-for='el in @object | limitBy(@num)'>{{el}}</li>
    </ul>
</div>
```

### orderBy

只能用于ms-for循环,对数组与对象都有效, 用于排序, 有两个参数

1. key: 要排序的属性名
2. dir: -1或1, 顺序或倒序,可选,默认1

```html
<script>
avalon.define({
    $id: "orderBy",
    array: [{a: 1, b: 33},{a: 2, b: 22}, {a: 3, b: 11}],
    order: 'a',
    dir: -1
})
</script>
<div ms-controller='orderBy'>
    <select ms-duplex='@order'>
        <option>a</option>
        <option>b</option>
    </select>
    <select ms-duplex-number='@dir'>
        <option>1</option>
        <option>-1</option>
    </select>
    <table border='1' width='200'>
        <tr ms-for="el in @array | orderBy(@order, @dir)">
            <td ms-for='elem in el'>{{elem}}</td>
        </tr>
    </table>
</div>
```

### filterBy

只能用于ms-for循环,对数组与对象都有效, 用于获取它们的某一子集, 有至少一个参数

search，如果为函数时, 通过返回true决定成为子集的一部分; 如果是字符串或数字, 将转换成正则, 如果数组元素或对象键值匹配它,则成为子集的一部分,但如果是空字符串则返回原对象 ;其他情况也返回原对象。 其他参数, 只有当search为函数时有效, 这时其参数依次是数组元素或对象键值(对象的情况下), 索引值或对象键名(对象的情况下), 多余的参数 此过滤多用于自动完成的模糊匹配!

![img](http://www.itprobie.com/jc/avalon/avalon_html/img/filterBy.png)

filterBy例子1

```html
<script>
    avalon.define({
        $id: "filterBy",
        array: ['aaaa', 'aab', 'acb', 'ccc', 'dddd'],
        object: {a: 'aaaa', b: 'aab', c: 'acb', d: 'ccc', e: 'dddd'},
        searchs: "a",
        searchFn: function (el, i) {
            return i > 2
        },
        searchFn2: function (el, i) {
            return el.length === 4
        },
        searchFn3: function (el, i) {
            return i === 'b' || i === 1
        }
    })
</script>
<div ms-controller='filterBy'>
    <select ms-duplex='@search'>
        <option>a</option>
        <option>b</option>
        <option>c</option>
    </select>
    <p><button ms-click="@search = @searchFn | prevent">变成过滤函数</button></p>
    <p><button ms-click="@search = @searchFn2 | prevent">变成过滤函数2</button></p>
    <p><button ms-click="@search = @searchFn3 | prevent">变成过滤函数3</button></p>
    <ul>
        <li ms-for='el in @array | filterBy(@search)'>{{el}}</li>
    </ul>
    <ul>
        <li ms-for='el in @object | filterBy(@search)'>{{el}}</li>
    </ul>
</div>
```

filterBy例子2

```html
<body>
    <script>
        var vm = avalon.define({
            $id: 'test',
             arr: [{color: 'red'},{color:'green'},{color:'red'}],
             fn: function(el, index, xxx){
                 console.log(el, index, xxx)
                 return el.color === 'red'
             }
        })
    </script>
    <style>

    </style>
    <div ms-controller="test">
        <ul>
            <li ms-for="el in @arr | filterBy(@fn, 'xxx')">{{el.color}}</li>

        </ul>
    </div>
</body>
```

filterBy例子3

```html
<script>
    var vm = avalon.define({
        $id: 'test',
        arr: [{name: 'wanglin', age: 11},
            {name: 'lin', age: 3},
            {name: 'Hunt', age: 22},
            {name: 'Joe', age: 33}],
        fn: function(a){//过滤数组中 name属性值带lin中的元素
            return  /lin/.test(a.name)
        }
    })
</script>
<div ms-controller='test' >    
    <div ms-for="(index,el) in @arr as items | filterBy(@fn)" >
        {{el.name}} -- {{items.length}}
    </div>    
</div>
```

### selectBy

只能用于ms-for循环,只对对象有效, 用于抽取目标对象的几个值,构成新数组返回.

1. array，要抽取的属性名
2. defaults，如果目标对象不存在这个属性,那么从这个默认对象中得到默认值,否则为空字符串, 可选 这个多用于表格, 每一列的对象可能存在属性顺序不一致或缺少的情况

```html
<script>
    avalon.define({
        $id: "selectBy",
        obj: {a: 'aaa', b: 'bbb', c: 'ccc', d: 'ddd', e: 'eee'},
        grid: [{a: 1, b: 2, c: 3}, {c: 11, b: 22, a: 33}, {b: 23, a: 44}],
        defaults: {
            a:'@@@',
            b:'$$$',
            c:'###'
        }
    })
</script>
<div ms-controller='selectBy'>
   <ul>
       <li ms-for='el in @obj | selectBy(["c","a","b"])'>{{el}}</li>
   </ul>
   <table border='1' width='200'>
       <tr ms-for="tr in @grid">
           <td ms-for="td in tr | selectBy(['a','b','c'],@defaults)">{{td}}</td>
       </tr>
   </table>
</div>
```

### 事件过滤器

事件过滤器只要是对一些常用操作进行简化处理

对按键事件(keyup,keydown,keypress)到底按下了哪些功能键 或方向键进行友好的处理.许多人都记不清回车退格的keyCode是多少. 对阻止默认行为与防止冒泡进行封装

### esc

当用户按下esc键时,执行你的回调

### tab

当用户按下tab键时,执行你的回调

### enter

当用户按下enter键时,执行你的回调

### space

当用户按下space键时,执行你的回调

### del

当用户按下del键时,执行你的回调

### up

当用户按下up键时,执行你的回调

### down

当用户按下down键时,执行你的回调

### left

当用户按下left键时,执行你的回调

### right

当用户按下right键时,执行你的回调

### prevent

阻止默为行为,多用于form的submit事件防止页面跳转,相当于调用了event.preventDefault

```html
<a href='./api.html' ms-click='@fn | prevent'>阻止跳转</a>
```

### stop

阻止事件冒泡,相当于调用了event.stopPropagation

> 页面的过滤器只能用于事件绑定

### 同步频率过滤器

这两个过滤器只用于ms-duplex

### change

在文本域或文本区使用ms-duplex时,默认是每输入一个字符就同步一次. 当我们想在失去焦点时才进行同步, 那么可以使用此过滤器

```html
<input ms-duplex='@aaa | change'>{{@aaa}}
```

### debounce

当我们实现搜索框的自动完成时, 每输入一个字符可能就会向后台请求一次(请求关键字列表), 这样太频繁,后端撑不住,但使用change过滤器,则又太慢了.改为每隔几十毫秒请求一次就最好. 基于此常用需要开发出此过滤器. 拥有一个参数.

1. debounceTime: 数字, 不写默认是300,不能少于4,否则做无效处理

   ```html
   <input ms-duplex='@aaa | debounce(200)'>
   ```

### 编写过滤器

编写一个过滤器是非常简单的. 目前用户可编写的过滤器有两种, 不带参数的及带参数.

比方说uppercase,就是不带参数

```javascript
vm.aaa = "aaa"

<div>{{@aaa | uppercase}}</div>
```

输出:

```html
<div>AAA</div>
```

那它是怎么实现的呢? 源码是这样的

```javascript
avalon.filters.uppercase = function (str) {
    return String(str).toUpperCase()
}
```

过滤器总是把它前方的表达式生成的东西作为过滤器的第一个参数,然后返回一个值

同理lowercase的源码也很简单. 之所以用String,因为我们总想返回一个字符串

```javascript
avalon.filters.lowercase = function (str) {
    return String(str).toLowerCase()
}
```

那么我们自定义一个过滤器,就首先要看一下文档,注意不要与现有的过滤器同名. 比如我们定义一个haha的过滤器

```html
<script>
   avalon.filters.haha = function(a){
       return a +'haha'
   } 
   avalon.define({
       $id:'test',
       aaa: '111'
   })
</script>
<div ms-controller='tesst'>{{@aaa | haha}}</div>// 111haha
```

我们再看带参数的,带参数的必须写的括号,把第二个,第三个,放到里面

```html
<div>{{@aaa | truncate(10,'...')}}</div>
```

truncate要传两个参数,那么看一下其源码是这样的:

```javascript
avalon.filters.truncate = function (str, length, truncation) {
    //length，新字符串长度，truncation，新字符串的结尾的字段,返回新字符串
    length = length || 30
    truncation = typeof truncation === "string" ? truncation : "..."
    return str.length > length ?
            str.slice(0, length - truncation.length) + truncation :
            String(str)
}
```

好了,我们看一下如何写一个带参数的过滤器,里面重复利用已有的过滤器

众所周知,ms-attr是返回一个对象. 我们只想对其中的一个字段进行格式化. 比如我们要处理title. 那么就起名为title.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <script type="text/javascript" src="../dist/avalon.js"></script>
        <script>
            avalon.filters.title = function (obj, a, b) {
                var title = obj.title
                var newTitle = avalon.filters.truncate(title, a, b)
                obj.title = newTitle
                return obj
            }
            var vm = avalon.define({
                $id: 'test',
                el: '123456789qwert'
            })

        </script>
    </head>
    <body ms-controller="test">
        <div ms-attr="{title:@el} | title(10,'...')">333</div>
    </body>
</html>
```

----

## $watch，$fire, $unwatch

**模块间通信及属性监控 $watch，$fire, $unwatch**

avalon内置了一个强大的自定义事件系统，它在绑定在每一个VM上。每一个VM都拥有$watch, $unwatch, $fire这三个方法，及一个$events对象。$events是用于储存各种回调。先从单个VM说起，如果一个VM拥有aaa这个属性，如果我们在VM通过$watch对它监控，那么当aaa改变值时，它对应的回调就会被触发！

```js
var vmodel = avalon.define({
     $id: "test", 
     aaa: 111
 })
  vmodel.$watch("aaa", function(newValue, oldValue){
       avalon.log(newValue) //222
       avalon.log(oldValue) //111
   })
 setTimeout(function(){
     vmodel.aaa = 222
 }, 1000)
```

**注意，它只能监听当前属性的变动。**

我们还可以通过$unwatch方法，移除对应的回调。如果传入两个参数，第一个是属性名，第二个是回调，那么只移除此回调；如果只传入一个属性名，那么此属性关联的所有回调都会被移除掉。

有时，我们还绑定了一些与属性名无关的事件回调，想触发它，那只能使用$fire方法了。$fire方法第一个参数为属性名（自定义事件名），其他参数随意。

```js
var vmodel = avalon.define({
     $id: "test", 
     aaa: 111
 })
 vmodel.$watch("cat", function(){
     avalon.log(avalon.slice(arguments)) //[1,2,3]
  })
 setTimeout(function(){
     vmodel.$fire("cat",1,2,3)
 }, 1000)
```

**更高级的玩法**，有时我们想在任何属性变化时都触发某一个回调，这时我们就需要$watch一个特殊的属性了——“$all”。不同的是，$watch回调的参数多了一个属性名，排在最前面。

```js
var vmodel = avalon.define({
     $id: "test",
     aaa: 111,
     bbb: 222,
     
 })
 vmodel.$watch("$all", function(){
     avalon.log(avalon.slice(arguments)) 
         // ["aaa", 2, 111]
         // ["bbb", 3, 222]
 })
 setTimeout(function(){
     vmodel.aaa = 2
     vmodel.bbb = 3
 }, 1000)
```

手动触发$fire是位随着高风险的，框架内部是做了处理（只有前后值发生变化才会触发），因此万不得已使用它，但又爆发死循环怎么办？这样就需要暂时中断VM的属性监控机制。使用$unwatch()，它里面什么也不传，就暂时将监控冻结了。恢复它也很简单，使用$watch()，里面也什么也不传！

不过最强大的用法是实现模块间的通信（因为在实际项目中，一个页面可能非常大，有多少人分块制作，每个人自己写自己的VM，这时就需要通过某种机制来进行数据与方法的联动了），这是使用$fire方法达成的。只要在$fire的自定义事件名前添加"up!", "down!", "all!"前缀,它就能实现angular相似的$emit,$broadcast功能。

```html
<!DOCTYPE html>
<html>
    <head>
        <title>by 司徒正美</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <script src="avalon.js"></script>
        <script>
          var vm1 = avalon.define({
                $id: "ancestor", 
                aaa : '1111111111',
                click : function() {
                    avalon.log("向下广播")
                    vm1.$fire("down!aaa", "capture")
                }
            })
            vm1.$watch("aaa", function(v) {
                    avalon.log(v)
                    avalon.log("ancestor.aaa事件被触发了")
            })
            var vm2 = avalon.define({
                $id: "parent", 
                text : "222222222"
                aaa : '3333333333',
                click : function() {
                    console.log("全局扩播")
                    vm2.$fire("all!aaa", "broadcast")
                }
            })
            vm2.$watch("aaa", function(v) {
                   avalon.log(v)
                   avalon.log("parent.aaa事件被触发了")
             })
            var vm3 = avalon.define(
                $id: "son", 
                click : function() {
                    console.log("向上冒泡")
                    vm3.$fire("up!aaa", "bubble")
                }
            })
            vm3.$watch("aaa", function(v) {
                    avalon.log(v)
                    avalon.log("son.aaa事件被触发了")
              })
        </script>
        <style>
 
        </style>
    </head>
    <body class="ms-controller"   ms-controller="ancestor">
        <h3>avalon vm.$fire的升级版 </h3>
        <button type="button" ms-click="click">
            capture
        </button>
        <div ms-controller="parent">
            <button type="button" ms-click="click">broadcast</button>
            <div ms-controller="son">
                <button type="button" ms-click="click">
                    bubble
                </button>
            </div>
        </div>
    </body>
</html>
```

-------

## 与jQuery共存

jQuery是世界上最流行的DOM库,它拥有各式各样的插件,在日常开发中我们可能还是离不开它.因此与它一起使用是常态, 下面是一些注意

### domReady后如何扫描

```javascript
$(function(){
   var vm = avalon.define({/* */})
   //如果你将vm定义在jQuery的ready方法内部,那么avalon的扫描就会失效,需要手动扫描
   avalon.scan(document.body) //现在只要传入扫描范围的根节点就行
})
```

### 如何AJAX提交数据

提交整个VM

```javascript
jQuery.ajax({
   method: "POST",
   url: 'url-adress',
   //这里是取vm的数据模型 ,通过JSON.stringify会去掉其所有方法, 变成JSON字符串
   //再用JSON.parse变回纯JS对象
   data: JSON.parse(JSON.stringify(vm.$model))
})
```

提交VM中的某个`对象`属性

~~~javascript
data: JSON.parse(JSON.stringify(vm.data.$model))
``

提交VM中的某个`数组`属性

```javascript
data: JSON.parse(JSON.stringify(vm.data.$model))
~~~

### 如何让后台回来的数据更新VM

后台的数据更新VM,只能是更新VM的某些已经定义属性. 如果后台数据很大,那么我们可以定义一个空对象(假如后台数据是对象类型)或一个空数组(假如后台数据是数组类型)来占位

```javascript
var vm = avalon.define({
    $id: 'aaa',
    array: []
})

jQuery.ajax({
   method: "POST",
   url: 'url-adress',
   data: {/**/},
   success: function(data){
     vm.array = data.array
  }
})
```

### 如何同步表单的数据

假如我的某个表单是用于jQuery的日历插件,那么它数据如何同步到vm

```javascript
$(datepick_input_css_selector).input(function(){
   vm.aaa = this.value
})
```

如何同步复选框 在avalon中,checkbox要对应一个数组 首选是取得所有同名的checkbox,并要求它们在选中状态,然后用map方法收集它们的value值

```javascript
$('checkbox').change(function(){
   var array = $('checkbox[name="'+this.name+'"]:checked').map(function(){
      return $(this).val();
   })
   vm.checkboxProps = array
})
```

如何同步下拉框

```javascript
$('select').change(function(){

   vm.selectProps = $(this).val()
})
```

