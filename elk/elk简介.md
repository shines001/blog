
1 Elastic Search

1.1 官方介绍  

Elasticsearch是一个基于Apache Lucene(TM)的开源搜索引擎。无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库。它实现以下目标：  
	分布式的实时文件存储，每个字段都被索引并可被搜索  
        分布式的实时分析搜索引擎  
	可以扩展到上百台服务器，处理PB级结构化或非结构化数据  

1.2 特点    
a)	分布式实时文件存储，支持将每个字段存入索引，使其可以被搜索，类似DB的索引    
b)	灵活的扩展性，支持集群部署，可以横向扩张成百上千台服务器，支持结构化和非结构化数据  
c)	速度快，包括检索数据和统计数据  
d)	支持插件扩展功能，比如： 分词插件，同步插件，hadoop插件，可视化插件  
e)	完全开源免费  


1.3 与数据库的对比  
为了方便理解，现将ES和传统数据库进行对比  

1 结构名称不同  
对照关系  


![image](https://github.com/shines001/blog/blob/master/elk/picture/1.PNG)

关系型数据库       数据库             表              行                列    
Elasticsearch      索引               类型        文档                 域（字段）  


2  ES分布式搜索，传统数据库遍历搜索  
3  ES采用倒排索引，传统数据库采用B+树索引    
  假设一个文档（用id标识）是有许多的单词（用value标识）组成的，每个单词可能同一个文档中重复出现很多次，也可能出现在不同的文档中。  
正排索引：从文档角度看其中的单词，表示每个文档都含有哪些单词，以及每个单词出现了多少次（词频）及其出现位置（相对于文档首部的偏移量）。  
倒排索引：从单词角度看文档，标识每个单词分别在那些文档中出现(文档ID)，以及在各自的文档中每个单词分别出现了多少次（词频）及其出现位置（相对于该文档首部的偏移量）。简单理解，  
正排索引：id ---> value  
倒排索引：value ---> id  
ES中为所有字段默认都建了倒排索引  

4 ES不支持事务，不支持回滚，没有用户验证和权限控制机制，不适合数据频繁更新的场景  
5  http访问方式，请求和应答数据均是json格式, 非常适合web系统  


1.4 适用场景   
   不需要事务的，不会对数据进行频繁更新  
   数据关系不是特别复杂  
   web系统，需要对数据进行快速检索，统计分析  
   可以作为后端数据库  

1.5 Es安装  
注：elk三个组件统一使用 6.4.2版本，版本不同会导致不兼容； 另外，因docker部署安装会省略很多细节，一下安装都基于Linux系统，手工安装，以加深对elk的理解  
1，	操作系统centos7  （6版本可能不支持，会很麻烦）  
2，	Jdk必须安装 1.8 ( 不要用1.7或者1.9)  
3，	去官网下载es安装包,  版本为 6.4.2  
4，	 elasticsearch不能root用户下运行，所以需要创建一个专门用户, 本人用户为lxy（各位安装的时候根据自己的情况而定）  
5，	把elasticsearch-6.4.2.tar.gz移动到  /home/lxy  
6，	解压缩  tar -xvf  elasticsearch-6.4.2.tar.gz  
7，	在 /home/lxy  下创建数据和日志目录  
mkdir   es  
cd  es/  
mkdir  log/  
mkdir  data/  
8，	切换到root用户下，修改/etc/security/limits.conf文件, 添加如下内容：  
* soft nofile 65536  
* hard nofile 131072  
* soft nproc 2048  
* hard nproc 4096  
9， 在root用户下, 修改 sysctl.conf, 添加如下内容：  
vm.max_map_count=655360  


9，	执行命令：  sysctl -p  
10，	修改  congfig/elasticsearch.yml  配置文件,主要是以下配置：  
 cluster.name: lxy-app             //集群名称  
 node.name: lxy-1                //节点名称  
 path.data: /home/lxy/es/data      //存放数据的路径  
 path.logs: /home/lxy/es/log       //存放日志的路径  
 network.host: 192.168.146.133     //服务IP  
 http.port: 9200                  //对外端口  
    
11，	启动es      
./bin/elasticsearch     
观察输出是否有错误；  
执行命令，测试es是否成功启动： curl  http://localhost:9200  
成功的返回如下信息：  

![](https://github.com/shines001/blog/blob/master/elk/picture/2.png)
 


可以关闭 es   然后后台启动 ./bin/elasticsearch  -d  

OK，收工！  


2 kibana  
2.1 官方介绍  

Kibana 是一款开源的数据分析和可视化平台，它是 Elastic Stack 成员之一，设计用于和 Elasticsearch 协作。您可以使用 Kibana 对 Elasticsearch 索引中的数据进行搜索、查看、交互操作。您可以很方便的利用图表、表格及地图对数据进行多元化的分析和呈现。  Kibana 可以使大数据通俗易懂。它很简单，基于浏览器的界面便于您快速创建和分享动态数据仪表板来追踪 Elasticsearch 的实时数据变化。搭建 Kibana 非常简单。您可以分分钟完成 Kibana 的安装并开始探索 Elasticsearch 的索引数据 — 没有代码、不需要额外的基础设施。  

2.3 kibana安装  

1 官网下载 kibana-6.4.2-linux-x86_64.tar.gz  并解压，放到适当的路径  
2 配置  config/kibana.yml文件，配置说明参考  
https://www.elastic.co/guide/cn/kibana/current/settings.html  
3  启动kibana    ./bin/kibana  
因为kibana在node中运行，其运行信息没有kibana关键字，查看kibana运行pid执行一下命令：  
netstat -tunlp|grep 5601   或者  ps -ef | grep node  

4  kibana汉化  

参考资料： https://github.com/anbai-inc/Kibana_Hanization/







3 beats   

3.1 beats和logstash区别  
Logstash是一款强大而成熟的日志分析工具，但通过研究，我觉得还是引入beats来代替logstash;   以下是二者的比较：  

![](https://github.com/shines001/blog/blob/master/elk/picture/3.PNG)

某种意义上来说， beats是 elastic公司推出用以代替logstash的，  虽然 logstash功能相对强大些，但其使用成本、资源消耗、性能跟beats相比有很大劣势，据此个人推荐使用beats代替logstash !  


3.2 beats简介
 
Beats 平台集合了多种单一用途数据采集器。这些采集器安装后可用作轻量型代理，从成百上千或成千上万台机器向 Logstash 或 Elasticsearch 发送数据。
Beats是一系列采集工具的总称，有以下几个子工具：  
（1） filebeat     
日志文件采集器，对系统和日志进行提取分析（基本可代替logstash）  

（2） metricbeat   
 指标采集器，用于从linux, windows, MacOS, Apache、NGINX、MongoDB、MySQL、PostgreSQL、Prometheus、Redis 系统或者服务收集指标（我们目前的主机采集模块只支持linux，其他的系统支持不好，用此工具可大大提升支持范围）  

（3） packetbeat  
   网络抓包采集器用于深挖网线上传输的数据，了解应用程序动态。Packetbeat 是一款轻量型网络数据包分析器; 可监控HTTP 等网络协议能够让您密切监控应用程序延时和错误、响应时间、SLA 性能、用户访问模式和趋势（CEM的功能!!）； 其目前支持的协议有：http, mysql,postgresql,redis,thrift-rpc, mongodb, DNS, memcache。  

(4)  winlogbeat  
  轻量级windows时间日志采集器，用于密切监控基于 Windows 的基础架构上发生的事件。Winlogbeat 能够以一种轻量型的方式，将 Windows 事件日志实时地流式传输至 Elasticsearch 和 Logstash  

（5） auditbeat  
审计日志采集器，收集Linux 审计框架的数据，监控文件完整性。Auditbeat 实时采集这些事件，然后发送到 Elastic Stack 其他部分做进一步分析。   

 （6）heartbeat  
   运行时间监控采集器，通过主动探测来监控服务的可用性； 可以理解为心跳检测， 监控服务是否挂掉。  


3.3 metricbeat测试  
在测试环境部署了metricbeat进行测试， 对系统进行监控，相当于主机监控功能，然后配合  es  和 kibana可以配置出如下界面；  

 ![](https://github.com/shines001/blog/blob/master/elk/picture/4.png)
 
 ![](https://github.com/shines001/blog/blob/master/elk/picture/5.png)
 
 ![](https://github.com/shines001/blog/blob/master/elk/picture/6.png)



 
通过metric配合 es, kibana可以实现一个完整的系统监控，几乎无需coding。

4 总结  
通过以上研究， EBK提供了一套完整框架， 使得我们可以较低成本实现全链的运维监控，对于需要深度加工的数据， 采用es配合hadoop的方案，可以实现灵活的扩展性。理论上来说，对现有产品融合在技术上可行， 实现包括基础组件、网络状态、主机系统、应用性能、数据库监控，http内容分析的完整数据链。  
另外，从产品开发成本上来说， 采用EBK之后，开发一些通用类监控产品，除了数据深度分析挖掘需要coding外，会减少低含量重复性的coding工作量。
