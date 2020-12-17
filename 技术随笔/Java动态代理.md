​	代理模式是一种设计思想。简言之，对于每一个真实类，都为它编写一个代理类，代理类持有真实类的一个对象，将真实类对象注入到代理类对象中，每当调用真实类对象的方法时，都通过代理对象调用。这样的好处是，方便业务逻辑的横向扩展。比如如果需要在所有真实类的方法执行前打印一行日志，通过代理类可以把此类公共逻辑抽取出来，避免对所有方法改动。

​	上述为每一个类编写一个代理类的方法称为静态代理，缺点是增加了开发量，代理类需要拷贝所有真实类的方法。	

​	通过动态代理解决这个问题。动态代理类的字节码在运行时生成，静态代理类的字节码编译时就已生成。编译时生成类字节码并调用方法，通过反射实现。



# 基于JDK实现

接口

``` java
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();
}
```

实现类

``` java
public class UserServiceImpl implements UserService {
    public void add() {
        System.out.println("增加了一个用户");
    }

    public void delete() {
        System.out.println("删除了一个用户");
    }

    public void update() {
        System.out.println("更新了一个用户");
    }

    public void query() {
        System.out.println("修改了一个用户");
    }
}

```

辅助类，并非代理类，而是帮助生成代理类

``` java
public class ProxyInvocationHandler implements InvocationHandler {
    // 代理的接口
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        Object result = method.invoke(target, args);
        return result;
    }

    public void log(String name) {
        System.out.println("执行" + name + "方法");
    }
}
```

调用方

``` java
public class Client {
    public static void main(String[] args) {
        // 真实角色
        UserServiceImpl userService = new UserServiceImpl();

        // 代理角色 不存在
        ProxyInvocationHandler pih = new ProxyInvocationHandler();

        // 设置要代理的对象
        pih.setTarget(userService);

        // 动态生成代理类
        UserService proxy = (UserService) pih.getProxy();

        // 调用方法
        proxy.add();
        proxy.delete();
    }
}
```



底层逻辑简言之，是拷贝接口里成员（UserService），通过反射生成一个新的类，新的类可以new实例。

Proxy.newProxyInstance()这个方法甚至帮我们隐藏了反射生成新的类的细节，直接返回了一个新的类的实例。

在代理对象调用任何方法时，都会走到invoke()里。

细节参照	https://www.zhihu.com/question/20794107/answer/658139129 高赞回答。



# 基于cglib实现

...

