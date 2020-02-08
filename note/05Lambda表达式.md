Lambda表达式
===
* 在JDK8之前，Java是不支持函数式编程的，所谓的函数编程，即可理解是将一个函数（也称为“⾏为”）作为一个参数进⾏传递，面向对象编程是对数据的抽象（各种各样的POJO类），而函数式编程则是对⾏为的抽象（将⾏为作为一个参数进⾏传递）
```java
new Thread(new Runnable() { 
        @Override     
        public void run() {
            System.out.println("123");     
        } 
});

new Thread(() -> System.out.println("123"));
////////////////////////////////////////////////
List<String> list =Arrays.asList("aaa","ggg","ffff","ccc"); 
Collections.sort(list, new Comparator<String>() { 
            @Override                   
            public int compare(String a, String b) {                        
                return b.compareTo(a);                    
            }                
});        
for (String string : list) {
    System.out.println(string);        
}

List<String> list =Arrays.asList("aaa","ggg","ffff","ccc");    
Collections.sort(list, (a,b)->b.compareTo(a));    
for (String string : list) {        
    System.out.println(string);    
}
////////////////////////////////////////////////
```
* lambda表达式使用场景(前提)：一个接⼝中只包含一个方法，则可以使用Lambda表达式
* 语法：(params) -> expression
* 第一部分为括号内用逗号分隔的形式参数，参数是函数式接口里面⽅法的参数；
* expression为表达式或代码块
* 括号中参数列表的数据类型可以不写
* 如果只有一个参数，可以不加（）
    * a->sout(a)
* 如果代码只有一行，可以不写{ },return,分号
* Lambda实质上是以匿名内部类的方式对接口实现
* 重构现有臃肿代码，提高开发效率

## 自定义函数式编程

```java
@FunctionalInterface
public interface LambdaFunction<R,T> {
    R operator(T t1,T t2);
}

```
```java
public class Main {
    public static void main(String[] args) {
        System.out.println(
                operator(1, 2, new LambdaFunction<Integer, Integer>() {
                    @Override
                    public Integer operator(Integer t1, Integer t2) {
                        return t1+t2;
                    }
                }));

        System.out.println(operator(1, 2, (x, y) -> x + y));
    }
    public static Integer operator(Integer x, Integer y, LambdaFunction<Integer, Integer> of) {
        return of.operator(x, y);
    }

}
```

## JDK8函数式接口
```java
//4大函数接口
//消费型接口 有参数，没有返回值
Consumer<T>
void accept(T t);
//供给型接口，没有参数，有返回值
Supplier<T>
T get();
//函数型接口，有参数，有返回值
Function<T,R>
R apply(T t );
//断言型，有参数，有返回值，返回值为boolean类型
Predicate<T>
boolean test(T t)
```
```java
public class FunctionObj implements Function {
    @Override
    public Object apply(Object o) {
        return o+"apply";
    }
}
```
```java
public class Main {
    public static void main(String[] args) {
        //Function
        //test("123",new FunctionObj());
        Function<Integer,Integer> func = p->{
            System.out.println("函数");
            return p*100;
        };
        Function<Integer,Integer> func2 = p->p*100;
        func2.apply(11);
        //BiFunction
        test1(1,2,(a,b)->a+b);
        //Consumer
        Consumer<String> consumer = obj->{
            System.out.println(obj);
            System.out.println("123123");
        };
        sendMsg("111111",consumer);
        sendMsg("11111", obj->{
            System.out.println(obj);
            System.out.println("123123");
        });
        List<String> list= Arrays.asList("123","1232");
        list.forEach(obj-> System.out.println(obj));
        //Supplier
        String str = newString();//工厂模式
        //predicate
        List<String> list2= Arrays.asList("123","1232","aaaaa","bbbbb","aaaa");
        List<String> newList = filter(list2, obj->obj.startsWith("a"));
        System.out.println(newList);
    }
    public static void test(String input,FunctionObj functionObj){
        System.out.println(functionObj.apply(input));
    }
    public static Integer test1(Integer a , Integer b , BiFunction<Integer,Integer,Integer> biFunction){
        return biFunction.apply(a, b);
    }
    public static void sendMsg(String phone,Consumer<String> consumer){
        consumer.accept(phone);
    }
    public static  String newString(){
        Supplier<String> supplier=()->new String("123123");
        return supplier.get();
    }
    public static List<String> filter(List<String> list, Predicate<String> predicate){
        List<String> result = new ArrayList<>();
        for(String str:list){
            if(predicate.test(str)){
                result.add(str);
            }
        }
        return result;
    }
}
```
## 方法与构造方法的引用
* 以前的调用方法`对象.方法名`,`类名.方法名`
* jdk1.8支持`::`调用
* 语法
    * 静态方法 `ClassName::methodName`
    * 实例方法 `Instance::methodName`
    * 构造函数 `类名::new`
```java
public static void main(String[] args) {
        // 使用双冒号::来构造静态函数引⽤
        Function<String, Integer> fun = Integer::parseInt;
        Integer value = fun.apply("1024");
        System.out.println(value);
        // 使用双冒号::来构造非静态函数引⽤用
        String content = "assdasdfsadf";
        Function<Integer, String> func = content::substring;
        String result = func.apply(1);
        System.out.println(result);
        // 构造函数引用，多个参数
        //BiFunction<Pram1,Pram2,Return>
        BiFunction<String, Integer, User> biFunction = User::new;
        User user1 = biFunction.apply("asdasd", 28);
        System.out.println(user1.toString());
        //构造函数引用，单个参数
        Function<String, User> function = User::new;
        User user2 = function.apply("asdasd");
        System.out.println(user2.toString());
        // 函数引用也是⼀种函数式接口，可以将函数引用作为⽅法的参数
        sayHello(String::toUpperCase, "asdasd");
    }

    /**
     * * @param func 函数引⽤用 * @param param 对应的参数
     */
    private static void sayHello(Function<String, String> func, String param) {
        String result = func.apply(param);
        System.out.println(result);
    }
}

class User {
    private String username;
    private Integer age;

    public User() {
    }

    public User(String username) {
        this.username = username;
    }

    public User(String username, Integer age) {
        this.username = username;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                '}';
    }
```

## 返回函数式接口
```java
private static <T> BinaryOperator<T> throwingMerger() {
        return (u,v) -> { throw new IllegalStateException(String.format("Duplicate key %s", u)); };
    }
```

