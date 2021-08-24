---
title: IntelliJ-IDEA使用技巧之Debug
date: 2021-08-20 20:51:30
tags:
- IDEA
- 开发工具
categories: IDEA使用技巧
---

#  Debug常用断点调试快捷键

|                             图标                             |      快捷键       | 作用                                                         |
| :----------------------------------------------------------: | :---------------: | ------------------------------------------------------------ |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/stepOver.png) |      **F8**       | **Step Over：步过，一行一行地往下走，如果这一行上有方法不会进入方法。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/stepInto.png) |      **F7**       | **Step Into：步入，如果当前行有方法，可以进入方法内部，一般用于进入自定义方法内，不会进入官方类库的方法。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/forceStepInto.png) | **Alt+Shift+F7**  | **Force Step Into：强制步入，能进入任何方法，查看底层源码的时候可以用这个进入官方类库的方法。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/stepOut.png) |   **Shift+F8**    | **Step Out：步出，从步入的方法内退出到方法调用处，此时方法已执行完毕，只是还没有完成赋值。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/runtoCursor.png) |    **Alt+F9**     | **Run to Cursor：运行到光标处，你可以将光标定位到你需要查看的那一行，然后使用这个功能，代码会运行至光标行，而不需要打断点。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/cursorstop.png) |    **Ctrl+F2**    | **Stop ‘xxx’：停止运行**                                     |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/viewbreakpoints.png) | **Ctrl+Shift+F8** | **View Breakpoints：查看所有断点**                           |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/mutebreakpoints.png) |                   | **Mute Breakpoints：选择这个后，所有断点变为灰色，断点失效，按F9则可以直接运行完程序。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/resume.png) |      **F9**       | **Resume Program：恢复程序，如果下面还有断点则运行到下一个断点。** |
| ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/dropframe.png) |                   | **Drop Frame：断点回退，断点会回退到我们打断点的位置。**     |

---------------

# 条件断点

在我们打断点的地方鼠标右击，然后在condition输入框里面输入条件

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/conditionbreakpoint1.png)

-----------------

# 求值表达式Evaluate Expression

在断点调试过程中，我们想查看某些变量或某些对象的属性值，我们可以通过快捷键**Alt+F8**打开**表达式求值**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/evaluateExpression1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/evaluateExpression2.png)

---------------------

# setValue

Intellij IDEA在debug模式下可以改变变量的值，下面这段代码为例，比如我们想看看i=12时会输出什么

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/debugsetvalue1.png)

此时**i==5**，我们把**i**设置成12

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/debugsetvalue2.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/debugsetvalue3.png)

按快捷键**F8**看看i是否会进入**if条件**里面

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/debugsetvalue4.png)

--------------------------------

--------------------

# 异常断点

**Java Exception Breakpoints可以使当程序遇见被选择的异常时，自动停在相应异常的位置处。**

如下面这段代码

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint1.png)

加上条件断点，使用快捷键**Ctrl+Shift+F8**或者点击图标![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/viewbreakpoints.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint2.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint3.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint4.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint5.png)

再使用Debug模式来运行程序，当程序出现异常，会之间停留在我们出现异常的代码处

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/exceptionBreakpoint6.png)

--------------------

#  避免操作资源

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource1.png)

如上图所示，我们在断点调试时，后续的一些操作可能会涉及操作一些资源，我们又不行后面的代码执行，直接让代码终止。通常我们想到的是点击终止按钮![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/cursorstop.png)

。但是点击这个按钮之后后续的操作还会执行，如下图所示

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource2.png)

-------------------------

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource3.png)

Debug调试界面，选择正在运行的Stack，鼠标右键选择Force Return，如下图所示

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource4.png)

上面的操作完成之后，代码会跑到**Thread.java**类中的**exit（）**方法里。再点击![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/resume.png)

程序就运行结束。我们就会看到后续的操作并没有执行

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource5.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/IDEA%E4%B9%8Bdebug%E8%B0%83%E8%AF%95/releaseSource6.png)