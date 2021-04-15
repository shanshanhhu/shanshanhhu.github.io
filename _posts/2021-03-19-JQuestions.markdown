---
title: JQuestions
date: 2021-03-19 15:50:22 +0800
categories: [others, notes]
tags: [notes]
---

# Why do you choose XX tools?
## Flink
### Advantages
- it supports both stream processing and batch processing.
- low latency, high throughput.  Flink can process hundreds of millions messages or events every second with only milliseconds of latency.
- it provides the `Exactly-once` consistency semantics. This guarantees data security.
### History


### Spark & Flink
**不同点**
- 从流处理的角度来讲，Spark基于微批量处理，把流数据看成是一个个小的批处理数据块分别处理，所以延迟性只能做到秒级。
而Flink基于每个事件处理，每当有新的数据输入都会立刻处理，是真正的流式计算，支持毫秒级计算。
由于相同的原因，Spark只支持基于时间的窗口操作（处理时间或者事件时间），而Flink支持的窗口操作则非常灵活，不仅支持时间窗口，还支持基于数据本身的窗口(另外还支持基于time、count、session，以及data-driven的窗口操作)，开发者可以自由定义想要的窗口操作。
- 从迭代计算的角度来讲，Spark对机器学习的支持很好，因为可以在内存中缓存中间计算结果来加速机器学习算法的运行。但是大部分机器学习算法其实是一个有环的数据流，在Spark中，却是用无环图来表示。
而Flink支持在运行时间中的有环数据流，从而可以更有效的对机器学习算法进行运算。
- 从SQL功能的角度来讲，Spark和Flink分别提供SparkSQL和Table API提供SQL交互支持。两者相比较，Spark对SQL支持更好，相应的优化、扩展和性能更好，而Flink在SQL支持方面还有很大提升空间。
- 从相应的生态系统角度来讲，Spark 的社区无疑更加活跃。Spark可以说有着Apache旗下最多的开源贡献者，而且有很多不同的库来用在不同场景。
而Flink由于较新，现阶段的开源社区不如Spark活跃，各种库的功能也不如Spark全面。但是Flink还在不断发展，各种功能也在逐渐完善。

**相同点**
- 都基于内存计算；
- 都有完善的错误恢复机制；
- 都支持Exactly once的语义一致性。
- 都有统一的批处理和流处理API，都支持类似SQL的编程接口；
- 都支持很多相同的转换操作，编程都是用类似于Scala Collection APl的函数式编程模式；

## HBase
[Difference Hbase, RDBMS Advantages Hbase](https://beyondcorner.com/learn-apache-hbase/difference-hbase-rdbms-advantages-hbase/#:~:text=Advantage%20of%20HBase%201%20Hbase%20can%20easily%20handle,system%20even%20on%20commodity%20hardware.%20More%20items...%20)
基于HDFS，支持海量数据读写（尤其是写），支持上亿行、上百万列的，面向列的分布式NoSql数据库。天然分布式，主从架构，不支持事务，不支持二级索引，不支持sql。
### advantages & disadvantages
1. **advantages**
- 列可动态扩展。比如人员信息表中需要添加一个新的“address”字段，MySQL需要提前alter表，HBase的话直接插入即可。
- Hbase支持高并发读写操作
- 表的稀疏性。能节省空间。HBase是把每一列都看做是一条记录，row+列名作为key，data作为value，依次存放。假如某一行的某一个列没有数据，则直接跳过该列。
![](../assets/img/sample/hbase-mysql.jpg)
- Hbase自动切分数据，scalability.
2. **disadvantages**
- 不能支持条件查询，只支持按照Row key来查询.
- 暂时不能支持Master server的故障切换,当Master宕机后,整个存储系统就会挂掉.

## Mysql
关系型数据库，主要面向OLTP（On-Line Transaction Processing联机事务处理），支持事务，支持二级索引，支持sql，支持主从、Group Replication架构模型。
### advantages & disadvantages
1. **advantages**
1.数据之间有关系，进行数据的增删改查的时候是非常方便的
2.支持事务操作，保证数据的完整性和一致性。
2. **disadvantages**
### Clustered Index(聚簇索引) & Nonclustered Index(非聚簇索引)
[聚簇索引与非聚簇索引](https://cloud.tencent.com/developer/article/1541265)
聚簇索引：将数据存储与索引放到了一块，找到索引也就找到了数据
非聚簇索引：将数据存储于索引分开结构，索引结构的叶子节点指向了数据的对应行，myisam通过key_buffer把索引先缓存到内存中，当需要访问数据时（通过索引访问数据），在内存中直接搜索索引，然后通过索引找到磁盘相应数据，这也就是为什么索引不在key buffer命中时，速度慢的原因
#### InnoDB(聚簇) & MyISAM(非聚簇)
InnoDB 只聚集在同一个页面中的记录

# Elastic Search Inverted Index(倒序索引)？

# kafka messages blocked?
# kafka消费乱序
[kafka 如何保证消息的有序性](https://blog.csdn.net/qq_43771096/article/details/106449794)

# Kafka
## __consumer_offsets
__consumer_offsets 是 Kafka 内部使用的一个 topic，专门用来存储 group 消费的情况，默认情况下有50个 partition，每个 partition 三副本，而具体 group 的消费情况要存储到哪一个 partition 上，是根据 abs(GroupId.hashCode()) % NumPartitions 来计算（其中，NumPartitions 是__consumer_offsets 的 partition 数，默认是50个）的。
Consumer通过发送OffsetCommitRequest请求到指定broker（偏移量管理者）提交偏移量。这个请求中包含一系列分区以及在这些分区中的消费位置（偏移量）。偏移量管理者会追加键值（key－value）形式的消息到一个指定的topic（__consumer_offsets）。key是由consumerGroup-topic-partition组成的，而value是偏移量。

## Group Coordinator
Coordinator一般指的是运行在broker上的group Coordinator，用于管理Consumer Group中各个成员，每个KafkaServer都有一个GroupCoordinator实例，管理多个消费者组，主要用于offset位移管理和Consumer Rebalance。

## Kafka Rebalance
[Kafka的Rebalance机制可能造成的影响及解决方案](https://blog.csdn.net/lzxlfly/article/details/106246879)
### 可能原因
1. topic分区个数的增加
2. 对Topic的订阅发生变化
3. 消费组成员的加入或离开
### 影响
1. 可能重复消费: Consumer被踢出消费组，可能还没有提交offset，Rebalance时会Partition重新分配其它Consumer,会造成重复消费，虽有幂等操作但耗费消费资源，亦增加集群压力
2. 集群不稳定：Rebalance扩散到整个ConsumerGroup的所有消费者，因为一个消费者的退出，导致整个Group进行了Rebalance，并在一个比较慢的时间内达到稳定状态，影响面较大
3. 影响消费速度：频繁的Rebalance反而降低了消息的消费速度，大部分时间都在重复消费和Rebalance

## Kafka消息挤压
### 监控消息堆积
1. kafka-consumer-groups.sh
> ./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group consumer-group-name
2. JMX（Java Management Extensions）
### 如何解决
1. 消费者线程池。增强消费者的消费能力，使用线程池消费或者将消费者中耗时业务改成异步.
2. 使用Kafka时，消费者每次poll的数据业务处理时间不能超过kafka的max.poll.interval.ms，可以考虑调大超时时间或者调小每次poll的数据量。
增加max.poll.interval.ms处理时长(默认间隔300s)
max.poll.records： 一次poll返回的最大记录数默认是500。

### Kafka命令
- 创建topic
> kafka-topics --create --zookeeper master:2181/kafka2 --replication-factor 2 --partitions 3 --topic demotopic
- 列出topic
> kafka-topics --list --zookeeper master:2181/kafka2
- 描述topic
> kafka-topics --describe --zookeeper master:2181/kafka2 --topic demotopic
- 生产者生产消息
> kafka-console-producer --broker-list master:9092 --topic demotopic
- 消费者消费消息并指定消费组名
> kafka-console-consumer --bootstrap-server master:9092,node01:9092,node02:9092 --new-consumer --consumer-property group.id=test_kafka_game_x_g1 --topic demotopic
- 查看正在运行的消费组
> kafka-consumer-groups --bootstrap-server master:9092 --list --new-consumer
- 计算消息的消息堆积情况
> kafka-consumer-groups --bootstrap-server master:9092 --describe --group test_kafka_game_x_g1


## Kafka消费者分配策略？ Kafka 一个consumer group 3个consumer消费5个partition, 如何分配？
1. RangeAssignor
2. RoundRobinAssignor
3. StickyAssignor

## Kafka和RabbitMQ有什么区别？
1. 应用场景
- RabbitMQ
RabbitMQ遵循AMQP协议，由内在高并发的erlanng语言开发，用在实时的对可靠性要求比较高的消息传递上，适合企业级的消息发送订阅。
- kafka
kafka是一种消息发布订阅系统,它主要用于处理活跃的流式数据,大数据量的数据处理上。常用日志采集，数据采集上。

2. 架构模型
- RabbitMQ
RabbitMQ遵循AMQP协议，RabbitMQ的broker由Exchange,Binding,queue组成，其中exchange和binding组成了消息的路由键；客户端Producer通过连接channel和server进行通信，Consumer从queue获取消息进行消费（长连接，queue有消息会推送到consumer端，consumer循环从输入流读取数据）。rabbitMQ以broker为中心；有消息的确认机制。
- kafka
kafka遵从一般的MQ结构，producer，broker，consumer，以consumer为中心，消息的消费信息保存的客户端consumer上，consumer根据消费的点，从broker上批量pull数据；无消息确认机制。

3. 吞吐量
- kafka
kafka具有高的吞吐量，内部采用消息的批量处理，zero-copy机制，数据的存储和获取是本地磁盘顺序批量操作，具有O(1)的复杂度，消息处理的效率很高。
- rabbitMQ
rabbitMQ在吞吐量方面稍逊于kafka，他们的出发点不一样，rabbitMQ支持对消息的可靠的传递，支持事务，不支持批量的操作；基于存储的可靠性的要求存储可以采用内存或者硬盘。

4. 可用性
- rabbitMQ
rabbitMQ支持miror的queue，主queue失效，miror queue接管。
- kafka
kafka的broker支持主备模式。

5. 集群负载均衡，
- kafka
kafka采用zookeeper对集群中的broker、consumer进行管理，可以注册topic到zookeeper上；通过zookeeper的协调机制，producer保存对应topic的broker信息，可以随机或者轮询发送到broker上；并且producer可以基于语义指定分片，消息发送到broker的某分片上。
- rabbitMQ rabbitMQ的负载均衡需要单独的loadbalancer进行支持。

## Redis消息的过期清除机制？
[redis的过期时间设置和过期删除机制](https://blog.csdn.net/jiangchunhui2009/article/details/81504073)
三种不同的删除策略：
1. 立即删除。在设置键的过期时间时，创建一个回调事件，当过期时间达到时，由时间处理器自动执行键的删除操作。
2. 惰性删除。键过期了就过期了，不管。每次从dict字典中按key取值时，先检查此key是否已经过期，如果过期了就删除它，并返回nil，如果没过期，就返回键值。
3. 定时删除。每隔一段时间，对expires字典进行检查，删除里面的过期键。
### redis里面对这些key的过期时间和生存时间的信息是怎么保存的？
在数据库结构redisDb中的expires字典中保存了数据库中所有键的过期时间，我们称expire这个字典为过期字典。
1. 过期字典是一个指针，指向键空间的某个键对象。
2. 过期字典的值是一个longlong类型的整数，这个整数保存了键所指向的数据库键的过期时间–一个毫秒级的 UNIX 时间戳。
![](../assets/img/sample/redis-expire-dict.png)
## Spring, SpringMVC, Springboot, Spring Cloud对比各有什么特点？
[SPRING、SPRINGMVC、SPRINGBOOT和SPRINGCLOUD的联系与区别](https://www.ngui.cc/51cto/show-551569.html)
- spring 是一个一站式的轻量级的java开发框架，核心是控制反转（IOC）和面向切面（AOP）.
- springMvc是spring基础之上的一个MVC框架，主要处理web开发的路径映射和视图渲染，属于spring框架中WEB层开发的一部分。其XML、config等配置相对比较繁琐复杂；
- springBoot框架相对于springMvc框架来说，更专注于开发微服务后台接口，不开发前端视图，同时遵循默认优于配置，简化了插件配置流程，不需要配置xml，相对springmvc，大大简化了配置流程。
- spring cloud大部分的功能插件都是基于springBoot去实现的，springCloud关注于全局的微服务整合和管理，将多个springBoot单体微服务进行整合以及管理； springCloud依赖于springBoot开发，而springBoot可以独立开发。
## 画出B+树结构
![](../assets/img/sample/b-puls-plus.png)
## Mysql最左索引匹配原则
- 最左匹配原则
(A,B,C) 这样3列，mysql会首先匹配A，然后再B，C。如果用(B,C)这样的数据来检索的话，就会找不到A使得索引失效。如果使用(A,C)这样的数据来检索的话，就会先找到所有A的值然后匹配C，此时联合索引是失效的。把最常用的，筛选数据最多的字段放在左侧。
- 原因：
联合索引(A,B,C)是一棵B+Tree，其非叶子节点存储的是第一个关键字的索引，而叶节点存储的则是三个关键字A、B、C三个关键字的数据，且按照A、B、C的顺序进行排序。当执行以下查询的时候，是无法使用这个联合索引的。
select * from STUDENT where B='b';因为联合索引中是先根据A进行排序的。如果A没有先确定，直接对B和C进行查询的话，就相当于乱序查询一样，因此索引无法生效，查询是全表查询。
## Mysql B-Tree Index & Hash Index
[Comparison of B-Tree and Hash Indexes](https://dev.mysql.com/doc/refman/8.0/en/index-btree-hash.html#hash-index-characteristics)
- A B-tree index can be used for column comparisons in expressions that use the =, >, >=, <, <=, or BETWEEN operators. The index also can be used for LIKE comparisons if the argument to LIKE is a constant string that does not start with a wildcard character.
The following SELECT statements do not use indexes:
> SELECT * FROM tbl_name WHERE key_col LIKE '%Patrick%';  // the LIKE value begins with a wildcard character.
> SELECT * FROM tbl_name WHERE key_col LIKE other_col;   // the LIKE value is not a constant.
They are used only for equality comparisons that use the = or <=> operators (but are very fast). They are not used for comparison operators such as < that find a range of values.
### Hash Index:
- The optimizer cannot use a hash index to speed up ORDER BY operations. (This type of index cannot be used to search for the next entry in order.)
- MySQL cannot determine approximately how many rows there are between two values (this is used by the range optimizer to decide which index to use). This may affect some queries if you change a MyISAM or InnoDB table to a hash-indexed MEMORY table.

Only whole keys can be used to search for a row. (With a B-tree index, any leftmost prefix of the key can be used to find rows.)

##  ElasticSearch索引存储，如何能节省内存。
[Elasticsearch索引机制](https://zhuanlan.zhihu.com/p/137574234)
- Finite State Transducers --compress index-->Inverted Index
- Frame of Reference
- Roaring Bitmaps


# 分布式锁
## Redis分布式锁
[浅析Redis分布式锁解决方案](https://www.infoq.cn/article/dvaaj71f4fbqsxmgvdce)
- 单机实现Redis的分布式锁
- Redlock --- 基于Redis多机实现的分布式锁
- 基于zookeeper实现分布式锁

## Synchronized 对象级别锁 & 类级别锁
[](https://www.cnblogs.com/CreateMyself/p/12363526.html)
- 对象级别锁，针对非静态方法执行同步块锁定。同步块中的监视器或锁定对象是基于对象实例的。
> 使用对象锁的情况，只有使用同一实例的线程才会受锁的影响，多个实例调用同一方法也不会受影响。
- 若在同步方法中存在静态数据，为保持静态数据线程安全，我们则需使用类级别锁，这意味着，
如果在运行时有多个实例的Test，则一次只能在一个实例中的一个线程上执行一个线程LockMethod()，而所有其他实例将被其他线程锁定。
> 类锁是所有线程共享的锁，所以同一时刻，只能有一个线程使用加了锁的方法或方法体，不管是不是同一个实例。

## ReentrantLock
[AQS](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)
[ReentrantLock](https://crossoverjie.top/2018/01/25/ReentrantLock/)
[Synchronize和ReentrantLock区别](https://juejin.cn/post/6844903695298068487)
相比于synchronized，ReentrantLock在功能上更加丰富，它具有可重入、可中断、可限时、公平锁等特点。
`tryLock()`
```
public class Counter {
    private final Lock lock = new ReentrantLock();
    private int count;

    public void add(int n) {
        lock.lock();
        try {
            count += n;
        } finally {
            lock.unlock();
        }
    }
}

if (lock.tryLock(1, TimeUnit.SECONDS)) {  //尝试获取锁。在尝试获取锁的时候，最多等待1秒。如果1秒后仍未获取到锁，tryLock()返回false，程序就可以做一些额外处理，而不是无限等待下去。
所以，使用ReentrantLock比直接使用synchronized更安全，线程在tryLock()失败的时候不会导致死锁。
    try {
        ...
    } finally {
        lock.unlock();
    }
}
```
# 设计模式
[设计模式](https://mp.weixin.qq.com/s/Wahq4TnCm4Pzb6VshWma1Q)
1. 单例模式：就是一个应用程序中，某个类的实例对象只有一个，你没有办法去new，因为构造器是被private修饰的，一般通过getInstance()的方法来获取它们的实例。
2. 观察者模式: 对象间一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。
3. 装饰者模式:
4. 适配器模式: 将两种完全不同的事物联系到一起，就像现实生活中的变压器。
5. 工厂模式: 一个抽象类，多个抽象类的实现类，一个工厂类，用来实例化抽象的接口
6. 代理模式（proxy）：
# abstract & interface
1. 抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
3. 接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。
## abstract
## interface
变量：public static final
方法：public abstract

# Spring / Spring MVC
Spring是一个轻量级的控制反转(IoC)和面向切面(AOP)的容器框架。
- 控制反转：
Spring通过一种称作控制反转（IoC）的技术促进了松耦合。当应用了IoC，一个对象依赖的其它对象会通过被动的方式传递进来，而不是这个对象自己创建或者查找依赖对象。你可以认为IoC与JNDI相反——不是对象从容器中查找依赖，而是容器在对象初始化时不等对象请求就主动将依赖传递给它。
- 面向切面：

## AOP
它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。
## IOC
借助于“第三方”实现具有依赖关系的对象之间的解耦。由于IOC容器的加入，对象A依赖于对象B，那么对象A在初始化或者运行到某一点的时候，自己必须主动去创建对象B或者使用已经创建的对象B。对象A与对象B之间失去了直接联系，所以，当对象A运行到需要对象B的时候，IOC容器会主动创建一个对象B注入到对象A需要的地方。
## spring 常用的注入方式有哪些？
构造方法注入
setter注入
基于注解的注入
## @RequestMapping 的作用是什么？
RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

# HBase
[Hbase的数据读写流程](https://zhuanlan.zhihu.com/p/65513466)
## HBase数据的写入过程
1、Client访问zookeeper，获取元数据存储所在的regionserver
2、通过刚刚获取的地址访问对应的regionserver，拿到对应的表存储的regionserver
3、去表所在的regionserver进行数据的添加
4、查找对应的region，在region中寻找列族，先向memstore中写入数据
5、当memstore写入的值变多，触发溢写操作（flush），进行文件的溢写，成为一个StoreFile

## .HLog的的功能：宕机数据恢复
在分布式系统环境中，我们是无法避免系统出错或者宕机的，一旦HRegionServer意外退出，
MemStore中的内存数据就会丢失，而引入HLog就是为了防止这种情况。
工作机制：每个HRegionServer中都会有一个HLog对象，HLog是一个实现Write Ahead Log的类，
每次用户操作写入Memstore的同时，也会写一份数据到HLog文件中，HLog文件定期会滚动出新，
并删除旧的文件（已持久化到Storefile中的数据），当HRegionServer意外终止后，
HMaster会通过Zookeeper感知，HMaster首先处理遗留的HLog文件，将不同region的log数据拆分，
分别放在相应region目录下，然后再将失效的region（带有刚刚拆分的log）重新分配，
领取到这些region的HRegionServer在Load Region的过程中，会发现有历史HLog需要处理，因此会Replay HLog中的数据到Memstore中，然后flush到StoreFile，完成数据恢复。

## HBase数据的读取流程

1. Client访问zookeeper，获取元数据存储所在的regionserver
2. 通过刚刚获取的地址访问对应的regionserver，拿到对应的表存储的regionserver
3. 去表所在的regionserver进行数据的读取
4. 查找对应的region，在region中寻找列族，先找到memstore，找不到去blockcache中寻找，再找不到就进行storefile的遍历
5. 找到数据之后会先缓存到blockcache中，再将结果返回
blockcache逐渐满了之后，会采用LRU的淘汰策略。

### HBase为什么读比写的速度快？
[深入理解什么是LSM-Tree](https://blog.csdn.net/u010454030/article/details/90414063)

## 公平锁&非公平锁

# Java8 语言特性
[](https://www.javacodegeeks.com/java-8-features-tutorial.html)
[Java 8的新特性—终极版](https://www.jianshu.com/p/5b800057f2d8)
## Lambda表达式
允许我们将函数当成参数传递给某个方法。
```
Arrays.asList( "a", "b", "d" ).forEach( e -> System.out.println( e ) );
// Lambda表达式可以引用类成员和局部变量（会将这些变量隐式得转换成final的）
String separator = ",";
Arrays.asList( "a", "b", "d" ).forEach(
    ( String e ) -> System.out.print( e + separator ) );
// Lambda表达式有返回值，返回值的类型也由编译器推理得出
Arrays.asList( "a", "b", "d" ).sort( ( e1, e2 ) -> e1.compareTo( e2 ) );
```
## 接口的默认方法和静态方法

# 堆和栈的区别
[堆和栈](https://iamjohnnyzhuang.github.io/java/2016/07/12/Java%E5%A0%86%E5%92%8C%E6%A0%88%E7%9C%8B%E8%BF%99%E7%AF%87%E5%B0%B1%E5%A4%9F.html)
- 功能不同
栈内存用来存储局部变量和方法调用。
堆内存用来存储Java中的对象。无论是成员变量，局部变量，还是类变量，它们指向的对象都存储在堆内存中。
- 共享性不同
栈内存是线程私有的。
堆内存是所有线程共有的。
- 异常错误不同
如果栈内存或者堆内存不足都会抛出异常。
栈空间不足：java.lang.StackOverFlowError。
堆空间不足：java.lang.OutOfMemoryError。
- 空间大小
栈的空间大小远远小于堆的。

# Jvm有哪些垃圾回收算法?
[JVM垃圾回收算法](https://juejin.cn/post/6844903639794843656)
1. 标记-清除算法
2. 标记-整理算法
3. 复制算法
4. 分代算法

# 类加载的执行过程
[JVM类加载过程](https://blog.csdn.net/zhangliangzi/article/details/51319033)
1. 加载: 根据查找路径找到相应的 class 文件然后导入；
2. 验证: 检查加载的 class 文件的正确性；
3. 准备: 给类中的静态变量分配内存空间；
4. 解析: 虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标示，而在直接引用直接指向内存中的地址；
5. 初始化：对静态变量和静态代码块执行初始化工作。

# 怎么判断对象是否可以被回收？
引用计数器：为每个对象创建一个引用计数，有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就可以被回收。它有一个缺点不能解决循环引用的问题；

可达性分析：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。

# Java可以手动释放内存吗？
java的GC回收是完全自动的，没有提供相关api手动回收，所有的内存分配和回收权限都在jvm，不在开发人员手里。
没有绝对的强制垃圾回收的方法，不过可以这样去做：
1. 对于不再引用的对象，及时把它的引用赋为null。 obj = null;
2. 如果内存确实很紧张，调用System.gc() 方法来建议垃圾回收器开始回收垃圾。通知GC运行，但是Java语言规范并不保证GC一定会执行。

下面是3种需要手动释放内存的
1、文件流要释放
2、数据库连接资源要释放
3、在结合使用hibernate时，session要释放

# 数据库三范式
[MySql--数据库设计三范式](https://www.jianshu.com/p/3e97c2a1687b)
第一范式：要求有主键，并且要求每一个字段原子性不可再分
第二范式：要求所有非主键字段完全依赖主键，不能产生部分依赖
第三范式：所有非主键字段和主键字段之间不能产生传递依赖

# Redis
[](https://mp.weixin.qq.com/s/5I1Y77GN76h_OkVH1TisvA)
1. Redis 使用场景：
数据高并发的读写
海量数据的读写
对扩展性要求高的数据
2. redis 有哪些功能？
数据缓存功能
分布式锁的功能
支持数据持久化
支持事务
支持消息队列
[缓存穿透，缓存击穿，缓存雪崩解决方案分析](https://blog.csdn.net/zeb_perfect/article/details/54135506)
3. 缓存穿透：指查询一个一定不存在的数据，由于缓存是不命中时需要从数据库查询，查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，造成缓存穿透。
4. 缓存雪崩: 缓存雪崩是指在我们设置缓存时采用了相同的过期时间，导致缓存在某一时刻同时失效，请求全部转发到DB，DB瞬时压力过重雪崩。大多数系统设计者考虑用加锁或者队列的方式保证缓存的单线 程（进程）写，从而避免失效时大量的并发请求落到底层存储系统上。
5. 缓存击穿: 对于一些设置了过期时间的key，如果这些key可能会在某些时间点被超高并发地访问，是一种非常“热点”的数据。这个时候，需要考虑一个问题：缓存被“击穿”的问题，这个和缓存雪崩的区别在于这里针对某一key缓存，前者则是很多key。
6. Redis SETNX 实现分布锁

# static
- static方法：在static方法内部不能调用非静态方法，反过来是可以的。而且可static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。以在没有创建任何对象的前提下，仅仅通过类本身来调用static方法。
被static关键字修饰的方法或者变量不需要依赖于对象来进行访问，只要类被加载了，就可以通过类名去进行访问。
- static变量：静态变量被所有的对象所共享，在内存中只有一个副本，它当且仅当在类初次加载时会被初始化。而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。
- static代码块：

# CopyOnWriteArraySet
```
public class CopyOnWriteArraySet<E> extends AbstractSet<E>
        implements java.io.Serializable {
    private static final long serialVersionUID = 5457747651344034263L;

    private final CopyOnWriteArrayList<E> al; //声明al

    /**
     * Creates an empty set.
     */
    public CopyOnWriteArraySet() {
        al = new CopyOnWriteArrayList<E>(); //初始化al
    }
   //省略部分代码
}
    public boolean addIfAbsent(E e) {//这个方法容器如果存在则直接返回，不存在的话在执行添加
    Object[] snapshot = getArray();
    //indexOf方法：是在snapshot数组的0到snapshot.length搜索元素e，如果不存在返回false，否则执行addIfAbsent方法
    return indexOf(e, snapshot, 0, snapshot.length) >= 0 ? false :
    addIfAbsent(e, snapshot);
    }
    private boolean addIfAbsent(E e, Object[] snapshot) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] current = getArray();
        int len = current.length;
        if (snapshot != current) {
            // Optimize for lost race to another addXXX operation
            int common = Math.min(snapshot.length, len);
            for (int i = 0; i < common; i++)
                if (current[i] != snapshot[i] && eq(e, current[i]))
                    return false;
            if (indexOf(e, current, common, len) >= 0)
                    return false;
        }
        Object[] newElements = Arrays.copyOf(current, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
    }
```

# Hash冲突
[HashMap的hash冲突解决方案](https://www.cnblogs.com/jing99/p/6985618.html)
- 开放定址法
- 链地址法（拉链法）
```
0
1  -> 40 -> 27 -> 53
2
3  -> 16 -> 42
4
5
6  -> 32 -> 71
7  -> 46
8
9
10 -> 36 -> 49
11 -> 24
12 -> 64
```
- 再哈希法
- 建立公共溢出区

# ConcurrentHashMap & Hashtable
1. 底层数据结构
ConcurrentHashMap 实现跟 HashMap1.8 的数据结构一样，都是 数组+链表/红黑二叉树. Hashtable 和 JDK1.8 之前的 HashMap 的底层数据结构类似，都是采用 数组+链表 的形式。
2. 实现线程安全的方式
ConcurrentHashMap用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作.
Hashtable (同一把锁, 全表锁) :使用 synchronized 来保证线程安全，效率非常低下。一个线程访问同步方法时，当其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程就不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈，效率就越低。

# HDFS优缺点
- 优点：
1. 处理超大文件。 这里的超大文件通常是指百MB、甚至数百TB大小的文件。目前在实际应用中，HDFS已经能用来存储管理PB级的数据了。
2. 流式的访问数据。
3. 运行于廉价的商用机器集群上。Hadoop设计对应急需求比较低，只须运行在低廉的商用硬件集群上，而无需在昂贵的高可用性机器上。廉价的商用机也就意味着大型集群中出现节点故障情况的概率非常高。HDFS遇到了上述故障时，被设计成能够继续运行且不让用户察觉到明显的中断。

- 缺点：
1. 不适合低延迟数据访问。HDFS是为了处理大型数据集分析任务的，主要是为达到高的数据吞吐量而设计的，这就可能要求以高延迟作为代价。
2. 无法高效存储大量的小文件。
3. 不支持多用户写入以及任意修改文件：在HDFS的一个文件中只有一个写入者，而且写操作只能在文件末尾完成，即只能执行追加操作。目前HDFS还不支持多个用户对同一文件的写操作，以及在文件任意位置进行修改。

## HDFS 文件读写存机制
[](https://blog.csdn.net/weixin_43955361/article/details/113182700)

## 大数据生态圈
[大数据生态圈](https://zhuanlan.zhihu.com/p/63180892)
[](../assets/img/sample/bigdata-ecology.jpg)
## HDFS
