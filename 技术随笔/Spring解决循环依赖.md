Spring是一个托管对象的容器，每个实例都是一个Bean。程序员把类托管给框架以后就使用类的对象就无需再通过new创建。

``` java
@Component
public class A {
  private B b;
  public void setB(B b) {
    this.b = b;
  }
}
@Component
public class B {
  private A a;
  public void setA(A a) {
    this.a = a;
  }
}
```





上述代码是循环依赖的一个例子。

先说结论，只有通过setter注入的单例bean才能被Spring解决循环依赖，否则会抛出异常。

以上述代码为例，Spring在创建类A的对象时，会先从缓存里取，缓存中没有则初始化一个新对象，这个对象是一个半成品，因为还没有为它注入属性。然后Spring会递归的去创建类A的成员对象，即B，同样先读缓存，缓存里没有就初始化一个半成品，然后也递归去创建B的成员对象，此时发现缓存里已经有一个A的半成品了，于是取出注入。递归回溯的时候再把B的对象注入到A里。



总结下来有两大要点：

* Spring是通过递归的方式创建目标bean和依赖的bean
* Spring通过setter注入创建bean是先实例化bean再进行属性注入



如果通过构造注入无法解决循环依赖，因为构造注入会在创建bean的时候就注入属性，此时找不到依赖的bean。

原型模式（prototype）无法解决循环依赖，因为不会缓存bean。 如果bean是无状态的还是使用singleton。



具体代码分析有待补充...