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

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd1 = sc.parallelize([ (1001, "zhangsan"), (1002, "lisi"), (1003, "wangwu"), (1004, "zhaoliu") ])
        rdd2 = sc.parallelize([ (1001, "销售部"), (1002, "科技部")])

        # 通过join算子来进行rdd之间的关联
        # 对于join算子来说 关联条件 按照二元元组的key来进行关联
        print(rdd1.join(rdd2).collect())

        # 左外连接, 右外连接 可以更换一下rdd的顺序 或者调用rightOuterJoin即可
        print(rdd1.leftOuterJoin(rdd2).collect())

operators_intersection
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd1 = sc.parallelize([('a', 1), ('a', 3)])
        rdd2 = sc.parallelize([('a', 1), ('b', 3)])

        # 通过intersection算子求RDD之间的交集, 将交集取出 返回新RDD
        rdd3 = rdd1.intersection(rdd2)

        print(rdd3.collect())

operators_glom
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9], 2)

        print(rdd.glom().flatMap(lambda x: x).collect())

operators_gorupByKey
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('a', 1), ('a', 1), ('b', 1), ('b', 1), ('b', 1)])

        rdd2 = rdd.groupByKey()

        print(rdd2.map(lambda x: (x[0], list(x[1]))).collect())

operators_sortBy
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('c', 3), ('f', 1), ('b', 11), ('c', 3), ('a', 1), ('c', 5), ('e', 1), ('n', 9), ('a', 1)], 3)

        # 使用sortBy对rdd执行排序

        # 按照value 数字进行排序
        # 参数1函数, 表示的是 ,  告知Spark 按照数据的哪个列进行排序
        # 参数2: True表示升序 False表示降序
        # 参数3: 排序的分区数
        """注意: 如果要全局有序, 排序分区数请设置为1"""
        print(rdd.sortBy(lambda x: x[1], ascending=True, numPartitions=1).collect())

        # 按照key来进行排序
        print(rdd.sortBy(lambda x: x[0], ascending=False, numPartitions=1).collect())

operators_sortByKey
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('a', 1), ('E', 1), ('C', 1), ('D', 1), ('b', 1), ('g', 1), ('f', 1),
                              ('y', 1), ('u', 1), ('i', 1), ('o', 1), ('p', 1),
                              ('m', 1), ('n', 1), ('j', 1), ('k', 1), ('l', 1)], 3)

        print(rdd.sortByKey(ascending=True, numPartitions=1, keyfunc=lambda key: str(key).lower()).collect())

operators_demo
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext
    import json

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 读取数据文件
        file_rdd = sc.textFile("../data/input/order.text")

        # 进行rdd数据的split 按照|符号进行, 得到一个个的json数据
        jsons_rdd = file_rdd.flatMap(lambda line: line.split("|"))

        # 通过Python 内置的json库, 完成json字符串到字典对象的转换
        dict_rdd = jsons_rdd.map(lambda json_str: json.loads(json_str))

        # 过滤数据, 只保留北京的数据
        beijing_rdd = dict_rdd.filter(lambda d: d['areaName'] == "北京")

        # 组合北京 和 商品类型形成新的字符串
        category_rdd = beijing_rdd.map(lambda x: x['areaName'] + "_" + x['category'])

        # 对结果集进行去重操作
        result_rdd = category_rdd.distinct()

        # 输出
        print(result_rdd.collect())

operators_demo_run_yarn
==================================================================================

.. code-block:: python

    def city_with_category(data):
        return data['areaName'] + "_" + data['category']

    # coding:utf8

    from pyspark import SparkConf, SparkContext
    from defs_19 import city_with_category
    import json
    import os
    os.environ['HADOOP_CONF_DIR'] = "/export/server/hadoop/etc/hadoop"

    if __name__ == '__main__':
        # 提交 到yarn集群, master 设置为yarn
        conf = SparkConf().setAppName("test-yarn-1").setMaster("yarn")
        # 如果提交到集群运行, 除了主代码以外, 还依赖了其它的代码文件
        # 需要设置一个参数, 来告知spark ,还有依赖文件要同步上传到集群中
        # 参数叫做: spark.submit.pyFiles
        # 参数的值可以是 单个.py文件,   也可以是.zip压缩包(有多个依赖文件的时候可以用zip压缩后上传)
        conf.set("spark.submit.pyFiles", "defs_19.py")
        sc = SparkContext(conf=conf)

        # 在集群中运行, 我们需要用HDFS路径了. 不能用本地路径
        file_rdd = sc.textFile("hdfs://node1:8020/input/order.text")

        # 进行rdd数据的split 按照|符号进行, 得到一个个的json数据
        jsons_rdd = file_rdd.flatMap(lambda line: line.split("|"))

        # 通过Python 内置的json库, 完成json字符串到字典对象的转换
        dict_rdd = jsons_rdd.map(lambda json_str: json.loads(json_str))

        # 过滤数据, 只保留北京的数据
        beijing_rdd = dict_rdd.filter(lambda d: d['areaName'] == "北京")

        # 组合北京 和 商品类型形成新的字符串
        category_rdd = beijing_rdd.map(city_with_category)

        # 对结果集进行去重操作
        result_rdd = category_rdd.distinct()

        # 输出
        print(result_rdd.collect())

operators_countByKey
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.textFile("../data/input/words.txt")
        rdd2 = rdd.flatMap(lambda x: x.split(" ")).map(lambda x: (x, 1))

        # 通过countByKey来对key进行计数, 这是一个Action算子
        result = rdd2.countByKey()

        print(result)
        print(type(result))

operators_countByValue
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('c', 3), ('f', 1), ('b', 11), ('c', 3), ('a', 1), ('c', 5), ('e', 1), ('n', 9), ('a', 1)], 3)

        # 使用sortBy对rdd执行排序

        # 按照value 数字进行排序
        # 参数1函数, 表示的是 ,  告知Spark 按照数据的哪个列进行排序
        # 参数2: True表示升序 False表示降序
        # 参数3: 排序的分区数
        """注意: 如果要全局有序, 排序分区数请设置为1"""
        print(rdd.sortBy(lambda x: x[1], ascending=True, numPartitions=1).collect())

        # 按照key来进行排序
        print(rdd.sortBy(lambda x: x[0], ascending=False, numPartitions=1).collect())

operators_reduce
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5])

        print(rdd.reduce(lambda a, b: a + b))

operators_fold
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9], 3)

        print(rdd.fold(10, lambda a, b: a + b))

operators_takeSample
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 5, 3, 1, 3, 2, 6, 7, 8, 6], 1)

        print(rdd.takeSample(False, 5, 1))

operators_takeOrdered
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 2, 4, 7, 9, 6], 1)

        print(rdd.takeOrdered(3))

        print(rdd.takeOrdered(3, lambda x: -x))

operators_foreach
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 2, 4, 7, 9, 6], 1)

        result = rdd.foreach(lambda x: print(x * 10))

operators_saveAsTextFile
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 2, 4, 7, 9, 6], 3)

        rdd.saveAsTextFile("hdfs://node1:8020/output/out1")

operators_mapPartitions
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 2, 4, 7, 9, 6], 3)

        def process(iter):
            result = list()
            for it in iter:
                result.append(it * 10)

            return result


        print(rdd.mapPartitions(process).collect())

operators_foreachPartition
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 3, 2, 4, 7, 9, 6], 3)

        def process(iter):
            result = list()
            for it in iter:
                result.append(it * 10)

            print(result)

        rdd.foreachPartition(process)

operators_partitionBy
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([('hadoop', 1), ('spark', 1), ('hello', 1), ('flink', 1), ('hadoop', 1), ('spark', 1)])

        # 使用partitionBy 自定义 分区
        def process(k):
            if 'hadoop' == k or 'hello' == k: return 0
            if 'spark' == k: return 1
            return 2

        print(rdd.partitionBy(3, process).glom().collect())

operators_repartition_and_coalesce
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark import SparkConf, SparkContext

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5], 3)

        # repartition 修改分区
        print(rdd.repartition(1).getNumPartitions())

        print(rdd.repartition(5).getNumPartitions())

        # coalesce 修改分区
        print(rdd.coalesce(1).getNumPartitions())

        print(rdd.coalesce(5, shuffle=True).getNumPartitions())

cache
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd1 = sc.textFile("../data/input/words.txt")
        rdd2 = rdd1.flatMap(lambda x: x.split(" "))
        rdd3 = rdd2.map(lambda x: (x, 1))

        rdd3.cache()
        rdd3.persist(StorageLevel.MEMORY_AND_DISK_2)

        rdd4 = rdd3.reduceByKey(lambda a, b: a + b)
        print(rdd4.collect())

        rdd5 = rdd3.groupByKey()
        rdd6 = rdd5.mapValues(lambda x: sum(x))
        print(rdd6.collect())

        rdd3.unpersist()
        time.sleep(100000)

checkpoint
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 1. 告知spark, 开启CheckPoint功能
        sc.setCheckpointDir("hdfs://node1:8020/output/ckp")
        rdd1 = sc.textFile("../data/input/words.txt")
        rdd2 = rdd1.flatMap(lambda x: x.split(" "))
        rdd3 = rdd2.map(lambda x: (x, 1))

        # 调用checkpoint API 保存数据即可
        rdd3.checkpoint()

        rdd4 = rdd3.reduceByKey(lambda a, b: a + b)
        print(rdd4.collect())

        rdd5 = rdd3.groupByKey()
        rdd6 = rdd5.mapValues(lambda x: sum(x))
        print(rdd6.collect())

        rdd3.unpersist()
        time.sleep(100000)

broadcast
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        stu_info_list = [(1, '张大仙', 11),
                         (2, '王晓晓', 13),
                         (3, '张甜甜', 11),
                         (4, '王大力', 11)]
        # 1. 将本地Python List对象标记为广播变量
        broadcast = sc.broadcast(stu_info_list)

        score_info_rdd = sc.parallelize([
            (1, '语文', 99),
            (2, '数学', 99),
            (3, '英语', 99),
            (4, '编程', 99),
            (1, '语文', 99),
            (2, '编程', 99),
            (3, '语文', 99),
            (4, '英语', 99),
            (1, '语文', 99),
            (3, '英语', 99),
            (2, '编程', 99)
        ])

        def map_func(data):
            id = data[0]
            name = ""
            # 匹配本地list和分布式rdd中的学生ID  匹配成功后 即可获得当前学生的姓名
            # 2. 在使用到本地集合对象的地方, 从广播变量中取出来用即可
            for stu_info in broadcast.value:
                stu_id = stu_info[0]
                if id == stu_id:
                    name = stu_info[1]

            return (name, data[1], data[2])


        print(score_info_rdd.map(map_func).collect())

    """
    场景: 本地集合对象 和 分布式集合对象(RDD) 进行关联的时候
    需要将本地集合对象 封装为广播变量
    可以节省:
    1. 网络IO的次数
    2. Executor的内存占用
    """

accumulator
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel
    import re

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 1. 读取数据文件
        file_rdd = sc.textFile("../data/input/accumulator_broadcast_data.txt")

        # 特殊字符的list定义
        abnormal_char = [",", ".", "!", "#", "$", "%"]

        # 2. 将特殊字符list 包装成广播变量
        broadcast = sc.broadcast(abnormal_char)

        # 3. 对特殊字符出现次数做累加, 累加使用累加器最好
        acmlt = sc.accumulator(0)

        # 4. 数据处理, 先处理数据的空行, 在Python中有内容就是True None就是False
        lines_rdd = file_rdd.filter(lambda line: line.strip())

        # 5. 去除前后的空格
        data_rdd = lines_rdd.map(lambda line: line.strip())

        # 6. 对数据进行切分, 按照正则表达式切分, 因为空格分隔符某些单词之间是两个或多个空格
        # 正则表达式 \s+ 表示 不确定多少个空格, 最少一个空格
        words_rdd = data_rdd.flatMap(lambda line: re.split("\s+", line))

        # 7. 当前words_rdd中有正常单词 也有特殊符号.
        # 现在需要过滤数据, 保留正常单词用于做单词计数, 在过滤 的过程中 对特殊符号做计数
        def filter_func(data):
            """过滤数据, 保留正常单词用于做单词计数, 在过滤 的过程中 对特殊符号做计数"""
            global acmlt
            # 取出广播变量中存储的特殊符号list
            abnormal_chars = broadcast.value
            if data in abnormal_chars:
                # 表示这个是 特殊字符
                acmlt += 1
                return False
            else:
                return True

        normal_words_rdd = words_rdd.filter(filter_func)
        # 8. 正常单词的单词计数逻辑
        result_rdd = normal_words_rdd.map(lambda x: (x, 1)).\
            reduceByKey(lambda a, b: a + b)

        print("正常单词计数结果: ", result_rdd.collect())
        print("特殊字符数量: ", acmlt)

accumulator
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 2)

        # Spark提供的累加器变量, 参数是初始值
        acmlt = sc.accumulator(0)

        def map_func(data):
            global acmlt
            acmlt += 1
            # print(acmlt)

        rdd2 = rdd.map(map_func)
        rdd2.cache()
        rdd2.collect()

        rdd3 = rdd2.map(lambda x:x)
        rdd3.collect()
        print(acmlt)

broadcast_and_accumulator_demo
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel
    import re

    if __name__ == '__main__':
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 1. 读取数据文件
        file_rdd = sc.textFile("../data/input/accumulator_broadcast_data.txt")

        # 特殊字符的list定义
        abnormal_char = [",", ".", "!", "#", "$", "%"]

        # 2. 将特殊字符list 包装成广播变量
        broadcast = sc.broadcast(abnormal_char)

        # 3. 对特殊字符出现次数做累加, 累加使用累加器最好
        acmlt = sc.accumulator(0)

        # 4. 数据处理, 先处理数据的空行, 在Python中有内容就是True None就是False
        lines_rdd = file_rdd.filter(lambda line: line.strip())

        # 5. 去除前后的空格
        data_rdd = lines_rdd.map(lambda line: line.strip())

        # 6. 对数据进行切分, 按照正则表达式切分, 因为空格分隔符某些单词之间是两个或多个空格
        # 正则表达式 \s+ 表示 不确定多少个空格, 最少一个空格
        words_rdd = data_rdd.flatMap(lambda line: re.split("\s+", line))

        # 7. 当前words_rdd中有正常单词 也有特殊符号.
        # 现在需要过滤数据, 保留正常单词用于做单词计数, 在过滤 的过程中 对特殊符号做计数
        def filter_func(data):
            """过滤数据, 保留正常单词用于做单词计数, 在过滤 的过程中 对特殊符号做计数"""
            global acmlt
            # 取出广播变量中存储的特殊符号list
            abnormal_chars = broadcast.value
            if data in abnormal_chars:
                # 表示这个是 特殊字符
                acmlt += 1
                return False
            else:
                return True

        normal_words_rdd = words_rdd.filter(filter_func)
        # 8. 正常单词的单词计数逻辑
        result_rdd = normal_words_rdd.map(lambda x: (x, 1)).\
            reduceByKey(lambda a, b: a + b)

        print("正常单词计数结果: ", result_rdd.collect())
        print("特殊字符数量: ", acmlt)

jieba 分词
==================================================================================

.. code-block:: python

    # coding:utf8
    import jieba

    def context_jieba(data):
        """通过jieba分词工具 进行分词操作"""
        seg = jieba.cut_for_search(data)
        l = list()
        for word in seg:
            l.append(word)
        return l


    def filter_words(data):
        """过滤不要的 谷 \ 帮 \ 客"""
        return data not in ['谷', '帮', '客']


    def append_words(data):
        """修订某些关键词的内容"""
        if data == '传智播': data = '传智播客'
        if data == '院校': data = '院校帮'
        if data == '博学': data = '博学谷'
        return (data, 1)


    def extract_user_and_word(data):
        """传入数据是 元组 (1, 我喜欢传智播客)"""
        user_id = data[0]
        content = data[1]
        # 对content进行分词
        words = context_jieba(content)

        return_list = list()
        for word in words:
            # 不要忘记过滤 \谷 \ 帮 \ 客
            if filter_words(word):
                return_list.append((user_id + "_" + append_words(word)[0], 1))

        return return_list

    # coding:utf8

    import jieba

    if __name__ == '__main__':
        content = "小明硕士毕业于中国科学院计算所,后在清华大学深造"

        result = jieba.cut(content, True)
        print(list(result))
        print(type(result))

        #
        result2 = jieba.cut(content, False)
        print(list(result2))

        # 搜索引擎模式, 等同于允许二次组合的场景
        result3 = jieba.cut_for_search(content)
        print(",".join(result3))

    # coding:utf8

    # 导入Spark的相关包
    import time

    from pyspark import SparkConf, SparkContext
    from pyspark.storagelevel import StorageLevel
    from defs import context_jieba, filter_words, append_words, extract_user_and_word
    from operator import add

    if __name__ == '__main__':
        # 0. 初始化执行环境 构建SparkContext对象
        conf = SparkConf().setAppName("test").setMaster("local[*]")
        sc = SparkContext(conf=conf)

        # 1. 读取数据文件
        file_rdd = sc.textFile("hdfs://node1:8020/input/SogouQ.txt")

        # 2. 对数据进行切分 \t
        split_rdd = file_rdd.map(lambda x: x.split("\t"))

        # 3. 因为要做多个需求, split_rdd 作为基础的rdd 会被多次使用.
        split_rdd.persist(StorageLevel.DISK_ONLY)

        # TODO: 需求1: 用户搜索的关键`词`分析
        # 主要分析热点词
        # 将所有的搜索内容取出
        # print(split_rdd.takeSample(True, 3))
        context_rdd = split_rdd.map(lambda x: x[2])

        # 对搜索的内容进行分词分析
        words_rdd = context_rdd.flatMap(context_jieba)

        # print(words_rdd.collect())
        # 院校 帮 -> 院校帮
        # 博学 谷 -> 博学谷
        # 传智播 客 -> 传智播客
        filtered_rdd = words_rdd.filter(filter_words)
        # 将关键词转换: 穿直播 -> 传智播客
        final_words_rdd = filtered_rdd.map(append_words)
        # 对单词进行 分组 聚合 排序 求出前5名
        result1 = final_words_rdd.reduceByKey(lambda a, b: a + b).\
            sortBy(lambda x: x[1], ascending=False, numPartitions=1).\
            take(5)

        print("需求1结果: ", result1)

        # TODO: 需求2: 用户和关键词组合分析
        # 1, 我喜欢传智播客
        # 1+我  1+喜欢 1+传智播客
        user_content_rdd = split_rdd.map(lambda x: (x[1], x[2]))
        # 对用户的搜索内容进行分词, 分词后和用户ID再次组合
        user_word_with_one_rdd = user_content_rdd.flatMap(extract_user_and_word)
        # 对内容进行 分组 聚合 排序 求前5
        result2 = user_word_with_one_rdd.reduceByKey(lambda a, b: a + b).\
            sortBy(lambda x: x[1], ascending=False, numPartitions=1).\
            take(5)

        print("需求2结果: ", result2)

        # TODO: 需求3: 热门搜索时间段分析
        # 取出来所有的时间
        time_rdd = split_rdd.map(lambda x: x[0])
        # 对时间进行处理, 只保留小时精度即可
        hour_with_one_rdd = time_rdd.map(lambda x: (x.split(":")[0], 1))
        # 分组 聚合 排序
        result3 = hour_with_one_rdd.reduceByKey(add).\
            sortBy(lambda x: x[1], ascending=False, numPartitions=1).\
            collect()

        print("需求3结果: ", result3)

        time.sleep(100000)

SQL
**********************************************************************************

spark_session_create
==================================================================================

.. code-block:: python

    # coding:utf8

    # SparkSession对象的导包, 对象是来自于 pyspark.sql包中
    from pyspark.sql import SparkSession

    if __name__ == '__main__':
        # 构建SparkSession执行环境入口对象
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()

        # 通过SparkSession对象 获取 SparkContext对象
        sc = spark.sparkContext

        # SparkSQL的HelloWorld
        df = spark.read.csv("../data/input/stu_score.txt", sep=',', header=False)
        df2 = df.toDF("id", "name", "score")
        df2.printSchema()
        df2.show()

        df2.createTempView("score")

        # SQL 风格
        spark.sql("""
            SELECT * FROM score WHERE name='语文' LIMIT 5
        """).show()

        # DSL 风格
        df2.where("name='语文'").limit(5).show()

dataframe_create_1
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 基于RDD转换成DataFrame
        rdd = sc.textFile("../data/input/sql/people.txt").\
            map(lambda x: x.split(",")).\
            map(lambda x: (x[0], int(x[1])))

        # 构建DataFrame对象
        # 参数1 被转换的RDD
        # 参数2 指定列名, 通过list的形式指定, 按照顺序依次提供字符串名称即可
        df = spark.createDataFrame(rdd, schema=['name', 'age'])

        # 打印DataFrame的表结构
        df.printSchema()

        # 打印df中的数据
        # 参数1 表示 展示出多少条数据, 默认不传的话是20
        # 参数2 表示是否对列进行截断, 如果列的数据长度超过20个字符串长度, 后续的内容不显示以...代替
        # 如果给False 表示不阶段全部显示, 默认是True
        df.show(20, False)

        # 将DF对象转换成临时视图表, 可供sql语句查询
        df.createOrReplaceTempView("people")
        spark.sql("SELECT * FROM people WHERE age < 30").show()

dataframe_create_2
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 基于RDD转换成DataFrame
        rdd = sc.textFile("../data/input/sql/people.txt").\
            map(lambda x: x.split(",")).\
            map(lambda x: (x[0], int(x[1])))

        # 构建表结构的描述对象: StructType对象
        schema = StructType().add("name", StringType(), nullable=True).\
            add("age", IntegerType(), nullable=False)

        # 基于StructType对象去构建RDD到DF的转换
        df = spark.createDataFrame(rdd, schema=schema)

        df.printSchema()
        df.show()

dataframe_create_3
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 基于RDD转换成DataFrame
        rdd = sc.textFile("../data/input/sql/people.txt").\
            map(lambda x: x.split(",")).\
            map(lambda x: (x[0], int(x[1])))


        # toDF的方式构建DataFrame
        df1 = rdd.toDF(["name", "age"])
        df1.printSchema()
        df1.show()

        # toDF的方式2 通过StructType来构建
        schema = StructType().add("name", StringType(), nullable=True).\
            add("age", IntegerType(), nullable=False)

        df2 = rdd.toDF(schema=schema)
        df2.printSchema()
        df2.show()

dataframe_create_4
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 基于Pandas的DataFrame构建SparkSQL的DataFrame对象
        pdf = pd.DataFrame(
            {
                "id": [1, 2, 3],
                "name": ["张大仙", "王晓晓", "吕不为"],
                "age": [11, 21, 11]
            }
        )

        df = spark.createDataFrame(pdf)

        df.printSchema()
        df.show()

dataframe_create_5_text
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 构建StructType, text数据源, 读取数据的特点是, 将一整行只作为`一个列`读取, 默认列名是value 类型是String
        schema = StructType().add("data", StringType(), nullable=True)
        df = spark.read.format("text").\
            schema(schema=schema).\
            load("../data/input/sql/people.txt")

        df.printSchema()
        df.show()

dataframe_create_6_json
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # JSON类型自带有Schema信息
        df = spark.read.format("json").load("../data/input/sql/people.json")
        df.printSchema()
        df.show()

dataframe_create_7_csv
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 读取CSV文件
        df = spark.read.format("csv").\
            option("sep", ";").\
            option("header", True).\
            option("encoding", "utf-8").\
            schema("name STRING, age INT, job STRING").\
            load("../data/input/sql/people.csv")

        df.printSchema()
        df.show()

dataframe_create_8_parquet
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # 读取parquet类型的文件
        df = spark.read.format("parquet").load("../data/input/sql/users.parquet")

        df.printSchema()
        df.show()

dataframe_process_dsl_helloworld
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        df = spark.read.format("csv").\
            schema("id INT, subject STRING, score INT").\
            load("../data/input/sql/stu_score.txt")

        # Column对象的获取
        id_column = df['id']
        subject_column = df['subject']

        # DLS风格演示
        df.select(["id", "subject"]).show()
        df.select("id", "subject").show()
        df.select(id_column, subject_column).show()

        # filter API
        df.filter("score < 99").show()
        df.filter(df['score'] < 99).show()

        # where API
        df.where("score < 99").show()
        df.where(df['score'] < 99).show()

        # group By API
        df.groupBy("subject").count().show()
        df.groupBy(df['subject']).count().show()


        # df.groupBy API的返回值 GroupedData
        # GroupedData对象 不是DataFrame
        # 它是一个 有分组关系的数据结构, 有一些API供我们对分组做聚合
        # SQL: group by 后接上聚合: sum avg count min man
        # GroupedData 类似于SQL分组后的数据结构, 同样有上述5种聚合方法
        # GroupedData 调用聚合方法后, 返回值依旧是DataFrame
        # GroupedData 只是一个中转的对象, 最终还是要获得DataFrame的结果
        r = df.groupBy("subject")

dataframe_process_sql_helloworld
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        df = spark.read.format("csv").\
            schema("id INT, subject STRING, score INT").\
            load("../data/input/sql/stu_score.txt")

        # 注册成临时表
        df.createTempView("score") # 注册临时视图(表)
        df.createOrReplaceTempView("score_2") # 注册 或者 替换  临时视图
        df.createGlobalTempView("score_3") # 注册全局临时视图 全局临时视图在使用的时候 需要在前面带上global_temp. 前缀

        # 可以通过SparkSession对象的sql api来完成sql语句的执行
        spark.sql("SELECT subject, COUNT(*) AS cnt FROM score GROUP BY subject").show()
        spark.sql("SELECT subject, COUNT(*) AS cnt FROM score_2 GROUP BY subject").show()
        spark.sql("SELECT subject, COUNT(*) AS cnt FROM global_temp.score_3 GROUP BY subject").show()

wordcount_demo
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            getOrCreate()
        sc = spark.sparkContext

        # TODO 1: SQL 风格进行处理
        rdd = sc.textFile("../data/input/words.txt").\
            flatMap(lambda x: x.split(" ")).\
            map(lambda x: [x])

        df = rdd.toDF(["word"])

        # 注册DF为表格
        df.createTempView("words")

        spark.sql("SELECT word, COUNT(*) AS cnt FROM words GROUP BY word ORDER BY cnt DESC").show()


        # TODO 2: DSL 风格处理
        df = spark.read.format("text").load("../data/input/words.txt")

        # withColumn方法
        # 方法功能: 对已存在的列进行操作, 返回一个新的列, 如果名字和老列相同, 那么替换, 否则作为新列存在
        df2 = df.withColumn("value", F.explode(F.split(df['value'], " ")))
        df2.groupBy("value").\
            count().\
            withColumnRenamed("value", "word").\
            withColumnRenamed("count", "cnt").\
            orderBy("cnt", ascending=False).\
            show()

movie_demo
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        """
        spark.sql.shuffle.partitions 参数指的是, 在sql计算中, shuffle算子阶段默认的分区数是200个.
        对于集群模式来说, 200个默认也算比较合适
        如果在local下运行, 200个很多, 在调度上会带来额外的损耗
        所以在local下建议修改比较低 比如2\4\10均可
        这个参数和Spark RDD中设置并行度的参数 是相互独立的.
        """

        # 1. 读取数据集
        schema = StructType().add("user_id", StringType(), nullable=True).\
            add("movie_id", IntegerType(), nullable=True).\
            add("rank", IntegerType(), nullable=True).\
            add("ts", StringType(), nullable=True)
        df = spark.read.format("csv").\
            option("sep", "\t").\
            option("header", False).\
            option("encoding", "utf-8").\
            schema(schema=schema).\
            load("../data/input/sql/u.data")

        # TODO 1: 用户平均分
        df.groupBy("user_id").\
            avg("rank").\
            withColumnRenamed("avg(rank)", "avg_rank").\
            withColumn("avg_rank", F.round("avg_rank", 2)).\
            orderBy("avg_rank", ascending=False).\
            show()

        # TODO 2: 电影的平均分查询
        df.createTempView("movie")
        spark.sql("""
            SELECT movie_id, ROUND(AVG(rank), 2) AS avg_rank FROM movie GROUP BY movie_id ORDER BY avg_rank DESC
        """).show()

        # TODO 3: 查询大于平均分的电影的数量 # Row
        print("大于平均分电影的数量: ", df.where(df['rank'] > df.select(F.avg(df['rank'])).first()['avg(rank)']).count())

        # TODO 4: 查询高分电影中(>3)打分次数最多的用户, 此人打分的平均分
        # 先找出这个人
        user_id = df.where("rank > 3").\
            groupBy("user_id").\
            count().\
            withColumnRenamed("count", "cnt").\
            orderBy("cnt", ascending=False).\
            limit(1).\
            first()['user_id']
        # 计算这个人的打分平均分
        df.filter(df['user_id'] == user_id).\
            select(F.round(F.avg("rank"), 2)).show()

        # TODO 5: 查询每个用户的平局打分, 最低打分, 最高打分
        df.groupBy("user_id").\
            agg(
                F.round(F.avg("rank"), 2).alias("avg_rank"),
                F.min("rank").alias("min_rank"),
                F.max("rank").alias("max_rank")
            ).show()

        # TODO 6: 查询评分超过100次的电影, 的平均分 排名 TOP10
        df.groupBy("movie_id").\
            agg(
                F.count("movie_id").alias("cnt"),
                F.round(F.avg("rank"), 2).alias("avg_rank")
            ).where("cnt > 100").\
            orderBy("avg_rank", ascending=False).\
            limit(10).\
            show()

        time.sleep(10000)

    """
    1. agg: 它是GroupedData对象的API, 作用是 在里面可以写多个聚合
    2. alias: 它是Column对象的API, 可以针对一个列 进行改名
    3. withColumnRenamed: 它是DataFrame的API, 可以对DF中的列进行改名, 一次改一个列, 改多个列 可以链式调用
    4. orderBy: DataFrame的API, 进行排序, 参数1是被排序的列, 参数2是 升序(True) 或 降序 False
    5. first: DataFrame的API, 取出DF的第一行数据, 返回值结果是Row对象.
    # Row对象 就是一个数组, 你可以通过row['列名'] 来取出当前行中, 某一列的具体数值. 返回值不再是DF 或者GroupedData 或者Column而是具体的值(字符串, 数字等)
    """

data_clear_api
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        """读取数据"""
        df = spark.read.format("csv").\
            option("sep", ";").\
            option("header", True).\
            load("../data/input/sql/people.csv")

        # # 数据清洗: 数据去重
        # # dropDuplicates 是DataFrame的API, 可以完成数据去重
        # # 无参数使用, 对全部的列 联合起来进行比较, 去除重复值, 只保留一条
        # df.dropDuplicates().show()
        #
        # df.dropDuplicates(['age', 'job']).show()
        #
        #
        # # 数据清洗: 缺失值处理
        # # dropna api是可以对缺失值的数据进行删除
        # # 无参数使用, 只要列中有null 就删除这一行数据
        # df.dropna().show()
        # # thresh = 3表示, 最少满足3个有效列,  不满足 就删除当前行数据
        # df.dropna(thresh=3).show()
        #
        # df.dropna(thresh=2, subset=['name', 'age']).show()

        # 缺失值处理也可以完成对缺失值进行填充
        # DataFrame的 fillna 对缺失的列进行填充
        df.fillna("loss").show()

        # 指定列进行填充
        df.fillna("N/A", subset=['job']).show()

        # 设定一个字典, 对所有的列 提供填充规则
        df.fillna({"name": "未知姓名", "age": 1, "job": "worker"}).show()

dataframe_write
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        # 1. 读取数据集
        schema = StructType().add("user_id", StringType(), nullable=True). \
            add("movie_id", IntegerType(), nullable=True). \
            add("rank", IntegerType(), nullable=True). \
            add("ts", StringType(), nullable=True)
        df = spark.read.format("csv"). \
            option("sep", "\t"). \
            option("header", False). \
            option("encoding", "utf-8"). \
            schema(schema=schema). \
            load("../data/input/sql/u.data")

        # Write text 写出, 只能写出一个列的数据, 需要将df转换为单列df
        df.select(F.concat_ws("---", "user_id", "movie_id", "rank", "ts")).\
            write.\
            mode("overwrite").\
            format("text").\
            save("../data/output/sql/text")

        # Write csv
        df.write.mode("overwrite").\
            format("csv").\
            option("sep", ";").\
            option("header", True).\
            save("../data/output/sql/csv")

        # Write json
        df.write.mode("overwrite").\
            format("json").\
            save("../data/output/sql/json")

        # Write parquet
        df.write.mode("overwrite").\
            format("parquet").\
            save("../data/output/sql/parquet")

dataframe_jdbc
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        # 1. 读取数据集
        schema = StructType().add("user_id", StringType(), nullable=True). \
            add("movie_id", IntegerType(), nullable=True). \
            add("rank", IntegerType(), nullable=True). \
            add("ts", StringType(), nullable=True)
        df = spark.read.format("csv"). \
            option("sep", "\t"). \
            option("header", False). \
            option("encoding", "utf-8"). \
            schema(schema=schema). \
            load("../data/input/sql/u.data")

        # # 1. 写出df到mysql数据库中
        # df.write.mode("overwrite").\
        #     format("jdbc").\
        #     option("url", "jdbc:mysql://node1:3306/bigdata?useSSL=false&useUnicode=true").\
        #     option("dbtable", "movie_data").\
        #     option("user", "root").\
        #     option("password", "2212072ok1").\
        #     save()

        df2 = spark.read.format("jdbc"). \
            option("url", "jdbc:mysql://node1:3306/bigdata?useSSL=false&useUnicode=true"). \
            option("dbtable", "movie_data"). \
            option("user", "root"). \
            option("password", "2212072ok1"). \
            load()

        df2.printSchema()
        df2.show()
    """
    JDBC写出, 会自动创建表的.
    因为DataFrame中有表结构信息, StructType记录的 各个字段的 名称 类型  和是否运行为空
    """

udf_define
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        # 构建一个RDD
        rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7]).map(lambda x:[x])
        df = rdd.toDF(["num"])

        # TODO 1: 方式1 sparksession.udf.register(), DSL和SQL风格均可以使用
        # UDF的处理函数
        def num_ride_10(num):
            return num * 10
        # 参数1: 注册的UDF的名称, 这个udf名称, 仅可以用于 SQL风格
        # 参数2: UDF的处理逻辑, 是一个单独的方法
        # 参数3: 声明UDF的返回值类型, 注意: UDF注册时候, 必须声明返回值类型, 并且UDF的真实返回值一定要和声明的返回值一致
        # 返回值对象: 这是一个UDF对象, 仅可以用于 DSL 语法
        # 当前这种方式定义的UDF, 可以通过参数1的名称用于SQL风格, 通过返回值对象用户DSL风格
        udf2 = spark.udf.register("udf1", num_ride_10, IntegerType())

        # SQL风格中使用
        # selectExpr 以SELECT的表达式执行, 表达式 SQL风格的表达式(字符串)
        # select方法, 接受普通的字符串字段名, 或者返回值是Column对象的计算
        df.selectExpr("udf1(num)").show()

        # DSL 风格中使用
        # 返回值UDF对象 如果作为方法使用, 传入的参数 一定是Column对象
        df.select(udf2(df['num'])).show()

        # TODO 2: 方式2注册, 仅能用于DSL风格
        udf3 = F.udf(num_ride_10, IntegerType())
        df.select(udf3(df['num'])).show()

        df.selectExpr("udf3(num)").show()

udf_define_return_array
==================================================================================

.. code-block:: python

    # coding:utf8
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType, ArrayType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        # 构建一个RDD
        rdd = sc.parallelize([["hadoop spark flink"], ["hadoop flink java"]])
        df = rdd.toDF(["line"])

        # 注册UDF, UDF的执行函数定义
        def split_line(data):
            return data.split(" ")  # 返回值是一个Array对象
        # TODO1 方式1 构建UDF
        udf2 = spark.udf.register("udf1", split_line, ArrayType(StringType()))

        # DLS风格
        df.select(udf2(df['line'])).show()
        # SQL风格
        df.createTempView("lines")
        spark.sql("SELECT udf1(line) FROM lines").show(truncate=False)

        # TODO 2 方式2的形式构建UDF
        udf3 = F.udf(split_line, ArrayType(StringType()))
        df.select(udf3(df['line'])).show(truncate=False)

udaf_by_rdd
==================================================================================

.. code-block:: python

    # coding:utf8
    import string
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType, ArrayType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        rdd = sc.parallelize([1, 2, 3, 4, 5], 3)
        df = rdd.map(lambda x: [x]).toDF(['num'])

        # 折中的方式 就是使用RDD的mapPartitions 算子来完成聚合操作
        # 如果用mapPartitions API 完成UDAF聚合, 一定要单分区
        single_partition_rdd = df.rdd.repartition(1)

        def process(iter):
            sum = 0
            for row in iter:
                sum += row['num']

            return [sum]    # 一定要嵌套list, 因为mapPartitions方法要求的返回值是list对象

        print(single_partition_rdd.mapPartitions(process).collect())

udf_define_return_dict
==================================================================================

.. code-block:: python

    # coding:utf8
    import string
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType, ArrayType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            getOrCreate()
        sc = spark.sparkContext

        # 假设 有三个数字  1 2 3  我们传入数字 ,返回数字所在序号对应的 字母 然后和数字结合形成dict返回
        # 比如传入1 我们返回 {"num":1, "letters": "a"}
        rdd = sc.parallelize([[1], [2], [3]])
        df = rdd.toDF(["num"])

        # 注册UDF
        def process(data):
            return {"num": data, "letters": string.ascii_letters[data]}

        """
        UDF的返回值是字典的话, 需要用StructType来接收
        """
        udf1 = spark.udf.register("udf1", process, StructType().add("num", IntegerType(), nullable=True).\
                                  add("letters", StringType(), nullable=True))

        df.selectExpr("udf1(num)").show(truncate=False)
        df.select(udf1(df['num'])).show(truncate=False)

spark_on_hive
==================================================================================

.. code-block:: python

    # coding:utf8
    import string
    import time

    from pyspark.sql import SparkSession
    from pyspark.sql.types import StructType, StringType, IntegerType, ArrayType
    import pandas as pd
    from pyspark.sql import functions as F


    if __name__ == '__main__':
        # 0. 构建执行环境入口对象SparkSession
        spark = SparkSession.builder.\
            appName("test").\
            master("local[*]").\
            config("spark.sql.shuffle.partitions", 2).\
            config("spark.sql.warehouse.dir", "hdfs://node1:8020/user/hive/warehouse").\
            config("hive.metastore.uris", "thrift://node3:9083").\
            enableHiveSupport().\
            getOrCreate()
        sc = spark.sparkContext

        spark.sql("SELECT * FROM student").show()

jdbc_spark_thrift_server
==================================================================================

.. code-block:: python

    # coding:utf8

    from pyhive import hive

    if __name__ == '__main__':
        # 获取到Hive(Spark ThriftServer的链接)
        conn = hive.Connection(host="node1", port=10000, username="hadoop")

        # 获取一个游标对象
        cursor = conn.cursor()

        # 执行SQL
        cursor.execute("SELECT * FROM student")

        # 通过fetchall API 获得返回值
        result = cursor.fetchall()

        print(result)





































































