## Java基础

#### 0x00 Java ClassLoader(类加载机制)

- Java是依赖于JVM(Java虚拟机)实现的跨平台开发语言
- Java程序运行前需要编译成class文件
- Java类初始化的时候会调用java.lang.ClassLoader加载类字节码
- ClassLoader会调用JVM的native方法来定义一个java.lang.class实列

------

#### 0x01 Java类

TestHelloWorld.java

```java
package com.anbai.sec.classloader;


public class TestHelloWorld {

    public String hello() {
        return "Hello World~";
    }

}
```

- 编译Java类: `javac *.java`
- jdk自带的javap可以反编译类或使用hexdump查看*.class文件二进制类容
- JVM执行TestHelloWorld之前会先解析class二进制内容，JVM执行的其实就是如上javap命令生成的字节码

------

#### 0x02 ClassLoader

- 一切的java类都必须经过JVM加载后才能运行
- ClassLoader的主要作用就是Java类文件的加载
- JVM类加载器中最顶层的是：
  - Bootstrap ClassLoader(引导类加载器)
  - Extension ClassLoader(扩展类加载器)
  - App Classloader(系统类加载器)
- AppClassLoader是默认类加载器
- Classloader.getSystemClassLoader()返回的系统类加载器也是AppClassLoader
- 某些时候获取一个类的加载器时候可能会返回一个null值,如:`java.io.File.Class.getClassLoader()`
  - java.io.File类在JVM初始化的时候会被Bootstrap ClassLoader(引导类加载器)加载(实现于JVM层，采用C++编写)
  - 我们尝试获取被BootStrap ClassLoader类加载器所加载的类的ClassLoader时候都会返回null
- ClassLoader类核心方法：
  - loadclass(加载指定的java类)
  - findclass(查找指定的Java类)
  - findloadclass(查找JVM已经加载过的类)
  - defineClass(定义一个Java类)
  - resolveClass(链接指定的Java类)

------

#### 0x03 Java类动态加载方式

- Java类加载方式分**显式**和**隐式**

  - 显式：即通常使用**Java反射**或者**classloader**来动态加载一个类对象

  - 隐式：指的是**类名.方法名()**或**new类实列**

  - **显式**类加载方式也可以理解为类动态加载，可以自定义类加载器去加载任意类

    ```Java
    // 反射加载TestHelloWorld示例
    Class.forName("com.anbai.sec.classloader.TestHelloWorld");
    // ClassLoader加载TestHelloWorld示例
    this.getClass().getClassLoader().loadClass("com.anbai.sec.classloader.TestHelloWorld");
    ```

  - **Class.forName("类名")**默认会初始化被加载类的静态属性和方法

  - 不初始化则可以使用**Class.forName("类名",是否初始化,类加载器)**

  - **Classloader.loadclass**默认不会初始化类方法

------

#### 0x04 ClassLoader类加载流程

###### ClassLoader加载com.anbai.sec.classloader.TestHelloWorld类流程：

1. `Classloader`调用`public class<?> loadclass(String name)`方法加载`com.anbai.sec.classloader.TestHelloWorld`类
2. 调用`findLoadedClass`方法检查`TestHelloWorLd`类是否被初始化，如果JVM已初始化过该类。则直接返回类对象
3. 如果创建当前`ClassLoader`时传入了父类加载器`(new ClassLoader(父类加载器)`)就使用父类加载器加载`TestHelloWorld`类，否则使用JVM的`Bootstrap ClassLoader`加载
4. 如果上一步无法加载`TestHelloWorld`类，那么调用自身的`findclass`方法常识加载`TestHelloWorld`类
5. 如果当前的`classloader`没有重写了`findclass`方法，那么直接返回类加载失败异常，如果当前类重写了findclass方法并通过传入的`com.anbai.sec.classloader.TestHelloWordl`类名找到了对应的类字节码，那么应该调用`defineClass`方法去JVM中注册该类
6. 如果调用`loadClass`的时候传入了`resolve`参数为true，那么还需要调用`resolveClass`方法链接类，默认为false
7. 返回一个被JVM加载后的`java.lang.Class`类对象

------

#### 0x05 自定义ClassLoader

