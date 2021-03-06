# 01讲基础架构：一条SQL查询语句是如何执行的

MySQL可以分为Server层和存储引擎层两部分

现在最常用的存储引擎是InnoDB，它从MySQL 5.5.5版本开始成为了默认存储引擎。

长连接会导致oom问题（ 过多资源分配给这个连接导致断开连接之后才会被释放）可以使用短连接（短连接风暴问题）或者使用mysql_reset_connection来初始话连接

查询缓存 使用kv的方式  k是语句v是结果，查询缓存的命中率低所以在8.0之后就移除了

# 02讲日志系统：一条SQL更新语句是如何执行的

redo log（重做日志）和binlog（归档日志）

通过redo日志保证数据库发生异常重启的时候，之前提交的记录都不会丢失，称为 **crash-safe**

redolog 属于引擎层 binlog属于日志层

redolog是在 innodb上保证 **crash-safe**，redolog是物理日志（物理的意思是记录是数据页上进行什么样的修改）binlog是逻辑日志（记录的是执行的语句）

采用两阶段提交的方式 先写redolog (prepare状态) 然后写binlog日志 最后写 redolog （commit）状态

redolog掉电不丢失的具体原因是因为每次都保存到磁盘



# 03讲事务隔离：为什么你改了我还看不见

读提交和可重复读 隔离级别在创建的时候都是创建一张表视图，进行内容的读取。可重复读时指在事务创建的时候创建一张视图表，而读提交是指在每条slq语句执行的时候创建一张视图

mysql 默认隔离级别是可重复读，oracle是读提交，数据库迁移的时候需要修改隔离级别

mvcc多版本并发控制蛮重要

# 04讲深入浅出索引（上）

innodb中主键索引也成为聚簇索引 

innodb中非主键索引也称为二级索引(非聚簇索引)

主键索引的插入会引入页分裂导致效率一定下降这也是使用

# 05讲深入浅出索引（下）

最做匹配原则使用联合索引的方式可以减少索引创建的个数，将经常作为查询的列放在左边

索引下推是指在5.6中通过对索引的判断减少索引的回表的下次，对于条件的判断会通过索引先进行一次验证减少回表的次数

# 06讲全局锁和表锁：给表加个字段怎么有这么多阻碍

全局锁：进行全库逻辑备份

表锁：

行锁

页锁

# 07讲行锁功过：怎么减少行锁对性能的影响

在innodb中行锁是在需要的时候进行加上，不需要的话没有立刻释放，而是等事务执行完毕后在释放，称为 **两阶段协议**

数据库发送死锁 解决的两种方式

1. 设置超时策略
2. 发起死锁检测

# 08讲事务到底是隔离的还是不隔离的（mvcc具体不是理解）

mvcc深入了解 

多版本并发控制级别下每条语句的执行会根据事务 tranction_id添加一个隐藏的row trx_id 来标志这条执行sql语句 ，语句开始执行的时候或者是事务开启的时候这个时候会获取到当前所有语句正在执行的一个数组，若读取语句的row_trx_id比最小的那个数组中最小的row_id还小，表示可以访问，比最大的row_id大表示不可能访问，在两者之间的需要判断访问的资源row_id是否在数组中如果不在表示可以访问，如果在的话表示不能访问，因为当事务执行的时候这条语句还是活跃的。

# 09讲普通索引和唯一索引，应该怎么选择

两者查询的区别，普通索引在获取到元素之后会进行向下查找直到找到不匹配的元素，唯一索引找到元素后立马返回。

两者写入内存区别，普通索引可以直接写入change buffer ，而唯一索引不能使用changebuffer 需要判断是否发生唯一性的冲突。

# 10讲MySQL为什么有时候会选错索引



# 20讲幻读是什么，幻读有什么问题



# 22讲MySQL有哪些“饮鸩止渴”提高性能的方法

1. 短连接风暴 
2. 减少连接过程的消耗

慢查询的问题

1. 索引没有设计好 （mysql5.6 版本之后支持Online DDL 直接修改table）
2. 语句没有写好
   1. 对索引字段做函数操作，会破坏索引值的有序性，优化器放弃走B+搜索功能
   2. 隐式类型转换 mysql 进行数值比较的时候 会将字符型转换为整数型
   3. 隐式字符转换，两张表进行联合查询 使用的字符集编码不同的时候会导致，短的字符集向高的字符集进行转换使用隐式 函数
3. MYSQL 选错了索引（优化器相关若索引需要回表 效率上有可能不如主键索引，而不走索引）





数据库问题总结

1. 二次写  来保证redo日志的 
2. jplog
3. 数据库日志
4. hash 索引和b+索引 hash会将所有的数据读到内存中
5. innodb 自适应hash  数据预热
6. 聚簇索引按照逻辑上去存储  
7. binary log  备数据库拉起主数据库的内容
8. 读写分离如何保证 节点同步
9. 大表怎么优化 建立索引 分库分表 
10. myisam不能回滚  聚簇索引 非聚簇索引 使用
11. 四大特性 引擎
12. 间隙锁
13. redo log undo log



2.http2.0  改进？优势？  
3.http 带来潜在问题 ？ http3.0了解么？
4.用http如何解决安全性问题
5.http用jwt加密怎么办？如何加强安全性不用https的情况下
6.http 和jwt 和https相比，安全性如何？
7.跨站攻击和中间人攻击说一下？
8.http常见的header有哪些？
图解http可以解决以上问题：书到了看书即可！！！！一天
9.tcp 4次挥手为什么？不用三次？ Time wait 防止什么？ 




四次挥手的原因
客户端发送了 FIN 连接释放报文之后，服务器收到了这个报文，就进入了 CLOSE-WAIT 状态。这个状态是为了让服务器端发送还未传送完毕的数据，传送完毕之后，服务器会发送 FIN 连接释放报文。
Time wait 
* 确保最后一个确认报文能够到达。如果 B 没收到 A 发送来的确认报文，那么就会重新发送连接释放请求报文，A 等待一段时间就是为了处理这种情况的发生。
* 等待一段时间是为了让本连接持续时间内所产生的所有报文都从网络中消失，使得下一个新的连接不会出现旧的连接请求报文。
10. ping 头条1.2.3.4 有什么数据交互？
Ping 的原理是通过向目的主机发送 ICMP Echo 请求报文，目的主机收到之后会发送 Echo 回答报文。Ping 会根据时间和成功响应的次数估算出数据包往返时间以及丢包率。
10. 局域网在ping的时候做了哪些转换？
11. Mac层数据的转换
MAC 地址是链路层地址，长度为 6 字节（48 位），用于唯一标识网络适配器（网卡）。
一台主机拥有多少个网络适配器就有多少个 MAC 地址。例如笔记本电脑普遍存在无线网络适配器和有线网络适配器，因此就有两个 MAC 地址。
10. ip Mac 和目的ip 和Mac的转换（没答出来）
每个主机都有一个 ARP 高速缓存，里面有本局域网上的各主机和路由器的 IP 地址到 MAC 地址的映射表。
如果主机 A 知道主机 B 的 IP 地址，但是 ARP 高速缓存中没有该 IP 地址到 MAC 地址的映射，此时主机 A 通过广播的方式发送 ARP 请求分组，主机 B 收到该请求后会发送 ARP 响应分组给主机 A 告知其 MAC 地址，随后主机 A 向其高速缓存中写入主机 B 的 IP 地址到 MAC 地址的映射。
10. kill - 9  操作系统拿到这个命令做了哪些操作kill 和kill - 9 有什么区别？
11. 如何用shell 筛选本地ip地址？ vim底层实现？
12. redis 分布式锁如何实现？底层命令是什么？原理？怎么实现的？如何保持原子性？ set和setnex 区别？5个线程争抢锁，如何判断抢成功失败？过程？
线程id 去判断是否加锁成功？
10. lru  怎么实现？如何set 的过程？ hashmap里面存的是什么？时间复杂度？on（1）存 取都是1
11. racketMq kafeka  和rockemq的区别？为什么选择racketMq？