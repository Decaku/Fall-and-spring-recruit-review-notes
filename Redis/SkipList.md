众所周知，redis中zset数据结构基于跳表来实现，而跳表就是普通的链表上提取部分结点成为新的链表来加快查询速度。 

但是关于跳表这种数据结构时间和空间复杂度的证明却少之又少。

如果需要深入学习，请看这篇blog：https://blog.csdn.net/yaling521/article/details/78130271

论文：https://klevas.mif.vu.lt/~ragaisis/ADS2006/skiplists.pdf



如果想快速学习，知道这种数据结构的用法，请看这里：https://zhuanlan.zhihu.com/p/68516038