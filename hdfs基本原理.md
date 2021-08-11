# HDFS 简介
HDFS（Hadoop Distribute File System）是大数据领域一种非常可靠的存储系统，它以分布式方式存储超大数据量文件，但它并不适合存储大量的小数据量文件。Hadoop HDFS是Hadoop和其他组件的数据存储层。HDFS是运行在由价格廉价的商用机器组成的集群上的，而价格低廉的机器发生故障的几率比较高，但是HDFS利用数据副本技术把数据存放在若干个机器上，保证数据的高可用性，使得数据不至于因为某几个机器发生故障发生丢失不可用的情况。HDFS以数据并发访问的方式达到高吞吐量的目的。下面开始详细介绍HDFS的各个关键点。

# HDFS 节点
Hadoop是以主从结构的方式运行的，HDFS也是以同样的方式工作的，它也有有两种节点类型，分别是namenode节点和datanode节点。  

# HDFS Master 节点（Namenode）
Namenode会规范客户端对文件的访问，它维护和管理Slave节点，并把读写任务分配给Slave节点。Namenode执行文件系统的名字空间操作，比如打开，关闭，重命名文件和目录，应该把Namenode部署在高可靠的硬件上面。

# HDFS Slave 节点（Datanode）
在HDFS集群，Datanode节点的数量可以扩展到1000。Datanode负责数据存储，它是真正干活的节点，比如响应客户端的数据读写请求，根据Namenode的指令创建和删除block。而且还会根据副本因子把block复制到其他节点。Datanode可以部署在价格低廉的商用机器上，没必要部署在昂贵的高可用机器上。

# Hadoop HDFS 进程
HDFS有2个守护进程。如下：

Namenode：该进程运行在master节点上。Namenode节点存储元数据，比如文件名，块数量，块副本数量，块的存储位置，以及块ID等。为了元数据的快速查询，这些都是存储在master节点的内存里面的，并在本地磁盘存储这些元数据的副本。

Datanode：该进程运行在Slave节点，这些节点是真正对数据进行处理和存储的节点。

# HDFS 的数据存储
把文件写入到HDFS的时候，HDFS会把文件分割成很多分片，也就是块（block）。默认情况下，HDFS块大小为128MB，块大小可以按需要修改。文件分割成块之后，HDFS会把他们以分布式方式存储在集群的不多节点上。这为MapReduce在集群并行化处理数据提供了很好的基础。  
![hdfs工作原理](https://github.com/huo-yuan-ja/jin_picture/blob/main/hdfs1.png)
