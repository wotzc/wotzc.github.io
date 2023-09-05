---
title: IntelliJ-IDEA使用技巧一
date: 2021-08-16 09:38:46
tags:
- IDEA
- 开发工具
categories: IDEA使用技巧
---

#  IDEA查看一个类所有的方法

## 快捷键Ctrl+F12

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/ctrlf12.png)

----------------------

# IDEA查看一个类的继承关系

## 方式一 以图的方式显示

1、找到当前类所在的位置，右键选择Diagrams，然后选择Show Diagrams……

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/diagram1.png)

2、在弹出的框中选择Java Class Diagrams：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/diagrams2.png)

3、可以看到如下的结果，所有的父类继承关系：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/digrams3.png)

4、在页面点击右键，选择 show categories，根据需要可以展开类中的属性、方法、构造方法等等。当然，第二种方法也可以直接使用上面的工具栏：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/diagrams4.png)

然后你就会得到：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/EasSaveFacadeServiceImpl.png)

## 方式二 通过hierarchy面板

可以点击编辑器最上端的Navigate，下拉选择Type Hierarchy，或者使用快捷键**Ctrl + H（mac对应的是Ctrl+H）**，就会出现一个有层级关系的关系列表，如下图所示，展示所有的父类或子类：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/hierarchy.png)

-----------

# 代码提示不区分大小写

settings -> Editor -> General -> Code Completion

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/codecompletion.png)

- IntelliJ IDEA 的代码提示和补充功能有一个特性：区分大小写。如上图标注所示，默认就是 First letter 区分大小写的。
- 区分大小写的情况是这样的：比如我们在 Java 代码文件中输入 stringBuffer，IntelliJ IDEA 默认是不会帮我们提示或是代码补充的，但是如果我们输入StringBuffer 就可以进行代码提示和补充。
- 如果想不区分大小写的话，取消勾选<code style="color:#b30049;background-color:#fdf5f5">Match case</code>选项即可。

-----------

#  tab 多行显示

settings -> Editor Tabs -> Configure Editor Tabs...，取消勾选 Show Tabs In Single Row选项。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/editortabs.png)

效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/canclesinglerow.png)

------------

# 代码块包裹功能 - Surround With

**Ctrl + Alt + T** 提供的是代码块包裹功能 -<code style="color:#b30049;background-color:#fdf5f5"> Surround With</code>。可以快速将选中的代码块，包裹到选择的语句块中。

（mac对应的是**Command+Option+T**)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/surroundwith.png)

--------------

# 设置文件头

File--> Settings--> Editor--> File and Code Templates--> Includes--> File Header--> 添加以下代码

${USER}会读取当前电脑的用户名

```
/**
 * @Author ${USER}
 * @Date ${DATE}
 */
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/fileHeader.png)

新建一个文件效果如下:

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/newfile.png)

---------

# 设置方法注释模板

先看效果

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate1.png)

IDEA还没有智能到自动为我们创建方法注释，这就是要我们手动为方法添加注释，使用Eclipse时我们生成注释的习惯是

**/**+Enter**，这里我们也按照这种习惯来设置IDEA的方法注释

1、File-->Settings-->Editor-->Live Templates

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate2.png)

2、点+号新建一个组或模板，选择Live template

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate3.png)

3、新建模板：命名为*，修改生成注释的快捷键为Enter

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate4.png)

4、设置模板：模板内容如下**，注意第一行，只有一个\*而不是/***，在设置参数名时必须用${参数名}$的方式

```java
*
 * @author $USER$
 * @Description $description$
 $param$ 
 $return$
 * @Date $date$ $time$
 **/

```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate5.png)

5、设置参数的获取方式，选择右侧的Edit variables按钮

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate6.png)

让params 多行显示，而非数组显示

其中params变量的内容一定要放在Default value中！！！内容为：

```
groovyScript("if(\"${_1}\".length() == 2) {return '';} else {def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();for(i = 0; i < params.size(); i++) {if(i==0){result+='* @param ' + params[i] + ': '}else{result+='\\n' + ' * @param ' + params[i] + ': '}}; return result;}", methodParameters());
```

其中return变量的内容也一定要放在Default value中！！！内容为：

```
groovyScript("def returnType = \"${_1}\"; def result = '* @return: ' + returnType; return result;", methodReturnType());
```

6、设置模板的应用场景，点击模板页面最下方的警告，来设置将模板应用于那些场景，一般选择EveryWhere-->Java即可（如果曾经修改过，则显示为change而不是define）

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/methodtemplate7.png)

------------------

# 全屏显示

我们可以使用【Presentation Mode】，将IDEA弄到最大，可以让你只关注一个类里面的代码，进行毫无干扰的coding。

可以使用**Alt+V**快捷键（mac对应的快捷键是**option+v**），弹出View视图，然后选择Appearance->Enter Presentation Mode。效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/mode1.png)

退出进入全屏模式 ，点击 View--->Appearance ---> Exit Persenetation Mode。

---------------

# 分屏操作

在某一个类上面右键

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/splitvertically.png)

效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/splitvertically1.png)

屏幕太多了，一直用鼠标也比较麻烦，这里我们可以直接 <code style="color:#b30049;background-color:#fdf5f5">Ctrl+E</code>进行页面切换

除了 `Ctrl+E`(mac对应的是<code style="color:#b30049;background-color:#fdf5f5">command+E</code>) 还可以只用 ，`Ctrl+Alt+ 方向键进行切换视图`

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/splitvertically2.png)

---------------------------

# 快速的开发常见操作 ：

## 判空

如图，通过<code style="color:#b30049;background-color:#fdf5f5">字符串.null</code>，然后点回车

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/ifempty.png)

效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/ifempty1.png)

## 循环

通过<code style="color:#b30049;background-color:#fdf5f5">.fori</code>遍历集合或数组

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/fori.png)

效果如下：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/fori1.png)

-------------------

## 变量抽取

 ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/extractVariable1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/extractVariable2.png)



## 代码抽取

**抽取方法**，很多时候，在俺们开发当中，经常会方法调用方法，有时候一段代码过于的冗余，所以需要进行抽取。

选中代码，然后快捷键<code style="color:#b30049;background-color:#fdf5f5">Ctrl+Alt+M</code>（mac对应的快捷键是<code style="color:#b30049;background-color:#fdf5f5">command+option+M</code>），就会自动抽取成方法

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/extractMethod.png)

效果如下

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/extractMethod1.png)

--------

## 快速完成声明 if while 等语句

智能提示，该功能可以基于上下文环境，智能帮你过滤可以使用方法，推导出最适合的方法。该快捷键为 **Ctrl+Shift+Space**(mac对应的快捷键是**ctrl+option+space**)。

快速完成语句在 IDEA 中，可以使用快捷键 <code style="color:#b30049;background-color:#fdf5f5">Ctrl+Shift+Enter</code>（mac对应的是**Command+shift+enter**）快速完成声明 if while 等语句。在下面的例子中，我们输入 while ，接着我们输入快捷键，我们可以看到 IDEA 自动帮我们完整这个结构，然后只需要输入判断条件即可。

-------

# IDEA 光标操作

快捷键**alt+j**在相同的字符后生成光标，mac对应的快捷键是**option+G**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/guangbiao1.png)

快捷键**ctrl+alt+shift+j**在相同的字符后生成光标，mac对应的快捷键是**command+option+G**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/guangbiao.png)

# 折叠代码

折叠代码，<code style="color:#b30049;background-color:#fdf5f5">ctrl+'-'</code>，mac对应的快捷键是`command+'-'`

展开代码，<code style="color:#b30049;background-color:#fdf5f5">ctrl+'+'</code>，mac对应的快捷键是`command+'+'`

折叠所有代码，<code style="color:#b30049;background-color:#fdf5f5">ctrl+shift+'-'</code>，mac对应的快捷键是`command+shift+'-'`

展开所有代码，<code style="color:#b30049;background-color:#fdf5f5">ctrl+shift+'+'</code>，mac对应的快捷键是`command+shift+'+'`

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7/folding.png)

