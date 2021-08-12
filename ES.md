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

