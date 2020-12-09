[toc]

# 简介

Java从语言层面支持反射。

反射允许程序在运行时获得一个类的所有信息。举个例子，在程序运行时传入一个类名字符串，程序可以生成该类的对象并获得它的所有方法和成员(包括被private修饰的)。用更加好懂的语言来描述就是程序能够通过一个字符串寻址到这个类所在的内存区域。

# API

略

# 三种获得class对象的方法

```java
package Test;

public class Test {
    static {
        System.out.println("this is static code block");
    }
    {
        System.out.println("this is not static code block");
    }
    Test() {
        System.out.println("this is construction method");
    }
}


package Test;

public class TestRun {
    public static void main(String[] args) throws Exception{
        Class<?> clz1 = Test.class;
        //Class<?> clz2 = Class.forName("Test.Test");
        //Class<?> clz3 = new Test().getClass();
    }
}
```

通过注释其他语句只保留一条语句运行，我们可以观察输出得出结论，Class.class生成Class对象时不会执行类的任何方法,Class.forName("")生成对象时会执行类的静态初始化方法, new Instance().getclass()生成对象时会执行类的静态初始化，非静态初始化和类的构造方法并返回引用运行时真正所指向的对象(父类型引用可能指向子类的对象)。



# 总结

反射增强了程序灵活性，解除各种耦合(通过配置文件生成对象),通常被应用于各种框架。但是反射的效率很低，并且会破坏面向对象的封装性(可以访问private成员)。