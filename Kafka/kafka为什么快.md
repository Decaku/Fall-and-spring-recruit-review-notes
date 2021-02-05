Kafka是吞吐量很大的消息中间件，这种高吞吐量由很多机制保证。



# 顺序IO

kafka的每个partion是一个文件，而在向partion里写入消息时，kafka保证了是append-only write（和redis AOF持久化方式一样），即每次只追加写在文件尾。实际上，磁盘IO的速度慢是由于磁头寻址导致的，而顺序IO大大提高了kafka的速度。但是，append-only write也会没法随机定位消息（消费者是通过offset偏移量来顺序消费），而且不能删除消息数据。

# Zero Copy

使用了零拷贝技术，避免数据在内核态和用户态重复搬运。

![image-20201209152013935](/Users/ferrarizhou/Library/Application Support/typora-user-images/image-20201209152013935.png)

不使用零拷贝技术，kafka发送消息时需要四步。

* 把数据从磁盘拷贝到内核读缓冲区
* 把数据从内核读缓冲区拷贝到应用程序空间缓冲区(kafka client)
* 把数据从应用程序空间缓冲区拷贝到内核socket缓冲区
* 把数据从内核socket缓冲区拷贝到网卡NIC缓冲区

这里发生了两次数据在内核态和用户态之间的搬运，效率不高。

zero-copy使用system call（sendfile）直接把数据从内核读缓冲区搬运到socket缓冲区。

# mmap/page cache

把磁盘文件映射到内存，用户通过修改内存就能修改磁盘上的数据。（由操作系统在适当时候写回磁盘）

mmap有不可靠的缺陷，因为写入mmap的数据实际上还没有真正写入内存

# 批量读写

除了底层技术，kafka还在应用层做了优化。kafka每次传输的消息都不是单条，这样可以避免因为重复发送消息导致的额外带宽开销（毕竟每个数据包除了消息还有一些附加的头字段等）和延迟。

# 批量压缩

会对传递的消息集合进行压缩。