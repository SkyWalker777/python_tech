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



































































































