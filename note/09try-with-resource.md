try-with-resources
===
## 旧方法
```java
 public static void test(String filePath) throws FileNotFoundException {
        OutputStream out = new FileOutputStream(filePath);
        try {
            out.write("123123123".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            try {
                out.close();
            }catch (IOException e){
                e.printStackTrace();
            }
        }
    }
```

## 新方法
```java
public static void test2(String filePath){
        try (OutputStream out = new FileOutputStream(filePath);
            OutputStream out2 = new FileOutputStream(filePath);
        ){
            out.write("aaaaaaaa".getBytes());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

## 注意点
* jdk7
* 实现了AutoCloseable接口的类，在try()里声明该类的时候，try结束后会自动调用close方法，这个动作会早于finally里调用方法
* 不管是否出现异常，try()里的实例都会调用close方法
* try里面可以声明多个自动关闭的对象，越早声明，会被越晚close