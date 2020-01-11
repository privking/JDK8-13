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

