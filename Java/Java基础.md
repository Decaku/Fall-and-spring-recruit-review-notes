# String,StringBuffer与StringBuilder的区别

String会产生临时对象，浪费空间。 为啥会产生临时对象，因为String类里的byte数组被final修饰，所以无法在原来的基础上修改。

StringBuilder和StringBuffer不会

StringBuilder不是线程安全的，所以速度快

StringBuffer线程安全





# 抽象类和接口

两者都不能实例化，继承类和实现类必须实现方法后才能实例化

接口更灵活点，毕竟Java不能多继承，这点参考Thread和Runnable