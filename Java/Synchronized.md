关于synchronized锁对象和锁方法，其实本质上都是锁对象

锁膨胀过程  偏向锁->轻量级锁(包括自旋锁和自适应自旋锁)->重量级锁

https://www.cnblogs.com/kubidemanong/p/9520071.html



CAS操作？ ABA问题：加版本号 时间戳 

