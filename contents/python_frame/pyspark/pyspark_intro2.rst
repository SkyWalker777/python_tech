pyspark 实战篇
##################################################################################

基础入门
**********************************************************************************

文件拷贝
==================================================================================

.. code-block:: python

    # coding:utf8

    if __name__ == '__main__':
        parent_path = """C:/Users/javac/AppData/Local/JianyingPro/User Data/Projects/com.lveditor.draft/202111031059"""
        fw = open(parent_path + """/draft_content.json-w""", "w", encoding="UTF-8")
        st = str(open(parent_path + """/draft_content.json.bak""", "r", encoding="UTF-8").readlines())
        st = st.replace("哈都不", "Hadoop")
        st = st.replace("哈杜普", "Hadoop")
        st = st.replace("他", "它")
        st = st.replace("楼口", "Local")
        st = st.replace("logo", "Local")
        st = st.replace("loco", "Local")
        st = st.replace("林尼克", "Linux")
        st = st.replace("李纽克斯", "Linux")
        st = st.replace(" spa 和", "Spark")
        st = st.replace("18个", "Spark")
        st = st.replace("十八个", "Spark")
        st = st.replace(" spa ", "Spark")
        st = st.replace("spa 可", "Spark")
        st = st.replace("史帕克", "Spark")
        st = st.replace("界 vm", "JVM")
        st = st.replace("jmm", "JVM")
        st = st.replace("接 vm", "JVM")
        st = st.replace("踏实课", "Task")
        st = st.replace("踏实克", "Task")
        st = st.replace("接离开", "JDK")
        st = st.replace("渗透", "Cent")
        st = st.replace("寻机", "虚拟机")
        st = st.replace("vivo vr", "VMWare")
        st = st.replace("星", "*")
        st = st.replace("gram", "JVM")
        st = st.replace("进城", "进程")
        st = st.replace("晋城", "进程")
        st = st.replace("县城", "线程")
        st = st.replace("现成", "线程")
        st = st.replace("鸡群", "集群")
        st = st.replace("机群", "集群")
        st = st.replace("肌群", "集群")
        st = st.replace("接龙性", "兼容性")
        st = st.replace("威尔worker斯带森", "VMWare WorkStation")
        st = st.replace("沃克", "Worker")
        st = st.replace("蜗壳", "Worker")
        st = st.replace("窝可", "Worker")
        st = st.replace("窝克", "Worker")
        st = st.replace("无可", "Worker")
        st = st.replace("马斯特", "Master")
        st = st.replace("砖窝", "Driver")
        st = st.replace("主要我", "Driver")
        st = st.replace("砖瓦", "Driver")
        st = st.replace("专我", "Driver")
        st = st.replace("drawer", "Driver")
        st = st.replace("dread 我", "Driver")
        st = st.replace("追我", "Driver")
        st = st.replace("一个q特", "Executor")
        st = st.replace("一个 q 特", "Executor")
        st = st.replace("艾克的 q 特", "Executor")
        st = st.replace("ascot", "Executor")
        st = st.replace("equip", "Executor")
        st = st.replace("Lakota", "Executor")
        st = st.replace("lakota", "Executor")
        st = st.replace("一个客客", "Executor")
        st = st.replace("一个扣特", "Executor")
        st = st.replace("ipa", "API")
        st = st.replace("记硕", "计数")

        fw.write(st)
        fw.flush()
        fw.close()

word count demo
==================================================================================

.. code-block:: python

    # coding:utf8
    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("WordCountHelloWorld")
        # 通过SparkConf对象构建SparkContext对象
        sc = SparkContext(conf=conf)

        # 需求 : wordcount单词计数, 读取HDFS上的words.txt文件, 对其内部的单词统计出现 的数量
        # 读取文件
        file_rdd = sc.textFile("hdfs://node1:8020/input/words.txt")

        # 将单词进行切割, 得到一个存储全部单词的集合对象
        words_rdd = file_rdd.flatMap(lambda line: line.split(" "))

        # 将单词转换为元组对象, key是单词, value是数字1
        words_with_one_rdd = words_rdd.map(lambda x: (x, 1))

        # 将元组的value 按照key来分组, 对所有的value执行聚合操作(相加)
        result_rdd = words_with_one_rdd.reduceByKey(lambda a, b: a + b)

        # 通过collect方法收集RDD的数据打印输出结果
        print(result_rdd.collect())

RDD
**********************************************************************************

create_parallelize
==================================================================================

.. code-block:: python

    # coding:utf8

    # 导入Spark的相关包
    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        # 0. 初始化执行环境 构建SparkContext对象
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 演示通过并行化集合的方式去创建RDD, 本地集合 -> 分布式对象(RDD)
        rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9])
        # parallelize方法, 没有给定 分区数, 默认分区数是多少?  根据CPU核心来定
        print("默认分区数: ", rdd.getNumPartitions())

        rdd = sc.parallelize([1, 2, 3], 3)
        print("分区数: ", rdd.getNumPartitions())

        # collect方法, 是将RDD(分布式对象)中每个分区的数据, 都发送到Driver中, 形成一个Python List对象
        # collect: 分布式 转 -> 本地集合
        print("rdd的内容是: ", rdd.collect())

create_textFile
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        # 构建SparkContext对象
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 通过textFile API 读取数据

        # 读取本地文件数据
        file_rdd1 = sc.textFile("../data/input/words.txt")
        print("默认读取分区数: ", file_rdd1.getNumPartitions())
        print("file_rdd1 内容:", file_rdd1.collect())

        # 加最小分区数参数的测试
        file_rdd2 = sc.textFile("../data/input/words.txt", 3)
        # 最小分区数是参考值, Spark有自己的判断, 你给的太大Spark不会理会
        file_rdd3 = sc.textFile("../data/input/words.txt", 100)
        print("file_rdd2 分区数:", file_rdd2.getNumPartitions())
        print("file_rdd3 分区数:", file_rdd3.getNumPartitions())

        # 读取HDFS文件数据测试
        hdfs_rdd = sc.textFile("hdfs://node1:8020/input/words.txt")
        print("hdfs_rdd 内容:", hdfs_rdd.collect())

create_wholeTextFile
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 读取小文件文件夹
        rdd= sc.wholeTextFiles("../data/input/tiny_files")
        print(rdd.map(lambda x:x[1]).collect())

operators_map
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5, 6], 3)

        # 定义方法, 作为算子的传入函数体
        def add(data):
            return data * 10


        print(rdd.map(add).collect())

        # 更简单的方式 是定义lambda表达式来写匿名函数
        print(rdd.map(lambda data: data * 10).collect())
    """
    对于算子的接受函数来说, 两种方法都可以
    lambda表达式 适用于 一行代码就搞定的函数体, 如果是多行, 需要定义独立的方法.
    """

operators_flatMap
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize(["hadoop spark hadoop", "spark hadoop hadoop", "hadoop flink spark"])
        # 得到所有的单词, 组成RDD, flatMap的传入参数 和map一致, 就是给map逻辑用的, 解除嵌套无需逻辑(传参)
        rdd2 = rdd.flatMap(lambda line: line.split(" "))
        print(rdd2.collect())

operators_reduceByKey
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('a', 1), ('a', 1), ('b', 1), ('b', 1), ('a', 1)])

        # reduceByKey 对相同key 的数据执行聚合相加
        print(rdd.reduceByKey(lambda a, b: a + b).collect())

wordcount_example
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 1. 读取文件获取数据 构建RDD
        file_rdd = sc.textFile("../data/input/words.txt")

        # 2. 通过flatMap API 取出所有的单词
        word_rdd = file_rdd.flatMap(lambda x: x.split(" "))

        # 3. 将单词转换成元组, key是单词, value是1
        word_with_one_rdd = word_rdd.map(lambda word: (word, 1))

        # 4. 用reduceByKey 对单词进行分组并进行value的聚合
        result_rdd = word_with_one_rdd.reduceByKey(lambda a, b: a + b)

        # 5. 通过collect算子, 将rdd的数据收集到Driver中, 打印输出
        print(result_rdd.collect())

wordcount_example
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('a', 1), ('a', 1), ('b', 1), ('b', 2), ('b', 3)])

        # 通过groupBy对数据进行分组
        # groupBy传入的函数的 意思是: 通过这个函数, 确定按照谁来分组(返回谁即可)
        # 分组规则 和SQL是一致的, 也就是相同的在一个组(Hash分组)
        result = rdd.groupBy(lambda t: t[0])
        print(result.map(lambda t:(t[0], list(t[1]))).collect())

operators_filter
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5, 6])

        # 通过Filter算子, 过滤奇数
        result = rdd.filter(lambda x: x % 2 == 1)

        print(result.collect())

operators_distinct
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 1, 1, 2, 2, 2, 3, 3, 3])

        # distinct 进行RDD数据去重操作
        print(rdd.distinct().collect())

        rdd2 = sc.parallelize([('a', 1), ('a', 1), ('a', 3)])
        print(rdd2.distinct().collect())

operators_union
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd1 = sc.parallelize([1, 1, 3, 3])
        rdd2 = sc.parallelize(["a", "b", "a"])

        rdd3 = rdd1.union(rdd2)
        print(rdd3.collect())

    """
    1. 可以看到 union算子是不会去重的
    2. RDD的类型不同也是可以合并的.
    """

operators_join
==================================================================================

.. code-block:: python













































































































