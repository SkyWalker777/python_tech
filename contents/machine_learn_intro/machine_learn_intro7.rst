机器学习 7 电影推荐系统实现
#######################################################################

离线推荐服务建设
***********************************************************************

离线推荐服务
========================================================================

离线推荐服务是综合用户所有的历史数据，利用设定的离线统计算法和离线推荐算法周期性的进行结果统计与保存，计算的结果在一定时间周期内是固定不变的，变更的频率取决于算法调度的频率。

离线推荐服务主要计算一些可以预先进行统计和计算的指标，为实时计算和前端业务相应提供数据支撑。

离线推荐服务主要分为统计性算法、基于 ALS 的协同过滤推荐算法以及基于 ElasticSearch 的内容推荐算法。 在 recommender 下新建子项目 StatisticsRecommender，pom.xml 文件中只需引入 spark、scala 和 mongodb 的相关依赖：

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
	    <!-- 用于代码方式连接 MongoDB -->
	    <dependency>
	        <groupId>org.mongodb</groupId>
	        <artifactId>casbah-core_2.11</artifactId>
	        <version>${casbah.version}</version>
	    </dependency>
	    <!-- 用于 Spark 和 MongoDB 的对接 -->
	    <dependency>
	        <groupId>org.mongodb.spark</groupId>
	        <artifactId>mongo-spark-connector_2.11</artifactId>
	        <version>${mongodb-spark.version}</version>
	    </dependency>
	</dependencies>

在 resources 文件夹下引入 log4j.properties，然后在 src/main/scala 下新建 scala 单例对象 com.atguigu.statistics.StatisticsRecommender。

同样，我们应该先建好样例类，在 main()方法中定义配置、创建 SparkSession 并加载数据，最后关闭 spark。代码如下：

.../.../.../...statistics/StatisticsRecommender.scala

.. code-block:: java
	:linenos:

	case class Movie(mid: Int, name: String, descri: String, timelong: String, issue: String,
	                 shoot: String, language: String, genres: String, actors: String, directors: String)

	case class Rating(uid: Int, mid: Int, score: Double, timestamp: Int )

	case class MongoConfig(uri:String, db:String)

	// 定义一个基准推荐对象
	case class Recommendation( mid: Int, score: Double )

	// 定义电影类别top10推荐对象
	case class GenresRecommendation( genres: String, recs: Seq[Recommendation] )

	object StatisticsRecommender {

	  // 定义表名
	  val MONGODB_MOVIE_COLLECTION = "Movie"
	  val MONGODB_RATING_COLLECTION = "Rating"

	  //统计的表的名称
	  val RATE_MORE_MOVIES = "RateMoreMovies"
	  val RATE_MORE_RECENTLY_MOVIES = "RateMoreRecentlyMovies"
	  val AVERAGE_MOVIES = "AverageMovies"
	  val GENRES_TOP_MOVIES = "GenresTopMovies"

	  def main(args: Array[String]): Unit = {
	    val config = Map(
	      "spark.cores" -> "local[*]",
	      "mongo.uri" -> "mongodb://localhost:27017/recommender",
	      "mongo.db" -> "recommender"
	    )

	    // 创建一个sparkConf
	    val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("StatisticsRecommeder")

	    // 创建一个SparkSession
	    val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	    import spark.implicits._

	    implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	    // 从mongodb加载数据
	    val ratingDF = spark.read
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_RATING_COLLECTION)
	      .format("com.mongodb.spark.sql")
	      .load()
	      .as[Rating]
	      .toDF()

	    val movieDF = spark.read
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_MOVIE_COLLECTION)
	      .format("com.mongodb.spark.sql")
	      .load()
	      .as[Movie]
	      .toDF()

	    // 创建名为ratings的临时表
	    ratingDF.createOrReplaceTempView("ratings")

	    // 不同的统计推荐结果
	    // 1. 历史热门统计，历史评分数据最多，mid，count
	    val rateMoreMoviesDF = spark.sql("select mid, count(mid) as count from ratings group by mid")
	    // 把结果写入对应的mongodb表中
	    storeDFInMongoDB( rateMoreMoviesDF, RATE_MORE_MOVIES )

	    // 2. 近期热门统计，按照“yyyyMM”格式选取最近的评分数据，统计评分个数
	    // 创建一个日期格式化工具
	    val simpleDateFormat = new SimpleDateFormat("yyyyMM")
	    // 注册udf，把时间戳转换成年月格式
	    spark.udf.register("changeDate", (x: Int)=>simpleDateFormat.format(new Date(x * 1000L)).toInt )

	    // 对原始数据做预处理，去掉uid
	    val ratingOfYearMonth = spark.sql("select mid, score, changeDate(timestamp) as yearmonth from ratings")
	    ratingOfYearMonth.createOrReplaceTempView("ratingOfMonth")

	    // 从ratingOfMonth中查找电影在各个月份的评分，mid，count，yearmonth
	    val rateMoreRecentlyMoviesDF = spark.sql("select mid, count(mid) as count, yearmonth from ratingOfMonth group by yearmonth, mid order by yearmonth desc, count desc")

	    // 存入mongodb
	    storeDFInMongoDB(rateMoreRecentlyMoviesDF, RATE_MORE_RECENTLY_MOVIES)

	    // 3. 优质电影统计，统计电影的平均评分，mid，avg
	    val averageMoviesDF = spark.sql("select mid, avg(score) as avg from ratings group by mid")
	    storeDFInMongoDB(averageMoviesDF, AVERAGE_MOVIES)

	    // 4. 各类别电影Top统计
	    // 定义所有类别
	    val genres = List("Action","Adventure","Animation","Comedy","Crime","Documentary","Drama","Family","Fantasy","Foreign","History","Horror","Music","Mystery"
	      ,"Romance","Science","Tv","Thriller","War","Western")

	    // 把平均评分加入movie表里，加一列，inner join
	    val movieWithScore = movieDF.join(averageMoviesDF, "mid")

	    // 为做笛卡尔积，把genres转成rdd
	    val genresRDD = spark.sparkContext.makeRDD(genres)

	    // 计算类别top10，首先对类别和电影做笛卡尔积
	    val genresTopMoviesDF = genresRDD.cartesian(movieWithScore.rdd)
	      .filter{
	        // 条件过滤，找出movie的字段genres值(Action|Adventure|Sci-Fi)包含当前类别genre(Action)的那些
	        case (genre, movieRow) => movieRow.getAs[String]("genres").toLowerCase.contains( genre.toLowerCase )
	      }
	      .map{
	        case (genre, movieRow) => ( genre, ( movieRow.getAs[Int]("mid"), movieRow.getAs[Double]("avg") ) )
	      }
	      .groupByKey()
	      .map{
	        case (genre, items) => GenresRecommendation( genre, items.toList.sortWith(_._2>_._2).take(10).map( item=> Recommendation(item._1, item._2)) )
	      }
	      .toDF()

	    storeDFInMongoDB(genresTopMoviesDF, GENRES_TOP_MOVIES)

	    spark.stop()
	  }

	  def storeDFInMongoDB(df: DataFrame, collection_name: String)(implicit mongoConfig: MongoConfig): Unit ={
	    df.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", collection_name)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()
	  }

	}

离线统计服务
========================================================================

历史热门电影统计
------------------------------------------------------------------------

根据所有历史评分数据，计算历史评分次数最多的电影。

实现思路：通过 Spark SQL 读取评分数据集，统计所有评分中评分数最多的电影，然后按照从大到小排序，将最终结果写入 MongoDB 的 RateMoreMovies 数据集中。

.. code-block:: java
	:linenos:

	//统计所有历史数据中每个电影的评分数
	//数据结构 -》 mid,count
	val rateMoreMoviesDF = spark.sql("select mid, count(mid) as count from ratings group by mid")
	rateMoreMoviesDF
		.write
		.option("uri",mongoConfig.uri)
		.option("collection",RATE_MORE_MOVIES)
		.mode("overwrite")
		.format("com.mongodb.spark.sql")
		.save()

最近热门电影统计
------------------------------------------------------------------------

根据评分，按月为单位计算最近时间的月份里面评分数最多的电影集合。

实现思路：通过 Spark SQL 读取评分数据集，通过 UDF 函数将评分的数据时间修改为月，然后统计每月电影的评分数 。 统 计 完 成 之 后 将 数 据 写 入 到 MongoDB 的 RateMoreRecentlyMovies 数据集中。

.. code-block:: java
	:linenos:

	//统计以月为单位拟每个电影的评分数
	//数据结构 -》 mid,count,time
	//创建一个日期格式化工具
	val simpleDateFormat = new SimpleDateFormat("yyyyMM")

	//注册一个 UDF 函数，用于将 timestamp 装换成年月格式 1260759144000 => 201605
	spark.udf.register("changeDate",(x:Int) => simpleDateFormat.format(new Date(x * 1000L)).toInt)

	// 将原来的 Rating 数据集中的时间转换成年月的格式
	val ratingOfYearMonth = spark.sql("select mid, score, changeDate(timestamp) as yearmonth from ratings")

	// 将新的数据集注册成为一张表
	ratingOfYearMonth.createOrReplaceTempView("ratingOfMonth")
	val rateMoreRecentlyMovies = spark.sql("select mid, count(mid) as count ,yearmonth from ratingOfMonth group by yearmonth,mid")

	rateMoreRecentlyMovies
		.write
		.option("uri",mongoConfig.uri)
		.option("collection",RATE_MORE_RECENTLY_MOVIES)
		.mode("overwrite")
		.format("com.mongodb.spark.sql")
		.save()

电影平均得分统计
------------------------------------------------------------------------

根据历史数据中所有用户对电影的评分，周期性的计算每个电影的平均得分。

实现思路：通过 Spark SQL 读取保存在 MongDB 中的 Rating 数据集，通过执行以下 SQL 语句实现对于电影的平均分统计：

.. code-block:: java
	:linenos:

	//统计每个电影的平均评分
	val averageMoviesDF = spark.sql("select mid, avg(score) as avg from ratings group by mid")
	averageMoviesDF
		.write
		.option("uri",mongoConfig.uri)
		.option("collection",AVERAGE_MOVIES)
		.mode("overwrite")
		.format("com.mongodb.spark.sql")
		.save()

统计完成之后将生成的新的 DataFrame 写出到 MongoDB 的 AverageMoviesScore 集合中。

每个类别优质电影统计
------------------------------------------------------------------------

根据提供的所有电影类别，分别计算每种类型的电影集合中评分最高的 10 个电影。

实现思路：在计算完整个电影的平均得分之后，将影片集合与电影类型做笛卡尔积，然后过滤掉电影类型不符合的条目，将 DataFrame 输出到 MongoDB 的 GenresTopMovies集合中。

.. code-block:: java
	:linenos:

	//统计每种电影类型中评分最高的 10 个电影
	val movieWithScore = movieDF.join(averageMoviesDF,Seq("mid")) 
	//所有的电影类别
	val genres = List("Action","Adventure","Animation","Comedy","Crime","Documentary","Drama","Family","Fantasy","Foreign","History","Horror","Music","Mystery","Romance","Science","Tv","Thriller","War","Western")

	//将电影类别转换成 RDD
	val genresRDD = spark.sparkContext.makeRDD(genres)

	//计算电影类别 top10
	val genrenTopMovies = genresRDD.cartesian(movieWithScore.rdd) 
		.filter{
			case (genres,row) => 
				row.getAs[String]("genres").toLowerCase.contains(genres.toLowerCase)
		}
		.map{
			// 将整个数据集的数据量减小，生成 RDD[String,Iter[mid,avg]]
			case (genres,row) => {
				(genres,(row.getAs[Int]("mid"), row.getAs[Double]("avg")))
			}
		}.groupByKey()
		.map{
			case (genres, items) => GenresRecommendation(genres,items.toList.sortWith(_._2 > _._2).take(10).map(item => Recommendation(item._1,item._2)))
		}.toDF()

	// 输出数据到 MongoDB
	genrenTopMovies
		.write
		.option("uri",mongoConfig.uri)
		.option("collection",GENRES_TOP_MOVIES)
		.mode("overwrite")
		.format("com.mongodb.spark.sql")
		.save()

基于隐语义模型的协同过滤推荐
========================================================================

项目采用 ALS 作为协同过滤算法，分别根据 MongoDB 中的用户评分表和电影数据集计算用户电影推荐矩阵以及电影相似度矩阵。

用户电影推荐矩阵
------------------------------------------------------------------------

通过 ALS 训练出来的 Model 来计算所有当前用户电影的推荐矩阵，主要思路如下：

::

	1. UserId 和 MovieID 做笛卡尔积，产生（uid，mid）的元组
	2. 通过模型预测（uid，mid）的元组。
	3. 将预测结果通过预测分值进行排序。
	4. 返回分值最大的 K 个电影，作为当前用户的推荐。

最后生成的数据结构如下：将数据保存到 MongoDB 的 UserRecs 表中

|image0|

新建 recommender 的子项目 OfflineRecommender，引入 spark、scala、mongo 和 jblas 的依赖： 

.. code-block:: java
	:linenos:

	<dependencies>
	    <!-- Java 中线性代数相关的库 -->
	    <dependency>
	        <groupId>org.scalanlp</groupId>
	        <artifactId>jblas</artifactId>
	        <version>${jblas.version}</version>
	    </dependency>
	    <!-- Spark 的依赖引入 -->
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-core_2.11</artifactId>
	    </dependency>
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-sql_2.11</artifactId>
	    </dependency>
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-mllib_2.11</artifactId>
	    </dependency>
	    <!-- 引入 Scala -->
	    <dependency>
	        <groupId>org.scala-lang</groupId>
	        <artifactId>scala-library</artifactId>
	    </dependency>
	    <!-- 加入 MongoDB 的驱动 -->
	    <!-- 用于代码方式连接 MongoDB -->
	    <dependency>
	        <groupId>org.mongodb</groupId>
	        <artifactId>casbah-core_2.11</artifactId>
	        <version>${casbah.version}</version>
	    </dependency>
	    <!-- 用于 Spark 和 MongoDB 的对接 -->
	    <dependency>
	        <groupId>org.mongodb.spark</groupId>
	        <artifactId>mongo-spark-connector_2.11</artifactId>
	        <version>${mongodb-spark.version}</version>
	    </dependency>
	</dependencies>

同样经过前期的构建样例类、声明配置、创建 SparkSession 等步骤，可以加载数据开始计算模型了。

核心代码如下：

src/main/scala/xxx.xxx.offline/OfflineRecommender.scala

.. code-block:: java
	:linenos:

	import org.apache.spark.SparkConf
	import org.apache.spark.mllib.recommendation.{ALS, Rating}
	import org.apache.spark.sql.SparkSession
	import org.jblas.DoubleMatrix

	// 基于评分数据的 LFM，只需要 rating 数据（用户评分表）注意：spark mllib 中有 Rating 类，为了便于区别，我们重新命名为 MovieRating
	case class MovieRating(uid: Int, mid: Int, score: Double, timestamp: Long)

	case class MongoConfig(uri: String, db: String)

	// 标准推荐对象
	case class Recommendation(mid: Int, score: Double)

	// 用户推荐列表
	case class UserRecs(uid: Int, recs: Seq[Recommendation])

	// 电影相似度（电影推荐）
	case class MovieRecs(mid: Int, recs: Seq[Recommendation])

	object OfflineRecommender {
	  // 定义 MongoDB 数据库中的一些表名
	  val MONGODB_RATING_COLLECTION = "Rating"

	  // 推荐表的名称
	  val USER_RECS = "UserRecs"
	  val MOVIE_RECS = "MovieRecs"

	  val USER_MAX_RECOMMENDATION = 20

	  def main(args: Array[String]): Unit = {
	    // 定义用到的配置参数
	    val config = Map(
	      "spark.cores" -> "local[*]",
	      "mongo.uri" -> "mongodb://localhost:27017/recommender",
	      "mongo.db" -> "recommender"
	    )

	    // 创建一个 SparkConf 对象
	    val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("OfflineRecommender")

	    // 创建一个 SparkSession 对象
	    val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	    // 在对 DataFrame 和 Dataset 进行许多操作都需要这个包进行支持
	    import spark.implicits._

	    // 声明一个隐式的配置对象
	    implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	    // 从 MongoDB 中加载数据
	    val ratingRDD = spark.read
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_RATING_COLLECTION)
	      .format("com.mongodb.spark.sql")
	      .load()
	      .as[MovieRating] // DataSet
	      .rdd
	      .map(rating => (rating.uid, rating.mid, rating.score)) // 转换成 RDD，并且去掉时间戳
	      .cache()

	    // 从 ratingRDD 数据中提取所有的 uid 和 mid ，并去重
	    val userRDD = ratingRDD.map(_._1).distinct()
	    val movieRDD = ratingRDD.map(_._2).distinct()

	    // 训练隐语义模型
	    val trainData = ratingRDD.map(x => Rating(x._1, x._2, x._3))

	    val (rank, iterations, lambda) = (50, 5, 0.01)
	    val model = ALS.train(trainData, rank, iterations, lambda)

	    // 基于用户和电影的隐特征，计算预测评分，得到用户推荐列表
	    // user 和 movie 做笛卡尔积，得到一个空评分矩阵，即产生 (uid，mid) 的元组
	    val userMovies = userRDD.cartesian(movieRDD)

	    // 调用 model 的 predict 方法进行预测评分
	    val preRatings = model.predict(userMovies)

	    val userRecs = preRatings
	      .filter(_.rating > 0) // 过滤出评分大于零的项
	      .map(rating => (rating.user, (rating.product, rating.rating)))
	      .groupByKey()
	      .map {
	        case (uid, recs) => UserRecs(uid, recs.toList.sortWith(_._2 > _._2).take(USER_MAX_RECOMMENDATION).map(x => Recommendation(x._1, x._2)))
	      }
	      .toDF()

	    // 把结果写入对应的 MongoDB 表中
	    userRecs.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", USER_RECS)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()

	    // TODO:计算电影相似度矩阵

	    spark.stop()
	}

电影相似度矩阵
------------------------------------------------------------------------

|image1|

数据集中任意两个电影间相似度都可以由公式计算得到，电影与电影之间的相似度在一段时间内基本是固定值。最后生成的数据保存到 MongoDB 的 MovieRecs【电影相似性矩阵】表中。 

核心代码如下：

.. code-block:: java
	:linenos:

	// 基于电影的隐特征，计算相似度矩阵，得到电影的相似度列表
	val movieFeatures = model.productFeatures.map {
	  case (mid, features) => (mid, new DoubleMatrix(features))
	}

	// 对所有电影两两计算它们的相似度，先做笛卡尔积
	val movieRecs = movieFeatures.cartesian(movieFeatures)
	  .filter {
	    // 把自己跟自己的配对过滤掉
	    case (a, b) => a._1 != b._1
	  }
	  .map {
	    case (a, b) => {
	      val simScore = this.consinSim(a._2, b._2)
	      (a._1, (b._1, simScore))
	    }
	  }
	  .filter(_._2._2 > 0.6) // 过滤出相似度大于 0.6 的
	  .groupByKey()
	  .map {
	    case (mid, recs) => MovieRecs(mid, recs.toList.sortWith(_._2 > _._2).map(x => Recommendation(x._1, x._2)))
	  }
	  .toDF()

	// 把结果写入对应的 MongoDB 表中
	movieRecs.write
	  .option("uri", mongoConfig.uri)
	  .option("collection", MOVIE_RECS)
	  .mode("overwrite")
	  .format("com.mongodb.spark.sql")
	  .save()

	// 求两个向量的余弦相似度
	def consinSim(movie1: DoubleMatrix, movie2: DoubleMatrix): Double = {
		movie1.dot(movie2) / (movie1.norm2() * movie2.norm2()) // l1范数：向量元素绝对值之和；l2范数：即向量的模长（向量的长度）,向量元素的平方和再开方
	}

模型评估和参数选取
------------------------------------------------------------------------

|image2|

在 scala/com.atguigu.offline/ 下新建单例对象 ALSTrainer，代码主体架构如下：

.. code-block:: java
	:linenos:

	import breeze.numerics.sqrt
	import com.atguigu.offline.OfflineRecommender.MONGODB_RATING_COLLECTION
	import org.apache.spark.SparkConf
	import org.apache.spark.mllib.recommendation.{ALS, MatrixFactorizationModel, Rating}
	import org.apache.spark.rdd.RDD
	import org.apache.spark.sql.SparkSession

	object ALSTrainer {

	  def main(args: Array[String]): Unit = {
	    // 定义用到的配置参数
	    val config = Map(
	      "spark.cores" -> "local[*]",
	      "mongo.uri" -> "mongodb://localhost:27017/recommender",
	      "mongo.db" -> "recommender"
	    )

	    // 创建一个 SparkConf 对象
	    val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("OfflineRecommender")

	    // 创建一个 SparkSession 对象
	    val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	    // 在对 DataFrame 和 Dataset 进行许多操作都需要这个包进行支持
	    import spark.implicits._

	    // 声明一个隐式的配置对象
	    implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	    // 从 MongoDB 中加载数据
	    val ratingRDD = spark.read
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_RATING_COLLECTION)
	      .format("com.mongodb.spark.sql")
	      .load()
	      .as[MovieRating] // DataSet
	      .rdd
	      .map(rating => Rating(rating.uid, rating.mid, rating.score)) // 转换成 RDD，并且去掉时间戳
	      .cache()

	    // 将一个 RDD 随机切分成两个 RDD，用以划分训练集和测试集
	    val splits = ratingRDD.randomSplit(Array(0.8, 0.2))
	    val trainingRDD = splits(0)
	    val testingRDD = splits(1)

	    // 模型参数选择，输出最优参数
	    adjustALSParam(trainingRDD, testingRDD)

	    spark.close()
	  }

其中 adjustALSParams 方法是模型评估的核心，输入一组训练数据和测试数据，输出计算得到最小 RMSE 的那组参数。代码实现如下：

.. code-block:: java
	:linenos:

	def adjustALSParam(trainData: RDD[Rating], testData: RDD[Rating]): Unit = {
		// 这里指定迭代次数为 5，rank 和 lambda 在几个值中选取调整
		val result = for (rank <- Array(50, 100, 200, 300); lambda <- Array(1, 0.1, 0.01, 0.001))
		  yield {
		    val model = ALS.train(trainData, rank, 5, lambda)
		    val rmse = getRMSE(model, testData)
		    (rank, lambda, rmse)
		  }
		// 控制台打印输出
		// println(result.sortBy(_._3).head)
		println(result.minBy(_._3))
	}

计算 RMSE 的函数 getRMSE 代码实现如下：

.. code-block:: java
	:linenos:

	def getRMSE(model: MatrixFactorizationModel, data: RDD[Rating]): Double = {
		// 计算预测评分
		val userProducts = data.map(item => (item.user, item.product))
		val predictRating = model.predict(userProducts)

		// 以 uid,mid 作为外键，将 实际观测值 和 预测值 使用内连接
		val observed = data.map(item => ((item.user, item.product), item.rating))
		val predicted = predictRating.map(item => ((item.user, item.product), item.rating))
		// 内连接，得到 (uid, mid), (observe, predict)

		// 计算 RMSE
		sqrt(
		  observed.join(predicted).map {
		    case ((uid, mid), (observe, predict)) =>
		      val err = observe - predict
		      err * err
		  }.mean()
		)
	}

运行代码，就可以得到目前数据的最优模型参数。

实时推荐服务建设
***********************************************************************

实时推荐服务
========================================================================

实时计算与离线计算应用于推荐系统上最大的不同在于实时计算推荐结果应该反映最近一段时间用户近期的偏好，而离线计算推荐结果则是根据用户从第一次评分起的所有评分记录来计算用户总体的偏好。
  
用户对物品的偏好随着时间的推移总是会改变的。比如一个用户 u 在某时刻对电影 p 给予了极高的评分，那么在近期一段时候，u 极有可能很喜欢与电影 p 类似的其他电影；而如果用户 u 在某时刻对电影 q 给予了极低的评分，那么在近期一段时候，u 极有可能不喜欢与电影 q 类似的其他电影。所以对于实时推荐，当用户对一个电影进行了评价后，用户会希望推荐结果基于最近这几次评分进行一定的更新，使得推荐结果匹配用户近期的偏好，满足用户近期的口味。

如果实时推荐继续采用离线推荐中的 ALS 算法，由于算法运行时间巨大，不具有实时得到新的推荐结果的能力；并且由于算法本身的使用的是评分表，用户本次评分后只更新了总评分表中的一项，使得算法运行后的推荐结果与用户本次评分之前的推荐结果基本没有多少差别，从而给用户一种推荐结果一直没变化的感觉，很影响用户体验。

另外，在实时推荐中由于时间性能上要满足实时或者准实时的要求，所以算法的计算量不能太大，避免复杂、过多的计算造成用户体验的下降。鉴于此，推荐精度往往不会很高。实时推荐系统更关心推荐结果的动态变化能力， 只要更新推荐结果的理由合理即可，至于推荐的精度要求则可以适当放宽。

所以对于实时推荐算法，主要有两点需求： 

::

	1、用户本次评分后、或最近几个评分后系统可以明显的更新推荐结果。
	2、计算量不大，满足响应时间上的实时或者准实时要求。

实时推荐算法设计
========================================================================

|image3|

在 recommender 下新建子项目 StreamingRecommender，引入 spark、scala、mongo、redis 和 kafka 的依赖:

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
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-streaming_2.11</artifactId>
	    </dependency>
	    <!-- 引入 Scala -->
	    <dependency>
	        <groupId>org.scala-lang</groupId>
	        <artifactId>scala-library</artifactId>
	    </dependency>
	    <!-- 加入 MongoDB 的驱动 -->
	    <!-- 用于代码方式连接 MongoDB -->
	    <dependency>
	        <groupId>org.mongodb</groupId>
	        <artifactId>casbah-core_2.11</artifactId>
	        <version>${casbah.version}</version>
	    </dependency>
	    <!-- 用于 Spark 和 MongoDB 的对接 -->
	    <dependency>
	        <groupId>org.mongodb.spark</groupId>
	        <artifactId>mongo-spark-connector_2.11</artifactId>
	        <version>${mongodb-spark.version}</version>
	    </dependency>
	    <!-- redis -->
	    <dependency>
	        <groupId>redis.clients</groupId>
	        <artifactId>jedis</artifactId>
	        <version>2.9.0</version>
	    </dependency>
	    <!-- kafka -->
	    <dependency>
	        <groupId>org.apache.kafka</groupId>
	        <artifactId>kafka-clients</artifactId>
	        <version>0.10.2.1</version>
	    </dependency>
	    <dependency>
	        <groupId>org.apache.spark</groupId>
	        <artifactId>spark-streaming-kafka-0-10_2.11</artifactId>
	        <version>${spark.version}</version>
	    </dependency>
	</dependencies>

代码中首先定义样例类和一个连接助手对象（用于建立 redis 和 mongo 连接），并在 StreamingRecommender 中定义一些常量：

src/xxx/xxx/StreamingRecommender.scala

.. code-block:: java
	:linenos:

	import com.mongodb.casbah.commons.MongoDBObject
	import com.mongodb.casbah.{MongoClient, MongoClientURI}
	import org.apache.kafka.common.serialization.StringDeserializer
	import org.apache.spark.SparkConf
	import org.apache.spark.sql.SparkSession
	import org.apache.spark.streaming.kafka010.{ConsumerStrategies, KafkaUtils, LocationStrategies}
	import org.apache.spark.streaming.{Seconds, StreamingContext}
	import redis.clients.jedis.Jedis

	// 定义连接助手对象并序列化
	object ConnHelper extends Serializable {
	  lazy val jedis = new Jedis("hadoop102")
	  lazy val mongoClient = MongoClient(MongoClientURI("mongodb://localhost:27017/recommender"))
	}

	case class MongoConfig(uri: String, db: String)

	// 定义一个基准推荐对象
	case class Recommendation(mid: Int, score: Double)

	// 定义基于预测评分的用户推荐列表
	case class UserRecs(uid: Int, recs: Seq[Recommendation])

	// 定义基于LFM电影特征向量的电影相似度列表
	case class MovieRecs(mid: Int, recs: Seq[Recommendation])

	object StreamingRecommender {

	  val MONGODB_STREAM_RECS_COLLECTION = "StreamRecs"
	  val MONGODB_RATING_COLLECTION = "Rating"
	  val MONGODB_MOVIE_RECS_COLLECTION = "MovieRecs"

	  val MAX_USER_RATINGS_NUM = 20
	  val MAX_SIM_MOVIES_NUM = 20

	  def main(args: Array[String]): Unit = {

	  }
	}

实时推荐主体代码如下：

.. code-block:: java
	:linenos:

	def main(args: Array[String]): Unit = {
	val config = Map(
	  "spark.cores" -> "local[*]",
	  "mongo.uri" -> "mongodb://localhost:27017/recommender",
	  "mongo.db" -> "recommender",
	  "kafka.topic" -> "recommender"
	)

	// 创建一个 SparkConf 对象
	val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("StreamingRecommender").set("spark.ui.port", "44040" )

	// 创建一个 SparkSession 对象
	val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	// 获取 Streaming Context
	val sc = spark.sparkContext
	val ssc = new StreamingContext(sc, Seconds(2)) // 微批次处理时间间隔

	// 在对 DataFrame 和 Dataset 进行许多操作都需要这个包进行支持
	import spark.implicits._

	// 声明一个隐式的配置对象
	implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	// 加载数据：电影相似度矩阵数据，转换成为 Map[Int, Map[Int, Double]]，把它广播出去
	val simMovieMatrix = spark.read
	  .option("uri", mongoConfig.uri)
	  .option("collection", MONGODB_MOVIE_RECS_COLLECTION)
	  .format("com.mongodb.spark.sql")
	  .load()
	  .as[MovieRecs]
	  .rdd
	  .map { movieRecs => // 为了查询相似度方便，转换成 KV
	    (movieRecs.mid, movieRecs.recs.map(x => (x.mid, x.score)).toMap)
	  }.collectAsMap()

	val simMovieMatrixBroadCast = sc.broadcast(simMovieMatrix)

	// 定义 Kafka 的连接参数
	val kafkaParam = Map(
	  "bootstrap.servers" -> "hadoop102:9092",
	  "key.deserializer" -> classOf[StringDeserializer],
	  "value.deserializer" -> classOf[StringDeserializer],
	  "group.id" -> "recommender",
	  "auto.offset.reset" -> "latest" // 偏移量的初始设置
	)

	// 通过 Kafka 创建一个 DStream
	val kafkaStream = KafkaUtils.createDirectStream[String, String](
	  ssc,
	  LocationStrategies.PreferConsistent,
	  ConsumerStrategies.Subscribe[String, String](Array(config("kafka.topic")), kafkaParam)
	)

	// 把原始数据 uid|mid|score|timestamp 转换成评分流
	val ratingStream = kafkaStream.map {
	  msg =>
	    val attr = msg.value().split("\\|")
	    (attr(0).toInt, attr(1).toInt, attr(2).toDouble, attr(3).toLong)
	}

	// 继续做流式处理，实时算法部分
	ratingStream.foreachRDD {
	  rdds =>
	    rdds.foreach {
	      case (uid, mid, score, timestamp) => {
	        println("rating data coming! >>>>>>>>>>>>>>>>>>>>")
	        // 1、从 redis 中获取当前用户最近的 K 次评分，保存成 Array[(mid, score)]
	        val userRecentlyRatings = getUserRecentlyRatings(MAX_USER_RATINGS_NUM, uid, ConnHelper.jedis)

	        // 2、从相思度矩阵中取出当前电影最相似的 N 个电影，作为备选电影列表，Array[mid]
	        val candidateMovies = getTopsSimMovies(MAX_SIM_MOVIES_NUM, mid, uid, simMovieMatrixBroadCast.value)

	        // 3、对每个备选电影，计算推荐优先级，得到当前用户的实时推荐列表，Array[(mid, score)]
	        val streamRecs = computeMovieScores(candidateMovies, userRecentlyRatings, simMovieMatrixBroadCast.value)

	        // 4、把推荐数据保存到 MongoDB 中
	        storeDataInMongDB(uid, streamRecs)
	      }
	    }
	}

	// 开始接收和处理数据
	ssc.start()

	println(">>>>>>>>>>>>>>>>>>>> streaming started!")

	ssc.awaitTermination()
	}

实时推荐算法的实现
========================================================================

实时推荐算法的前提：

::

	1、在 Redis 集群中存储了每一个用户最近对电影的 K 次评分。实时算法可以快速获取。
	2、离线推荐算法已经将电影相似度矩阵提前计算到了 MongoDB 中。
	3、Kafka 已经获取到了用户实时的评分数据。算法过程如下： 
	实时推荐算法输入为一个评分<uid, mid, rate, timestamp>，而执行的核心内容包括：获取 uid 最近 K 次评分、获取 mid 最相似 K 个电影、计算候选电影的推荐优先级、更新对 uid 的实时推荐结果。

获取用户的 K 次最近评分
------------------------------------------------------------------------

业务服务器在接收用户评分的时候，默认会将该评分情况以 uid, mid, rate, timestamp 的格式插入到 Redis 中该用户对应的队列当中，在实时算法中，只需要通过 Redis 客户端获取相对应的队列内容即可。

.. code-block:: java
	:linenos:

	// 因为 redis 操作返回的是 java 类，为了使用 map 操作需要引入转换类
	import scala.collection.JavaConversions._

	/**
	 * 获取当前最近的 K 次电影评分
	 *
	 * @param num 评分的个数
	 * @param uid 谁的评分
	 * @return
	 */
	def getUserRecentlyRatings(num: Int, uid: Int, jedis: Jedis): Array[(Int, Double)] = {
	  // 从 redis 中读取数据，用户评分数据保存在 uid:UID 为 key 的队列中，里面的 value 是 MID:SCORE
	  jedis.lrange("uid:" + uid.toString, 0, num) // 从用户的队列中取出 num 个评分
	    .map {
	      item => // 具体的每一个评分是以冒号分割的两个值
	        val attr = item.split("\\:")
	        (attr(0).trim.toInt, attr(1).trim.toDouble)
	    }
	    .toArray
	}

获取当前电影最相似的 K 个电影
------------------------------------------------------------------------

在离线算法中，已经预先将电影的相似度矩阵进行了计算，所以每个电影 mid 的最相似的 K 个电影很容易获取：从 MongoDB 中读取 MovieRecs 数据， 从 mid 在 simHash 对应的子哈希表中获取相似度前 K 大的那些电影。输出是数据类型为 Array[Int] 的数组， 表示与 mid 最相似的电影集合， 并命名为 candidateMovies 以作为候选电影集合。

.. code-block:: java
	:linenos:

	/**
	 * 获取与当前电影 K 个相似的电影，作为备选电影
	 *
	 * @param num         相似电影的数量
	 * @param mid         当前电影的 ID
	 * @param uid         当前的评分用户 ID
	 * @param simMovies   电影相似度矩阵的广播变量值
	 * @param mongoConfig MongoDB 的配置
	 * @return 过滤之后的备选电影列表
	 */
	def getTopsSimMovies(num: Int, mid: Int, uid: Int, simMovies: scala.collection.Map[Int, scala.collection.immutable.Map[Int, Double]])
	                    (implicit mongoConfig: MongoConfig): Array[Int] = {
	  // 1、从相似度矩阵中拿到所有相似的电影
	  val allSimMovies = simMovies(mid).toArray

	  // 2、从 MongnDB 中查询用户已经看过的电影
	  val ratingExist = ConnHelper.mongoClient(mongoConfig.db)(MONGODB_RATING_COLLECTION)
	    .find(MongoDBObject("uid" -> uid))
	    .toArray
	    .map {
	      item => item.get("mid").toString.toInt
	    }

	  // 3、把看过的过滤掉，得到备选电影的输出列表
	  allSimMovies.filter(x => !ratingExist.contains(x._1))
	    .sortWith(_._2 > _._2)
	    .take(num)
	    .map(x => x._1)
	}

电影推荐优先级计算
------------------------------------------------------------------------

对于候选电影集合 simiHash 和 uid 的最近 K 个评分 recentRatings， 算法代码内容如下：

.. code-block:: java
	:linenos:

	/**
	 * 计算待选电影的推荐分数
	 *
	 * @param candidateMovies     与当前电影最相似的 N 个电影（待选电影）
	 * @param userRecentlyRatings 用户最近的 K 次评分
	 * @param simMovies           电影相似度矩阵的广播变量值
	 * @return
	 */
	def computeMovieScores(candidateMovies: Array[Int], userRecentlyRatings: Array[(Int, Double)],
	                       simMovies: scala.collection.Map[Int, scala.collection.immutable.Map[Int, Double]]): Array[(Int, Double)] = {
	  // 定义一个 ArrayBuffer，用于保存每一个备选电影的基础得分
	  val scores = scala.collection.mutable.ArrayBuffer[(Int, Double)]()
	  // 定义一个 HashMap，保存每一个备选电影的增强减弱因子
	  val increMap = scala.collection.mutable.HashMap[Int, Int]()
	  val decreMap = scala.collection.mutable.HashMap[Int, Int]()

	  for (candidateMovies <- candidateMovies; userRecentlyRatings <- userRecentlyRatings) {

	    // 获取备选电影和最近评分电影的相似度的得分
	    val simScore = getMoviesSimScore(candidateMovies, userRecentlyRatings._1, simMovies)

	    if (simScore > 0.7) {
	      // 计算候选电影的基础推荐得分
	      scores += ((candidateMovies, simScore * userRecentlyRatings._2))
	      if (userRecentlyRatings._2 > 3) {
	        increMap(candidateMovies) = increMap.getOrDefault(candidateMovies, 0) + 1
	      } else {
	        decreMap(candidateMovies) = decreMap.getOrDefault(candidateMovies, 0) + 1
	      }
	    }
	  }

	  // 根据备选电影的 mid 做 groupBy，根据公式求最后的推荐得分
	  scores.groupBy(_._1).map {
	    // groupBy 之后得到的数据是 Map(mid -> ArrayBuffer[(mid, score)])
	    case (mid, scoreList) =>
	      (mid, scoreList.map(_._2).sum / scoreList.length + log(increMap.getOrDefault(mid, 1)) - log(decreMap.getOrDefault(mid, 1)))
	  }.toArray
	}

其中，getMovieSimScore 是取候选电影和已评分电影的相似度，代码如下：

.. code-block:: java
	:linenos:

	/**
	 * 获取备选电影和最近评分电影的相似度的得分
	 *
	 * @param mid1      备选电影
	 * @param mid2      最近评分电影
	 * @param simMovies 电影相似度矩阵的广播变量值
	 * @return
	 */
	def getMoviesSimScore(mid1: Int, mid2: Int,
	                      simMovies: scala.collection.Map[Int, scala.collection.immutable.Map[Int, Double]]): Double = {
	  simMovies.get(mid1) match {
	    case Some(sims) => sims.get(mid2) match {
	      case Some(score) => score
	      case None => 0.0
	    }
	    case None => 0.0
	  }
	}

而 log 是对数运算， 这里实现为取 10 的对数（常用对数）：

.. code-block:: java
	:linenos:

	/**
	 * 求一个数的对数，底数默认为 10
	 *
	 * @param m
	 * @return
	 */
	def log(m: Int): Double = {
	  val N = 10
	  math.log(m) / math.log(N)
	}

将结果保存到 mongoDB
------------------------------------------------------------------------

saveRecsToMongoDB 函数实现了结果的保存：

.. code-block:: java
	:linenos:

	/**
	 * 把结果写入对应的 MongoDB 表中
	 *
	 * @param uid
	 * @param streamRecs 流式的推荐结果
	 * @param mongoConfig MongoDB 的配置
	 */
	def storeDataInMongDB(uid: Int, streamRecs: Array[(Int, Double)])(implicit mongoConfig: MongoConfig): Unit = {
	  // 定义到 MongoDB 中 StreamRecs 表的连接
	  val streamRecsCollection = ConnHelper.mongoClient(mongoConfig.db)(MONGODB_STREAM_RECS_COLLECTION)
	  // 如果表中已有 uid 对应的数据，则删除
	  streamRecsCollection.findAndRemove(MongoDBObject("uid" -> uid))
	  // 将新的 streamRecs 存入表 StreamRecs 中
	  streamRecsCollection.insert(MongoDBObject("uid" -> uid, "recs" -> streamRecs.map(x => MongoDBObject("mid" -> x._1, "score" -> x._2))))
	}

更新实时推荐结果
------------------------------------------------------------------------

当计算出候选电影的推荐优先级的数组 updatedRecommends<mid, E> 后，这个数组将被发送到 Web 后台服务器，与后台服务器上 uid 的上次实时推荐结果 recentRecommends<mid, E> 进行合并、替换并选出优先级 E 前 K 大的电影作为本次新的实时推荐。具体而言： 

::

	a、合并：将 updatedRecommends 与 recentRecommends 并集合成为一个新的 <mid, E> 数组； 
	b、替换（去重）：当 updatedRecommends 与 recentRecommends 有重复的电影 mid 时，recentRecommends 中 mid 的推荐优先级由于是上次实时推荐的结果，于是将作废，被替换成代表了更新后的 updatedRecommends 的 mid  的推荐优先级； 
	c、选取 TopK：在合并、替换后的 <mid, E> 数组上，根据每个 movie 的推荐优先级，选择出前 K 大的电影，作为本次实时推荐的最终结果。

实时系统联调
========================================================================

实时推荐的数据流向是：业务系统(评分数据) -> 日志 -> flume 日志采集 -> kafka streaming 数据清洗和预处理 -> spark streaming 流式计算。在我们完成实时推荐服务的代码后，应该与其它工具进行联调测试，确保系统正常运行。

启动实时系统的基本组件
------------------------------------------------------------------------

启动实时推荐系统 StreamingRecommender 以及 MongoDB、Redis。

::

	1、运行 StreamingRecommender.scala 代码
	2、启动 MongoDB. C:\Windows\system32>mongod
	3、启动 Redis，并进行连通测试
		[atguigu@hadoop102 bin]$ pwd
		/usr/local/bin
		[atguigu@hadoop102 bin]$ ./redis-server /opt/module/redis-3.0.4/myredis/redis.conf
		[atguigu@hadoop102 bin]$ ./redis-cli -p 6379

先启动 zookeeper 集群
------------------------------------------------------------------------

::

	[atguigu@hadoop102 zookeeper-3.4.10]$ bin/zkServer.sh start
	[atguigu@hadoop103 zookeeper-3.4.10]$ bin/zkServer.sh start
	[atguigu@hadoop104 zookeeper-3.4.10]$ bin/zkServer.sh start

再启动 kafka 集群
------------------------------------------------------------------------

::

	[atguigu@hadoop102 kafka]$ bin/kafka-server-start.sh config/server.properties &
	[atguigu@hadoop103 kafka]$ bin/kafka-server-start.sh config/server.properties &
	[atguigu@hadoop104 kafka]$ bin/kafka-server-start.sh config/server.properties &

构建 Kafka Streaming 程序(简单的 ETL)
------------------------------------------------------------------------

在 recommender 下新建模块 KafkaStreaming，主要用来做日志数据的预处理，过滤出需要的内容。pom.xml 文件需要引入依赖：

.. code-block:: java
	:linenos:

	<?xml version="1.0" encoding="UTF-8"?>
	  <project xmlns="http://maven.apache.org/POM/4.0.0"
	           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	    <parent>
	      <artifactId>recommender</artifactId>
	      <groupId>com.atguigu</groupId>
	      <version>1.0-SNAPSHOT</version>
	    </parent>
	    <modelVersion>4.0.0</modelVersion>

	    <artifactId>KafkaStreaming</artifactId>

	    <dependencies>
	      <dependency>
	        <groupId>org.apache.kafka</groupId>
	        <artifactId>kafka-streams</artifactId>
	        <version>0.10.2.1</version>
	      </dependency>
	      <dependency>
	        <groupId>org.apache.kafka</groupId>
	        <artifactId>kafka-clients</artifactId>
	        <version>0.10.2.1</version>
	      </dependency>
	    </dependencies>

	    <build>
	      <finalName>kafkastream</finalName>
	      <plugins>
	        <plugin>
	          <groupId>org.apache.maven.plugins</groupId>
	          <artifactId>maven-assembly-plugin</artifactId>
	          <configuration>
	            <archive>
	              <manifest>
	                <mainClass>com.atguigu.kafkastream.Application</mainClass>
	              </manifest>
	            </archive>
	            <descriptorRefs>
	              <descriptorRef>jar-with-dependencies</descriptorRef>
	            </descriptorRefs>
	          </configuration>
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
	      </plugins>
	    </build>
	  </project>

在 src/main/java 下新建 java 类 com.atguigu.kafkastreaming.Application

.. code-block:: java
	:linenos:

	import org.apache.kafka.streams.KafkaStreams;
	import org.apache.kafka.streams.StreamsConfig;
	import org.apache.kafka.streams.processor.TopologyBuilder;

	import java.util.Properties;

	public class Application {
	  public static void main(String[] args) {
	    String brokers = "hadoop102:9092";
	    String zookeepers = "hadoop102:2181";

	    // 输入和输出的 topic
	    String from = "log";
	    String to = "recommender";

	    // 定义 kafka streaming 的配置
	    Properties settings = new Properties();
	    settings.put(StreamsConfig.APPLICATION_ID_CONFIG, "logFilter");
	    settings.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, brokers);
	    settings.put(StreamsConfig.ZOOKEEPER_CONNECT_CONFIG, zookeepers);

	    // 创建 kafka streaming 配置对象
	    StreamsConfig config = new StreamsConfig(settings);

	    // 创建一个拓扑建构器
	    TopologyBuilder builder = new TopologyBuilder();

	    // 定义流处理的拓扑结构
	    builder.addSource("SOURCE", from)
	      .addProcessor("PROCESSOR", () -> new LogProcessor(), "SOURCE")
	      .addSink("SINK", to, "PROCESSOR");

	    // 创建 KafkaStreams 对象
	    KafkaStreams streams = new KafkaStreams(builder, config);

	    streams.start();

	    System.out.println("kafka stream started! >>>>>>>>>>>>>>>>>>>>");
	  }
	}

这个程序会将 topic 为 “log” 的信息流获取来做处理，并以 “recommender” 为新的 topic 转发出去。流处理程序 LogProcess.java

.. code-block:: java
	:linenos:

	import org.apache.kafka.streams.processor.Processor
	import org.apache.kafka.streams.processor.ProcessorContext

	class LogProcessor extends Processor[Array[Byte], Array[Byte]] {
	  private var context = null

	  override def init(processorContext: ProcessorContext): Unit = {
	    this.context = processorContext
	  }

	  override def process(dummy: Array[Byte], line: Array[Byte]): Unit = { // dummy 表示 哑变量，没什么用
	    // 把收集到的日志信息用 String 表示
	    var input = new String(line)
	    // 根据前缀 MOVIE_RATING_PREFIX: 从日志信息中提取评分数据
	    if (input.contains("MOVIE_RATING_PREFIX:")) {
	      System.out.println("movie rating data coming! >>>>>>>>>>>>>>>>>>>>" + input)
	      input = input.split("MOVIE_RATING_PREFIX:")(1).trim
	      context.forward("logProcessor".getBytes, input.getBytes)
	    }
	  }

	  def punctuate(l: Long): Unit = {
	  }

	  override def close(): Unit = {
	  }
	}

完成代码后，启动 Application。

配置并启动 flume
------------------------------------------------------------------------

在 flume 的 conf 目录下新建 flume-log-kafka.conf，对 flume 连接 kafka 做配置：flume-log-kafka.conf

::

	agent.sources = exectail 
	agent.channels = memoryChannel 
	agent.sinks = kafkasink

	# For each one of the sources, the type is defined.
	agent.sources.exectail.type = exec

	# 下面这个路径是需要收集日志的绝对路径，改为自己的日志目录
	agent.sources.exectail.command = tail –f /opt/module/flume/log/a gent.log
	agent.sources.exectail.interceptors = i1
	agent.sources.exectail.interceptors.i1.type = regex_filter
	# 定义日志过滤前缀的正则
	agent.sources.exectail.interceptors.i1.regex = .+MOVIE_RATING_PREFIX.+

	# The channel can be defined as follows.
	agent.sources.exectail.channels = memoryChannel

	# Each sink's type must be defined.
	agent.sinks.kafkasink.type = org.apache.flume.sink.kafka.KafkaSink
	agent.sinks.kafkasink.kafka.topic = log
	agent.sinks.kafkasink.kafka.bootstrap.servers = hadoop102:9092,hadoop103:9092,hadoop104:9092
	agent.sinks.kafkasink.kafka.producer.acks = 1
	agent.sinks.kafkasink.kafka.flumeBatchSize = 20

	# Specify the channel the sink should use.
	agent.sinks.kafkasink.channel = memoryChannel
	# Each channel's type is defined. 
	agent.channels.memoryChannel.type = memory

	# Other config values specific to each type of channel(sink or source)
	# can be defined as well
	# In this case, it specifies the capacity of the memory channel.
	agent.channels.memoryChannel.capacity = 10000

配置好后，启动 flume：

::

	[atguigu@hadoop102 flume]$ bin/flume-ng agent --conf conf/ --name a1 --conf-file job/flume-log-kafka.conf -Dflume.root.logger=INFO,console

启动业务系统后台
------------------------------------------------------------------------

将业务代码加入系统中。注意在 src/main/resources/ 下的 log4j.properties 中，log4j.appender.file.File 的值应该替换为自己的日志目录，与 flume 中的配置应该相同。

启动业务系统后台，访问 localhost:8088/index.html；点击某个电影进行评分， 查看实时推荐列表是否会发生变化。

基于内容的推荐服务建设
***********************************************************************

基于内容的推荐服务
========================================================================

原始数据中的 tag 文件，是用户给电影打上的标签，这部分内容想要直接转成评分并不容易，不过我们可以将标签内容进行提取，得到电影的内容特征向量，进而可以通过求取相似度矩阵。这部分可以与实时推荐系统直接对接，计算出与用户当前评分电影的相似电影，实现基于内容的实时推荐。为了避免热门标签对特征提取的影响，我们还可以通过 TF-IDF 算法对标签的权重进行调整，从而尽可能地接近用户偏好。

基于内容推荐的实现
========================================================================

基于以上思想，加入 TF-IDF 算法的求取电影特征向量的核心代码如下：

.. code-block:: java
	:linenos:

	import org.apache.spark.SparkConf
	import org.apache.spark.ml.feature.{HashingTF, IDF, Tokenizer}
	import org.apache.spark.ml.linalg.SparseVector
	import org.apache.spark.sql.SparkSession
	import org.jblas.DoubleMatrix

	// 需要的数据源是电影内容信息
	case class Movie(mid: Int, name: String, descri: String, timelong: String, issue: String,
	                 shoot: String, language: String, genres: String, actors: String, directors: String)

	case class MongoConfig(uri: String, db: String)

	// 定义一个基准推荐对象
	case class Recommendation(mid: Int, score: Double)

	// 定义电影内容信息提取出的特征向量的电影相似度列表
	case class MovieRecs(mid: Int, recs: Seq[Recommendation])

	object ContentRecommender {

	  // 定义表名和常量
	  val MONGODB_MOVIE_COLLECTION = "Movie"
	  val CONTENT_MOVIE_RECS = "ContentMovieRecs"

	  def main(args: Array[String]): Unit = {
	    val config = Map(
	      "spark.cores" -> "local[*]",
	      "mongo.uri" -> "mongodb://localhost:27017/recommender",
	      "mongo.db" -> "recommender"
	    )

	    // 创建一个 SparkConf 对象
	    val sparkConf = new SparkConf().setMaster(config("spark.cores")).setAppName("ContentRecommender")

	    // 创建一个 SparkSession 对象
	    val spark = SparkSession.builder().config(sparkConf).getOrCreate()

	    // 声明一个隐式的配置对象
	    implicit val mongoConfig = MongoConfig(config("mongo.uri"), config("mongo.db"))

	    // 在对 DataFrame 和 Dataset 进行许多操作都需要这个包进行支持
	    import spark.implicits._

	    // 加载数据，并作预处理
	    val movieTagsDF = spark.read
	      .option("uri", mongoConfig.uri)
	      .option("collection", MONGODB_MOVIE_COLLECTION)
	      .format("com.mongodb.spark.sql")
	      .load()
	      .as[Movie]
	      .map { // 提取 mid,name,genres 三项作为原始的内容特征，分词器默认分隔符是空格
	        x => (x.mid, x.name, x.genres.map(c => if (c == '|') ' ' else c))
	      }
	      .toDF("mid", "name", "genres")
	      .cache()

	    // TODO:从内容信息中提取电影特征的特征向量
	    // 创建一个分词器，默认按照空格分词
	    val tokenizer = new Tokenizer().setInputCol("genres").setOutputCol("words")
	    // 用分词器对原始数据进行转换，生成新的一列words
	    val wordsData = tokenizer.transform(movieTagsDF)

	    // 引入 HashingTF 工具，该工具可以将词语序列转换成对应的词频
	    val hashingTF = new HashingTF().setInputCol("words").setOutputCol("rawFeatures").setNumFeatures(50)
	    val featurizeData = hashingTF.transform(wordsData)

	    // 测试
	    // wordsData.show()
	    // featurizeData.show()
	    // featurizeData.show(truncate = false) // 不压缩显示

	    // 引入 IDF 工具，该工具可以得到 IDF 模型
	    val idf = new IDF().setInputCol("rawFeatures").setOutputCol("features")
	    // 训练 IDF 模型，得到每个词的逆文档频率
	    val idfModel = idf.fit(featurizeData)

	    // 用 IDF 模型对原数据进行处理，得到文档中每个词的 TF-IDF，作为新的特征向量
	    val rescaleData = idfModel.transform(featurizeData)

	    // 测试
	    // rescaleData.show(truncate = false) // 不压缩显示

	    val movieFeatures = rescaleData.map(
	      row => (row.getAs[Int]("mid"), row.getAs[SparseVector]("features").toArray)
	    ).rdd.map(
	      x => (x._1, new DoubleMatrix(x._2))
	    )

	    // 测试
	    // movieFeatures.collect().foreach(println)

	    // 对所有电影两两计算它们的相似度，先做笛卡尔积
	    val movieRecs = movieFeatures.cartesian(movieFeatures)
	      .filter {
	        // 把自己跟自己的配对过滤掉
	        case (a, b) => a._1 != b._1
	      }
	      .map {
	        case (a, b) => {
	          val simScore = this.consinSim(a._2, b._2)
	          (a._1, (b._1, simScore))
	        }
	      }
	      .filter(_._2._2 > 0.6) // 过滤出相似度大于 0.6 的
	      .groupByKey()
	      .map {
	        case (mid, recs) => MovieRecs(mid, recs.toList.sortWith(_._2 > _._2).map(x => Recommendation(x._1, x._2)))
	      }
	      .toDF()

	    // 把结果写入对应的 MongoDB 表中
	    movieRecs.write
	      .option("uri", mongoConfig.uri)
	      .option("collection", CONTENT_MOVIE_RECS)
	      .mode("overwrite")
	      .format("com.mongodb.spark.sql")
	      .save()

	    spark.stop()
	  }

	  // 求两个向量的余弦相似度
	  def consinSim(movie1: DoubleMatrix, movie2: DoubleMatrix): Double = {
	    movie1.dot(movie2) / (movie1.norm2() * movie2.norm2()) // l1范数：向量元素绝对值之和；l2范数：即向量的模长（向量的长度）,向量元素的平方和再开方
	  }
	}

然后通过电影特征向量进而求出相似度矩阵，就可以为实时推荐提供基础，得到用户推荐列表了。可以看出，基于内容和基于隐语义模型，目的都是为了提取出物品的特征向量，从而可以计算出相似度矩阵。而我们的实时推荐系统算法正是基于相似度来定义的。

.. |image0| image:: /_static/machine_learn_intro/n74tu1egje.png
.. |image1| image:: /_static/machine_learn_intro/afhg7mqapl.png
.. |image2| image:: /_static/machine_learn_intro/gvi0v19d78.png
.. |image3| image:: /_static/machine_learn_intro/7d6rvpa9qq.png































































































































