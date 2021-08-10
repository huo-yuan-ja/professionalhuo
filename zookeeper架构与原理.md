# 什么是 Zookeeper
Zookeeper 分布式服务框架是Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：  
 * 统一命名服务
 * 状态同步服务
 * 集群管理
 * 分布式应用配置项的管理等
 * Zookeeper已经成为Hadoop生态系统中的基础组件。

## Zookeeper的基本原理和架构  
### Zookeeper的角色

* 领导者（leader）：负责进行投票的发起和决议，更新系统状态。

* 学习者（learner）：包括跟随者（follower）和观察者（observer），follower用于接受客户端请求并想客户端返回结果，在选主过程中参与投票。

* Observer：可以接受客户端连接，将写请求转发给leader，但observer不参加投票过程，只同步leader的状态，observer的目的是为了扩展系统，提高读取速度

* 客户端（client）：请求发起方  
![zookeeper集群架构](https://github.com/huo-yuan-ja/jin_picture/blob/main/2084.jpeg)  
![角色描述](https://github.com/huo-yuan-ja/jin_picture/blob/main/%E8%A7%92%E8%89%B2%E6%8F%8F%E8%BF%B0.jpeg)  
• Zookeeper的核心是原子广播，这个机制保证了各个Server之间的同步。实现这个机制的协议叫做Zab协议。

Zab协议有两种模式，它们分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，Zab就进入了恢复模式，当领导者被选举出来，且大多数Server完成了和leader的状态同步以后，恢复模式就结束了。状态同步保证了leader和Server具有相同的系统状态。

• 为了保证事务的顺序一致性，zookeeper采用了递增的事务id号（zxid）来标识事务。所有的提议（proposal）都在被提出的时候加上了zxid。实现中zxid是一个64位的数字，它高32位是epoch用来标识leader关系是否改变，每次一个leader被选出来，它都会有一个新的epoch，标识当前属于那个leader的统治时期。低32位用于递增计数。

• 每个Server在工作过程中有三种状态：

LOOKING：当前Server不知道leader是谁，正在搜寻

LEADING：当前Server即为选举出来的leader

FOLLOWING：leader已经选举出来，当前Server与之同步

### Zookeeper 的读写机制

» Zookeeper是一个由多个server组成的集群

» 一个leader，多个follower

» 每个server保存一份数据副本

» 全局数据一致

» 分布式读写

» 更新请求转发，由leader实施

### Zookeeper 的保证

» 更新请求顺序进行，来自同一个client的更新请求按其发送顺序依次执行

» 数据更新原子性，一次数据更新要么成功，要么失败

» 全局唯一数据视图，client无论连接到哪个server，数据视图都是一致的

» 实时性，在一定事件范围内，client能读到最新数据

### Zookeeper节点数据操作流程  
![数据操作流程](https://github.com/huo-yuan-ja/jin_picture/blob/main/%E4%BC%A0%E8%BE%93.jpeg)  


