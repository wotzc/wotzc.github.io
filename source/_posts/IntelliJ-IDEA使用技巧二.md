---
title: IntelliJ-IDEA使用技巧二
date: 2021-08-16 09:38:46
tags:
- IDEA
- 开发工具
categories: IDEA使用技巧

---

#  IDEA设置鼠标悬浮提示

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/mousemovepng.png"  />

效果如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/mousemoveshow.png"  />

-----------

# 设置行号与方法的分隔符

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/appearanceset.png"  />

效果如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/appearancesetshow.png"  />

-----------

# 设置自动编译

Intellij Idea 默认状态为不自动编译状态，Eclipse 默认为自动编译。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/compileauto.png"  />

------------------

# 使用模板

IDEA中代码代码模板所在的位置(Editor – Live Templates 和 Editor – General – Postfix Completion)

**Live Templates(实时代码模板)功能介绍**

它的原理就是配置一些常用代码字母缩写，在输入简写时可以出现你预定义的固定模式的代码，使得开发效率大大提高，同时也可以增加个性化。最简单的例子
就是在Java中输入<code>sout</code>会出现<code>System.out.println()</code>;

已有的常用模板

**Postfix Completion 默认如下**

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/postfixCompletion.png"  />

**Live Templates默认如下**

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/livetemplates.png"  />

二者的区别：<code>Live Templates</code>可以自定义，而<code>Postfix Completion</code>不可以。

**常用模板**

**psvm** : 可生成<code>main</code>方法

**sout** : <code>System.out.println()</code>快捷输出

类似的：**soutp**

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutp.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutpshow.png"  />

**soutv**=<code>System.out.println("变量名 = " + 变量);</code>

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutv.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutvshow.png"  />

**soutm**=<code>System.out.println("当前类名.当前方法");</code>

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutm.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/soutmshow.png"  />

**“abc”.sout** =><code> System.out.println("abc");</code>

 **list.for** : 可生成集合 list 的 的 for 循环

输入: **list.for** 即可输出

```java
for(String s:list){
}
```

又如：**list.fori** 或 **list.forr**

```java
List<String> list = new ArrayList<String>();
```

输入: **list.for** 即可输出

又如：**list.fori** 或 **list.forr**

```java
for(String s:list){
}
```

又如：**list.fori** 或 **list.forr**

**ifn** ：可生成 <code>if(xxx = null)</code>
        类似的：
        **inn**：可生成<code>if(xxx != null)</code>或<code>xxx.nn</code>或<code>xxx.null</code>

**prsf** ：可生成<code>private static final</code>
类似的：
**psf**：可生成<code>public static final</code>
**psfi**：可生成<code>public static final int</code>
**psfs**：可生成<code>public static final String</code>

------------------------------------

**自定义模板**

IDEA 提供了很多现成的 Templates。但你也可以根据自己的需要创建新的Template

1. 自定义模板组，并命名为CustomDefine

   <img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/newtemplateGroup1.png"  />

   <img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/newtemplateGroup2.png"  />

2. 在自定义的模板组里点“+”号新建自定义模板

   <img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/newTemplateLive1.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/newTemplateLive2.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/newTemplateLive3.png"  />

1. Abbreviation:模板的缩略名称
2. Description:模板的描述
3. Template text:模板的代码片段
4. 应用范围。Define

-------------

# 生成 javadoc

**Tools**--->**Generate JavaDoc...**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/generatejavadoc1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/generatejavadoc2.png)

输入：

```
    Locale：输入语言类型：zh_CN
    Other command line arguments：-encoding UTF-8 -charset UTF-8
```

--------------------

# IDEA常用快捷键Windows版

| **作用**                                             | **快捷键**                       |
| :--------------------------------------------------- | :------------------------------- |
| **返回上次编辑位置（Last Edit Location)**            | **Ctrl+Shift+Backspace**         |
| **Back返回上次光标所在位置**                         | **Ctrl+Alt+向左箭头**            |
| **Forward前进到下次光标所在位置**                    | **Ctrl+Alt+向右箭头**            |
| **搜索类Class**                                      | **Ctrl+N**                       |
| **搜索文件File**                                     | **Ctrl+Shift+N**                 |
| **搜索符号Symbol**                                   | **Ctrl+Alt+Shift+N**             |
| **全局搜索**                                         | **Ctrl+Shift+F**                 |
| **当前文件搜索**                                     | **Ctrl+F**                       |
| **执行（run)**                                       | **Shift+F10**                    |
| **提示补全、导包、万能快捷键**                       | **Alt+Enter**                    |
| **单行注释**                                         | **Ctrl+/**                       |
| **多行注释**                                         | **Ctrl+Shift+/**                 |
| **向下复制一行(Duplicate Entire Lines）**            | **Ctrl+D**                       |
| **删除一行或选中行**                                 | **Ctrl+Y**                       |
| **向下移动行（move statement down）**                | **Ctrl+Shift+向下箭头**          |
| **向上移动行（move statement up）**                  | **Ctrl+Shift+向上箭头**          |
| **向下开始新的一行**                                 | **Shift+Enter**                  |
| **向上开始新的一行 (Start New Line before current)** | **Ctrl+Alt+Enter**               |
| **查看继承关系(type hierarchy)**                     | **Ctrl+H**                       |
| **格式化代码(reformat code)**                        | **Ctrl+Alt+L**                   |
| **提示方法参数类型(Parameter Info)**                 | **Ctrl+P**                       |
| **反撤销**                                           | **Ctrl+Shift+Z**                 |
| **选中数行，整体往后移动**                           | **Tab**                          |
| **选中数行，整体往前移动**                           | **Shift+Tab**                    |
| **查看类的结构：类的方法与变量**                     | **Alt+7**                        |
| **大写转小写/ 小写转大写(toggle case)**              | **Ctrl+Shift+U**                 |
| **生成构造 器/get/set/toString**                     | **Alt+Insert**                   |
| **收起所有的方法(collapse all)**                     | **Ctrl+Shift+减号**              |
| **打开所有方法(expand all)**                         | **Ctrl+Shift+加号**              |
| **打开代码所在硬盘文件夹(show in explorer)**         | **Ctrl+Shift+X（需要自己设置）** |
| **生成 try-catch 等(surround with)**                 | **Ctrl+Alt+T**                   |
| **查看方法的多层重写结构(method hierarchy)**         | **Ctrl+Shift+H**                 |
| **抽取方法(Extract Method)**                         | **Ctrl+Alt+M**                   |
| **打开 最近 修改的文件(Recently Files)**             | **Ctrl+E**                       |
| **关闭当前打开的代码栏(close)**                      | **Ctrl+F4**                      |
| **关闭其他所有代码栏(close others)**                 | **Ctrl+Shift+O（需要自己设置）** |
| **快速搜索类中的错误(next highlighted error)**       | **F2**                           |
| **选择要粘贴的内容(Show in Explorer)**               | **Ctrl+Shift+V**                 |
| **查找方法在哪里被调用(Call Hierarchy)**             | **Ctrl+Alt+H**                   |

-----------------

# IDEA常用快捷键Mac版

| **作用**                                             | **快捷键**                          |
| :--------------------------------------------------- | :---------------------------------- |
| **返回上次编辑位置（Last Edit Location)**            | **command+Shift+Delete**            |
| **Back返回上次光标所在位置**                         | **Command+[**                       |
| **Forward前进到下次光标所在位置**                    | **Command+]**                       |
| **搜索类Class**                                      | **Command+O**                       |
| **搜索文件File**                                     | **Command+shift+O**                 |
| **搜索符号Symbol**                                   | **Command+option+O**                |
| **全局搜索**                                         | **Command+option+F**                |
| **当前文件搜索**                                     | **Command+F**                       |
| **执行（run)**                                       | **option+R**                        |
| **提示补全、导包、万能快捷键**                       | **option+Enter**                    |
| **单行注释**                                         | **Command+/**                       |
| **多行注释**                                         | **Command+Option+/**                |
| **向下复制一行(Duplicate Entire Lines）**            | **Command+D**                       |
| **删除一行或选中行**                                 | **Ctrl+Y**                          |
| **向下移动行（move statement down）**                | **Command+option+向下箭头**         |
| **向上移动行（move statement up）**                  | **command+option+向上箭头**         |
| **向下开始新的一行**                                 | **Shift+Enter**                     |
| **向上开始新的一行 (Start New Line before current)** | **Command+option+Enter**            |
| **查看继承关系(type hierarchy)**                     | **Ctrl+H**                          |
| **格式化代码(reformat code)**                        | **Command+option+L**                |
| **提示方法参数类型(Parameter Info)**                 | **Command+P**                       |
| **反撤销**                                           | **Command+Shift+Z**                 |
| **选中数行，整体往后移动**                           | **Tab**                             |
| **选中数行，整体往前移动**                           | **Shift+Tab**                       |
| **查看类的结构：类的方法与变量**                     | **command+7**                       |
| **大写转小写/ 小写转大写(toggle case)**              | **Command+Shift+U**                 |
| **生成构造 器/get/set/toString**                     | **Command+N**                       |
| **收起所有的方法(collapse all)**                     | **Command+Shift+减号**              |
| **打开所有方法(expand all)**                         | **Command+Shift+加号**              |
| **打开代码所在硬盘文件夹(show in explorer)**         | **Command+Shift+X（需要自己设置）** |
| **生成 try-catch 等(surround with)**                 | **Command+option+T**                |
| **查看方法的多层重写结构(method hierarchy)**         | **Command+Shift+H**                 |
| **抽取方法(Extract Method)**                         | **Command+option+M**                |
| **打开 最近 修改的文件(Recently Files)**             | **Ctrl+E**                          |
| **关闭当前打开的代码栏(close)**                      | **Command+W**                       |
| **关闭其他所有代码栏(close others)**                 | **Ctrl+Shift+O（需要自己设置）**    |
| **快速搜索类中的错误(next highlighted error)**       | **F2**                              |
| **选择要粘贴的内容(Show in Explorer)**               | **Ctrl+Shift+V**                    |
| **查找方法在哪里被调用(Call Hierarchy)**             | **Ctrl+Alt+H**                      |

-----------------



# Alt+Enter快捷键功能

## 自动创建函数

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter2.png)

--------------

## 格式化代码

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter3.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter4.png)

-----

## 为接口创建实现类和方法

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter5.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter6.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter7.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnter8.png)

--------------

## 单词拼写

如果idea检查到我们的单词拼错了会有下波浪线的提示，使用alt+enter的快捷键可以帮我们进行修正

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnterWord1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altEnterWord2.png)

--------------

## 导包

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altenterImport1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/altenterImport2.png)

----------------------

# 寻找代码修改轨迹

## annotate

使用annotate，可以在某个文件内部查看哪一行代码是谁提交的，而且还有提交时间、提交备注信息、提交人姓名、提交的版本号、第几次提交等

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/annotate1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/annotate2.png)

---------------

## Revert  Changes撤销修改

想撤销对当前文件的修改，在空白处使用快捷键**Ctrl+alt+Z**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/revert.png)

------------------

## Local History

在IntelliJ IDEA中一不小心将你本地代码给覆盖了，或者忘记了修改某些地方，出现了一些无法修复的异常，这个时候可以通过历史记录回到以前未修改前，并且记录的机制是修改文件会触发记录的时间点，所以很多天以前的记录也能找到

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/localhistory1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/localhistory2.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%BD%BF%E7%94%A8%E6%8A%80%E5%B7%A7%E4%BA%8C/localhistory3.png)