kafka可以对已提交的消息做有限程度的持久化保证。“已提交的消息”，至少有一个Broker收到了消息，“有限程度”，总不能所有机器宕机，地球爆炸也能保证消息不丢失吧，除非把消息备份到火星去。

# Producer

producer.send(msg)发送消息是异步的，也就是发送完以后就不会再去管这条消息，那么一旦消息丢失就没有办法及时处理了（fire and forget）。正确的做法是设置回调函数，producer.send(msg,callback),发送失败时producer能及时得到信息并设置处理逻辑，如重新发送。

设置ACK确认机制，partition的master向所有slave收集ack后，把信息整合发送给producer。



# Consumer

维持先消费消息，再更新offset，否则先更新offset后consumer宕机就丢失了一条消息。但是先消费消息可能带来重复消费同一条消息的问题，比如消费完以后还没来得及提交offset，consumer宕机了。



# 配置

* ack=all 
* retries=3 producer
* replication.factor = 3 
* min.insync.replicas > 1
* unclean.leader.election.enable = false
* ...



