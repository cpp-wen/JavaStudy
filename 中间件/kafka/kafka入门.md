http://www.iocoder.cn/Books/Kafka-books-recommended/

http://www.iocoder.cn/Kafka/install/?self

## 上面是项目入门使用kafka



## 极客专栏 重点记录

**Apache Kafka 是一款开源的消息引擎系统**。

系统 A 发送消息给消息引擎系统，系统 B 从消息引擎系统中读取 A 发送的消息。

消息编码格式：纯二进制的字节序列        序列化框架，比如 Google 的 Protocol Buffer 或 Facebook 的 Thrift。

**点对点模型**

**发布 / 订阅模型**

引入原因：“**削峰填谷**”

至此我们能够完整地串联起 Kafka 的三层消息架构：

- 第一层是主题层，每个主题可以配置 M 个分区，而每个分区又可以配置 N 个副本。
- 第二层是分区层，每个分区的 N 个副本中只能有一个充当领导者角色，对外提供服务；其他 N-1 个副本是追随者副本，只是提供数据冗余之用。
- 第三层是消息层，分区中包含若干条消息，每条消息的位移从 0 开始，依次递增。
- 最后，客户端程序只能与分区的领导者副本进行交互。

顺序化往消息日志上追加内容，同时使用日志段对日志进行划分，定时任务判断日志段是否可以被删除。

重平衡问题 

分区位移  消费者位移

- 消息：Record。Kafka 是消息引擎嘛，这里的消息就是指 Kafka 处理的主要对象。
- 主题：Topic。主题是承载消息的逻辑容器，在实际使用中多用来区分具体的业务。
- 分区：Partition。一个有序不变的消息序列。每个主题下可以有多个分区。
- 消息位移：Offset。表示分区中每条消息的位置信息，是一个单调递增且不变的值。
- 副本：Replica。Kafka 中同一条消息能够被拷贝到多个地方以提供数据冗余，这些地方就是所谓的副本。副本还分为领导者副本和追随者副本，各自有不同的角色划分。副本是在分区层级下的，即每个分区可配置多个副本实现高可用。
- 生产者：Producer。向主题发布新消息的应用程序。
- 消费者：Consumer。从主题订阅新消息的应用程序。
- 消费者位移：Consumer Offset。表征消费者消费进度，每个消费者都有自己的消费者位移。
- 消费者组：Consumer Group。多个消费者实例共同组成的一个组，同时消费多个分区以实现高吞吐。
- 重平衡：Rebalance。消费者组内某个消费者实例挂掉后，其他消费者实例自动重新分配订阅主题分区的过程。Rebalance 是 Kafka 消费者端实现高可用的重要手段。

**Apache Kafka 是消息引擎系统，也是一个分布式流处理平台**

言归正传，Kafka 在设计之初就旨在提供三个方面的特性：

- 提供一套 API 实现生产者和消费者；
- 降低网络传输和磁盘存储开销；
- 实现高伸缩性架构。

kafka与其他主流大数据流式计算框架相比更容易实现端到端的正确性，同时提供非重量级的完整功能系统



 

## 极客时间第三章介绍

线上集群部署方案

部署集群首选 linux 三个原因 io模型 数据网络传输效率 社区支持度  epoll 和poll对比 以及零拷贝技术

RAID 实现负载均衡以及磁盘存储空间

kafka 磁盘需要考虑因素

1. 新增消息数
2. 消息留存时间
3. 平均消息大小
4. 备份数
5. 是否启动压缩



集群参数配置

Broker参数设置：  配置存储信息

1. log.dirs 使用的若干个文件目录路径 使用csv进行分割
2. log.dir

实现故障转移



broker连接相关：

listeners:监听器 告诉外部服务通过什么协议访问指定主机名和端口开放的kafka服务

advertised.listeners:  对外宣称的公布的 监听器

监听器三元组格式<协议名称,主机名,端口号>。

主机名
统一的建议:最好全部使用主机名,即 Broker 端和 Client端应用配置中全部填写主机名。 Broker源代码中也使用主机名



zk参数设置： 作用kafka集群的所有元数据信息，比如哪些broker在运行，创建哪些topic 分区，leader副本信息

多套kafka集群使用同一套zk集群进行管理

zookeeper.connect:csv格式

这样指定:zk1:2181,zk2:2181,zk3:2181/kafka1和
zk1:2181,zk2:2181,zk3:2181/kafka2。



topic参数:

auto.create.topics.enable:是否允许自动创建Topic。 简直设置为false
unclean.leader.election.enable:是否允许Unclean Leader 选举。 建议显示设置为false， unclear指的是是否在多文件副本都挂的情况下给予存储副本信息较少的节点成为leader的机会
auto.leader.rebalance.enable:是否允许定期进行 Leader 选举。 换leader成本很高建议设置为false



数据留存方面：

log.retention.{hour|minutes|ms}:这是个“三兄弟”,都是控制一条消息数据被保存多长时间。从优先级上来说 ms 设置最高、minutes 次之、hour 最低。
log.retention.bytes:这是指定 Broker 为消息保存的总磁盘容量大小。
message.max.bytes:控制 Broker 能够接收的最大消息大小。



topic参数 会覆盖全局参数的值

retention.ms:规定了该 Topic 消息被保存的时长。默认是 7 天,即该 Topic 只保存最近 7 天的消息。一旦设置了这个值,它会覆盖掉 Broker 端的全局参数值。
retention.bytes:规定了要为该 Topic 预留多大的磁盘空间。和全局参数作用相似,这个值通常在多租户的Kafka 集群中会有用武之地。当前默认值是 -1,表示可以无限使用磁盘空间。

max.message.bytes设置处理消息大小

使用kafka-config脚本来调整topic参数



jvm参数设置

业界希望设置大小为6g

jdk8 使用默认的c1垃圾回收就可以了

在启动kafka之前将这些环境变量的大小设置完毕



操作系统关注参数

1. 文件描述符限制 ulimit -n 设置大没事 ，否认会出现 Too many open files
2. 系统文件类型 xfs
3. swappiness  建议设置一个接近0的数字
4. 提交时间 flush落盘时间



## kafka原理剖析（第四章）：

RAID思想：类比kafka分区  同一个topic的分区可以存放在不同的kafka机器上面

分区策略可以自己实现 但是主要的算法也就只有那几种：

1. 轮询策略 （负载均衡表现最优异，数据均匀分布，日常用的最多的算法）
2. 随机策略（就是随机）
3. 消息建保存策略（按照时间戳放入到特定的分区，保证在单个分区中是有序的） 需要指定key 这种分区适用于特定的场景



压缩思想：时间换空间 用较少的cpu对内容进行压缩 减少网络传输  时间换空间思想

kafka使用v1 v2版本压缩

1. 消息头提取
2. crc检验提取消息层进行实现
3. 压缩消息体从消息体字段压缩整个消息集合 压缩

压缩算法使用场景：

1. produce端 （直接props.put(“compression.type”, “gzip”)）
2. broker端（可以直接保存produce产生的数据 但是特定场景需要重新压缩）



两种例外需要重新压缩

1. broker采用新压缩算法 （解压以及再压缩会导致一定时间cpu飙升）
2. boker端发生消息格式转换，新消息格式会向老消息格式转换    **丢失零拷贝优势**



broker 端 对于传递过来的数据总会解压缩 进行数据验证  会增加cpu百分之50消耗

压缩算法  关注（压缩比 以及吞吐量）

1. snappy
2. gzip
3. lz4

是否开始压缩考虑两个因数

1. cpu占用率
2. 带宽使用情况





消息丢失前提：只对已提交的消息做有限度的持久化保存

生产者端：kafka本质使用异步消息发送   无法保证消息发送成功需要带上回调函数producer.send(msg,callback) 这个函数

消费者端: 消息丢失以及消息重复消费问题

隐藏问题：多线程读取消息 某个线程运行失败 导致欺骗 盲目更新位移 （解决方案使用应用程序手动提交位移，consume程序不要开启自动提交位移）

1. 不要使用 producer.send(msg)，而要使用 producer.send(msg, callback)。记住，一定要使用带有回调通知的 send 方法。
2. 设置 acks = all。acks 是 Producer 的一个参数，代表了你对“已提交”消息的定义。如果设置成 all，则表明所有副本 Broker 都要接收到消息，该消息才算是“已提交”。这是最高等级的“已提交”定义。
3. 设置 retries 为一个较大的值。这里的 retries 同样是 Producer 的参数，对应前面提到的 Producer 自动重试。当出现网络的瞬时抖动时，消息发送可能会失败，此时配置了 retries > 0 的 Producer 能够自动重试消息发送，避免消息丢失。
4. 设置 unclean.leader.election.enable = false。这是 Broker 端的参数，它控制的是哪些 Broker 有资格竞选分区的 Leader。如果一个 Broker 落后原先的 Leader 太多，那么它一旦成为新的 Leader，必然会造成消息的丢失。故一般都要将该参数设置成 false，即不允许这种情况的发生。
5. 设置 replication.factor >= 3。这也是 Broker 端的参数。其实这里想表述的是，最好将消息多保存几份，毕竟目前防止消息丢失的主要机制就是冗余。
6. 设置 min.insync.replicas > 1。这依然是 Broker 端参数，控制的是消息至少要被写入到多少个副本才算是“已提交”。设置成大于 1 可以提升消息持久性。在实际环境中千万不要使用默认值 1。
7. 确保 replication.factor > min.insync.replicas。如果两者相等，那么只要有一个副本挂机，整个分区就无法正常工作了。我们不仅要改善消息的持久性，防止数据丢失，还要在不降低可用性的基础上完成。推荐设置成 replication.factor = min.insync.replicas + 1。
8. 确保消息消费完成再提交。Consumer 端有个参数 enable.auto.commit，最好把它设置成 false，并采用手动提交位移的方式。就像前面说的，这对于单 Consumer 多线程处理的场景而言是至关重要的。



冷门功能:拦截器的使用

TCP：连接 生产者创建生产者类的时候就会同全部broker对象进行连接

（我并不建议把集群中所有的 Broker 信息都配置到 bootstrap.servers 中，通常你指定 3～4 台就足以了。因为 Producer 一旦连接到集群中的任一台 Broker，就能拿到整个集群的 Broker 信息，故没必要为 bootstrap.servers 指定所有的 Broker。）

与一台服务器创建连接之后就会去获取集群 元数据信息。

另外两种建立TCP连接的地方

1. 更新元数据后
2. 消息发送时



关闭连接时机

1. 用户主动关闭
2. kafka自动关闭（客户端主动关闭产生close _wait连接）



消息交付可靠性:

- 最多一次（at most once）：消息可能会丢失，但绝不会被重复发送。
- 至少一次（at least once）：消息不会丢失，但有可能被重复发送。
- 精确一次（exactly once）：消息不会丢失，也不会被重复发送。

精确一次需要 幂等性以及事务

幂等性通过空间换时间 使用类似set去重的思想实现

问题：多分区 多会话消息幂等性如何保证

解决方案：采用事务型producer 

幂等性 prodecer 性能更好。抉择使用



**理想情况下，Consumer 实例的数量应该等于该 Group 订阅主题的分区总数。**

老版本将comsumer group  位移保存在zookeeper中来实现服务器无状态来保证自由扩容。但是由于zk 不适合频繁写更新 会照成zk集群效率变低。 所以新版本将位移保存在kafka 内部主题



**todo 这边不是很懂 需要项目配合理解**总结：kafka 创建 consume Group主要是为了解决发布订阅模型 中，一个消费者全量订阅关注主题的所有分区，所以设置消费者组，对于组中的每一个消费者监听分区。如果consumer Group中consumer 大于分区个数 会有部分consumer 



消费这边策略是由Map这样一种格式进行记录

reblance 策略：

三种分配策略：暂时了解就行



consumer_offsets 位移主题 

提交位移主题 topic

提交位移 方式主要有两种分别是 自动提交位移以及手动提交位移

自动提交省事但是会不断提交位移消息 所以需要针对特定消息进行删除策略

策略：compact策略，删除位移主题中过早数据，后台开log cleaner 线程进行检查。类似java中的垃圾回收线程。



消费者组的重平衡可以避免吗？

**所有 Broker 都有各自的 Coordinator 组件**消费者组如何确定为他服务的coordinator组件保存在哪个broker上面通过先计算哪个分区来保存group数据再使用计算出来的分区号计算出来leader副本保存位置 这个broker就是coordinator

重平衡问题避免方法：（目前没有解决方法 无解）

1. 粘性的分区分配方法 在社会版0.11.0.0提出 实际生产环境不多

Rebalance 发生的时机有三个：

- 组成员数量发生变化
- 订阅主题数量发生变化
- 订阅主题的分区数发生变化

1. 仔细地设置**session.timeout.ms 和 heartbeat.interval.ms**的值
2. 排查一下**Consumer 端的 GC 表现**

