JDK集合框架之收集器
===
## collect()
* 方法和作用
    * 一个终端操作，用于对流中的数据进行归集操作，collect的方法接受的参数是一个Collector
    * 有两个重载方法
        * `<R, A> R collect(Collector<? super T, A, R> collector);`
        * `<R> R collect(Supplier<R> supplier,BiConsumer<R, ? super T> accumulator,BiConsumer<R, R> combiner);`
    
## Collector作用
* Collector是一个接口，其工具类提供了很多工厂方法
```java
//Collectors.toList
public static <T>
    Collector<T, ?, List<T>> toList() {
        return new CollectorImpl<>((Supplier<List<T>>) ArrayList::new, List::add,
                                   (left, right) -> { left.addAll(right); return left; },
                                   CH_ID);
    }
//ArrayList::new 创建一个ArrayList作为累加器
//List::add 对流元素的操作是直接添加到累加器中
//reduce操作 后一个子任务的结果直接全部添加到前一个子任务的结果中
//CH_ID是一个unmodifiableSet集合
```
```java
List<String> list = Arrays.asList("aa", "bb", "ccccc");
List<String> list1 = list.stream().collect(Collectors.toList());
CopyOnWriteArrayList<String> list2 = list.stream().collect(Collectors.toCollection(CopyOnWriteArrayList::new));
TreeSet<String> set = list.stream().collect(Collectors.toCollection(TreeSet::new));
System.out.println(list1);
System.out.println("list2 = " + list2);
System.out.println("set = " + set);
Map<String, String> collect = list.stream().collect(Collectors.toMap(obj -> "name:" + obj, obj -> obj));
System.out.println("collect = " + collect);
```
### join函数
* 拼接函数
```java
 public static Collector<CharSequence, ?, String> joining() {
        return new CollectorImpl<CharSequence, StringBuilder, String>(
                StringBuilder::new, StringBuilder::append,
                (r1, r2) -> { r1.append(r2); return r1; },
                StringBuilder::toString, CH_NOID);
    }

 public static Collector<CharSequence, ?, String> joining(CharSequence delimiter) {
        return joining(delimiter, "", "");
    }

 public static Collector<CharSequence, ?, String> joining(CharSequence delimiter,
                                                             CharSequence prefix,
                                                             CharSequence suffix) {
        return new CollectorImpl<>(
                () -> new StringJoiner(delimiter, prefix, suffix),
                StringJoiner::add, StringJoiner::merge,
                StringJoiner::toString, CH_NOID);
    }
```
```java
String result1 = list.stream().collect(Collectors.joining());
System.out.println("result1 = " + result1);
String result2 = list.stream().collect(Collectors.joining("||"));
System.out.println("result2 = " + result2);
String result3 = list.stream().collect(Collectors.joining("||", "(", ")"));
System.out.println("result3 = " + result3);
//result1 = aabbccccc
//result2 = aa||bb||ccccc
//result3 = (aa||bb||ccccc)

String result4 = Stream.of("aaa", "sss", "ddd").collect(Collectors.joining(",", "[", "]"));
System.out.println("result4 = " + result4);

```

### partitioningBy分组
* key是boolean类型
```java
public static <T>
    Collector<T, ?, Map<Boolean, List<T>>> partitioningBy(Predicate<? super T> predicate) {
        return partitioningBy(predicate, toList());
    }
```
```java
List<String> list = Arrays.asList("aa", "bb", "ccccc");
Map<Boolean, List<String>> result5 = list.stream().collect(Collectors.partitioningBy(obj -> obj.length() > 2));
System.out.println("result5 = " + result5);
//result5 = {false=[aa, bb], true=[ccccc]}
```

### groupingBy分组
```java
public static <T, K> Collector<T, ?, Map<K, List<T>>>
        groupingBy(Function<? super T, ? extends K> classifier) {
            return groupingBy(classifier, toList());
        }
```

```java
List<Student> students = Arrays.asList(new Student(12, "aaa1")
                , new Student(13, "aaa2")
                , new Student(14, "aaa3")
                , new Student(15, "aaa4")
                , new Student(16, "aaa5")
                , new Student(17, "aaa5")
                , new Student(18, "aaa5")
                , new Student(19, "aaa5")
);
Map<String, List<Student>> listMap = students.stream().collect(Collectors.groupingBy(obj -> obj.getProvince()));
listMap.forEach((key,value)->{
    System.out.println("=====");
    System.out.println(key);
    value.forEach(obj->{
        System.out.println(obj.getAge());
    });
});

Map<String, Long> longMap = students.stream().collect(Collectors.groupingBy(obj -> obj.getProvince(), Collectors.counting()));
longMap.forEach((key,value)->{
    System.out.println(key+"------"+value);
});
/**
=====
aaa5
16
17
18
19
=====
aaa2
13
=====
aaa1
12
=====
aaa4
15
=====
aaa3
14
aaa5------4
aaa2------1
aaa1------1
aaa4------1
aaa3------1
**/
```

### summarizing集合统计
```java
IntSummaryStatistics summaryStatistics = students.stream().collect(Collectors.summarizingInt(Student::getAge));
System.out.println("平均值："+summaryStatistics.getAverage());
System.out.println("人数："+summaryStatistics.getCount());
System.out.println("最大值："+summaryStatistics.getMax());
System.out.println("最小值："+summaryStatistics.getMin());
System.out.println("最小值："+summaryStatistics.getSum());
/**
平均值：15.5
人数：8
最大值：19
最小值：12
最小值：124
**/
```

