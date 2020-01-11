Optional
===
* 主要是为了解决空指针异常
```java
    Student student =new Student();
    //为空抛出异常
    Optional<Student> optional = Optional.of(student);
    //为空不抛异常
    Optional<Student> optional1 = Optional.ofNullable(student);

    if(optional1.isPresent()){
        System.out.println("student 不为空");
        Student s =optional1.get();
    }else{
        System.out.println("student 为空");
    }

    //兜底方法
    Student student1 = Optional.ofNullable(student).orElse(new Student());

    //兜底方法2
    int age = Optional.ofNullable(student).map(obj -> obj.getAge()).orElse(7);

```