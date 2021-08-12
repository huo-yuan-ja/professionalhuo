# es 概述
es是一个分布式可扩展的实时搜索和分析引擎，一个建立在全文搜素引擎Apache Lucene基础上的搜素引擎，基于RESTful web接口操作哦ES，也可以利用java API。
分布式实时文件存储，并将每一个字段编入搜索引擎，使其可以被搜索。
实时分析的分布式搜索引擎
可以扩展到上百台服务器，处理PB级别的结构化数据
##### 分片
每个索引都有分片，分摊索引的搜索压力，分片还支持水平拓展和拆分以及分布式操作，可以提高搜素和其他处理的效率。  
主分片  
每个文档都存储在一个分片中，当你存储一个文档时，系统会首先存储在主分片中，然后会复制到不同的副本中，可以在事先制定分片数量，当分片一旦建立，分片的数量不能修改。  
副本分片  
每一个分片有零个或多个副本，副本主要是主分片的复制
增加高可用性：当主分片故障时，可以从副本分片中选择一个作为主分片
提高性能：当查询的时候增加。副本需部署在不同的节点上，不能部署在和主分片相同的节点上，可以到主分片或副本分片中进行查询。副本分片可以在后续动态配置。
## es模块架构
![es模块架构](https://github.com/huo-yuan-ja/jin_picture/blob/main/es.png)  
第一层 —— Gateway：即Elasticsearch支持的索引数据的存储格式，当Elasticsearch关闭再启动的时候，它就会从这个gateway里面读取索引数据；支持的格式有：本地的Local FileSystem、分布式的Shared FileSystem、Hadoop的文件系统HDFS等。 

第二层 —— Lucene框架： Elasticsearch 的底层 API 是 由 Lucene 提供的，每一个 Elasticsearch 节点上都有一个 Lucene 引擎的支持 。 

 第三层 —— Elasticsearch数据的加工处理方式：Index Module（创建Index模块）、Search Module（搜索模块）、Mapping（映射）、River（运行在Elasticsearch集群内部的一个插件，主要用来从外部获取获取异构数据，然后在Elasticsearch里创建索引，常见的插件有RabbitMQ River、Twitter River，在2.x之后已经不再使用）。

第四层 —— Elasticsearch发现机制、脚本：Discovery 是Elasticsearch自动发现节点的机制；Zen是用来实现节点自动发现、Master节点选举用；（Elasticsearch是基于P2P的系统，它首先通过广播的机制寻找存在的节点，然后再通过多播协议来进行节点间的通信，同时也支持点对点的交互）。Scripting 是脚本执行功能，有这个功能能很方便对查询出来的数据进行加工处理。3rd Plugins 表示Elasticsearch支持安装很多第三方的插件，例如elasticsearch-ik分词插件、elasticsearch-sql sql插件。 

第五层 ——Elasticsearch 的传输模块和 JMX 。 传输模块支持 Thrift, Memcached 、 HTTP,默认使用 HTTP 传输 。 JMX 是 Java 的管理框架，用来管理 Elasticsearch 应用 。

第六层 —— Elasticsearch的API支持模式：通过阻STful API 和 Elasticsearch 集群进行交互
### 部署架构
master  
端口：5100  
agent  
端口：5400  
es  
端口1:9200 http协议（用于外部通讯）  
端口2:9300 tcp协议（集群内部通讯）  
config文件为配置文件目录  
### 常用命令
查看集群整体信息  
`curl localhost:5100/search/clusters|python -m json.tool`  
集群健康状态  
`curl -XGET http://localhost:9200/_cluster/health?pretty`  

status:green yello red 
initializing_shards: 0 （待分配的分片）  
unassigned_shards: 0（未分配的分片）  
active_shards_percent_as_number:100%   
集群node整体信息   
`curl -XGET localhost:9200/_cat/nodes?v`   

heap.percent:内存（关注内存是否爆表）  
集群node详细信息    
`curl -XGET localhost:9200/_nodes/process?pretty`    

查看分片索引细节    
`curl http://localhost:9200/_cat/indices?v`  

查看分片状态  
`curl -XGET http://localhost:9200/_cat/shards?v`  

查看unassigned shards（多少分片未分配）  
`curl -XGET localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason| grep UNASSIGNED`

查看哪些分片出现问题  
查看allocation issue  
`curl -XGET  localhost:9200/_cluster/allocation/expain?pretty`   

查看snapshots    
`curl localhost:9200/_cat/snapshots/{repository}`   

查看备份  
es首次备份全量备份，之后是增量备份   
es 集群重启  
通过接口来重起
```
curl -XPOST localhost:5100/cluster/update -d '{
"cluster_name":"XXXXX",
"operator":"XXXXX",
"restart_type":"XXXX"
}'  
```
重启策略  
rolling_restart 滚动重启（建议）  
full_cluster_restart 全量重启  
no_restart 不重启  
2.通过kill进程，自动拉起集群重启
## 配置项的修改
### 修改配置文件
修改三个节点的elasticsearch.yml,kill掉es进程（重启es集群），需要注意的是所有es节点都要重启。  
### 修改集群配置
通过api修改
```
curl -X PUT localhost:9200 /_cluster/settings -d
{
  "persistent": {
    "discovery.zen.minimum_master_nodes":2 #临时生效
  },
  "transient": {
     "discovery.zen.minimum_master_nodes":2 #永久生效
  }
}
```
### 修改索引配置
配置单个索引的方法：
```
curl -X PUT ip:port/index_name/_settings -d '{
#例子
"number_of_replicas":2 #修改副本数量
}'
```
配置所有索引的方法：
```
curl -X PUT ip:port/_all/_settings -d '{
#例子
"number_of_replicas":2 #修改副本数量
}'
```
es监控工具cerebro(开源)
nohup ./cerebro -D http.port=9001 &  

### es日常巡检
1.查看集群状态  
`curl -XGET http://localhost:9200/_cluster/health?pretty`    
2.查看集群JVM内存大小，如果超过80%，则集群写入会不正常  
`curl -XGET "http://localhost:9200/_nodes/stats/jvm?pretty"|grep heap_used_percent`    

3.集群空间检查  
`curl http://localhost:9200/_cat/allocation?v`  

4.检查磁盘空间  
df -h  
