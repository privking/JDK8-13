JDK集合框架
===
# stream
* stream 称为“流”，通过将集合转换为一种叫流的元素队列，通过声明性的方式，能够对集合中的每一个元素进行一系列并行或串行的流水线操作
* 元素是特定类型的对象，所以元素集合看做一种流，流在管道中传输，且可以在管道的节点上进行处理，比如排序，聚合，过滤等操作
* 操作详情
    * 数据元素便是原始集合，如List,Set,Map等
    * 生成流，可以进行串行流“stream（）” 或并行流“parallelStream（）”
    * 中间操作，可以是，排序，聚合，过滤，转换等
    * 终端操作，很多流操作，本身就会返回一个流，所以多个操作可以直接连接起来，最后统一进行收集

##  map
* map应用场景
    * 将对象T映射为R(实现类型转换)
    * 对集合中元素进行修改
```java
public class StreamDemo2 {
    public static void main(String[] args) {
        List<User> list = Arrays.asList(new User(1,"king","123"),
                new User(2,"king0","1231"),
                new User(3,"king1","1232"),
                new User(4,"king2","1233"),
                new User(5,"king3","1234"),
                new User(6,"king4","1235"));

        List<UserDTO> collect = list.stream().map(obj -> {
            UserDTO userDTO = new UserDTO(obj.getId(), obj.getName());
            return userDTO;
        }).collect(Collectors.toList());
        System.out.println(collect);
    }
}

public class User {
    private int id ;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

public class UserDTO {
    private int userID;

    private String username;

    public UserDTO() {
    }

    public UserDTO(int userID, String username) {
        this.userID = userID;
        this.username = username;
    }

    public int getUserID() {
        return userID;
    }

    public void setUserID(int userID) {
        this.userID = userID;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    @Override
    public String toString() {
        return "UserDTO{" +
                "userID=" + userID +
                ", username='" + username + '\'' +
                '}';
    }
}

```
## filter
* 通常用于设置过滤条件过滤元素
```java
public class StreamDemo2 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("11111","123123","aaaaaaa","fffffffff","aa","xzc","asd");
        List<String> list1 = list.stream().filter(obj -> obj.length() > 5).collect(Collectors.toList());
        System.out.println(list1);
    }

}
//[123123, aaaaaaa, fffffffff]
```
## sorted
* `sorted()`对元素实现自然排序（升序），元素必须实现Comparable接口
* `sorted(Comparator<? super T> comparator)`自定义排序
```java
 List<String> list = Arrays.asList("aaa", "vvv", "xxxx", "cdcd", "cece", "caca", "cbcbc");
//默认升序
List<String> list1 = list.stream().sorted().collect(Collectors.toList());
System.out.println(list1);
List<String> list2 = list.stream().sorted(Comparator.comparing(obj -> obj.length())).collect(Collectors.toList());
List<String> list3 = list.stream().sorted(Comparator.comparing(obj -> obj.length(),Comparator.reverseOrder())).collect(Collectors.toList());
List<String> list4 = list.stream().sorted(Comparator.comparing(String::length).reversed()).collect(Collectors.toList());
System.out.println(list2);
System.out.println(list3);
System.out.println(list4);
```
## limit
* 截断流
* `Stream<T> limit(long maxSize);`
```java
 List<String> list5 = list.stream().sorted(Comparator.comparing(String::length).reversed()).limit(3).collect(Collectors.toList());
System.out.println(list5);
```
## allMatch && anyMatch
* allMatch全部匹配
* anyMatch任意匹配
```java
boolean allMatch = list.stream().allMatch(obj -> obj.length() > 1);
boolean anyMatch = list.stream().anyMatch(obj -> obj.length() > 1);
System.out.println(anyMatch);
System.out.println(allMatch);
```
## max && min
* `Optional<T> max(Comparator<? super T> comparator);`
* 返回最大或最小的元素
```java
Optional<String> max = list.stream().max(Comparator.comparing(obj -> obj.length()));
System.out.println(max.get());
```
## reduce
* 聚合操作
* 最终结果返回一个值
* `T reduce(T identity, BinaryOperator<T> accumulator);`
* `Optional<T> reduce(BinaryOperator<T> accumulator);`
```java
public static void main(String[] args) {
        //求和
        Optional<Integer> reduce = Stream.of(1, 2, 3, 4, 5).reduce((obj1, obj2) -> obj1 + obj2);
        System.out.println(reduce.get());
        //初始值为100求和
        Integer reduce1 = Stream.of(1, 2, 3, 4, 5).reduce(100, (obj1, obj2) -> obj1 + obj2);
        System.out.println(reduce1);

        //求最大值
        Optional<Integer> reduce2 = Stream.of(1, 2, 3, 4, 5).reduce((obj1, obj2) -> obj1 > obj2 ? obj1 : obj2);
        System.out.println(reduce2.get());

    }
    //15
    //115
    //5
```

# parallelStream
* 多线程流
    * 集合做重复的操作，采用多线程加快
    * jdk8的parallelStream用fork/join框架提供并发执行能力
* 底层原理
    * 线程池(forkJoinPool)维护一个线程队列
    * 可以分割任务，将父任务拆分成子任务
* 在数据量小的情况下parallelStream比stream慢，ForkJoin会消耗性能
* 不是所有情况都能使用parallelStream,部分情况会有线程安全问题，比如集合一定要使用线程安全的集合，不然会引发多线程安全问题


