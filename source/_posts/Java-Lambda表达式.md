---
title: 深入浅出 Java 8 Lambda 表达式
date: 2021-10-23 19:01:50
tags: 
- Lambda
- JavaWeb
- Java8
categories: JavaWeb
---

## 简介

在Java世界里面，面向对象还是主流思想，对于习惯了面向对象编程的开发者来说，抽象的概念并不陌生。面向对象编程是对数据进行抽象，而函数式编程是对行为进行抽象。现实世界中，数据和行为并存，程序也是如此，因此这两种编程方式我们都得学。

这种新的抽象方式还有其他好处。很多人不总是在编写性能优先的代码，对于这些人来说，函数式编程带来的好处尤为明显。程序员能编写出更容易阅读的代码——这种代码更多地表达了业务逻辑，而不是从机制上如何实现。易读的代码也易于维护、更可靠、更不容易出错。

在写回调函数和事件处理器时，程序员不必再纠缠于匿名内部类的冗繁和可读性，函数式编程让事件处理系统变得更加简单。能将函数方便地传递也让编写惰性代码变得容易，只有在真正需要的时候，才初始化变量的值。

Java *Lambda表达式*的一个重要用法是简化某些*匿名内部类*（`Anonymous Classes`）的写法。实际上Lambda表达式并不仅仅是匿名内部类的语法糖，JVM内部是通过*invokedynamic*指令来实现Lambda表达式的。

Lambda表达式（也称闭包），是Java8中最受期待和欢迎的新特性之一。Lambda表达式本质是一个匿名函数，但是它并不是匿名类的语法糖，它让 Java 开始走向函数式编程，其实现原理区别于一般的匿名类中的匿名函数。在Java语法层面Lambda表达式允许函数作为一个方法的参数（函数作为参数传递到方法中），或者把代码看成数据。Lambda表达式可以简化函数式接口的使用。函数式接口就是一个只有一个抽象方法的普通接口，像这样的接口就可以使用Lambda表达式来简化代码的编写。


# Lambda表达式

Lambda 表达式为 Java 添加了缺失的函数式编程特点，使我们能将函数当做一等公民看待。尽管不完全正确，我们很快就会见识到 Lambda 与闭包的不同之处，但是又无限地接近闭包。在支持一类函数的语言中，Lambda 表达式的类型将是函数。但是，在 Java 中，Lambda 表达式是对象，他们必须依附于一类特别的对象类型——函数式接口(functional interface)。函数式接口在java中是指:**有且仅有一个抽象方法的接口**，**只有函数式接口，才可以转换为lambda表达式**，**函数式接口可以显式的被@FunctionalInterface所表示，当被标识的接口不满足规定时，编译器会提示报错**。我们会在后文详细介绍函数式接口。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F/java-lambda-expression.png)



在认识Lambda表达式基础语法之前，先来看一段用两种方式创建线程的代码

```java
// 创建线程
// 匿名类
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello!");
    }
}).start();

// Lambda 表达式
new Thread(() -> System.out.println("Hello!")).start();

```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F/javalambdaexpression.png)

Lambda 表达式的基础语法：Java8中引入了一个新的操作符 <code style="color:#b30049;background-color:#fdf5f5">-></code>该操作符称为箭头操作符或 Lambda 操作符

箭头操作符将 Lambda 表达式拆分成两部分：

左侧：Lambda 表达式的参数列表

右侧：Lambda 表达式中所需实现的功能， 即 Lambda 体

Java 中的 Lambda 表达式通常使用<code style="color:#b30049;background-color:#fdf5f5">(argument) -> (body)</code>语法书写，例如：

```java
(arg1, arg2...) -> { body }

(type1 arg1, type2 arg2...) -> { body }
```

以下是一些 Lambda 表达式的例子：

```java
(int a, int b) -> {  return a + b; }

() -> System.out.println("Hello World");

(String s) -> { System.out.println(s); }

() -> 42

() -> { return 3.1415 };
```

# 什么是函数式接口

在 Java 中，Marker（标记）类型的接口是一种没有方法或属性声明的接口，简单地说，marker 接口是空接口。相似地，函数式接口是只包含一个抽象方法声明的接口。

<code style="color:#b30049;background-color:#fdf5f5">java.lang.Runnable </code>就是一种函数式接口，在 Runnable 接口中只声明了一个方法<code style="color:#b30049;background-color:#fdf5f5">void run()</code>，相似地，ActionListener 接口也是一种函数式接口，我们使用匿名内部类来实例化函数式接口的对象，有了 Lambda 表达式，这一方式可以得到简化。

每个 Lambda 表达式都能隐式地赋值给函数式接口，例如，我们可以通过 Lambda 表达式创建 Runnable 接口的引用。

```java
Runnable r = () -> System.out.println("hello world");
```

当不指明函数式接口时，编译器会自动解释这种转化：

```java
new Thread(
   () -> System.out.println("hello world")
).start();
```

因此，在上面的代码中，编译器会自动推断：根据线程类的构造函数签名 <span style="color:red">`public Thread(Runnable r) { }`</span>，将该 Lambda 表达式赋给 Runnable 接口。

以下是一些 Lambda 表达式及其函数式接口：

```java
Consumer<Integer>  c = (int x) -> { System.out.println(x) };

BiConsumer<Integer, String> b = (Integer x, String y) -> System.out.println(x + " : " + y);

Predicate<String> p = (String s) -> { s == null };
```

[@FunctionalInterface](http://download.java.net/jdk8/docs/api/java/lang/FunctionalInterface.html) 是 Java 8 新加入的一种接口，用于指明该接口类型声明是根据 Java 语言规范定义的函数式接口。Java 8 还声明了一些 Lambda 表达式可以使用的函数式接口，当你注释的接口不是有效的函数式接口时，可以使用 @FunctionalInterface 解决编译层面的错误。

以下是一种自定义的函数式接口： @FunctionalInterface public interface WorkerInterface {

```java
   public void doSomeWork();

}
```

根据定义，函数式接口只能有一个抽象方法，如果你尝试添加第二个抽象方法，将抛出编译时错误。例如：

```java
@FunctionalInterface
public interface WorkerInterface {

    public void doSomeWork();

    public void doSomeMoreWork();

}
```

错误：

```java
Unexpected @FunctionalInterface annotation 
    @FunctionalInterface ^ WorkerInterface is not a functional interface multiple 
    non-overriding abstract methods found in interface WorkerInterface 1 error
```

函数式接口定义好后，我们可以在 API 中使用它，同时利用 Lambda 表达式。例如：

```java
 //定义一个函数式接口
@FunctionalInterface
public interface WorkerInterface {

   public void doSomeWork();

}


public class WorkerInterfaceTest {

public static void execute(WorkerInterface worker) {
    worker.doSomeWork();
}

public static void main(String [] args) {

    //invoke doSomeWork using Annonymous class
    execute(new WorkerInterface() {
        @Override
        public void doSomeWork() {
            System.out.println("Worker invoked using Anonymous class");
        }
    });

    //invoke doSomeWork using Lambda expression 
    execute( () -> System.out.println("Worker invoked using Lambda expression") );
}

}
```

输出：

```java
Worker invoked using Anonymous class 
Worker invoked using Lambda expression
```

这上面的例子里，我们创建了自定义的函数式接口并与 Lambda 表达式一起使用。execute() 方法现在可以将 Lambda 表达式作为参数。

# Lambda 表达式的结构

让我们了解一下 Lambda 表达式的结构。

- 一个 Lambda 表达式可以有<code style="color:#b30049;background-color:#fdf5f5">零个</code>或<code style="color:#b30049;background-color:#fdf5f5">多个</code>参数
- 参数的类型既可以明确声明，也可以根据上下文来推断。例如：<code style="color:#b30049;background-color:#fdf5f5">(int a)</code>与<code style="color:#b30049;background-color:#fdf5f5">(a)</code>效果相同
- 当只有一个参数，且其类型可推导时，圆括号<code style="color:#b30049;background-color:#fdf5f5">()</code>可省略。例如：<code style="color:#b30049;background-color:#fdf5f5">a -> return a*a</code>
- 当参数大于一个时，所有参数需包含在圆括号内，参数之间用逗号相隔。例如：<code style="color:#b30049;background-color:#fdf5f5">(a, b)</code> 或 <code style="color:#b30049;background-color:#fdf5f5">(int a, int b)</code>或 <code style="color:#b30049;background-color:#fdf5f5">(String a, int b, float c)</code>
- 空圆括号代表没有参数。例如：<code style="color:#b30049;background-color:#fdf5f5">() -> 42</code>
- Lambda 表达式的主体可包含<code style="color:#b30049;background-color:#fdf5f5">零条</code>或<code style="color:#b30049;background-color:#fdf5f5">多条</code>语句
- 如果 Lambda 表达式的主体只有一条语句，花括号<code style="color:#b30049;background-color:#fdf5f5">{}</code>可省略，如果Lambda体不加<code style="color:#b30049;background-color:#fdf5f5">{}</code>就不用写<code style="color:#b30049;background-color:#fdf5f5">return</code>。
- 如果 Lambda 表达式的主体包含一条以上语句，则表达式必须包含在花括号<code style="color:#b30049;background-color:#fdf5f5">{}中</code>（形成代码块）Lambda体加上<code style="color:#b30049;background-color:#fdf5f5">{}</code>就需要添加<code style="color:#b30049;background-color:#fdf5f5">return</code>。匿名函数的返回类型与代码块的返回类型一致，若没有返回则为空

# Lambda表达式和内部类区别一

读过上一篇之后，相信对Lambda表达式的语法以及基本原理有了一定了解。对于编写代码，有这些知识已经够用。本文将进一步区分Lambda表达式和匿名内部类在JVM层面的区别。

经过第一篇的的介绍，我们看到Lambda表达式似乎只是为了简化匿名内部类书写，这看起来仅仅通过语法糖在编译阶段把所有的Lambda表达式替换成匿名内部类就可以了。但实时并非如此。在JVM层面，Lambda表达式和匿名内部类有着明显的差别。

### 匿名内部类实现

**匿名内部类仍然是一个类，只是不需要程序员显示指定类名，编译器会自动为该类取名**。因此如果有如下形式的代码，编译之后将会产生两个class文件：

```java
public class MainAnonymousClass {
	public static void main(String[] args) {
		new Thread(new Runnable(){
			@Override
			public void run(){
				System.out.println("Anonymous Class Thread run()");
			}
		}).start();;
	}
}
```

编译之后文件分布如下，两个class文件分别是主类和匿名内部类产生的：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F/2-AnonymousClass.png)

进一步分析主类MainAnonymousClass.class的字节码，可发现其创建了匿名内部类的对象：

```java
// javap -c MainAnonymousClass.class
public class MainAnonymousClass {
  ...
  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: new           #3                  // class MainAnonymousClass$1 /*创建内部类对象*/
       7: dup
       8: invokespecial #4                  // Method MainAnonymousClass$1."<init>":()V
      11: invokespecial #5                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      14: invokevirtual #6                  // Method java/lang/Thread.start:()V
      17: return
}
```

### Lambda表达式实现

**Lambda表达式通过invokedynamic指令实现，书写Lambda表达式不会产生新的类**。如果有如下代码，编译之后只有一个class文件：

```java
public class MainLambda {
	public static void main(String[] args) {
		new Thread(
				() -> System.out.println("Lambda Thread run()")
			).start();;
	}
}
```

编译之后的结果：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F/2-Lambda.png)

通过javap反编译命名，我们更能看出Lambda表达式内部表示的不同：

```
// javap -c -p MainLambda.class
public class MainLambda {
  ...
  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: invokedynamic #3,  0              // InvokeDynamic #0:run:()Ljava/lang/Runnable; /*使用invokedynamic指令调用*/
       9: invokespecial #4                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      12: invokevirtual #5                  // Method java/lang/Thread.start:()V
      15: return

  private static void lambda$main$0();  /*Lambda表达式被封装成主类的私有方法*/
    Code:
       0: getstatic     #6                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #7                  // String Lambda Thread run()
       5: invokevirtual #8                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

反编译之后我们发现Lambda表达式被封装成了主类的一个私有方法，并通过<code style="color:#b30049;background-color:#fdf5f5">invokedynamic</code>指令进行调用。

它没有创建将包装 Lambda 函数的新对象，而是使用Java 7 中添加的新<code style="color:#b30049;background-color:#fdf5f5">invokeDynamic</code>指令将此调用站点动态链接到实际的 Lambda 函数。

{% note no-icon success %}

看到 Java，最“严格”的现代语言现在如何使用动态链接来支持其新的 Lambda 表达式，真是令人着迷。这也是一种有效的方法，因为不需要额外的类加载和编译——Lambda 方法只是我们类中的另一个私有方法。

{% endnote %}

------------

# Lambda表达式和内部类区别二

尽管在实际开发中基本上用 lambda 表达式替换了内部类，但这两个概念在一个重要方面是不同的：作用域。

既然Lambda表达式不是内部类的简写，那么Lambda内部的`this`引用也就跟内部类对象没什么关系了。在Lambda表达式中`this`的意义跟在表达式外部完全一样。因此下列代码将输出两遍`Hello Hoolee`，而不是两个引用地址。

```java
public class Hello {
	Runnable r1 = () -> { System.out.println(this); };
	Runnable r2 = () -> { System.out.println(toString()); };
	public static void main(String[] args) {
		new Hello().r1.run();
		new Hello().r2.run();
	}
	public String toString() { return "Hello Hoolee"; }
}
```

而对于匿名类，关键词 `this` 解读为匿名类对象。

```java
new Thread(new Runnable() {
    String name = "zhangsan";
    @Override
    public void run() {
        System.out.println(this.name); //zhangsan
    }
}).start();
```

--------------------

# Lambda and Collections

我们先从最熟悉的*Java集合框架(Java Collections Framework, JCF)*开始说起。

为引入Lambda表达式，Java8新增了<code style="color:#b30049;background-color:#fdf5f5">java.util.funcion</code>包，里面包含常用的**函数接口**，这是Lambda表达式的基础，Java集合框架也新增部分接口，以便与Lambda表达式对接。

首先回顾一下Java集合框架的接口继承结构：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/lambda%E8%A1%A8%E8%BE%BE%E5%BC%8F/JCF_Collection_Interfaces.png)

上图中绿色标注的接口类，表示在Java8中加入了新的接口方法，当然由于继承关系，他们相应的子类也都会继承这些新方法。下表详细列举了这些方法。

| 接口名                                                       | Java8新加入的方法                                            |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| <code style="color:#b30049;background-color:#fdf5f5">Collection</code> | <code style="color:#b30049;background-color:#fdf5f5">removeIf() spliterator() stream() parallelStream() forEach()</code> |
| <code style="color:#b30049;background-color:#fdf5f5">List</code> | <code style="color:#b30049;background-color:#fdf5f5">replaceAll() sort()</code> |
| <code style="color:#b30049;background-color:#fdf5f5">Map</code> | <code style="color:#b30049;background-color:#fdf5f5">getOrDefault() forEach() replaceAll() putIfAbsent() remove() replace() computeIfAbsent() computeIfPresent() compute() merge()</code> |

这些新加入的方法大部分要用到<code style="color:#b30049;background-color:#fdf5f5">java.util.function</code>包下的接口，这意味着这些方法大部分都跟Lambda表达式相关。我们将逐一学习这些方法。

### Collection中的新方法

如上所示，接口<code style="color:#b30049;background-color:#fdf5f5">Collection</code>和<code style="color:#b30049;background-color:#fdf5f5">List</code>新加入了一些方法，我们以<code style="color:#b30049;background-color:#fdf5f5">List</code>的子类<code style="color:#b30049;background-color:#fdf5f5">ArrayList</code>为例来说明。了解[Java7`ArrayList`实现原理](https://github.com/CarpenterLee/JCFInternals/blob/master/markdown/2-ArrayList.md)，将有助于理解下文。

#### forEach()

该方法的签名为<code style="color:#b30049;background-color:#fdf5f5">void forEach(Consumer<? super E> action)</code>，作用是对容器中的每个元素执行`action`指定的动作，其中<code style="color:#b30049;background-color:#fdf5f5">Consumer</code>是个函数接口，里面只有一个待实现方法<code style="color:#b30049;background-color:#fdf5f5">void accept(T t)</code>（后面我们会看到，这个方法叫什么根本不重要，你甚至不需要记忆它的名字）。

需求：*假设有一个字符串列表，需要打印出其中所有长度大于3的字符串.*

Java7及以前我们可以用<code style="color:#b30049;background-color:#fdf5f5">增强的for循环</code>实现：

```java
// 使用曾强for循环迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
for(String str : list){
    if(str.length()>3)
        System.out.println(str);
}
```

现在使用<code style="color:#b30049;background-color:#fdf5f5">forEach()</code>方法结合匿名内部类，可以这样实现：

```java
// 使用forEach()结合匿名内部类迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.forEach(new Consumer<String>(){
    @Override
    public void accept(String str){
        if(str.length()>3)
            System.out.println(str);
    }
});
```

上述代码调用<code style="color:#b30049;background-color:#fdf5f5">forEach()</code>方法，并使用匿名内部类实现<code style="color:#b30049;background-color:#fdf5f5">Comsumer</code>接口。到目前为止我们没看到这种设计有什么好处，但是不要忘记Lambda表达式，使用Lambda表达式实现如下：

```java
// 使用forEach()结合Lambda表达式迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.forEach( str -> {
        if(str.length()>3)
            System.out.println(str);
    });
```

上述代码给<code style="color:#b30049;background-color:#fdf5f5">forEach()</code>方法传入一个Lambda表达式，我们不需要知道<code style="color:#b30049;background-color:#fdf5f5">accept()</code>方法，也不需要知道<code style="color:#b30049;background-color:#fdf5f5">Consumer</code>接口，类型推导帮我们做了一切。

#### removeIf()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">boolean removeIf(Predicate<? super E> filter)</code>，作用是**删除容器中所有满足`filter`指定条件的元素**，其中<code style="color:#b30049;background-color:#fdf5f5">Predicate</code>是一个函数接口，里面只有一个待实现方法<code style="color:#b30049;background-color:#fdf5f5">boolean test(T t)</code>，同样的这个方法的名字根本不重要，因为用的时候不需要书写这个名字。

需求：*假设有一个字符串列表，需要删除其中所有长度大于3的字符串。*

我们知道如果需要在迭代过程冲对容器进行删除操作必须使用迭代器，否则会抛出<code style="color:#b30049;background-color:#fdf5f5">ConcurrentModificationException</code>，所以上述任务传统的写法是：

```java
// 使用迭代器删除列表元素
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
Iterator<String> it = list.iterator();
while(it.hasNext()){
    if(it.next().length()>3) // 删除长度大于3的元素
        it.remove();
}
```

现在使用<code style="color:#b30049;background-color:#fdf5f5">removeIf()</code>方法结合匿名内部类，我们可是这样实现：

```java
// 使用removeIf()结合匿名名内部类实现
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.removeIf(new Predicate<String>(){ // 删除长度大于3的元素
    @Override
    public boolean test(String str){
        return str.length()>3;
    }
});
```

上述代码使用<code style="color:#b30049;background-color:#fdf5f5">removeIf()</code>方法，并使用匿名内部类实现<code style="color:#b30049;background-color:#fdf5f5">Precicate</code>接口。相信你已经想到用Lambda表达式该怎么写了：

```java
// 使用removeIf()结合Lambda表达式实现
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.removeIf(str -> str.length()>3); // 删除长度大于3的元素
```

使用Lambda表达式不需要记忆`Predicate`接口名，也不需要记忆`test()`方法名，只需要知道此处需要一个返回布尔类型的Lambda表达式就行了。

#### replaceAll()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">void replaceAll(UnaryOperator\<E> operator)</code>，作用是**对每个元素执行`operator`指定的操作，并用操作结果来替换原来的元素**。其中<code style="color:#b30049;background-color:#fdf5f5">UnaryOperator</code>是一个函数接口，里面只有一个待实现函数<code style="color:#b30049;background-color:#fdf5f5">T apply(T t)</code>。

需求：*假设有一个字符串列表，将其中所有长度大于3的元素转换成大写，其余元素不变。*

Java7及之前似乎没有优雅的办法：

```java
// 使用下标实现元素替换
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
for(int i=0; i<list.size(); i++){
    String str = list.get(i);
    if(str.length()>3)
        list.set(i, str.toUpperCase());
}
```

使用<code style="color:#b30049;background-color:#fdf5f5">replaceAll()</code>方法结合匿名内部类可以实现如下：

```java
// 使用匿名内部类实现
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.replaceAll(new UnaryOperator<String>(){
    @Override
    public String apply(String str){
        if(str.length()>3)
            return str.toUpperCase();
        return str;
    }
});
```

上述代码调用<code style="color:#b30049;background-color:#fdf5f5">replaceAll()</code>方法，并使用匿名内部类实现<code style="color:#b30049;background-color:#fdf5f5">UnaryOperator</code>接口。我们知道可以用更为简洁的Lambda表达式实现：

```java
// 使用Lambda表达式实现
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.replaceAll(str -> {
    if(str.length()>3)
        return str.toUpperCase();
    return str;
});
```

#### sort()

该方法定义在<code style="color:#b30049;background-color:#fdf5f5">List</code>接口中，方法签名为<code style="color:#b30049;background-color:#fdf5f5">void sort(Comparator<? super E> c)</code>，该方法**根据`c`指定的比较规则对容器元素进行排序**。<code style="color:#b30049;background-color:#fdf5f5">Comparator</code>接口我们并不陌生，其中有一个方法<code style="color:#b30049;background-color:#fdf5f5">int compare(T o1, T o2)</code>需要实现，显然该接口是个函数接口。

需求：*假设有一个字符串列表，按照字符串长度增序对元素排序。*

由于Java7以及之前`sort()`方法在`Collections`工具类中，所以代码要这样写：

```java
// Collections.sort()方法
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
Collections.sort(list, new Comparator<String>(){
    @Override
    public int compare(String str1, String str2){
        return str1.length()-str2.length();
    }
});
```

现在可以直接使用<code style="color:#b30049;background-color:#fdf5f5">List.sort()方法</code>，结合Lambda表达式，可以这样写：

```java
// List.sort()方法结合Lambda表达式
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.sort((str1, str2) -> str1.length()-str2.length());
```

#### spliterator()

方法签名为<code style="color:#b30049;background-color:#fdf5f5">Spliterator\<E> spliterator()</code>，该方法返回容器的**可拆分迭代器**。从名字来看该方法跟<code style="color:#b30049;background-color:#fdf5f5">iterator()</code>方法有点像，我们知道<code style="color:#b30049;background-color:#fdf5f5">iterator()</code>是用来迭代容器的，<code style="color:#b30049;background-color:#fdf5f5">Spliterator</code>也有类似作用，但二者有如下不同：

1. <code style="color:#b30049;background-color:#fdf5f5">Spliterator</code>既可以像<code style="color:#b30049;background-color:#fdf5f5">iterator()</code>那样逐个迭代，也可以批量迭代。批量迭代可以降低迭代的开销。
2. <code style="color:#b30049;background-color:#fdf5f5">Spliterator</code>是可拆分的，一个<code style="color:#b30049;background-color:#fdf5f5">Spliterator</code>可以通过调用<code style="color:#b30049;background-color:#fdf5f5">Spliterator\<T> trySplit()</code>方法来尝试分成两个。一个是<code style="color:#b30049;background-color:#fdf5f5">this</code>，另一个是新返回的那个，这两个迭代器代表的元素没有重叠。

可通过（多次）调用<code style="color:#b30049;background-color:#fdf5f5">Spliterator.trySplit()</code>方法来分解负载，以便多线程处理。

#### stream()和parallelStream()

<code style="color:#b30049;background-color:#fdf5f5">stream()</code>和<code style="color:#b30049;background-color:#fdf5f5">parallelStream()</code>分别**返回该容器的`Stream`视图表示**，不同之处在于<code style="color:#b30049;background-color:#fdf5f5">parallelStream()</code>返回并行的<code style="color:#b30049;background-color:#fdf5f5">stream()</code>。<code style="color:#b30049;background-color:#fdf5f5">stream()</code>**是Java函数式编程的核心类**，我们会在后面章节中学习。

----------------

### Map中的新方法

相比`Collection`，Map`中加入了更多的方法，我们以`HashMap`为例来逐一探秘。了解[Java7`HashMap`实现原理](https://github.com/CarpenterLee/JCFInternals/blob/master/markdown/6-HashSet and HashMap.md)，将有助于理解下文。

#### forEach()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">void forEach(BiConsumer<? super K,? super V> action)</code>，作用是**对`Map`中的每个映射执行`action`指定的操作**，其中<code style="color:#b30049;background-color:#fdf5f5">BiConsumer</code>是一个函数接口，里面有一个待实现方法<code style="color:#b30049;background-color:#fdf5f5">void accept(T t, U u)</code>。`BinConsumer`接口名字和`accept()`方法名字都不重要，请不要记忆他们。

需求：*假设有一个数字到对应英文单词的Map，请输出Map中的所有映射关系．*

Java7以及之前经典的代码如下：

```java
// Java7以及之前迭代Map
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
for(Map.Entry<Integer, String> entry : map.entrySet()){
    System.out.println(entry.getKey() + "=" + entry.getValue());
}
```

使用<code style="color:#b30049;background-color:#fdf5f5">Map.forEach()</code>方法，结合匿名内部类，代码如下：

```java
// 使用forEach()结合匿名内部类迭代Map
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.forEach(new BiConsumer<Integer, String>(){
    @Override
    public void accept(Integer k, String v){
        System.out.println(k + "=" + v);
    }
});
```

上述代码调用<code style="color:#b30049;background-color:#fdf5f5">forEach()</code>方法，并使用匿名内部类实现<code style="color:#b30049;background-color:#fdf5f5">BiConsumer</code>接口。当然，实际场景中没人使用匿名内部类写法，因为有Lambda表达式：

```java
// 使用forEach()结合Lambda表达式迭代Map
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.forEach((k, v) -> System.out.println(k + "=" + v));
}
```

#### getOrDefault()

该方法跟Lambda表达式没关系，但是很有用。方法签名为<code style="color:#b30049;background-color:#fdf5f5">V getOrDefault(Object key, V defaultValue)</code>，作用是按照给定的<code style="color:#b30049;background-color:#fdf5f5">key</code>查询<code style="color:#b30049;background-color:#fdf5f5">Map</code>中对应的<code style="color:#b30049;background-color:#fdf5f5">value</code>，如果没有找到则返回<code style="color:#b30049;background-color:#fdf5f5">defaultValue</code>。使用该方法程序员可以省去查询指定键值是否存在的麻烦．

需求；*假设有一个数字到对应英文单词的Map，输出4对应的英文单词，如果不存在则输出NoValue*

```java
// 查询Map中指定的值，不存在时使用默认值
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
// Java7以及之前做法
if(map.containsKey(4)){ // 1
    System.out.println(map.get(4));
}else{
    System.out.println("NoValue");
}
// Java8使用Map.getOrDefault()
System.out.println(map.getOrDefault(4, "NoValue")); // 2
```

#### putIfAbsent()

该方法跟Lambda表达式没关系，但是很有用。方法签名为<code style="color:#b30049;background-color:#fdf5f5">V putIfAbsent(K key, V value)</code>，作用是只有在**不存在`key`值的映射或映射值为`null`时**，才将`value`指定的值放入到`Map`中，否则不对`Map`做更改．该方法将条件判断和赋值合二为一，使用起来更加方便．

#### remove()

我们都知道`Map`中有一个<code style="color:#b30049;background-color:#fdf5f5">remove(Object key)</code>方法，来根据指定`key`值删除`Map`中的映射关系；Java8新增了<code style="color:#b30049;background-color:#fdf5f5">remove(Object key, Object value)</code>方法，只有在当前`Map`中**`key`正好映射到`value`时**才删除该映射，否则什么也不做．

#### replace()

在Java7及以前，要想替换`Map`中的映射关系可通过<code style="color:#b30049;background-color:#fdf5f5">put(K key, V value)</code>方法实现，该方法总是会用新值替换原来的值．为了更精确的控制替换行为，Java8在`Map`中加入了两个<code style="color:#b30049;background-color:#fdf5f5">replace()</code>方法，分别如下：

- <code style="color:#b30049;background-color:#fdf5f5">replace(K key, V value)</code>，只有在当前`Map`中**`key`的映射存在时**才用`value`去替换原来的值，否则什么也不做．
- <code style="color:#b30049;background-color:#fdf5f5">replace(K key, V oldValue, V newValue)</code>，只有在当前`Map`中**`key`的映射存在且等于`oldValue`时**才用`newValue`去替换原来的值，否则什么也不做．

#### replaceAll()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">replaceAll(BiFunction<? super K,? super V,? extends V> function)</code>，作用是对`Map`中的每个映射执行`function`指定的操作，并用`function`的执行结果替换原来的`value`，其中`BiFunction`是一个函数接口，里面有一个待实现方法`R apply(T t, U u)`．不要被如此多的函数接口吓到，因为使用的时候根本不需要知道他们的名字．

需求：*假设有一个数字到对应英文单词的Map，请将原来映射关系中的单词都转换成大写．*

Java7以及之前经典的代码如下：

```java
// Java7以及之前替换所有Map中所有映射关系
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
for(Map.Entry<Integer, String> entry : map.entrySet()){
    entry.setValue(entry.getValue().toUpperCase());
}
```

使用<code style="color:#b30049;background-color:#fdf5f5">replaceAll()</code>方法结合匿名内部类，实现如下：

```java
// 使用replaceAll()结合匿名内部类实现
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.replaceAll(new BiFunction<Integer, String, String>(){
    @Override
    public String apply(Integer k, String v){
        return v.toUpperCase();
    }
});
```

上述代码调用<code style="color:#b30049;background-color:#fdf5f5">replaceAll()</code>方法，并使用匿名内部类实现`BiFunction`接口。更进一步的，使用Lambda表达式实现如下：

```java
// 使用replaceAll()结合Lambda表达式实现
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "one");
map.put(2, "two");
map.put(3, "three");
map.replaceAll((k, v) -> v.toUpperCase());
```

简洁到让人难以置信．

#### merge()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction)</code>，作用是：

1. 如果<code style="color:#b30049;background-color:#fdf5f5">Map</code>中<code style="color:#b30049;background-color:#fdf5f5">key</code>对应的映射不存在或者为<code style="color:#b30049;background-color:#fdf5f5">null</code>，则将<code style="color:#b30049;background-color:#fdf5f5">value</code>（不能是`null`）关联到<code style="color:#b30049;background-color:#fdf5f5">key</code>上；
2. 否则执行<code style="color:#b30049;background-color:#fdf5f5">remappingFunction</code>，如果执行结果非<code style="color:#b30049;background-color:#fdf5f5">null</code>则用该结果跟<code style="color:#b30049;background-color:#fdf5f5">key</code>关联，否则在<code style="color:#b30049;background-color:#fdf5f5">Map</code>中删除<code style="color:#b30049;background-color:#fdf5f5">key</code>的映射．

参数中`BiFunction`函数接口前面已经介绍过，里面有一个待实现方法`R apply(T t, U u)`．

<code style="color:#b30049;background-color:#fdf5f5">merge()</code>方法虽然语义有些复杂，但该方法的用方式很明确，一个比较常见的场景是将新的错误信息拼接到原来的信息上，比如：

```java
map.merge(key, newMsg, (v1, v2) -> v1+v2);
```

#### compute()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction)</code>，作用是把<code style="color:#b30049;background-color:#fdf5f5">remappingFunction</code>的计算结果关联到<code style="color:#b30049;background-color:#fdf5f5">key</code>上，如果计算结果为<code style="color:#b30049;background-color:#fdf5f5">null</code>，则在<code style="color:#b30049;background-color:#fdf5f5">Map</code>中删除<code style="color:#b30049;background-color:#fdf5f5">key</code>的映射．

要实现上述<code style="color:#b30049;background-color:#fdf5f5">merge()</code>方法中错误信息拼接的例子，使用<code style="color:#b30049;background-color:#fdf5f5">compute()</code>代码如下：

```java
map.compute(key, (k,v) -> v==null ? newMsg : v.concat(newMsg));
```

#### computeIfAbsent()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">V computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction)</code>，作用是：只有在当前<code style="color:#b30049;background-color:#fdf5f5">Map</code>中**不存在`key`值的映射或映射值为`null`时**，才调用<code style="color:#b30049;background-color:#fdf5f5">mappingFunction</code>，并在<code style="color:#b30049;background-color:#fdf5f5">mappingFunction</code>执行结果非<code style="color:#b30049;background-color:#fdf5f5">null</code>时，将结果跟<code style="color:#b30049;background-color:#fdf5f5">key</code>关联．

<code style="color:#b30049;background-color:#fdf5f5">Function</code>是一个函数接口，里面有一个待实现方法<code style="color:#b30049;background-color:#fdf5f5">R apply(T t)</code>．

<code style="color:#b30049;background-color:#fdf5f5">computeIfAbsent()</code>常用来对<code style="color:#b30049;background-color:#fdf5f5">Map</code>的某个<code style="color:#b30049;background-color:#fdf5f5">key</code>值建立初始化映射．比如我们要实现一个多值映射，`Map`的定义可能是<code style="color:#b30049;background-color:#fdf5f5">Map<K,Set\<V>></code>，要向`Map`中放入新值，可通过如下代码实现：

```java
Map<Integer, Set<String>> map = new HashMap<>();
// Java7及以前的实现方式
if(map.containsKey(1)){
    map.get(1).add("one");
}else{
    Set<String> valueSet = new HashSet<String>();
    valueSet.add("one");
    map.put(1, valueSet);
}
// Java8的实现方式
map.computeIfAbsent(1, v -> new HashSet<String>()).add("yi");
```

使用<code style="color:#b30049;background-color:#fdf5f5">computeIfAbsent()</code>将条件判断和添加操作合二为一，使代码更加简洁．

#### computeIfPresent()

该方法签名为<code style="color:#b30049;background-color:#fdf5f5">V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction)</code>，作用跟<code style="color:#b30049;background-color:#fdf5f5">computeIfAbsent()</code>相反，即，只有在当前`Map`中**存在`key`值的映射且非`null`时**，才调用`remappingFunction`，如果`remappingFunction`执行结果为`null`，则删除`key`的映射，否则使用该结果替换`key`原来的映射．

这个函数的功能跟如下代码是等效的：

```java
// Java7及以前跟computeIfPresent()等效的代码
if (map.get(key) != null) {
    V oldValue = map.get(key);
    V newValue = remappingFunction.apply(key, oldValue);
    if (newValue != null)
        map.put(key, newValue);
    else
        map.remove(key);
    return newValue;
}
return null;
```

1. Java8为容器新增一些有用的方法，这些方法有些是为**完善原有功能**，有些是为**引入函数式编程**，学习和使用这些方法有助于我们写出更加简洁有效的代码．
2. **函数接口**虽然很多，但绝大多数时候我们根本不需要知道它们的名字，书写Lambda表达式时类型推断帮我们做了一切．

-----------------------

# 方法引用

方法引用（<code style="color:#b30049;background-color:#fdf5f5">Method Reference</code>）是用来直接访问类或者实例已经存在的方法或者构造方法。方法引用提供了一种引用而不执行方法的方式，它需要由兼容的函数式接口构成的目标类型上下文。计算时，方法引用会创建函数式接口的一个实例。

当Lambda表达式中只是执行一个方法调用时，不用Lambda表达式，直接通过方法引用的形式可读性更高一些。

作用

- 方法引用的唯一用途是支持Lambda的简写。
- 方法引用提高了代码的可读性，也使逻辑更加清晰。

组成

- 使用<code style="color:#b30049;background-color:#fdf5f5">::</code>操作符将方法名和对象或类的名字分隔开。<code style="color:#b30049;background-color:#fdf5f5">::</code>是域操作符（也可以称作定界符、分隔符）。

| **方法引用**                                                 | 等价的Lambda表达式                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <code style="color:#b30049;background-color:#fdf5f5">String::valueOf</code> | <code style="color:#b30049;background-color:#fdf5f5">x -> String.valueOf(x)</code> |
| <code style="color:#b30049;background-color:#fdf5f5">Object::toString</code> | <code style="color:#b30049;background-color:#fdf5f5">x -> x.toString()</code> |
| <code style="color:#b30049;background-color:#fdf5f5">x::toString</code> | <code style="color:#b30049;background-color:#fdf5f5">() -> x.toString()</code> |
| <code style="color:#b30049;background-color:#fdf5f5">ArrayList::new</code> | <code style="color:#b30049;background-color:#fdf5f5">() -> new ArrayList<>()</code> |


方法引用的类型归结如下：

| 类型                     | 例子                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 静态方法引用             | <code style="color:#b30049;background-color:#fdf5f5">ClassName::methodName</code> |
| 指定对象实例方法引用     | <code style="color:#b30049;background-color:#fdf5f5">instanceRef::methodName</code> |
| 特定类型任意对象方法引用 | <code style="color:#b30049;background-color:#fdf5f5">ContainingType::methodName</code> |
| 超类方法引用             | <code style="color:#b30049;background-color:#fdf5f5">supper::methodName</code> |
| 构造器方法引用           | <code style="color:#b30049;background-color:#fdf5f5">ClassName::new</code> |
| 数组构造器方法引用       | <code style="color:#b30049;background-color:#fdf5f5">TypeName[]::new</code> |


可见其基本形式是：<code style="color:#b30049;background-color:#fdf5f5">方法容器::方法名称或者关键字</code>。


举一些基本的使用例子：

## 静态方法引用

```java
public class StaticMethodRef {

    public static void main(String[] args) {
        Function<String, Integer> function = StaticMethodRef::staticMethod;
        // 等同于
        // Function<String, Integer> function1 = (String s) -> StaticMethodRef.staticMethod(s);
        Integer result = function.apply("10086");
        // 10086
        System.out.println(result);
    }

    public static Integer staticMethod(String value) {
        return Integer.parseInt(value);
    }

}
```

## 指定对象实例方法引用

```java
public class ParticularInstanceRef {

    public Integer refMethod(String value) {
        return Integer.parseInt(value);
    }

    public static void main(String[] args) {
        ParticularInstanceRef ref = new ParticularInstanceRef();
        Function<String, Integer> function = ref::refMethod;
        // 等同于
        // Function<String,Integer> function1 = (String s) -> ref.refMethod(s);
        Integer result = function.apply("10086");
        // 10086
        System.out.println(result);
    }
}
```

## 特定类型任意对象方法引用

```java
String[] stringArray = {"C", "a", "B"};
Arrays.sort(stringArray, String::compareToIgnoreCase);
// 等同于
// Arrays.sort(stringArray, (String s1, String s2) -> s1.compareToIgnoreCase(s2));
// [a, B, C]
System.out.println(Arrays.toString(stringArray));
```

## 超类方法引用

```java
public class SupperRef {

    public static void main(String[] args) throws Exception {
        Sub sub = new Sub();
        // 10086
        System.out.println(sub.refMethod("10086"));
    }

    private static class Supper {
        private Integer supperRefMethod(String value) {
            return Integer.parseInt(value);
        }
    }

    private static class Sub extends Supper {
        private Integer refMethod(String value) {
            Function<String, Integer> function = super::supperRefMethod;
            // 等同于
            // Function<String,Integer> function1 = (String s) -> super.supperRefMethod(s);
            return function.apply(value);
        }
    }

}
```

## 构造器方法引用

```java
public class ConstructorRef {

    public static void main(String[] args) {
        Function<String, Person> function = Person::new;
        // 等同于
        // Function<String,Person> function1 = (String s) -> new Person(s);
        Person person = function.apply("thinkwon");
        // doge
        System.out.println(person.getName());
    }

    private static class Person {

        private final String name;

        public Person(String name) {
            this.name = name;
        }

        public String getName() {
            return name;
        }
    }

}
```

## 数组构造器方法引用

```java
Function<Integer, Integer[]> function = Integer[]::new;
// 等同于
// Function<Integer, Integer[]> function1 = (Integer i) -> new Integer[i];
Integer[] array = function.apply(10);
// [null, null, null, null, null, null, null, null, null, null]
System.out.println(Arrays.toString(array));

```

----------------------

# Filter & Predicate

常规用法

```java
public static void main(args[]){
    List languages = Arrays.asList("Java", "Scala", "C++", "Haskell", "Lisp");
 
    System.out.println("Languages which starts with J :");
    filter(languages, (str)->str.startsWith("J"));
 
    System.out.println("Languages which ends with a ");
    filter(languages, (str)->str.endsWith("a"));
 
    System.out.println("Print all languages :");
    filter(languages, (str)->true);
 
    System.out.println("Print no language : ");
    filter(languages, (str)->false);
 
    System.out.println("Print language whose length greater than 4:");
    filter(languages, (str)->str.length() > 4);
}
 
public static void filter(List names, Predicate condition) {
    names.stream().filter((name) -> (condition.test(name))).forEach((name) -> {
        System.out.println(name + " ");
    });
}
  
```

多个Predicate组合filter

```java
// 可以用and()、or()和xor()逻辑函数来合并Predicate，
// 例如要找到所有以J开始，长度为四个字母的名字，你可以合并两个Predicate并传入
Predicate<String> startsWithJ = (n) -> n.startsWith("J");
Predicate<String> fourLetterLong = (n) -> n.length() == 4;
names.stream()
    .filter(startsWithJ.and(fourLetterLong))
    .forEach((n) -> System.out.print("nName, which starts with 'J' and four letter long is : " + n));
```