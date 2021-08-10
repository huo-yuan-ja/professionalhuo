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
