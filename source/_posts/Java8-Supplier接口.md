---
title: Java8 Supplier接口
date: 2023-12-15 10:32:23
tags: 
- Supplier
- 函数式接口
categories: Java8
---

Java `Supplier` 是一个代表结果提供者的函数式接口。 `Supplier`的函数方法是`get()`。 `Supplier` 可以使用 lambda 表达式或方法引用或默认构造函数进行实例化。 `Supplier` 已在 Java 8 中引入，属于 `java.util.function` 包。 `Supplier`函数式接口源码如下。

```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
```

`Supplier`是Java8配合Lambda表达式和函数式接口编程组合使用的一个接口。

接口`Supplier<T>` 最适合表示工厂。带有`Supplier<T>` 的方法，通常应该限制输入工厂的类型参数使用有限制的通配符类型，以便客户端可以传入工厂，来创建制定类型的任意子类。

简而言之，`Supplier<T>`就是来创建对象的。

```java
public static void main(String[] args) {

        Supplier<Double> doubleSupplier = Math::random;
        Double random1 = doubleSupplier.get();
        Double random2 = doubleSupplier.get();
        Double random3 = doubleSupplier.get();
        System.out.println(random1);
        System.out.println(random2);
        System.out.println(random3);

}
```

Out:

#### 使用Lambda表达式实例化Supplier

函数式接口`Supplier`的`get()`仅仅返回值，而不接受任何参数，所以我们使用lambda表达式时其参数部分为空。

```java
public static void main(String[] args) {
    Supplier<String> strSupplier = () -> "hello world";
    System.out.println(strSupplier.get());

    Random random = new Random();
    Supplier<Integer> integerSupplier = () -> random.nextInt(100);
    System.out.println(integerSupplier.get());
    System.out.println(integerSupplier.get());
}
```

Out:

```
hello world
65
54
```

#### 使用方法引用实例化Supplier

```java
public static void main(String[] args) {
    Supplier<Person> personSupplier = Person::new;
    Person person1 = personSupplier.get();
    person1.setName("张三");
    Person person2 = personSupplier.get();
    person2.setName("李四");
    System.out.println(person1);
    System.out.println(person2);

    Random random = new Random();
    Supplier<Integer> randomSupplier = random::nextInt;
    System.out.println(randomSupplier.get());
    System.out.println(randomSupplier.get());
}
```

Out:

```
com.design.patterns.factory.Person{name='张三', sex='null', age='null'}
com.design.patterns.factory.Person{name='李四', sex='null', age='null'}
-1841962637
567349876
```

