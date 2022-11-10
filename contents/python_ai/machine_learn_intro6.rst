机器学习 6 电影推荐系统实现
#######################################################################

项目体系架构设计
***********************************************************************

项目系统架构
========================================================================

项目以推荐系统建设领域知名的经过修改过的 MovieLens 数据集作为依托，以 某科技公司电影网站真实业务数据架构为基础，构建了符合教学体系的一体化的电 影推荐系统，包含了离线推荐与实时推荐体系，综合利用了协同过滤算法以及基于 内容的推荐方法来提供混合推荐。提供了从前端应用、后台服务、算法设计实现、 平台部署等多方位的闭环的业务实现。

|image0|

用户可视化:主要负责实现和用户的交互以及业务数据的展示，主体采用 AngularJS2 进行实现，部署在 Apache 服务上。

综合业务服务:主要实现 JavaEE 层面整体的业务逻辑，通过 Spring 进行构建， 对接业务需求。部署在 Tomcat 上。

【数据存储部分】

| 业务数据库:项目采用广泛应用的文档数据库 MongDB 作为主数据库，主要负责平台业务逻辑数据的存储。
| 搜索服务器:项目爱用 ElasticSearch 作为模糊检索服务器，通过利用 ES 强大 的匹配查询能力实现基于内容的推荐服务。
| 缓存数据库:项目采用 Redis 作为缓存数据库，主要用来支撑实时推荐系统部 分对于数据的高速获取需求。

【离线推荐部分】

| 离线统计服务:批处理统计性业务采用 Spark Core + Spark SQL 进行实现，实现对指标类数据的统计任务。
| 离线推荐服务:离线推荐业务采用 Spark Core + Spark MLlib 进行实现，采用 ALS 算法进行实现。 
| 工作调度服务:对于离线推荐部分需要以一定的时间频率对算法进行调度，采用 Azkaban 进行任务的调度。

【实时推荐部分】

| 日志采集服务:通过利用 Flume-ng 对业务平台中用户对于电影的一次评分行为 进行采集，实时发送到 Kafka 集群。
| 消息缓冲服务:项目采用 Kafka 作为流式数据的缓存组件，接受来自 Flume 的 数据采集请求。并将数据推送到项目的实时推荐系统部分。
| 实时推荐服务:项目采用 Spark Streaming 作为实时推荐系统，通过接收 Kafka 中缓存的数据，通过设计的推荐算法实现对实时推荐的数据处理，并将结构合并更 新到 MongoDB 数据库。

项目数据流程
========================================================================

|image1|

【系统初始化部分】
	
0. 通过 Spark SQL 将系统初始化数据加载到 MongoDB 和 ElasticSearch 中。

【离线推荐部分】

1. 通过 Azkaban 实现对于离线统计服务以离线推荐服务的调度，通过设定的运行时间完成对任务的触发执行。
2. 离线统计服务从 MongoDB 中加载数据，将【电影平均评分统计】、【电影评分个数统计】、【最近电影评分个数统计】三个统计算法进行运行实现，并将计算结果回 写到 MongoDB 中;离线推荐服务从 MongoDB 中加载数据，通过 ALS 算法分别将【用户推荐结果矩阵】、【影片相似度矩阵】回写到 MongoDB 中。

【实时推荐部分】

3. Flume 从综合业务服务的运行日志中读取日志更新，并将更新的日志实时推送到 Kafka 中;Kafka 在收到这些日志之后，通过 kafkaStream 程序对获取的日志信息进行 过滤处理，获取用户评分数据流【UID|MID|SCORE|TIMESTAMP】，并发送到另外一 个 Kafka 队列;Spark Streaming 监听 Kafka 队列，实时获取 Kafka 过滤出来的用户评 分数据流，融合存储在 Redis 中的用户最近评分队列数据，提交给实时推荐算法， 完成对用户新的推荐结果计算;计算完成之后，将新的推荐结构和 MongDB 数据库中的推荐结果进行合并。

【业务系统部分】

4. 推荐结果展示部分，从 MongoDB、ElasticSearch 中将离线推荐结果、实时推荐结果、 内容推荐结果进行混合，综合给出相对应的数据。
5. 电影信息查询服务通过对接 MongoDB 实现对电影信息的查询操作。
6. 电影评分部分，获取用户通过 UI 给出的评分动作，后台服务进行数据库记录后，一 方面将数据推动到 Redis 群中，另一方面，通过预设的日志框架输出到 Tomcat 中的日志中。
7. 项目通过 ElasticSearch 实现对电影的模糊检索。
8. 电影标签部分，项目提供用户对电影打标签服务。

数据模型
========================================================================

1. Movie【电影数据表】

::

	字段名		字段类型		字段描述		字段备注
	mid 		Int 		电影的 ID	
	name		String 		电影的名称
	descri 		String 		电影的描述
	timelong	String 		电影的时长
	shoot 		String 		电影拍摄时间
	issue 		String 		电影发布时间
	language	String 		电影语言
	genres 		String 		电影所属类别
	director 	String 		电影的导演
	actors 		String 		电影的演员

2、Rating【用户评分表】

::

	字段名 		字段类型 			字段描述 		字段备注
	uid 		Int 			用户的 ID 	无
	mid 		Int 			电影的 ID 	无
	score 		Double 			电影的分值 	无
	timestamp 	Long 			评分的时间 	无

3、Tag【电影标签表】

::

	字段名 		字段类型 		字段描述 		字段备注
	uid 		Int 		用户的 ID 	无
	mid 		Int 		电影的 ID 	无
	tag 		String 		电影的标签 	无
	timestamp 	Long 		评分的时间 	无

4、User【用户表】

::

	字段名 		字段类型  	字段描述 		字段备注
	uid 		Int 		用户的 ID 	无
	username 	String 		用户名 		无
	password 	String 		用户密码 		无
	first 		Boolean 	用于是否第一次登录  无
	genres 		List< String> 	用户偏爱的电影类型		无
	timestamp 	Long 			用户创建的时间		无

5、RateMoreMoviesRecently【最近电影评分个数统计表】

::

	字段名		字段类型 		字段描述 		字段备注
	mid 		Int 		电影的 ID 	无
	count 		Int 		电影的评分数 	无
	yearmonth 	String 		评分的时段 	yyyymm

6、RateMoreMovies【电影评分个数统计表】

::

	字段名 	字段类型 		字段描述 		字段备注
	mid 	Int 		电影的 ID 	无
	count 	Int 		电影的评分数 	无

7、AverageMoviesScore【电影平均评分表】

::

	字段名 		字段类型 		字段描述 			字段备注
	mid 		Int 		电影的 ID 		无
	avg 		Double 		电影的平均评分  	无

8、MovieRecs【电影相似性矩阵】

::

	字段名 		字段类型 		字段描述 		字段备注
	mid 		Int 		电影的 ID 	无
	recs 		Array[(mid: Int, score: Double)] 	该电影最相似的电影集合 		无

9、UserRecs【用户电影推荐矩阵】

::

	字段名 		字段类型 		字段描述 		字段备注
	uid 		Int 		用户的 ID 	无
	recs 		Array[(mid: Int, score: Double)] 	推荐给该用户的电影集合 	无

10、StreamRecs【用户实时电影推荐矩阵】

::

	字段名 		字段类型 		字段描述 		字段备注
	uid 		Int 		用户的 ID 	无
	recs 		Array[(mid: Int, score: Double)] 	实时推荐给该用户的电影集合 	无

11、GenresTopMovies【电影类别 TOP10】

::

	字段名 		字段类型 			字段描述 		字段备注
	genres 		String 			电影类型 		无
	recs 		Array[(mid: Int, score: Double)] 	TOP10 电影  	无

工具环境搭建
***********************************************************************

项目中用到了多种工具进行数据的存储、计算、采集和传输，本章主要简单介绍设计的工具环境搭建。如果机器的配置不足， 推荐只采用一台虚拟机进行配置，而非完全分布式，将该虚拟机 CPU 的内存设置的尽可能大，推荐为 CPU > 4、MEM > 4GB。

MongoDB（单节点）环境配置
========================================================================

::

	// 通过 WGET 下载 Linux 版本的 MongoDB
	[bigdata@linux ~]$ wget 
	https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel62-3.
	4.3.tgz
	// 将压缩包解压到指定目录
	[bigdata@linux backup]$ tar -xf 
	mongodb-linux-x86_64-rhel62-3.4.3.tgz -C ~/
	// 将解压后的文件移动到最终的安装目录
	[bigdata@linux ~]$ mv mongodb-linux-x86_64-rhel62-3.4.3/ 
	/usr/local/mongodb
	// 在安装目录下创建 data 文件夹用于存放数据和日志
	[bigdata@linux mongodb]$ mkdir /usr/local/mongodb/data/
	// 在 data 文件夹下创建 db 文件夹，用于存放数据
	[bigdata@linux mongodb]$ mkdir /usr/local/mongodb/data/db/
	// 在 data 文件夹下创建 logs 文件夹，用于存放日志
	[bigdata@linux mongodb]$ mkdir /usr/local/mongodb/data/logs/
	// 在 logs 文件夹下创建 log 文件
	[bigdata@linux mongodb]$ touch /usr/local/mongodb/data/logs/
	mongodb.log
	// 在 data 文件夹下创建 mongodb.conf 配置文件
	[bigdata@linux mongodb]$ touch 
	/usr/local/mongodb/data/mongodb.conf
	// 在 mongodb.conf 文件中输入如下内容
	[bigdata@linux mongodb]$ vim ./data/mongodb.conf
	#端口号 port = 27017
	#数据目录
	dbpath = /usr/local/mongodb/data/db
	#日志目录
	logpath = /usr/local/mongodb/data/logs/mongodb.log
	#设置后台运行
	fork = true
	#日志输出方式
	logappend = true
	#开启认证
	#auth = true

完成 MongoDB 的安装后，启动 MongoDB 服务器：

::

	// 启动 MongoDB 服务器
	[bigdata@linux mongodb]$ sudo /usr/local/mongodb/bin/mongod 
	-config /usr/local/mongodb/data/mongodb.conf
	// 访问 MongoDB 服务器
	[bigdata@linux mongodb]$ /usr/local/mongodb/bin/mongo
	// 停止 MongoDB 服务器
	[bigdata@linux mongodb]$ sudo /usr/local/mongodb/bin/mongod 
	-shutdown -config /usr/local/mongodb/data/mongodb.conf

Redis（单节点）环境配置
========================================================================

::

	// 通过 WGET 下载 REDIS 的源码
	[bigdata@linux ~]$wget 
	http://download.redis.io/releases/redis-4.0.2.tar.gz
	// 将源代码解压到安装目录
	[bigdata@linux ~]$ tar -xf redis-4.0.2.tar.gz -C ~/
	// 进入 Redis 源代码目录，编译安装
	[bigdata@linux ~]$ cd redis-4.0.2/
	// 安装 GCC
	[bigdata@linux ~]$ sudo yum install gcc
	// 编译源代码
	[bigdata@linux redis-4.0.2]$ make MALLOC=libc
	// 编译安装
	[bigdata@linux redis-4.0.2]$ sudo make install
	// 创建配置文件
	[bigdata@linux redis-4.0.2]$ sudo cp ~/redis-4.0.2/redis.conf 
	/etc/ 
	// 修改配置文件中以下内容
	[bigdata@linux redis-4.0.2]$ sudo vim /etc/redis.conf
	daemonize yes #37 行 #是否以后台 daemon 方式运行，默认不是后台运行
	pidfile /var/run/redis/redis.pid #41 行 #redis 的 PID 文件路径（可选）
	bind 0.0.0.0 #64 行 #绑定主机 IP，默认值为 127.0.0.1，我们是跨机器运行，所
	以需要更改
	logfile /var/log/redis/redis.log #104 行 #定义 log 文件位置，模式 log
	信息定向到 stdout，输出到/dev/null（可选）
	dir “/usr/local/rdbfile” #188 行 #本地数据库存放路径，默认为./，编译
	安装默认存在在/usr/local/bin 下（可选）

在安装完 Redis 之后，启动 Redis

::

	// 启动 Redis 服务器
	[bigdata@linux redis-4.0.2]$ redis-server /etc/redis.conf
	// 连接 Redis 服务器
	[bigdata@linux redis-4.0.2]$ redis-cli
	// 停止 Redis 服务器
	[bigdata@linux redis-4.0.2]$ redis-cli shutdown

ElasticSearch（单节点）环境配置
========================================================================

::

	// 通过 Wget 下载 ElasticSearch 安装包
	[bigdata@linux ~]$wget 
	https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.6.2.tar.gz

修改 Linux 配置参数：

::

	// 修改文件数配置，在文件末尾添加如下配置
	[bigdata@linux ~]$sudo vim /etc/security/limits.conf 
	* soft nofile 65536
	* hard nofile 131072
	* soft nproc 2048
	* hard nproc 4096
	// 修改* soft nproc 1024 为 * soft nproc 2048
	[bigdata@linux ~]$sudo vim /etc/security/limits.d/90-nproc.conf 
	* soft nproc 2048 #将该条目修改成 2048
	// 在文件末尾添加：
	[bigdata@linux ~]$sudo vim /etc/sysctl.conf 
	vm.max_map_count=655360
	// 在文件末尾添加：
	[bigdata@linux elasticsearch-5.6.2]$ sudo sysctl -p

配置 ElasticSearch：

::

	// 解压 ElasticSearch 到安装目录
	[bigdata@linux ~]$ tar -xf elasticsearch-5.6.2.tar.gz -C ./cluster/
	// 进入 ElasticSearch 安装目录
	[bigdata@linux cluster]$ cd elasticsearch-5.6.2/
	// 创建 ElasticSearch 数据文件夹 data
	[bigdata@linux cluster]$ mkdir elasticsearch-5.6.2/data/
	// 创建 ElasticSearch 日志文件夹 logs
	[bigdata@linux cluster]$ mkdir elasticsearch-5.6.2/logs/
	// 修改 ElasticSearch 配置文件
	[bigdata@linux 
	elasticsearch-5.6.2]$ vim ./config/elasticsearch.yml
	cluster.name: es-cluster #设置集群的名称
	node.name: es-node #修改当前节点的名称
	path.data: /home/bigdata/cluster/elasticsearch-5.6.2/data #修改数据路径
	path.logs: /home/bigdata/cluster/elasticsearch-5.6.2/logs #修改日志路径
	bootstrap.memory_lock: false #设置 ES 节点允许内存交换
	bootstrap.system_call_filter: false #禁用系统调用过滤器
	network.host: linux #设置当前主机名称
	discovery.zen.ping.unicast.hosts: ["linux"] #设置集群的主机列表

完成 ElasticSearch 的配置后：

::

	// 启动 ElasticSearch 服务
	[bigdata@linux elasticsearch-5.6.2]$ ./bin/elasticsearch -d
	// 访问 ElasticSearch 服务
	[bigdata@linux elasticsearch-5.6.2]$ curl http://linux:9200/
	{
		"name" : "es-node",
		"cluster_name" : "es-cluster",
		"cluster_uuid" : "VUjWSShBS8KM_EPJdIer6g",
		"version" : {
		"number" : "5.6.2",
		"build_hash" : "57e20f3",
		"build_date" : "2017-09-23T13:16:45.703Z",
		"build_snapshot" : false,
		"lucene_version" : "6.6.1"
	},
	"tagline" : "You Know, for Search"
	}
	// 停止 ElasticSearch 服务
	[bigdata@linux ~]$ jps
	8514 Elasticsearch
	6131 GradleDaemon
	8908 Jps
	[bigdata@linux ~]$ kill -9 8514

Azkaban（单节点）环境配置
========================================================================

安装 Git
------------------------------------------------------------------------

::

	// 安装 GIT
	[bigdata@linux ~]$ sudo yum install git 
	// 通过 git 下载 Azkaban 源代码
	[bigdata@linux ~]$ git clone https://github.com/azkaban/azkaban.git
	// 进入 azkaban 目录
	[bigdata@linux ~]$ cd azkaban/
	// 切换到 3.36.0 版本
	[bigdata@linux azkaban]$ git checkout -b 3.36.0

编译 Azkaban
------------------------------------------------------------------------

详细请参照：https://github.com/azkaban/azkaban

::

	// 安装编译环境
	[bigdata@linux azkaban]$sudo yum install gcc
	[bigdata@linux azkaban]$sudo yum install -y gcc-c++*
	// 执行编译命令
	[bigdata@linux azkaban]$ ./gradlew clean build

部署 Azkaban Solo
------------------------------------------------------------------------

::

	// 将编译好的 azkaban 中的 azkaban-solo-server-3.36.0.tar.gz 拷贝到根目录
	[bigdata@linux 
	azkaban]$ cp ./azkaban-solo-server/build/distributions/azkaban-soloserver-3.36.0.tar.gz ~/
	// 解压 azkaban-solo-server-3.36.0.tar.gz 到安装目录
	[bigdata@linux ~]$ tar -xf azkaban-solo-server-3.36.0.tar.gz 
	-C ./cluster
	// 启动 Azkaban Solo 单节点服务
	[bigdata@linux azkaban-solo-server-3.36.0]$ bin/azkaban-solo-start.sh
	// 访问 azkaban 服务，通过浏览器代开 http://ip:8081，通过用户名：azkaban，密码 azkaban 登录。

	// 关闭 Azkaban 服务
	[bigdata@linux 
	azkaban-solo-server-3.36.0]$ bin/azkaban-solo-shutdown.sh

Spark（单节点）环境配置
========================================================================

::

	// 通过 wget 下载 zookeeper 安装包
	[bigdata@linux ~]$ wget 
	https://d3kbcqa49mib13.cloudfront.net/spark-2.1.1-bin-hadoop2.7.tgz
	// 将 spark 解压到安装目录
	[bigdata@linux ~]$ tar –xf spark-2.1.1-bin-hadoop2.7.tgz –C ./cluster
	// 进入 spark 安装目录
	[bigdata@linux cluster]$ cd spark-2.1.1-bin-hadoop2.7/
	// 复制 slave 配置文件
	[bigdata@linux 
	spark-2.1.1-bin-hadoop2.7]$ cp ./conf/slaves.template ./conf/slaves
	// 修改 slave 配置文件
	[bigdata@linux spark-2.1.1-bin-hadoop2.7]$ vim ./conf/slaves
	linux #在文件最后将本机主机名进行添加
	// 复制 Spark-Env 配置文件
	[bigdata@linux 
	spark-2.1.1-bin-hadoop2.7]$ cp ./conf/spark-env.sh.template ./conf/s
	park-env.sh 
	SPARK_MASTER_HOST=linux #添加 spark master 的主机名
	SPARK_MASTER_PORT=7077 #添加 spark master 的端口号

安装完成之后，启动 Spark

::

	// 启动 Spark 集群
	[bigdata@linux spark-2.1.1-bin-hadoop2.7]$ sbin/start-all.sh
	// 访问 Spark 集群，浏览器访问 http://linux:8080
	// 关闭 Spark 集群
	[bigdata@linux spark-2.1.1-bin-hadoop2.7]$ sbin/stop-all.sh

Zookeeper（单节点）环境配置
========================================================================

::

	// 通过 wget 下载 zookeeper 安装包
	[bigdata@linux ~]$ wget 
	http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
	// 将 zookeeper 解压到安装目录
	[bigdata@linux ~]$ tar –xf zookeeper-3.4.10.tar.gz –C ./cluster
	// 进入 zookeeper 安装目录
	[bigdata@linux cluster]$ cd zookeeper-3.4.10/
	// 创建 data 数据目录
	[bigdata@linux zookeeper-3.4.10]$ mkdir data/
	// 复制 zookeeper 配置文件
	[bigdata@linux 
	zookeeper-3.4.10]$ cp ./conf/zoo_sample.cfg ./conf/zoo.cfg 
	// 修改 zookeeper 配置文件
	[bigdata@linux zookeeper-3.4.10]$ vim conf/zoo.cfg
	dataDir=/home/bigdata/cluster/zookeeper-3.4.10/data #将数据目录地址修改为创建的目录
	// 启动 Zookeeper 服务
	[bigdata@linux zookeeper-3.4.10]$ bin/zkServer.sh start
	// 查看 Zookeeper 服务状态
	[bigdata@linux zookeeper-3.4.10]$ bin/zkServer.sh status
	ZooKeeper JMX enabled by default
	Using config: 
	/home/bigdata/cluster/zookeeper-3.4.10/bin/../conf/zoo.cfg
	Mode: standalone
	// 关闭 Zookeeper 服务
	[bigdata@linux zookeeper-3.4.10]$ bin/zkServer.sh stop

Flume-ng（单节点）环境配置
========================================================================

::

	[bigdata@linux ~]$ wget 
	http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz
	[bigdata@linux ~]$ tar –xf apache-flume-1.8.0-bin.tar.gz –C ./cluster

Kafka（单节点）环境配置
========================================================================

::

	[bigdata@linux ~]$ wget 
	http://mirrors.tuna.tsinghua.edu.cn/apache/kafka/0.10.2.1/kafka_2.11-0.10.2.1.tgz
	// 将 kafka 解压到安装目录
	[bigdata@linux ~]$ tar –xf kafka_2.12-0.10.2.1.tgz –C ./cluster
	// 进入 kafka 安装目录
	[bigdata@linux cluster]$ cd kafka_2.12-0.10.2.1/ 
	// 修改 kafka 配置文件
	[bigdata@linux kafka_2.12-0.10.2.1]$ vim config/server.properties
	host.name=linux #修改主机名
	port=9092 #修改服务端口号
	zookeeper.connect=linux:2181 #修改 Zookeeper 服务器地址
	// 启动 kafka 服务 !!! 启动之前需要启动 Zookeeper 服务
	[bigdata@linux kafka_2.12-0.10.2.1]$ bin/kafka-server-start.sh -daemon ./config/server.properties
	// 关闭 kafka 服务
	[bigdata@linux kafka_2.12-0.10.2.1]$ bin/kafka-server-stop.sh
	// 创建 topic
	[bigdata@linux kafka_2.12-0.10.2.1]$ bin/kafka-topics.sh --create --zookeeper linux:2181 --replication-factor 1 --partitions 1 --topic 
	recommender
	// kafka-console-producer
	[bigdata@linux kafka_2.12-0.10.2.1]$ bin/kafka-console-producer.sh --broker-list linux:9092 --topic recommender
	// kafka-console-consumer
	[bigdata@linux kafka_2.12-0.10.2.1]$ bin/kafka-console-consumer.sh --bootstrap-server linux:9092 --topic recommender

创建项目并初始化业务数据
***********************************************************************

项目主体用 Scala 编写，采用 IDEA 作为开发环境进行项目编写，采用 maven 作为项目构建和管理工具。

在 IDEA 中创建 maven 项目
========================================================================

打开 IDEA，创建一个 maven 项目，命名为 MovieRecommendSystem。为了方便 后期的联调，我们会把业务系统的代码也添加进来，所以我们可以以 MovieRecommendSystem 作为父项目，并在其下建一个名为 recommender 的子项目， 然后再在下面搭建多个子项目用于提供不同的推荐服务。

项目框架搭建
------------------------------------------------------------------------

在 MovieRecommendSystem 的 pom.xml 文 件 中 加 入 元 素 <packaging>pom</packaging>，然后新建一个 maven module 作为子项目，命名为 recommender。同样的，再以 recommender 为父项目，在它的 pom.xml 中加入 <packing>pom</packaging>，然后新建一个 maven module 作为子项目。我们的第一 步是初始化业务数据，所以子项目命名为 DataLoader。

父项目只是为了规范化项目结构，方便依赖管理，本身是不需要代码实现的，所以 MovieRecommendSystem 和 recommender 下的 src 文件夹都可以删掉。

目前的整体项目框架如下：

|image2|

声明项目中工具的版本信息
------------------------------------------------------------------------

整个项目需要用到多个工具，它们的不同版本可能会对程序运行造成影响，所以应该在最外层的 MovieRecommendSystem 中声明所有子项目共用的版本信息。 在 pom.xml 中加入以下配置:

MovieRecommendSystem/pom.xml

::

	<properties>
	    <log4j.version>1.2.17</log4j.version>
	    <slf4j.version>1.7.22</slf4j.version>
	    <mongodb-spark.version>2.0.0</mongodb-spark.version>
	    <casbah.version>3.1.1</casbah.version>
	    <elasticsearch-spark.version>5.6.2</elasticsearch-spark.version>
	    <elasticsearch.version>5.6.2</elasticsearch.version>
	    <redis.version>2.9.0</redis.version>
	    <kafka.version>0.10.2.1</kafka.version>
	    <spark.version>2.1.1</spark.version>
	    <scala.version>2.11.8</scala.version>
	    <jblas.version>1.2.1</jblas.version>
	</properties>

添加项目依赖
------------------------------------------------------------------------

首先，对于整个项目而言，应该有同样的日志管理，在 MovieRecommendSystem 中引入公有依赖：

MovieRecommendSystem/pom.xml

.. code-block:: java
	:linenos:

	<dependencies>
	    <!—引入共同的日志管理工具 -->
	    <dependency>
	        <groupId>org.slf4j</groupId>
	        <artifactId>jcl-over-slf4j</artifactId>
	        <version>${slf4j.version}</version>
	    </dependency>
	    <dependency>
	        <groupId>org.slf4j</groupId>
	        <artifactId>slf4j-api</artifactId>
	        <version>${slf4j.version}</version>
	    </dependency>
	    <dependency>
	        <groupId>org.slf4j</groupId>
	        <artifactId>slf4j-log4j12</artifactId>
	        <version>${slf4j.version}</version>
	    </dependency>
	    <dependency>
	        <groupId>log4j</groupId>
	        <artifactId>log4j</artifactId>
	        <version>${log4j.version}</version>
	    </dependency>
	</dependencies>

同样，对于 maven 项目的构建，可以引入公有的插件：

.. code-block:: java
	:linenos:

	<build>
	    <!--声明并引入子项目共有的插件-->
	    <plugins>
	        <plugin>
	            <groupId>org.apache.maven.plugins</groupId>
	            <artifactId>maven-compiler-plugin</artifactId>
	            <version>3.6.1</version>
	            <!--所有的编译用 JDK1.8-->
	            <configuration>
	                <source>1.8</source>
	                <target>1.8</target>
	            </configuration>
	        </plugin>
	    </plugins>
	    <pluginManagement>
	        <plugins>
	            <!--maven 的打包插件-->
	            <plugin>
	                <groupId>org.apache.maven.plugins</groupId>
	                <artifactId>maven-assembly-plugin</artifactId>
	                <version>3.0.0</version>
	                <executions>
	                    <execution>
	                        <id>make-assembly</id>
	                        <phase>package</phase>
	                        <goals>
	                            <goal>single</goal>
	                        </goals>
	                    </execution>
	                </executions>
	            </plugin>
	            <!--该插件用于将 scala 代码编译成 class 文件-->
	            <plugin>
	                <groupId>net.alchim31.maven</groupId>
	                <artifactId>scala-maven-plugin</artifactId>
	                <version>3.2.2</version>
	                <executions>
	                    <!--绑定到 maven 的编译阶段-->
	                    <execution>
	                        <goals>
	                            <goal>compile</goal>
	                            <goal>testCompile</goal>
	                        </goals>
	                    </execution>
	                </executions>
	            </plugin>
	        </plugins>
	    </pluginManagement>
	</build>

然后，在 recommender 模块中，可以为所有的推荐模块声明 spark 相关依赖（这里的 dependencyManagement 表示仅声明相关信息，子项目如果依赖需要自行引入）：

MovieRecommendSystem/recommender/pom.xml

.. code-block:: java
	:linenos:

	<dependencyManagement>
	    <dependencies>
	        <!-- 引入 Spark 相关的 Jar 包 -->
	        <dependency>
	            <groupId>org.apache.spark</groupId>
	            <artifactId>spark-core_2.11</artifactId>
	            <version>${spark.version}</version>
	        </dependency>
	        <dependency>
	            <groupId>org.apache.spark</groupId>
	            <artifactId>spark-sql_2.11</artifactId>
	            <version>${spark.version}</version>
	        </dependency>
	        <dependency>
	            <groupId>org.apache.spark</groupId>
	            <artifactId>spark-streaming_2.11</artifactId>
	            <version>${spark.version}</version>
	        </dependency>
	        <dependency>
	            <groupId>org.apache.spark</groupId>
	            <artifactId>spark-mllib_2.11</artifactId>
	            <version>${spark.version}</version>
	        </dependency>
	        <dependency>
	            <groupId>org.apache.spark</groupId>
	            <artifactId>spark-graphx_2.11</artifactId>
	            <version>${spark.version}</version>
	        </dependency>
	        <dependency>
	            <groupId>org.scala-lang</groupId>
	            <artifactId>scala-library</artifactId>
	            <version>${scala.version}</version>
	        </dependency>
	    </dependencies>
	</dependencyManagement>

由于各推荐模块都是 scala 代码，还应该引入 scala-maven-plugin 插件，用于 scala 程序的编译。因为插件已经在父项目中声明，所以这里不需要再声明版本和具体配置：

.. code-block:: java
	:linenos:

	<build>
	    <plugins>
	        <!-- 父项目已声明该 plugin，子项目在引入的时候，不用声明版本和已经声明的配置 -->
	        <plugin>
	            <groupId>net.alchim31.maven</groupId>
	            <artifactId>scala-maven-plugin</artifactId>
	        </plugin>
	    </plugins>
	</build>

对于具体的 DataLoader 子项目，需要 spark 相关组件，还需要 mongodb、elasticsearch 的相关依赖，我们在 pom.xml 文件中引入所有依赖（在父项目中已声明的不需要再加详细信息）：

MovieRecommendSystem/recommender/DataLoader/pom.xml

.. code-block:: java
	:linenos:

	<dependencies>
	    <!-- Spark 的依赖引入 -->
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-core_2.11</artifactId>
	    </dependency>
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-sql_2.11</artifactId>
	    </dependency>
	    <!-- 引入 Scala -->
	    <dependency>
	        <groupId>org.scala-lang</groupId>
	        <artifactId>scala-library</artifactId>
	    </dependency>
	    <!-- 加入 MongoDB 的驱动 -->
	    <dependency>
	        <groupId>org.mongodb</groupId>
	        <artifactId>casbah-core_2.11</artifactId>
	        <version>${casbah.version}</version>
	    </dependency>
	    <dependency>
	        <groupId>org.mongodb.spark</groupId>
	        <artifactId>mongo-spark-connector_2.11</artifactId>
	        <version>${mongodb-spark.version}</version>
	    </dependency>
	    <!-- 加入 ElasticSearch 的驱动 -->
	    <dependency>
	        <groupId>org.elasticsearch.client</groupId>
	        <artifactId>transport</artifactId>
	        <version>${elasticsearch.version}</version>
	    </dependency>
	    <dependency>
	        <groupId>org.elasticsearch</groupId>
	        <artifactId>elasticsearch-spark-20_2.11</artifactId>
	        <version>${elasticsearch-spark.version}</version>
	        <!-- 将不需要依赖的包从依赖路径中除去 -->
	        <exclusions>
	            <exclusion>
	                <groupId>org.apache.hive</groupId>
	                <artifactId>hive-service</artifactId>
	            </exclusion>
	        </exclusions>
	    </dependency>
	</dependencies>

至此，做数据加载需要的依赖都已配置好，可以开始写代码了。

数据加载准备
========================================================================

在 src/main/目录下，可以看到已有的默认源文件目录是 java，我们可以将其改名为 scala。将数据文件 movies.csv，ratings.csv，tags.csv 复制到资源文件目录 src/main/resources 下，我们将从这里读取数据并加载到 mongodb 和 elastic search 中。

Movies 数据集
------------------------------------------------------------------------

数据格式：mid,name,descri,timelong,issue,shoot,language,genres,actors,directors

e.g.

::

	1^Toy Story (1995)^ ^81 minutes^March 20, 2001^1995^English^Adventure|Animation|Children|Comedy|Fantasy ^Tom Hanks|Tim Allen|Don Rickles|Jim Varney|Wallace Shawn|John Ratzenberger|Annie Potts|John Morris|Erik von Detten|Laurie Metcalf|R. Lee Ermey|Sarah Freeman|Penn Jillette|Tom Hanks|Tim Allen|Don Rickles|Jim Varney|Wallace Shawn ^John Lasseter

Movie 数据集有 10 个字段，每个字段之间通过“^”符号进行分割。

::

	字段名 字段类型 字段描述 字段备注
	mid Int 电影的 ID
	name String 电影的名称
	descri String 电影的描述
	timelong String 电影的时长
	shoot String 电影拍摄时间
	issue String 电影发布时间
	language Array[String] 电影语言 每一项用“|”分割
	genres Array[String] 电影所属类别 每一项用“|”分割
	director Array[String] 电影的导演 每一项用“|”分割
	actors Array[String] 电影的演员 每一项用“|”分割

Ratings 数据集
------------------------------------------------------------------------

数据格式：userId,movieId,rating,timestamp

e.g.

::

	1,31,2.5,1260759144

Rating 数据集有 4 个字段，每个字段之间通过“，”分割。

::

	字段名 字段类型 字段描述 字段备注
	uid Int 用户的 ID
	mid Int 电影的 ID
	score Double 电影的分值
	timestamp Long 评分的时间

Tag 数据集
------------------------------------------------------------------------

数据格式：userId,movieId,tag,timestamp

e.g.

::

	1,31,action,1260759144

Rating 数据集有 4 个字段，每个字段之间通过“，”分割。

::

	字段名 字段类型 字段描述 字段备注
	uid Int 用户的 ID
	mid Int 电影的 ID
	tag String 电影的标签
	timestamp Long 标签的时间

日志管理配置文件
------------------------------------------------------------------------

log4j 对日志的管理，需要通过配置文件来生效。在 src/main/resources 下新建配置文件 log4j.properties，写入以下内容：

::

	log4j.rootLogger=info, stdout
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss,SSS} %5p ---
	[%50t] %-80c(line:%5L) : %m%n

数据初始化到 MongoDB
========================================================================

数据加载程序主体实现
------------------------------------------------------------------------

| 这里会为原始数据定义几个样例类，通过 SparkContext 的 textFile 方法从文件中读取数据，并转换成 DataFrame，再利用 Spark SQL 提供的 write 方法进行数据的分布式插入。
| 在 DataLoader/src/main/scala 下新建 package，命名为 com.atguigu.recommender，新建名为 DataLoader 的 scala class 文件。

程序主体代码如下：

DataLoader/src/main/scala/com.atguigu.recommerder/DataLoader.scala

.. code-block:: java
	:linenos:

	import java.net.InetAddress

	import com.mongodb.casbah.commons.MongoDBObject
	import com.mongodb.casbah.{MongoClient, MongoClientURI}
	import org.apache.spark.SparkConf
	import org.apache.spark.sql.{DataFrame, SparkSession}
	import org.elasticsearch.action.admin.indices.create.CreateIndexRequest
	import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest
	import org.elasticsearch.action.admin.indices.exists.indices.IndicesExistsRequest
	import org.elasticsearch.common.settings.Settings
	import org.elasticsearch.common.transport.InetSocketTransportAddress
	import org.elasticsearch.transport.client.PreBuiltTransportClient


	/**
	  * Movie 数据集
	  *
	  * 260                                         电影ID，mid
	  * Star Wars: Episode IV - A New Hope (1977)   电影名称，name
	  * Princess Leia is captured and held hostage  详情描述，descri
	  * 121 minutes                                 时长，timelong
	  * September 21, 2004                          发行时间，issue
	  * 1977                                        拍摄时间，shoot
	  * English                                     语言，language
	  * Action|Adventure|Sci-Fi                     类型，genres
	  * Mark Hamill|Harrison Ford|Carrie Fisher     演员表，actors
	  * George Lucas                                导演，directors
	  *
	  */
	case class Movie(mid: Int, name: String, descri: String, timelong: String, issue: String,
	                 shoot: String, language: String, genres: String, actors: String, directors: String)

	/**
	  * Rating数据集
	  *
	  * 1,31,2.5,1260759144
	  */
	case class Rating(uid: Int, mid: Int, score: Double, timestamp: Int )

	/**
	  * Tag数据集
	  *
	  * 15,1955,dentist,1193435061
	  */
	case class Tag(uid: Int, mid: Int, tag: String, timestamp: Int)

	// 把mongo和es的配置封装成样例类

	/**
	  *
	  * @param uri MongoDB连接
	  * @param db  MongoDB数据库
	  */
	case class MongoConfig(uri:String, db:String)

	/**
	  *
	  * @param httpHosts       http主机列表，逗号分隔
	  * @param transportHosts  transport主机列表
	  * @param index            需要操作的索引
	  * @param clustername      集群名称，默认elasticsearch
	  */
	case class ESConfig(httpHosts:String, transportHosts:String, index:String, clustername:String)

	object DataLoader {

	  // 定义常量
	  val MOVIE_DATA_PATH = "D:\\Projects\\BigData\\MovieRecommendSystem\\recommender\\DataLoader\\src\\main\\resources\\movies.csv"
	  val RATING_DATA_PATH = "D:\\Projects\\BigData\\MovieRecommendSystem\\recommender\\DataLoader\\src\\main\\resources\\ratings.csv"
	  val TAG_DATA_PATH = "D:\\Projects\\BigData\\MovieRecommendSystem\\recommender\\DataLoader\\src\\main\\resources\\tags.csv"

	  val MONGODB_MOVIE_COLLECTION = "Movie"
	  val MONGODB_RATING_COLLECTION = "Rating"
	  val MONGODB_TAG_COLLECTION = "Tag"
	  val ES_MOVIE_INDEX = "Movie"

	  def main(args: Array[String]): Unit = {

	    val config = Map(
	      "spark.cores" -> "local[*]",
	      "mongo.uri" -> "mongodb://localhost:27017/recommender",
	      "mongo.db" -> "recommender",
	      "es.httpHosts" -> "localhost:9200",
	      "es.transportHosts" -> "localhost:9300",
	      "es.index" -> "recommender",
	      "es.cluster.name" -> "elasticsearch"
	    )

	    // 创建一个sparkConf
	    val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("DataLoader")

	    // 创建一个SparkSession
	    val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	    import spark.implicits._

	    // 加载数据
	    val movieRDD = spark.sparkContext.textFile(MOVIE_DATA_PATH)

	    val movieDF = movieRDD.map(
	      item => {
	        val attr = item.split("\\^")
	        Movie(attr(0).toInt, attr(1).trim, attr(2).trim, attr(3).trim, attr(4).trim, attr(5).trim, attr(6).trim, attr(7).trim, attr(8).trim, attr(9).trim)
	      }
	    ).toDF()

	    val ratingRDD = spark.sparkContext.textFile(RATING_DATA_PATH)

	    val ratingDF = ratingRDD.map(item => {
	      val attr = item.split(",")
	      Rating(attr(0).toInt,attr(1).toInt,attr(2).toDouble,attr(3).toInt)
	    }).toDF()

	    val tagRDD = spark.sparkContext.textFile(TAG_DATA_PATH)
	    //将tagRDD装换为DataFrame
	    val tagDF = tagRDD.map(item => {
	      val attr = item.split(",")
	      Tag(attr(0).toInt,attr(1).toInt,attr(2).trim,attr(3).toInt)
	    }).toDF()

	    implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	    // 将数据保存到MongoDB
	    storeDataInMongoDB(movieDF, ratingDF, tagDF)

	    // 数据预处理，把movie对应的tag信息添加进去，加一列 tag1|tag2|tag3...
	    import org.apache.spark.sql.functions._

	    /**
	      * mid, tags
	      *
	      * tags: tag1|tag2|tag3...
	      */
	    val newTag = tagDF.groupBy($"mid")
	      .agg( concat_ws( "|", collect_set($"tag") ).as("tags") )
	      .select("mid", "tags")

	    // newTag和movie做join，数据合并在一起，左外连接
	    val movieWithTagsDF = movieDF.join(newTag, Seq("mid"), "left")

	    implicit val esConfig = ESConfig(config("es.httpHosts"), config("es.transportHosts"), config("es.index"), config("es.cluster.name"))

	    // 保存数据到ES
	    storeDataInES(movieWithTagsDF)

	    spark.stop()
	  }

	  def storeDataInMongoDB(movieDF: DataFrame, ratingDF: DataFrame, tagDF: DataFrame)(implicit mongoConfig: MongoConfig): Unit ={
	    // 新建一个mongodb的连接
	    val mongoClient = MongoClient(MongoClientURI(mongoConfig.uri))

	    // 如果mongodb中已经有相应的数据库，先删除
	    mongoClient(mongoConfig.db)(MONGODB_MOVIE_COLLECTION).dropCollection()
	    mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).dropCollection()
	    mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).dropCollection()

	    // 将DF数据写入对应的mongodb表中
	    movieDF.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_MOVIE_COLLECTION)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()

	    ratingDF.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_RATING_COLLECTION)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()

	    tagDF.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_TAG_COLLECTION)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()

	    //对数据表建索引
	    mongoClient(mongoConfig.db)(MONGODB_MOVIE_COLLECTION).createIndex(MongoDBObject("mid" -> 1))
	    mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).createIndex(MongoDBObject("uid" -> 1))
	    mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).createIndex(MongoDBObject("mid" -> 1))
	    mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).createIndex(MongoDBObject("uid" -> 1))
	    mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).createIndex(MongoDBObject("mid" -> 1))

	    mongoClient.close()

	  }

	  def storeDataInES(movieDF: DataFrame)(implicit eSConfig: ESConfig): Unit ={
	    // 新建es配置
	    val settings: Settings = Settings.builder().put("cluster.name", eSConfig.clustername).build()

	    // 新建一个es客户端
	    val esClient = new PreBuiltTransportClient(settings)

	    val REGEX_HOST_PORT = "(.+):(\\d+)".r
	    eSConfig.transportHosts.split(",").foreach{
	      case REGEX_HOST_PORT(host: String, port: String) => {
	        esClient.addTransportAddress(new InetSocketTransportAddress( InetAddress.getByName(host), port.toInt ))
	      }
	    }

	    // 先清理遗留的数据
	    if( esClient.admin().indices().exists( new IndicesExistsRequest(eSConfig.index) )
	        .actionGet()
	        .isExists
	    ){
	      esClient.admin().indices().delete( new DeleteIndexRequest(eSConfig.index) )
	    }

	    esClient.admin().indices().create( new CreateIndexRequest(eSConfig.index) )

	    movieDF.write
	      .option("es.nodes", eSConfig.httpHosts)
	      .option("es.http.timeout", "100m")
	      .option("es.mapping.id", "mid")
	      .mode("overwrite")
	      .format("org.elasticsearch.spark.sql")
	      .save(eSConfig.index + "/" + ES_MOVIE_INDEX)
	  }

	}

将数据写入 MongoDB
------------------------------------------------------------------------

接下来，实现 storeDataInMongo 方法，将数据写入 mongodb 中：

.. code-block:: java
	:linenos:

	def storeDataInMongoDB(movieDF: DataFrame, ratingDF: DataFrame, tagDF: DataFrame)(implicit mongoConfig: MongoConfig): Unit ={
	// 新建一个mongodb的连接
	val mongoClient = MongoClient(MongoClientURI(mongoConfig.uri))

	// 如果mongodb中已经有相应的数据库，先删除
	mongoClient(mongoConfig.db)(MONGODB_MOVIE_COLLECTION).dropCollection()
	mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).dropCollection()
	mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).dropCollection()

	// 将DF数据写入对应的mongodb表中
	movieDF.write
	  .option("uri", mongoConfig.uri)
	  .option("collection", MONGODB_MOVIE_COLLECTION)
	  .mode("overwrite")
	  .format("com.mongodb.spark.sql")
	  .save()

	ratingDF.write
	  .option("uri", mongoConfig.uri)
	  .option("collection", MONGODB_RATING_COLLECTION)
	  .mode("overwrite")
	  .format("com.mongodb.spark.sql")
	  .save()

	tagDF.write
	  .option("uri", mongoConfig.uri)
	  .option("collection", MONGODB_TAG_COLLECTION)
	  .mode("overwrite")
	  .format("com.mongodb.spark.sql")
	  .save()

	//对数据表建索引
	mongoClient(mongoConfig.db)(MONGODB_MOVIE_COLLECTION).createIndex(MongoDBObject("mid" -> 1))
	mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).createIndex(MongoDBObject("uid" -> 1))
	mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION).createIndex(MongoDBObject("mid" -> 1))
	mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).createIndex(MongoDBObject("uid" -> 1))
	mongoClient(mongoConfig.db)(MONGODB_TAG_COLLECTION).createIndex(MongoDBObject("mid" -> 1))

	mongoClient.close()

	}

数据初始化到 ElasticSearch
========================================================================

将数据写入 ElasticSearch
------------------------------------------------------------------------

与上节类似，同样主要通过 Spark SQL 提供的 write 方法进行数据的分布式插入，实现 storeDataInES 方法：

.. code-block:: java
	:linenos:

	def storeDataInES(movieDF: DataFrame)(implicit eSConfig: ESConfig): Unit ={
		// 新建es配置
		val settings: Settings = Settings.builder().put("cluster.name", eSConfig.clustername).build()

		// 新建一个es客户端
		val esClient = new PreBuiltTransportClient(settings)

		val REGEX_HOST_PORT = "(.+):(\\d+)".r
		eSConfig.transportHosts.split(",").foreach{
		  case REGEX_HOST_PORT(host: String, port: String) => {
		    esClient.addTransportAddress(new InetSocketTransportAddress( InetAddress.getByName(host), port.toInt ))
		  }
		}

		// 先清理遗留的数据
		if( esClient.admin().indices().exists( new IndicesExistsRequest(eSConfig.index) )
		    .actionGet()
		    .isExists
		){
		  esClient.admin().indices().delete( new DeleteIndexRequest(eSConfig.index) )
		}

		esClient.admin().indices().create( new CreateIndexRequest(eSConfig.index) )

		movieDF.write
		  .option("es.nodes", eSConfig.httpHosts)
		  .option("es.http.timeout", "100m")
		  .option("es.mapping.id", "mid")
		  .mode("overwrite")
		  .format("org.elasticsearch.spark.sql")
		  .save(eSConfig.index + "/" + ES_MOVIE_INDEX)
	}

.. |image0| image:: /_static/machine_learn_intro/WX20200818-101320@2x.webp
.. |image1| image:: /_static/machine_learn_intro/WX20200818-102126@2x.webp
.. |image2| image:: /_static/machine_learn_intro/WX20200820-092754@2x.webp



















































































































































