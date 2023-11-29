---
title: Optional
date: 2023-09-25 10:29:54
tags:
- JavaWeb
- Java8
- Null
categories: Java8
---

# 概述

{% note info no-icon %}

"Null sucks." \- Doug Lea

{% endnote %}

Doug Lea 是一位美国的计算机科学家，他是 Java 平台的并发和集合框架的主要设计者之一。他在 2014 年的一篇文章中说过：“Null sucks.”，意思是 null 很糟糕。他认为 null 是一种不明确的表示，它既可以表示一个值不存在，也可以表示一个值未知，也可以表示一个值无效。这样就会导致很多逻辑错误和空指针异常，给程序员带来很多麻烦。他建议使用 Optional 类来封装可能为空的值，从而提高代码的可读性和健壮性。

null 引用是一种表示一个对象变量没有指向任何对象的方式，它是 Java 语言中的一个特殊值，也是导致空指针异常（NullPointerException）的主要原因。虽然 null 引用可以用来表示一个值不存在或未知，也可以用来节省内存空间。但是它也不符合面向对象的思想，因为它不是一个对象，不能调用任何方法或属性。

可以看到，null 引用并不好，我们应该尽量避免使用 null，那么我们该怎么避免 null 引用引起的逻辑错误和运行时异常嘞？

其实这个问题 Java 的设计者也知道，于是他们在 Java8 之后设计引入了 Optional 类解决这个问题，本文将给大家详细介绍下 Optional 类的设计目的以及使用方法。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2023/optional/java_optional.png)

-------

# Optional 类是什么？

`Optional` 类是 java 8 中引入的一个新的类，它的作用是封装一个可能为空的值，从而避免空指针异常（`NullPointerException`）。`Optional` 类可以看作是一个容器，它可以包含一个非空的值，也可以为空。`Optional` 类提供了一些方法，让我们可以更方便地处理可能为空的值，而不需要显式地进行空值检查或者使用 null。

# Optional 类的设计

Optional 类的设计是基于函数式编程的思想，它借鉴了 Scala 和 Haskell 等语言中的 Option 类型。Optional 类实现了 java.util.function 包中的 Supplier、Consumer、Predicate、Function 等接口，这使得它可以和 lambda 表达式或者方法引用一起使用，形成更简洁和优雅的代码。

Optional 类被 final 修饰，因此它是一个不可变的类，它有两个静态方法用于创建 Optional 对象。

## `Optional.empty()`

`Optional.empty` 表示一个空的 `Optional` 对象，它不包含任何值。

```java
// 创建一个空的 Optional 对象
Optional<String> empty = Optional.empty();
```

## `Optional.of(T value)`

`Optional.of` 表示一个非空的 `Optional` 对象，它包含一个非空的值。

```java
// 创建一个非空的 Optional 对象
Optional<String> hello = Optional.of("Hello");
```

## `Optional.ofNullable(T value)`

注意，如果我们使用 `Optional.of` 方法传入一个 `null` 值，会抛出 `NullPointerException`。如果我们不确定一个值是否为空，可以使用 `Optional.ofNullable` 方法，它会根据值是否为空，返回一个相应的 `Optional` 对象。例如：

```java
// 创建一个可能为空的 Optional 对象
Optional<String> name = Optional.ofNullable("Hello");
```

-------

# Optional 对象的使用方法

`Optional` 对象提供了一些方法，让我们可以更方便地处理可能为空的值，而不需要显式地进行空值检查或者使用 `null`。以下是一些常用的方法。

## `isPresent()`

```java
Optional<Person> personOptional = daoService.selectOne(person1);
if (personOptional.isPresent()) {
  System.out.println("查询不为空");
} else {
  System.out.println("查询为空");
}
```

## `get()`

如果 `Optional` 对象包含一个非空的值，返回该值，否则抛出 `NoSuchElementException` 异常。

```java
Optional<Person> personOptional = daoService.selectOne(person1);
if (personOptional.isPresent()) {
  Person person = personOptional.get();
  System.out.println(person);
} else {
	System.out.println("查询为空");
}
```

## `ifPresent(Consumer<? super T> action)`

```java
Optional<Person> personOptional = daoService.selectOne(person1);
personOptional.ifPresent(person -> System.out.println(person.getName()));
```

## `orElse(T other)`

如果 `Optional` 对象包含一个非空的值，返回该值，否则返回给定的默认值。

```java
Optional<Person> personOptional = daoService.selectOne(person1);
Person person = personOptional.orElse(new Person());
```

## `orElseGet(Supplier<? extends T> supplier)`

如果 `Optional` 对象包含一个非空的值，返回该值，否则返回由给定的供应者操作生成的值。

当`orElse`的参数是间接计算得来的时候，使用`orElse`可能导致`NullPointerException`，当`orElse`的参数是间接计算得来的时候。虽然这种说法有点牵强（因为并不是`orElse`导致了空指针异常），但是使用`orElseGet`确实可以避免这种情况。

两者的明显（也是唯一）区别是前者需要传递的参数是一个值（通常是为空时的默认值），后者传递的是一个函数。我们看一下源代码：

```java
/**
 * Return the value if present, otherwise return {@code other}.
 */
public T orElse(T other) {
    return value != null ? value : other;
}

/**
 * Return the value if present, otherwise invoke {@code other} and return
 * the result of that invocation.
 */
public T orElseGet(Supplier<? extends T> other) {
    return value != null ? value : other.get();
}
```

简单解释为，我们使用`Optional`包装的变量如果不为空，返回它本身，否则返回我们传递进去的值。`orElseGet`参数为`Supplier`接口，它是一个函数式接口，它的形式是这样的：`() -> { return computedResult }`，即*入参为空，有返回值（任意类型的）*。

我们可能考虑的问题是：何时使用`orElse`和何时使用`orElseGet`？看起来可以使用`orElseGet`的时候，使用`orElse`也可以代替（因为`Supplier`接口没有入参），而且使用`orElseGet`还需要将计算过程额外包装成一个 lambda 表达式。

一个关键的点是，使用`Supplier`能够做到*懒计算*，即使用`orElseGet`时。它的好处是，**只有在需要的时候才会计算结果**。具体到我们的场景，使用`orElse`的时候，每次它都会执行计算结果的过程，而对于`orElseGet`，只有`Optional`中的值为空时，它才会计算备选结果。这样做的好处是可以**避免提前计算结果的风险**。

```java
Person person1 = new Person();
person1.setName("zhangsan");
Person person2 = null;

String orElse = Optional.ofNullable(person1.getName()).orElse(person2.getName()); // 报错，空指针

String orElseGet = Optional.ofNullable(person1.getName()).orElseGet(() -> person2.getName()); // 不报错

System.out.println(orElseGet);
```

## `map(Function<? super T,? extends U> mapper)`

如果 `Optional` 对象包含一个非空的值，对该值应用给定的映射函数，返回一个包含映射结果的 Optional 对象，否则返回一个空的 `Optional` 对象。

```java
Person person1 = new Person();
person1.setName("zhangsan");
Optional<Person> person = Optional.ofNullable(person1);
Optional<String> optionalS = person.map(p -> p.getName().toUpperCase());
if (optionalS.isPresent()) {
  String upperName = optionalS.get();
  System.out.println(upperName);
}
// ZHANGSAN
```

## `flatMap(Function<? super T,Optional<U>> mapper)`

### map和flatMap的区别:

`map`会将传入的`Function`函数的结果进行封装，先看源码：

```java
public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent())
        return empty();
    else {
        return Optional.ofNullable(mapper.apply(value));//会使用Optional的ofNullable方法包装Function函数返回的值
    }
}
```

`flatMap`会直接返回`Function`函数执行的结果，看源码：

```java
public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent())
        return empty();
    else {
        return Objects.requireNonNull(mapper.apply(value));//直接返回Function执行的结果

    }
}
```

所以在使用的时候，下面这段代码会报错。

```java
public static String getStudentName(School school){
        return Optional.ofNullable(school)
                .map(School::getTearch)
                .map(Tearch::getStudent)
                .map(Student::getName)
                .orElse("false");
}
```

而下面这段代码不会报错。

```java
public static String getStudentName(School school){
        return Optional.ofNullable(school)
                .flatMap(School::getTearch)
                .flatMap(Tearch::getStudent)
                .map(Student::getName).orElse("false");
}
```

## `filter(Predicate<? super T> predicate)`

如果 `Optional` 对象包含一个非空的值，并且该值满足给定的谓词条件，返回包含该值的 `Optional` 对象，否则返回一个空的 `Optional` 对象。

```java
Person person1 = new Person();
person1.setName("zhangsan");
Optional<Person> optionalPerson = Optional.of(person1);
Optional<Person> optional = optionalPerson.filter(person -> "ZHANGSAN".equals(person.getName()));)
if (optional.isPresent()){
	System.out.println(optional);
} else {
	System.out.println("optional为空");
}
```

