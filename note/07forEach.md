forEach
====
* 不能修改外部变量的值
* 不能用break或continue跳过循环

```java
list.forEach(obj-> System.out.println(obj));
list.forEach(System.out::println);
```
```java
   default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
```