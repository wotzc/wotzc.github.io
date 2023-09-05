---
title: Java 8 Stream使用
date: 2022-01-09 13:25:39
tags: 
- Stream
- 流
- java8
Categories: Java8
---

# Stream简介

我们先来看看Java里面是怎么定义Stream的：

{% note no-icon primary %}

A sequence of elements supporting sequential and parallel aggregate operations.

{% endnote %}

我们来解读一下上面的那句话：

1. Stream是元素的集合，这点让Stream看起来用些类似Iterator；
2. 可以支持顺序和并行的对原Stream进行汇聚的操作；

Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。元素流在管道中经过中间操作（intermediate operation）的处理，最后由最终操作(terminal operation)得到前面处理的结果。

Stream有哪些特点：

- <code>元素的序列</code>：与集合一样可以访问里面的元素，集合讲的是数据，而流讲的是操作，比如：filter、map
- <code>源</code>: 流也需要又一个提供数据的源，顺序和生成时的顺序一致
- <code>数据的操作</code>：流支持类似于数据库的操作，支持顺序或者并行处理数据；上面的例子用流来实现会更加的简洁

```java
public List<Integer> getGt5Data() {
    return Stream.of(1, 7, 3, 8, 2, 4, 9)
            .filter(num -> num > 5)
            .collect(toList());
}
```

- <code>流水线操作</code>：很多流的方法本身也会返回一个流，这样可以把多个操作连接起来，形成流水线操作

- <code>内部迭代</code>：与以往的迭代不同，流使用的内部迭代，用户只需要专注于数据处理

- <code>只能遍历一次</code>： 遍历完成之后我们的流就已经消费完了，再次遍历的话会抛出异常

  <img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/java8Stream/JavaStream.png"  />

------

## 使用Stream

Java8中的Stream定义了很多方法，基本可以把他们分为两类：中间操作、终端操作；要使用一个流一般都需要三个操作：

1. 定义一个数据源
2. 定义中间操作形成流水线
3. 定义终端操作，执行流水线，生成计算结果

## 操作符

什么是操作符呢？操作符就是对数据进行的一种处理工作，一道加工程序；就好像工厂的工人对流水线上的产品进行一道加工程序一样。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/java8Stream/CoreStreamOperations.png"  />

**Stream**的操作符大体上分为两种：**中间操作符**和**终止操作符**

### 中间操作符

对于数据流来说，中间操作符在执行制定处理程序后，数据流依然可以传递给下一级的操作符。

中间操作符包含<code>8种</code>(排除了parallel,sequential,这两个操作并不涉及到对数据流的加工操作)：

> 1. <code>map</code>(mapToInt,mapToLong,mapToDouble) 转换操作符，把比如<code>A->B</code>，这里默认提供了转int，long，double的操作符。
> 2. <code>flatmap</code>(flatmapToInt,flatmapToLong,flatmapToDouble) 拍平操作比如把 int[]{2,3,4} 拍平 变成 2，3，4 也就是从原来的一个数据变成了3个数据，这里默认提供了拍平成int,long,double的操作符。
> 3. <code>limit</code>限流操作，比如数据流中有10个 我只要出前3个就可以使用。
> 4. <code>distint</code>去重操作，对重复元素去重，底层使用了equals方法。
> 5. <code>filter</code>过滤操作，把不想要的数据过滤。
> 6. <code>peek</code>挑出操作，如果想对数据进行某些操作，如：读取、编辑修改等。
> 7. <code>skip</code>跳过操作，跳过某些元素。
> 8. <code>sorted</code>(unordered)排序操作，对元素排序，前提是实现Comparable接口，当然也可以自定义比较器。

### 终止操作符

数据经过中间加工操作，就轮到终止操作符上场了；终止操作符就是用来对数据进行收集或者消费的，数据到了终止操作这里就不会向下流动了，终止操作符只能使用一次。

> 1. <code>collect</code>收集操作，将所有数据收集起来，这个操作非常重要，官方的提供的Collectors 提供了非常多收集器，可以说Stream 的核心在于Collectors。
> 2. <code>count</code>统计操作，统计最终的数据个数。
> 3. <code>findFirst</code>、<code>findAny</code>查找操作，查找第一个、查找任何一个 返回的类型为Optional。
> 4. <code>noneMatch</code>、<code>allMatch</code>、<code>anyMatch</code>匹配操作，数据流中是否存在符合条件的元素 返回值为bool 值。
> 5. <code>min</code>、<code>max</code>最值操作，需要自定义比较器，返回数据流中最大最小的值。
> 6. <code>reduce </code>约操作，将整个数据流的值规约为一个值，count、min、max底层就是使用reduce。
> 7. <code>forEach</code>、<code>forEachOrdered</code>遍历操作，这里就是对最终的数据进行消费了。
> 8. <code>toArray</code>数组操作，将数据流的元素转换成数组。

这里只介绍了Stream，并没有涉及到**IntStream**、**LongStream**、**DoubleStream**，这三个流实现了一些特有的操作符，这里不做介绍。

-------------

# 创建流Stream

## Stream.of()

通过`Stream类`中的静态方法 <code>of()</code>

```java
Stream<Integer> stream = Stream.of(1,2,3,4,5,6,7,8,9);
stream.forEach(p -> System.out.println(p));
```

## Stream.of(array)

```java
Stream<Integer> stream = Stream.of( new Integer[]{1,2,3,4,5,6,7,8,9} );
stream.forEach(p -> System.out.println(p));
```

## List.stream()

通过`Collection 系列集合`提供的<code>串行流：stream()</code>、<code>并行流： paralleStream()</code>

```java
List<Integer> list = new ArrayList<Integer>();

for(int i = 1; i< 10; i++){
      list.add(i);
}

Stream<Integer> stream = list.stream();
stream.forEach(p -> System.out.println(p));
```

## Stream.generate() or Stream.iterate()

**使用Stream类的静态方法 iterate 创建无限流**
iterate方法：

Stream\<T> iterate(final T seed, final UnaryOperator\<T> f)

参数 seed 起始值，UnaryOperator 函数式接口 继承Function<T,T> 此时参数类型符合返回值类型一致

```java
Stream<Integer> stream4 = Stream.iterate(0, (x) -> x + 2);
stream4.limit(5).forEach(System.out::println);
```

**使用`Stream`类的静态方法 `generate`创建`无限流`**

generate方法参数为`Supplier<T>` `供给型接口`

```java
Stream<Integer> randomNumbers = Stream
      .generate(() -> (new Random()).nextInt(100));

randomNumbers.limit(20).forEach(System.out::println);
```

## Stream of String chars or tokens

```java
IntStream stream = "12345_abcdefg".chars();
stream.forEach(p -> System.out.println(p));
```

------

# Stream中间操作

中间操作会返回另外一个流，这样可以让多个操作连接起来形成一个流水线的操作，{%	label danger @只要不触发终端操作，那么这个中间操作都不会实际执行。	%}

**注意点**：

- 若只有中间操作，则`不会执行`
- 只有终止操作执行后，所有的中间操作`一次执行`，此时就称为`延迟加载`或者`惰性求值`

```java
		//取age>30的Stu元素
    //若只有中间操作，则不会执行
    Stream<Stu> stuStream = stuList.stream().filter((i) -> {
        System.out.println("验证是否是延迟加载");
        return  i.getAge() > 40;
    });
```

{%	label danger @此时只有中间操作，无终止操作，无结果，控制台无输出。	%}

在继续之前，让我们先构建一个`List`字符串。我们将在此列表上构建我们的示例，以便于关联和理解。

```java
List<String> memberNames = new ArrayList<>();
memberNames.add("Amitabh");
memberNames.add("Shekhar");
memberNames.add("Aman");
memberNames.add("Rahul");
memberNames.add("Shahrukh");
memberNames.add("Salman");
memberNames.add("Yana");
memberNames.add("Lokesh");
```

## filter()

该<code>filter()</code>方法接受一个Predicate来过滤流的所有元素。此操作是中间操作，它使我们能够对结果调用另一个流操作（例如forEach()）。

Lambda表达式写法：

```java
memberNames.stream().filter((s) -> s.startsWith("A"))
                    .forEach(System.out::println);
```

非Lambda表达式写法：

```java
memberNames.stream().filter((s) -> {
    return s.startsWith("A");
}).forEach(System.out::println);
```

程序输出：

```bash
Amitabh
Aman
```

## map()

<code>map()</code>中间操作的流中的每个元素转换成经由给定功能的另一个对象。

以下示例将每个字符串转换为大写字符串。但我们也可以使用<code>map()</code>将对象转换为另一种类型。

```java
memberNames.stream().filter((s) -> s.startsWith("A"))
                  .map(String::toUpperCase)
                  .forEach(System.out::println);
```

程序输出：

```bash
AMITABH
AMAN
```

## flatmap()

flatMap()是两步过程，即**map() + Flattening**。它有助于转换`Collection<Collection<T>>`为`Collection<T>`

- flatmap 作用就是将元素拍平拍扁 ，将拍扁的元素重新组成Stream，并将这些Stream 串行合并成一条Stream

```java
flatMap() 示例
List<Integer> list1 = Arrays.asList(1,2,3);
List<Integer> list2 = Arrays.asList(4,5,6);
List<Integer> list3 = Arrays.asList(7,8,9);
 
List<List<Integer>> listOfLists = Arrays.asList(list1, list2, list3);
 
List<Integer> listOfAllIntegers = listOfLists.stream()
                            .flatMap(x -> x.stream())
                            .collect(Collectors.toList());
 
System.out.println(listOfAllIntegers);      //[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

## sorted()

对Stream的排序通过<code>sorted</code>进行，它比数组的排序更强之处在于你可以首先对Stream进行各类map、filter、limit、skip甚至distinct来减少元素数量后再排序，这能帮助程序明显缩短执行时间。

该<code>sorted()</code>方法是返回流的排序视图的中间操作。除非我们传递自定义Comparator ，否则流中的元素按自然顺序排序。

### 自然排序

```java
List<String> stringList = Arrays.asList("aa", "bb", "dd", "cc","哈哈","啊");
//根据String类中Comparable方式进行默认排序，即compare to()方法
stringList.stream()
        .sorted().forEach(System.out::println);
```

程序输出：

```bash
aa
bb
cc
dd
哈哈
啊
```

### 指定排序

```java
sorted(Comparator com)
```

- 根据`实现Comparator接口的指定方法`进行排序

```java
stuList.stream().sorted(
            (a,b) ->{
                if (a.getAge().equals(b.getAge())){
                    return a.getName().compareTo(b.getName());
                }else{
                    return a.getAge().compareTo(b.getAge());
                }
            }
    ).forEach(System.out::println);
```

## limit()

**语法**

```java
Stream<T> limit(long maxSize)
```

这里<code>maxSize</code>应该限制流的元素数量；并且方法返回值是一个新的，`Stream`由从原始流中挑选的元素组成。

**示例 1：Java 程序从无限的偶数流中获取前 10 个偶数**

```java
Stream<Integer> evenNumInfiniteStream = Stream.iterate(0, n -> n + 2);
    
    List<Integer> newList = evenNumInfiniteStream.limit(10)
                      .collect(Collectors.toList());
    System.out.println(newList);
```

程序输出。

```
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

## skip()

```java
方法语法
Stream<T> skip(long n)
```

返回一个`跳过前n个元素`的流，若流中`元素不足n个`，则返回一个`空流`。

如果为负，该方法可能会抛出**IllegalArgumentException**`n`。

**示例：从无限的偶数流中跳过前 5 个偶数，然后将接下来的 10 个偶数收集到一个新的流中。**

```java
Stream<Integer> evenNumInfiniteStream = Stream.iterate(0, n -> n + 2);
         
        List<Integer> newList = evenNumInfiniteStream
                .skip(5)
                .limit(10)
                .collect(Collectors.toList());
        System.out.println(newList);
```

程序输出

```
[10, 12, 14, 16, 18, 20, 22, 24, 26, 28]
```

## distinct()

<code>distinct()</code>去重原理为通过流所生成元素的<code>hashCode()</code>和<code>equals()</code>来去除`重复`元素

### 字符串或包装类型去重

很容易从一个字符串集合或包装类型集合中找到重复的元素，因为这些类型实现了`equals()`方法

**示例：使用 Stream 遍历所有`String`元素并使用终端操作将不同的`String`元素收集到另一个元素中。`List Stream.collect()`**

```java
// 查找所有不同的字符串
Collection<String> list = Arrays.asList("A", "B", "C", "D", "A", "B", "C");
 
// Get collection without duplicate i.e. distinct only
List<String> distinctElements = list.stream()
                        .distinct()
                        .collect(Collectors.toList());
 
//Let's verify distinct elements
System.out.println(distinctElements);
```

程序输出：

```bash
[A, B, C, D]
```

------

### 重写`equals()` 方法和`hashCode()` 方法的复杂类型去重

让我们为我们的示例创建一个 Person 类。它有三个领域：`id`，`fname`和`lname`。如果两个人相同，则他们`id`是相等的。

{% note primary no-icon %}
不要忘记覆盖该类型的`equals()`和`hashCode()`方法
{% endnote %}

```java
public class Person {
    private Integer id;
    private String fname;
    private String lname;
  
    public Person(Integer id, String fname, String lname) {
        super();
        this.id = id;
        this.fname = fname;
        this.lname = lname;
    }
 
    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getFname() {
        return fname;
    }

    public void setFname(String fname) {
        this.fname = fname;
    }

    public String getLname() {
        return lname;
    }

    public void setLname(String lname) {
        this.lname = lname;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Person other = (Person) obj;
        return Objects.equals(id, other.id);
    }

    @Override
    public String toString() {
        return "Person [id=" + id + ", fname=" + fname + ", lname=" + lname + "]";
    }
}
```

我们测试一下代码。我们创建`List`. 然后我们将使用该<code>Stream.distinct()</code>方法查找具有唯一性的 Person 类的所有实例`id`。

```java
//Java 程序通过 id 查找不同的人
Person lokeshOne = new Person(1, "Lokesh", "Gupta");
Person lokeshTwo = new Person(1, "Lokesh", "Gupta");
Person lokeshThree = new Person(1, "Lokesh", "Gupta");
Person brianOne = new Person(2, "Brian", "Clooney");
Person brianTwo = new Person(2, "Brian", "Clooney");
Person alex = new Person(3, "Alex", "Kolen");
 
//Add some random persons
Collection<Person> list = Arrays.asList(alex, brianOne, brianTwo, lokeshOne, lokeshTwo, lokeshThree);

// Get distinct people by id
List<Person> distinctElements = list.stream()
            .distinct()
            .collect( Collectors.toList() );

// Let's verify distinct people
System.out.println( distinctElements );
```

```bash
输出
[
Person [id=1, fname=Lokesh, lname=Gupta],
Person [id=2, fname=Brian, lname=Clooney],
Person [id=3, fname=Alex, lname=Kolen]
]
```

### 自定义的复杂类型去重

有时，我们希望根据自定义逻辑找到不同的项目。

例如，我们可能需要找到所有可能有不同`id`但全名相同的人。在这种情况下，我们必须根据`Person`类`fname`和`lname`字段检查相等性。

Java 没有任何用于在使用提供的用户函数比较对象时查找不同对象的本机 API。因此，我们将创建自己的实用程序函数，然后使用它。

**distinctByKey()**

该<code>distinctByKey()</code>函数使用一个<code>ConcurrentHashMap</code>实例来确定是否存在具有相同值的现有键——其中键是从函数引用中获取的。

此函数的参数是一个lambda 表达式，用于生成映射键以进行比较。

```java
按类字段查找不同的实用函数public static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor) 
{
    Map<Object, Boolean> map = new ConcurrentHashMap<>();
    return t -> map.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
}
```

我们可以将任何字段 getter 方法作为方法参数传递，这将导致字段值充当映射的键。

**示例：**

检查我们<code>distinctByKey(p -> p.getFname() + " " + p.getLname())</code>在<code>filter()</code>方法中的使用方式。

```java
Java 程序按姓名查找不同的人Person lokeshOne = new Person(1, "Lokesh", "Gupta");
Person lokeshTwo = new Person(2, "Lokesh", "Gupta");
Person lokeshThree = new Person(3, "Lokesh", "Gupta");
Person brianOne = new Person(4, "Brian", "Clooney");
Person brianTwo = new Person(5, "Brian", "Clooney");
Person alex = new Person(6, "Alex", "Kolen");
 
//Add some random persons
Collection<Person> list = Arrays.asList(alex, brianOne, brianTwo, lokeshOne, lokeshTwo, lokeshThree);

// Get distinct objects by key
List<Person> distinctElements = list.stream()
            .filter( distinctByKey(p -> p.getFname() + " " + p.getLname()) )
            .collect( Collectors.toList() );

// Again verify distinct people
System.out.println( distinctElements );
```

程序输出：

```bash
输出[
Person [id=1, fname=Lokesh, lname=Gupta],
Person [id=4, fname=Brian, lname=Clooney],
Person [id=6, fname=Alex, lname=Kolen]
]
```

----------

## peek()

 <code>peek</code>：如同于map，能得到流中的每一个元素。但map接收的是一个Function表达式，有返回值；而peek接收的是Consumer表达式，没有返回值。

`peek()`存在主要是为了支持**调试**

我们看下debug用途的使用：

```java
Stream.of("one", "two", "three","four").filter(e -> e.length() > 3)
                .peek(e -> System.out.println("Filtered value: " + e))
                .map(String::toUpperCase)
                .peek(e -> System.out.println("Mapped value: " + e))
                .collect(Collectors.toList());
```

上面的例子输出：

```txt
Filtered value: three
Mapped value: THREE
Filtered value: four
Mapped value: FOUR
```

上面的例子我们输出了stream的中间值，方便我们的调试。

为什么只作为debug使用呢？我们再看一个例子：

```java
Stream.of("one", "two", "three","four").peek(u -> u.toUpperCase()).forEach(System.out::println);
```

上面的例子我们使用peek将element转换成为upper case。然后输出：

```txt
one
two
three
four
```

可以看到stream中的元素并没有被转换成大写格式。

再看一个map的对比：

```java
Stream.of("one", "two", "three","four").map(u -> u.toUpperCase()).forEach(System.out::println);
```

输出：

```txt
ONE
TWO
THREE
FOUR
```

可以看到map是真正的对元素进行了转换。

----------

# Stream终止操作

## foreach()

<code>Stream forEach(Consumer action)</code>对流的每个元素执行一个操作。Stream forEach(Consumer action) 是一个终端操作，即它可以遍历流以产生结果或副作用。

```java
memberNames.forEach(System.out::println);
```

**方法语法**

该<code>forEach()</code>方法的语法如下：

```java
void forEach(Consumer<? super T> action)
```

<code>Consumer</code>是一个[功能接口](https://howtodoinjava.com/java8/functional-interface-tutorial/)，<code>action</code>表示要对 Stream 中的每个元素执行的[非干扰操作](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/package-summary.html#NonInterference)。

**特点：**

- 该<code>forEach()</code>方法是**终端操作**。这意味着它不再返回`Stream`。
- 执行<code>forEach()</code>后，认为流管道已消耗，不能再使用 Stream。
- 如果我们需要再次遍历相同的数据源（支持 Stream 的集合），我们必须返回数据源以获取新的流。
- 对于*并行流*，该`forEach()`操作不保证流中元素的顺序，因为这样做会牺牲[并行性](https://howtodoinjava.com/java/multi-threading/concurrency-vs-parallelism/)的好处。
- **使用 Lambda 运算符：**在stream().forEach() 中，使用了 lambda，因此不允许对循环外的变量进行操作。只能对相关集合进行操作。

```java
List<String> arr1 = new ArrayList<String>();
        int count = 0;
        arr1.add("Geeks");
        arr1.add("For");
        arr1.add("Geeks");
        arr1.stream().forEach(s -> {
            // this will cause an error
            count++;
 
            // print all elements
            System.out.print(s);
        });
```

**示例：**

```java
list.stream().forEach( System.out::println );
```

## forEachOrdered()

同forEach()

- forEachOrdered 适用用于并行流的情况下进行迭代，能保证迭代的有序性

**示例：**

```
List<Integer> list = Arrays.asList(2, 4, 6, 8, 10);
list.stream().parallel().forEach( System.out::println ); //1 
list.stream().parallel().forEachOrdered( System.out::println ); //2
```

In the above example, both statements guarantee that output will be 2, 4, 6, 8, 10.

```
//forEach()
6
10
8
4
2 
//forEachOrdered()
2
4
6
8
10
```

## toArray()

该<code>toArray()</code>方法返回一个包含给定流元素的数组。这是**终端操作**。

**示例 ：将字符串流转换为字符串数组**

```java
将字符串流转换为数组
Stream<String> tokenStream = Arrays.asList("A", "B", "C", "D").stream();  //stream
         
String[] tokenArray = tokenStream.toArray(String[]::new);   //array
 
System.out.println(Arrays.toString(tokenArray));    
```

程序输出

```bash
[A, B, C, D]
```

## reduce() 

很多时候，我们需要执行将流归约为单个结果值的操作，例如，最大值、最小值、总和、乘积等。reduce是组合所有元素的重复过程。

*reduce*操作将二元运算符应用于流中的每个元素，其中运算符的第一个参数是前一个reduce操作的返回值，第二个参数是当前流元素。

<code>Optional\<T> reduce(BinaryOperator\<T> accumulator)</code>：第一次执行时，accumulator函数的第一个参数为流中的第一个元素，第二个参数为流中元素的第二个元素；第二次执行时，第一个参数为第一次函数执行的结果，第二个参数为流中的第三个元素；依次类推。
**示例 1：获取最长的字符串**

```java
List<String> words = Arrays.asList("GFG", "Geeks", "for","GeeksQuiz", "GeeksforGeeks");
Optional<String> longestString = words.stream().reduce((word1, word2) -> word1.length() > word2.length() ? word1 : word2);
  
longestString.ifPresent(System.out::println);
```

**示例 3：求和**

```java
List<Integer> array = Arrays.asList(-2, 0, 4, 6, 8);
int sum = array.stream().reduce(0,(element1, element2) -> element1 + element2);
System.out.println("The sum of all elements is " + sum);
```

## collect()

### collect() 流式传输到 List

<code>collect()</code>方法用于从`stream`接收元素并将它们存储在集合中。

```java
List<String> memNamesInUppercase = memberNames.stream().sorted()
                            .map(String::toUpperCase)
                            .collect(Collectors.toList());

System.out.print(memNamesInUppercase);
```

程序输出：

```bash
[AMAN, AMITABH, LOKESH, RAHUL, SALMAN, SHAHRUKH, SHEKHAR, YANA]
```

### collect() 流式传输到 Set

我们可以使用<code>Collectors.toSet()</code>将流元素收集到一个新的 Set 中。

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

Set<Integer> oddNumbers = numbers.parallelStream().filter(x -> x % 2 != 0).collect(Collectors.toSet());
System.out.println(oddNumbers); // [1, 3, 5]
```

### collect() 流式传输到 Map

我们可以使用<code>Collectors.toMap()</code>函数将流元素收集到`Map`。此方法接受映射键的两个参数和 Map 中的相应值。

```
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

Map<Integer, String> mapOddNumbers = numbers.parallelStream().filter(x -> x % 2 != 0)
		.collect(Collectors.toMap(Function.identity(), x -> String.valueOf(x)));
System.out.println(mapOddNumbers); // {1=1, 3=3, 5=5}
```

### Collectors joining() 

我们可以使用 Collectors join() 方法来获取一个 Collector，它将输入流 CharSequence 元素按照遇到的顺序连接起来。我们可以使用它来连接字符串流、StringBuffer或 StringBuilder。

```java
String value = Stream.of("a", "b", "c").collect(Collectors.joining());
// "abc"

String valueCSV = Stream.of("a", "b", "c").collect(Collectors.joining(","));
// "a,b,c"

String valueCSVLikeArray = Stream.of("a", "b", "c").collect(Collectors.joining(",", "{", "}"));
// "{a,b,c}"

String valueObject = Stream.of("1", new StringBuffer("2"), new StringBuilder("3")).collect(Collectors.joining());
// "123"
```

### Collectors类中的常用方法

- <code>counting</code>-统计数量
- <code>averagingDouble</code>-求平均值并转换成Double类型
- <code>averagingInt</code>-求平均值并转换成Int类型
- <code>averagingLong</code>-求平均值并转换成Long类型
- <code>summingDouble</code>-求和并转换成Double类型
- <code>summingInt</code>-求和并转换成Int类型
- <code>summingLong</code>-求和并转换成Long类型
- <code>maxBy</code>-根据函数条件求最大值
- <code>groupingBy</code>-分组
- <code>partitioningBy</code>-分区
- <code>joining</code>-连接字符串

示例 求年龄的平均值

```java
Double ageAve = stuList.stream()
            .collect(Collectors.averagingDouble(Stu::getAge));
System.out.println(ageAve);//37.0
```

求年龄之和

```java
Double ageSum = stuList.stream()
        .collect(Collectors.summingDouble(Stu::getAge));
System.out.println(ageSum);//296.0
```

根据年龄找出最大年龄值的stu对象

```java
//根据年龄找出最大年龄值的stu对象
Optional<Stu> stuOptional = stuList.stream()
        .collect(Collectors.maxBy((a, b) -> Double.compare(a.getAge(), b.getAge())));
System.out.println(stuOptional.get());//Stu{id=5, name='dd', age=52}

```

## min()

返回流中的最小值

- 这是**终端操作**。所以这个方法执行后就不能使用stream了。
- 根据提供的Comparator回此流的最小元素。
- 这是**流缩减的**一个特例，缩减到只有一个元素。

- 该方法返回一个描述此流的最小元素的Optional，`Optional`如果流为空，则返回一个空。

**示例：查找最小数字**

```java
List<Integer> list = Arrays.asList(2, 4, 1, 3, 7, 5, 9, 6, 8);
 
Optional<Integer> minNumber = list.stream().min((i, j) -> i.compareTo(j));
 
System.out.println(minNumber.get());
```

程序输出。

```bash
1
```

## max()

返回流中的最大值

- 这是**终端操作**。所以这个方法执行后就不能使用stream了。
- 根据提供的Comparator回此流的最大元素。
- 这是**流缩减的**一个特例，缩减到只有一个元素。

- 该方法返回一个描述此流的最大元素的Optional，`Optional`如果流为空，则返回一个空。

**示例：查找最大数字**

```java
List<Integer> list = Arrays.asList(2, 4, 1, 3, 7, 5, 9, 6, 8);
 
Optional<Integer> maxNumber = list.stream().max((i, j) -> i.compareTo(j));
 
System.out.println(maxNumber.get());
```

程序输出。

```bash
9
```

## count()

返回流中元素的总个数

**示例 1：计算 List 中元素个数**

```java
long count = Stream.of("how","to","do","in","java").count();
System.out.printf("There are %d words in the stream %n", count);
```

**示例 2：使用 Collectors.counting() 计算元素数量**

```java
long count = Stream.of("how","to","do","in","java").collect(Collectors.counting());
System.out.printf("There are %d words in the stream %n", count);
```

## anyMatch()

接收一个 Predicate 函数，只要流中有一个元素满足该断言则返回true，否则返回false

- 这是一个短路终端操作。
- 它返回此流的任何元素是否与提供的谓词匹配。
- 如果不需要确定结果，则可能不会评估所有元素的谓词。`true`一旦遇到第一个匹配元素，方法就会返回。
- 如果流为空，则`false`返回并且不评估谓词。
- **allMatch() 和 anyMatch() 之间**的**区别在于**，如果流中的任何元素与给定的谓词匹配，则`anyMatch()`返回`true`。使用 时`allMatch()`，所有元素必须匹配给定的谓词。

**示例 1：检查流是否包含特定元素**

```java
Stream<String> stream = Stream.of("one", "two", "three", "four");
boolean match = stream.anyMatch(s -> s.contains("four"));
System.out.println(match); //true
```

### anyMatch() 与 contains() 的区别

对于集合List来说，`anyMatch()`和contains()没有区别

`anyMatch()`在某些需要检查流中是否至少有一个元素的情况下，该方法很有用。

较短的版本`list.contains()`也做同样的事情，可以代替使用。

## allMatch()

`allMatch`-检查是否`匹配所有`元素

**示例 1：`Stream.allMatch()`检查所有流元素是否不包含任何数字字符**

```java
Stream<String> stream = Stream.of("one", "two", "three", "four");
Predicate<String> containsDigit = s -> s.contains("\\d+") == false;
boolean match = stream.allMatch(containsDigit);
System.out.println(match); //true
```

## noneMatch()

**句法**

```java
boolean noneMatch(Predicate<? super T> predicate)
```

的`noneMatch()`回报：

- **true** – 如果流中没有元素匹配给定的谓词，或者流为空。
- **false** – 如果至少一个元素与给定的谓词匹配。

- 它与方法allMatch() 完全相反。

**示例 1：`Stream.noneMatch()`检查 Stream 中的元素是否包含任何数字/数字字符**

```java
Stream<String> stream = Stream.of("one", "two", "three", "four");
         
boolean match = stream.noneMatch( s -> s.contains("\\d+") );
         
System.out.println(match);      //true
```

## findFirst()

```
Optional<T> findFirst()
```

此方法返回一个Optional描述**此流**的**第一个匹配元素**。

```java
Optional<Customer> optional = allCustomers.stream()
        .filter(customer -> customer.getAge() > 20)
        .findFirst();
```

## findAny()

`findAny`-返回当前流中的`任意一个元素`

```java
//从集合中随便找个age>30的Stu对象  可以使用串行流stream,也可以使用parallelStream 并行流
Optional<Stu> any = stuList.parallelStream()
    .filter((e) -> e.getAge() > 30).findAny();
System.out.println(any.get());//Stu{id=4, name='cc', age=42}
Optional<Stu> any1 = stuList.stream()
    .filter((e) -> e.getAge() > 30).findAny();
System.out.println(any1.get());//Stu{id=3, name='bb', age=32}
```

### findFirst() 与 findAny()

在非并行流中，大多数情况下两者都可能返回流的第一个元素，但不提供此行为的任何保证。`findAny()`

用于`findAny()`在更快的时间内从任何并行流中获取任何元素。否则，我们总是可以`findFirst()`在大多数情况下使用。

-----

# Stream使用示例

## 示例1:查找流的最后一个元素

### Stream.reduce() API

该`reduce()`方法对流的元素使用归约技术。在这种情况下，它将选择流的两个元素并选择后者。这将一直持续到所有元素都用尽为止。

在归约过程结束时，我们将得到流的最后一个元素。

```java
// 使用 reduce() 方法获取最后一个元素
Stream<Integer> stream = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9).stream();
 
Integer lastElement = stream.reduce((first, second) -> second)
                            .orElse(-1);
 
System.out.println(lastElement);    // Prints 9
```

如果流本身是空的，则lastElement为-1。

## 示例2:查找和计算重复项并删除这些重复项

### Stream.distinct() - 删除重复项

#### 删除重复的字符串

distinct()方法根据对象的`equals()`方法检查对象是否相等

```java
List<String> list = Arrays.asList("A", "B", "C", "D", "A", "B", "C");

List<String> distinctItems = list.stream().distinct().collect(Collectors.toList());

System.out.println(distinctItems); // [A, B, C, D]
```

#### 删除重复的自定义对象

可以使用相同的语法从*List 中*删除重复的对象。为此，我们需要非常小心对象的`equals()`和`hashCode()`方法，因为它将决定对象是重复的还是唯一的。

考虑下面的例子，如果两个*Person*实例具有相同的*id*值，则它们被认为是相等的。

```java
public class Person 
{
    public Person(Integer id, String fname, String lname) {
        super();
        this.id = id;
        this.fname = fname;
        this.lname = lname;
    }
 
    private Integer id;
    private String fname;
    private String lname;
 
    //Getters and Setters are hidden for brevity
    
    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Person other = (Person) obj;
        return Objects.equals(id, other.id);
    }
  
  @Override
    public int hashCode() {
        return Objects.hash(id);
    }

    @Override
    public String toString() {
        return "Person [id=" + id + ", fname=" + fname + ", lname=" + lname + "]";
    }
}
```

删除重复的*Person*对象

```java
Person p1 = new Person(1,"zhang","san");
Person p2 = new Person(2,"li","si");
Person p3 = new Person(3,"wang","wu");
Person p4 = new Person(1,"zhang","san");
Person p5 = new Person(2,"li","si");
Collection<Person> list = Arrays.asList(p1, p2, p3, p4, p5);

List<Person> distinctElements = list.stream().distinct().collect( Collectors.toList() );
System.out.println(distinctElements); 
//[Person [id=1, fname=zhang, lname=san], Person [id=2, fname=li, lname=si], Person [id=3, fname=wang, lname=wu]]
```

### Collectors.toSet() - 删除重复项

另一种简单且非常有用的方法是将所有元素存储在`Set`. **根据定义，集合仅存储不同的元素。**请注意，*Set*通过使用*equals()*方法比较对象来存储不同的项目。

```java
ArrayList<Integer> numbersList
= new ArrayList<>(Arrays.asList(1, 1, 2, 3, 3, 3, 4, 5, 6, 6, 6, 7, 8));
 
Set<Integer> setWithoutDuplicates = numbersList.stream().collect(Collectors.toSet());
 
System.out.println(setWithoutDuplicates);
```

程序输出：

```java
[1, 2, 3, 4, 5, 6, 7, 8]
```

### Collectors.toMap() - 计算重复项

有时，我们有兴趣找出哪些元素是重复的，以及它们在原始列表中出现的次数。我们可以使用 一个`Map`来存储这些信息。

我们必须遍历列表，将元素作为 Map 键，并将其所有出现在 Map 值中。

```java
// ArrayList with duplicate elements
ArrayList<Integer> numbersList
= new ArrayList<>(Arrays.asList(1, 1, 2, 3, 3, 3, 4, 5, 6, 6, 6, 7, 8));
 
Map<Integer, Long> elementCountMap = numbersList.stream()
.collect(Collectors.toMap(Function.identity(), v -> 1L, Long::sum));
 
System.out.println(elementCountMap);
```

程序输出：

```java
{1=2, 2=1, 3=3, 4=1, 5=1, 6=3, 7=1, 8=1}
```

去重

```java
Person p1 = new Person(1,"zhang","san");
Person p2 = new Person(2,"li","si");
Person p3 = new Person(3,"wang","wu");
Person p4 = new Person(1,"zhang","san");
Person p5 = new Person(2,"li","si");
Collection<Person> list = Arrays.asList(p1, p2, p3, p4, p5);

// key值重复时取第一个
// map: key:Person::getId,value:person -> person,重复key处理(oldPerson, newPerson) -> oldPerson
Map<Integer, Person> distinctElements = list.stream().distinct().collect(Collectors.toMap(Person::getId, person -> person, (oldPerson, newPerson) -> oldPerson));
System.out.println(distinctElements);
// {1=Person [id=1, fname=zhang, lname=san], 2=Person [id=2, fname=li, lname=si], 3=Person [id=3, fname=wang, lname=wu]}
```

## 示例3: 图解Stream处理过程

```java
List<Integer> transactionsIds = 
    transactions.stream()
                .filter(t -> t.getType() == Transaction.GROCERY)
                .sorted(comparing(Transaction::getValue).reversed())
                .map(Transaction::getId)
                .collect(toList());
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/java8Stream/transac1.png"  />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/java8Stream/transac.png"  />
