Default关键字
===
## interface中的default方法
* 实现接口是可以不用实现default方法
```java
public interface Animal {
    default void breath(){
        System.out.println("呼吸");
    }
}
```
## interface中的static关键字
```java
public interface Animal {

    static void sleep(){
        System.out.println("sleep");
    }
}
```
* 可以直接以Animal.sleep();的形式调用
