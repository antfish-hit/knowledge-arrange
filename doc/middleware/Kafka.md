## 对Kafka有什么了解吗？
Kafka是一个分布式流式处理平台。在Java Web开发里可以用作**消息队列**。
有如下特点：
- 高吞吐量、低延迟：每秒可处理几十万条消息，延迟是毫秒级的。
- 可扩展性、容错性：支持集群扩展，部分节点故障不影响整体功能。
- 持久性、可靠性：消息会被持久化到本地磁盘，并且支持数据备份。
- 高并发：支持很多客户端同时读写。
## 和其他消息队列相比，Kafka的优势在哪里？
1. **性能强大**：高吞吐，低延迟。
2. **生态系统兼容性高**：Kafka与周边生态系统的兼容性是最好的没有之一，尤其在大数据和流计算领域。
## Kafka为什么这么快？
- 顺序写入优化：将消息顺序写入磁盘意味着更少的磁盘寻道时间。
- 批量处理技术：生产者可以批量发送数据Kafka，减少了网络开销和IO操作次数。
- 零拷贝技术：可以直接将数据从磁盘发送到网络套接字，避免了在用户空间和内核空间之间的多次数据拷贝。
- 压缩技术：支持对消息进行压缩，减少了网络传输的数据量。
## Kafka的消息模型知道吗？
### 发布-订阅模型：Kafka消息模型
![](_v_images/20210225160142920_1753.png)
发布订阅模型：发布者发布一条消息至指定topic，订阅了该topic的所有订阅者就会收到消息。
## 什么是Producer、Consumer、Broker、Topic、Partition？
Kafka将生产者发布的消息发送到**Topic(主题)**中，需要这些消息的消费者可以订阅这些**Topic(主题)**：
![](_v_images/20210225160823705_21052.png)

1. **Producer(生产者)**：产生消息的一方。
2. **Consumer(消费者)**：消费消息的一方。
3. **Broker(代理)**：可以看做是一个独立的Kafka实例。多个`Kafka Broker`组成一个`Kafka Cluster`。

每个Broker中又包含了Topic和Partition：

1. **Topic(主题)**：Producer将消息发送到特定的主题，Consumer通过订阅特定的Topic来消费消息。
2. **Partition(分区)**：Partition属于Topic的一部分，一个Topic可以有多个Partition，并且同一Topic下的Partition可以分布在不同的Broker上，这也就表明一个Topic可以横跨多个Broker。Partiton可以对应于消息队列中的队列。
## Kafka的多副本机制了解吗？带来了什么好处？
Kafka为分区(Partition)引入了多副本(Replica)机制。分区(Partition)中的多个副本之间会有一个`leader`，其余称为`follower`。我们发送的消息会被发送到`leader`副本，然后`follower`副本才能从`leader`副本中拉取消息进行同步。

> 生产者和消费者只与`leader`副本交互。可以理解为其他副本只是`leader`副本的拷贝，他们的存在只是为了保证消息存储的安全性。当`leader`副本发生故障时会从`follower`中选举出一个`leader`，但是`follower`中如果有和`leader`同步程度达不到要求的参加不了`leader`的竞选。

### 多分区和多副本的好处
1. Kafka通过给特定Topic指定多个Partition，而各个Partition可以分布在不同的Broker上，这样便能提供比较好的并发能力（负载均衡）。
2. Partition可以指定对应的Replica数，这也极大地提高了消息存储的安全性，提高了容灾能力，不过也相应的增加了所需要的存储空间。
#### 简化版
1. 多分区可以应用负载均衡，提高并发能力。
2. 多副本提高灾备能力。
## Kafka如何保证消息的消费顺序？
- 每次添加消息到分区中都会采用尾加法，会被分配一个特定的偏移量(offset)来保证消息的顺序。Kafka只能为我们保证分区中的消息有序，而不能保证跨分区的消息有序。
- 跨分区保证消息顺序可以考虑以下方案：
	- 只使用一个分区（不推荐）
	- 业务层面通过对消息编号或增加时间戳等标识进行排序处理（不推荐）。
	- 发送消息的时候指定key/Partition（推荐）。
## Kafka为什么一个分区只能由消费者组的一个消费者消费？这样设计的意义是什么？
因为多个消费者消费同一个分区的消息等于多线程并发消费同一组消息，会造成消息处理的重复，浪费资源，且不能保证消息的顺序。
## 消费线程数和分区数的关系是怎么样的？
默认一个线程可以消费所有分区的数据。所以消费线程数最好等于分区数，多出的线程因为不会被分配到任何分区，所以只是浪费资源。
## Kafka如何保证消息不丢失
### 生产者丢失消息的情况
生产者丢失消息的情况可能是因为网络问题导致消息没发送过去，可以设置消息发送失败重试。
### 消费者丢失消息的情况
消费者丢失消息可能是在拉取消息并自动提交offset之后，消费者在消费完消息之前出故障了，所以可以设置每次在真正消费完消息之后手动提交offset。
### Kafka弄丢了消息
#### leader所在的broker挂掉但是follower还没从leader那完全同步消息
1. 设置`acks = all`，但是延迟会很高
    - `acks`的默认值为1，代表我们的消息被leader副本接收之后就算被成功发送。当我们配置`acks = all`代表所有副本都要接收到该消息之后该消息才算真正成功被发送。
2. 设置`replication.factor >= 3`
    - 为了保证leader副本能有follower副本能同步消息，我们一般会为topic设置replication.factor >= 3。这样就可以保证每个Partition至少有3个副本。虽然造成了数据冗余，但是带来了数据的安全性。
3. 设置`min/insync.replicas > 1`
    - 一般情况下我们还需要设置`min.insync.replicas > 1`，这样配置代表消息至少要被写入到2个副本才算是被成功发送。`min.insync.replicas`的默认值为1，在实际生产中应尽量避免默认值1。
    - 但是，为了保证整个Kafka服务的高可用性，你需要确认`replication.factor > min.insync.replicas`。为什么呢？设想一下加入两者相等的话，只要是有一个副本挂掉，整个分区就无法正常工作了。这明显违反高可用性！一般推荐设置成`replication.factor = min.insync.replicas + 1`。
## Kafka 如何保证消息不重复消费？
出现消息重复消费的原因：
- 服务端已经消费的数据没有成功提交offset。
解决方案：
- 消费消息的服务做幂等校验，例如使用唯一ID作为幂等性的标识符，确保同样的操作只会被执行一次。
## Kafka 重试机制
- 当消息消费失败时，Kafka默认会重试至多10次，且间隔为0，如果还是失败则认为消息消费失败。
- 可以自定义重试次数和间隔，也可以自定义重试失败的逻辑，手动实现对应ErrorHandler即可。
- 对于消费失败的消息，可以考虑在重试失败的处理逻辑里将其发送到事先设置的死信队列中，再进一步分析和处理。