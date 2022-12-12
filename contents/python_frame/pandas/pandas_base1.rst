Pandas 基础篇 1
##################################################################################

为什么要学习 pandas
**********************************************************************************

我们并不是不愿意学习新的知识，只是在学习之前更想知道学习他们能够帮助我们解决什么问题。

numpy 已经能够帮助我们处理数据，能够结合 matplotlib 解决我们数据分析的问题，那么pandas学习的目的在什么地方呢？
 
numpy 能够帮我们处理处理数值型数据，但是这还不够。 很多时候，我们的数据除了数值之外，还有字符串，还有时间序列等

	| 比如：我们通过爬虫获取到了存储在数据库中的数据
	| 比如：之前 youtube 的例子中除了数值之外还有国家的信息，视频的分类(tag)信息，标题信息等

所以，numpy 能够帮助我们处理数值，但是pandas除了处理数值之外(基于numpy)，还能够帮助我们处理其他类型的数据

什么是 pandas
**********************************************************************************

pandas is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the Python programming language.

pandas 的常用数据类型
**********************************************************************************

	| Series 一维，带标签数组
	| DataFrame 二维，Series 容器

Series
**********************************************************************************

.. code-block:: python

	import string
	import pandas as pd

	t = pd.Series(np.arange(10), index=list(string.ascii_uppercase[:10]))
	print(t)
	# A    0
	# B    1
	# C    2
	# D    3
	# E    4
	# F    5
	# G    6
	# H    7
	# I    8
	# J    9
	# dtype: int64

	print(type(t))
	# <class 'pandas.core.series.Series'>

注意这样几个问题:

	| pd.Series能干什么，能够传入什么类型的数据让其变为series结构
	| index是什么，在什么位置，对于我们常见的数据库数据或者ndarray来说，index到底是什么
	| 如何给一-组数据指定index

Series 创建
==================================================================================

通过字典创建一个 Series，注意其中的索引就是字典的键

.. code-block:: python

	a = {string.ascii_uppercase[i]: i for i in range(10)} # 字段推导式创建一个字典
	print(a)
	# {'A': 0,
	#  'B': 1,
	#  'C': 2,
	#  'D': 3,
	#  'E': 4,
	#  'F': 5,
	#  'G': 6,
	#  'H': 7,
	#  'I': 8,
	#  'J': 9}

	# 通过字典创建一个 Series， 注意其中的索引就是字典的键
	a1 = pd.Series(a)
	print(a1)
	# A    0
	# B    1
	# C    2
	# D    3
	# E    4
	# F    5
	# G    6
	# H    7
	# I    8
	# J    9
	# dtype: int64

	# 重新给其指定其他的索引之后，如果能够对应上，就取其值，如果不能，就为Nan
	# 其实非常好理解，是吧?
	# 一个人有10种水果, 你要了苹果,香蕉，菠萝，他有苹果，香蕉，但是没有菠萝，这个时候菠萝就是nan
	a2 = pd.Series(a1, index=list(string.ascii_uppercase[5:15]))
	print(a2)
	# F    5.0
	# G    6.0
	# H    7.0
	# I    8.0
	# J    9.0
	# K    NaN
	# dtype: float64
	# 为什么类型为float呢?
	# numpy中nan为float,pandas会自动根据数据类更改 series 的 dtype 类型
	# 那么问题来了，如何修改 dtype 呢? 和 numpy 的方法一样

Series 切片和索引
==================================================================================

.. code-block:: python

	print(t)
	# A    0
	# B    1
	# C    2
	# D    3
	# E    4
	# F    5
	# G    6
	# H    7
	# I    8
	# J    9
	# dtype: int64

	print(t[2:10:2])
	# C    2
	# E    4
	# G    6
	# I    8
	# dtype: int64

	print(t[1])
	# 1

	print(t[[2,3,6]])
	# C    2
	# D    3
	# G    6
	# dtype: int64

	print(t[t>4])
	# F    5
	# G    6
	# H    7
	# I    8
	# J    9
	# dtype: int64

	print(t[["A","F","g"]])
	# KeyError: "['g'] not in index"

.. tip::

	| 切片:直接传入start end或者步长即可
	| 索引:一个的时候直接传入序号或者index，多个的时候传入序号或者index的列表

Series 的索引和值
==================================================================================

.. code-block:: python

	print(t.index)
	# Index(['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J'], dtype='object')

	print(t.values)
	# [0 1 2 3 4 5 6 7 8 9]

	print(type(t.index))
	# <class 'pandas.core.indexes.base.Index'>

	print(type(t.values))
	# <class 'numpy.ndarray'>

.. tip::

	| Series对象本质上由两个数组构成, 一个数组构成对象的键(index，索引), 一个数组构成对象的值(values)，键->值
	| ndarray的很多方法都可以运用于series类型，比如argmax, clip
	| series具有where方法，但是结果和ndarray不同

DataFrame
**********************************************************************************

读取外部数据
==================================================================================

| 现在假设我们有一个组关于狗的名字的统计数据，那么为了观察这组数据的情况，我们应该怎么做呢？
| 数据来源：https://www.kaggle.com/new-york-city/nyc-dog-names/data

这组数据存在csv中，我们直接使用 pd.read_csv 即可. 和我们想象的有些差别，我们以为他会是一个 Series 类型，但是他是一个 DataFrame，那么接下来我们就来了解这种数据类型

.. code-block:: python

	dog = pd.read_csv(filepath_or_buffer="/Users/lilizhao/Downloads/dogs.csv")
	print(dog)
	#    AnimalName  Count_AnimalName
	# 0           1                 1
	# 1           2                 2
	# 2       40804                 1
	# 3       90201                 1
	# 4       90203                 1
	# 5      102201                 1
	# 6     3010271                 1
	# 7       MARCH                 2
	# 8       APRIL                51
	# 9      AUGUST                14
	# 10   DECEMBER                 4
	# 11     SUNDAY                13
	# 12     MONDAY                 4

	print(type(dog))
	# <class 'pandas.core.frame.DataFrame'>

但是，还有一个问题：对于数据库比如mysql或者mongodb中数据我们如何使用呢？

	| pd.read_sql(sql_sentence,connection)

那么，mongodb 呢？

pandas 之 DataFrame
==================================================================================

.. code-block:: python

	t = pd.DataFrame(np.arange(12).reshape((3,4)))
	print(t)
	   # 0  1   2   3
	# 0  0  1   2   3
	# 1  4  5   6   7
	# 2  8  9  10  11

	t = pd.DataFrame(np.arange(12).reshape((3,4)),index=list(string.ascii_uppercase[:3]),columns=list(string.ascii_uppercase[-4:]))
	print(t)
	   # W  X   Y   Z
	# A  0  1   2   3
	# B  4  5   6   7
	# C  8  9  10  11

| DataFrame 对象既有行索引，又有列索引
| 行索引，表明不同行，横向索引，叫index，0轴，axis=0
| 列索引，表名不同列，纵向索引，叫columns，1轴，axis=1

那么问题来了：

| DataFrame 和 Series有什么关系呢？
| Series 能够传入字典，那么 DataFrame 能够传入字典作为数据么？那么 mongodb 的数据是不是也可以这样传入呢？
| 对于一个 dataframe 类型，既有行索引，又有列索引，我们能够对他做什么操作呢

和一个 ndarray 一样，我们通过 shape，ndim，dtype 了解这个ndarray的基本信息，那么对于DataFrame我们有什么方法了解呢

* DataFrame 的基础属性

	| df.shape #行数列数
	| df.dtypes #列数据类型
	| df.ndim #数据维度
	| df.index #行索引
	| df.columns #列索引
	| df.values #对象值，二维ndarray数组

* DataFrame 整体情况查询

	| df.head(3) #显示头部几行，默认5行
	| df.tail(3) #显示末尾几行，默认5行
	| df.info() #相关信息概览:行数，列数，列索引，列非空值个数，列类型，列类型，内存占用
	| df.describe() #快速综合统计结果:计数，均值，标准差，最大值，四分位数，最小值

排序
==================================================================================

| 那么回到之前我们读取的狗名字统计的数据上，我们尝试一下刚刚的方法
| 那么问题来了：很多同学肯定想知道使用次数最高的前几个名字是什么呢？

	| df.sort_values(by="Count_AnimalName",ascending=False)

那么问题又来了：如果我的数据有10列，我想按照其中的第1，第3，第8列排序，怎么办？(看ipythpn的帮助文档)

.. code-block:: python

	dog_sorted = dog.sort_values(by="Count_AnimalName",ascending=False)
	print(dog_sorted)
	   # AnimalName  Count_AnimalName
	# 8       APRIL                51
	# 9      AUGUST                14
	# 11     SUNDAY                13
	# 10   DECEMBER                 4
	# 12     MONDAY                 4
	# 1           2                 2
	# 7       MARCH                 2
	# 0           1                 1
	# 2       40804                 1
	# 3       90201                 1
	# 4       90203                 1
	# 5      102201                 1
	# 6     3010271                 1

取行或者列
==================================================================================

刚刚知道了如何给数据按照某一行或者列排序，那么现在我们想单独研究使用次数前100的数据，应该如何做？

	| df_sorted = df.sort_values(by="Count_AnimalName")
	| df_sorted[:100]

那么问题来了：

| 具体要选择某一列该怎么选择呢？df["Count_AnimalName"]
| 要同时选择行和列改怎么办？df[:100]["Count_AnimalName "]

.. code-block:: python

	dog_sorted = dog.sort_values(by="Count_AnimalName",ascending=False)
	print(dog_sorted[:5])
	   # AnimalName  Count_AnimalName
	# 8       APRIL                51
	# 9      AUGUST                14
	# 11     SUNDAY                13
	# 10   DECEMBER                 4
	# 12     MONDAY                 4

	# 选择某一列
	print(dog["AnimalName"])
	# 0            1
	# 1            2
	# 2        40804
	# 3        90201
	# 4        90203
	# 5       102201
	# 6      3010271
	# 7        MARCH
	# 8        APRIL
	# 9       AUGUST
	# 10    DECEMBER
	# 11      SUNDAY
	# 12      MONDAY
	# Name: AnimalName, dtype: ob

	# 同时选择行和列
	print(dog_sorted[:100]["AnimalName"])
	# 8        APRIL
	# 9       AUGUST
	# 11      SUNDAY
	# 10    DECEMBER
	# 12      MONDAY
	# 1            2
	# 7        MARCH
	# 0            1
	# 2        40804
	# 3        90201
	# 4        90203
	# 5       102201
	# 6      3010271
	# Name: AnimalName, dtype: object

pandas 之 loc, iloc
==================================================================================

还有更多的经过 pandas 优化过的选择方式：

	| df.loc 通过标签索引行数据
	| df.iloc 通过位置获取行数据

冒号在 loc 里面是闭合的,即会选择到冒号后面的数据

.. code-block:: python

	t = pd.DataFrame(np.arange(12).reshape((3,4)),index=list(string.ascii_uppercase[:3]),columns=list(string.ascii_uppercase[-4:]))
	print(t)
	   # W  X   Y   Z
	# A  0  1   2   3
	# B  4  5   6   7
	# C  8  9  10  11

	v = t.loc["A",'W']
	print(v)
	# 0

	v = t.loc["A",["W","Z"]]
	print(v)
	# W    0
	# Z    3
	# Name: A, dtype: int64
	print(type(v))
	# <class 'pandas.core.series.Series'>

	# 选择间隔的多行多列
	v = t.loc[["A","C"],["W","Z"]]
	print(v)
	print(type(v))
	print(type(v))
	   # W   Z
	# A  0   3
	# C  8  11
	# <class 'pandas.core.frame.DataFrame'>

	# 冒号在loc里面是闭合的, 即会选择到冒号后面的数据
	v = t.loc["A":"C",["W","Z"]]
	print(v)
	   # W   Z
	# A  0   3
	# B  4   7
	# C  8  11

	v = t.iloc[1:3,[2,3]]
	print(v)
	    # Y   Z
	# B   6   7
	# C  10  11

	# 赋值更改数据的过程
	t.loc["A","Y"] = 100
	print(t)
	   # W  X    Y   Z
	# A  0  1  100   3
	# B  4  5    6   7
	# C  8  9   10  11

	v = t.iloc[1:3,1:3]
	print(v)
	   # X   Y
	# B  5   6
	# C  9  10

	t.iloc[1:2,0:2] = 200
	print(t)
	     # W    X    Y   Z
	# A    0    1  100   3
	# B  200  200    6   7
	# C    8    9   10  11

布尔索引
==================================================================================

回到之前狗的名字的问题上，假如我们想找到所有的使用次数超过 10 的狗的名字，应该怎么选择？

.. code-block:: python

	dog_filter = dog["Count_AnimalName"] > 10
	print(dog_filter)
	# 0     False
	# 1     False
	# 2     False
	# 3     False
	# 4     False
	# 5     False
	# 6     False
	# 7     False
	# 8      True
	# 9      True
	# 10    False
	# 11     True
	# 12    False
	# Name: Count_AnimalName, dtype: bool
	print(type(dog_filter))
	# <class 'pandas.core.series.Series'>

	dog_bgt_10 = dog[dog["Count_AnimalName"] > 10]
	print(dog_bgt_10)
	   # AnimalName  Count_AnimalName
	# 8       APRIL                51
	# 9      AUGUST                14
	# 11     SUNDAY                13

回到之前狗的名字的问题上，假如我们想找到所有的使用次数超过 10 并且名字的字符串的长度大于 5 的狗的名字，应该怎么选择？

.. code-block:: python

	dog_filter = dog[(dog["AnimalName"].str.len() > 5) & (dog["Count_AnimalName"] > 10)]
	print(dog_filter)
	# AnimalName  Count_AnimalName
	# 9      AUGUST                14
	# 11     SUNDAY                13

两个问题:

	| 1. & 符号表示什么? 哪有其他的连接符号可以使用么?
	| 2. .str 是什么方法?
	| 注意点:不同的条件之间需要用括号括起来

字符串方法
**********************************************************************************

	| 方法            说明
	| cat.           实现元素级的字符串连接操作，可指定分隔符
	| contains       返回表示各字符串是否含有指定模式的布尔型数组.
	| count          模式的出现次数
	| endswith、startswith.     相当于对各个元素执行x.endswith(pattern)或x.startswith(pattern)
	| findall        计算各字符串的模式列表
	| get            获取各元素的第i个字符
	| join           根据指定的分隔符将Series中各元素的字符串连接起来
	| len.           计算各字符串的长度
	| lower、upper.  转换大小写。相当于对各个元素执行x.lower()或x.upper()
	| match          根据指定的正则表达式对各个元素执行re.match
	| pad            在字符串的左边、右边或左右两边添加空白符
	| center         相当于pad(side='both')
	| repeat         重复值。例如，s.str.repeat(3)相当 于对各个字符串执行x*3 
	| replace        用指定字符串替换找到的模式
	| slice          对Series中的各个字符串进行子串截取
	| split          根据分隔符或正则表达式对字符串进行拆分
	| strip、rstrip、 lstrip   去除空白符，包括换行符。相当于对各个元素执行x.strip(). x.rstrip()、x.Istrip()

缺失数据的处理
**********************************************************************************

观察下面这组数据

.. code-block:: python

	t = pd.DataFrame(np.arange(24).reshape((4,6)),index=list(string.ascii_uppercase[:4]),columns=list(string.ascii_uppercase[-6:]))
	print(t)
	    # U   V   W   X   Y   Z
	# A   0   1   2   3   4   5
	# B   6   7   8   9  10  11
	# C  12  13  14  15  16  17
	# D  18  19  20  21  22  23

	t.loc["A","U"] = np.nan
	t.loc["A","Z"] = np.nan
	t.loc["B","Y"] = 0
	t.loc["D","W"] = np.nan
	print(t)
	      # U   V     W   X   Y     Z
	# A   NaN   1   2.0   3   4   NaN
	# B   6.0   7   8.0   9   0  11.0
	# C  12.0  13  14.0  15  16  17.0
	# D  18.0  19   NaN  21  22  23.0

数据缺失通常有两种情况：

	| 一种就是空，None 等，在 pandas 是 NaN(和 np.nan 一样)
	| 另一种是我们让其为0，蓝色框中

对于 NaN 的数据，在 numpy 中是如何处理的？在 pandas 中我们处理起来非常容易. 判断数据是否为 NaN：pd.isnull(df),pd.notnull(df)

	| 处理方式 1：删除 NaN 所在的行列 dropna (axis=0, how='any', inplace=False)
	| 处理方式 2：填充数据，t.fillna(t.mean()),t.fiallna(t.median()),t.fillna(0)

.. code-block:: python

	t1 = t.dropna(axis=0, how='any', inplace=False)
	print(t1)
	      # U   V     W   X   Y     Z
	# B   6.0   7   8.0   9   0  11.0
	# C  12.0  13  14.0  15  16  17.0

	t2 = t.fillna(t.mean());
	print(t2)
	      # U   V     W   X   Y     Z
	# A  12.0   1   2.0   3   4  17.0
	# B   6.0   7   8.0   9   0  11.0
	# C  12.0  13  14.0  15  16  17.0
	# D  18.0  19   8.0  21  22  23.0

| 处理为 0 的数据：t[t==0]=np.nan
| 当然并不是每次为0的数据都需要处理. 计算平均值等情况，nan是不参与计算的，但是 0 会

.. code-block:: python

	t[t==0]=np.nan
	print(t)
	      # U   V     W   X     Y     Z
	# A   NaN   1   2.0   3   4.0   NaN
	# B   6.0   7   8.0   9   NaN  11.0
	# C  12.0  13  14.0  15  16.0  17.0
	# D  18.0  19   NaN  21  22.0  23.0

pandas 常用统计方法
**********************************************************************************

| 假设现在我们有一组从 2006 年到 2016 年 1000 部最流行的电影数据，我们想知道这些电影数据中评分的平均分，导演的人数等信息，我们应该怎么获取？
| 
| 数据来源：https://www.kaggle.com/damianpanek/sunday-eda/data

.. code-block:: python

	df = pd.read_csv(filepath_or_buffer="/Users/lilizhao/Downloads/IMDB-Movie-Data.csv")
	# print(df)

	# 评分的平均分
	rating_mean = df["Rating"].mean()
	print(rating_mean)
	# 6.723200000000003

	# 导演的人员
	temp_list = df["Actors"].str.split(",").tolist()
	nums = set([i for j in temp_list for i in j])
	print(len(nums))
	# 2394

	# 电影时长的最大最小值
	max_runtime = df["Runtime (Minutes)"].max()
	print(max_runtime)
	# 191
	max_runtime_index = df["Runtime (Minutes)"].argmax()
	print(max_runtime_index)
	# 828
	min_runtime = df["Runtime (Minutes)"].min()
	print(min_runtime)
	# 66
	min_runtime_index = df["Runtime (Minutes)"].argmin()
	print(min_runtime_index)
	# 793
	min_runtime_median = df["Runtime (Minutes)"].median()
	print(min_runtime_median)
	# 111.0

数据合并
**********************************************************************************

数据合并之 join
==================================================================================

join: 默认情况下他是把行索引相同的数据合并到一起

.. code-block:: python

	t1 = pd.DataFrame(np.ones((3,4)),index=list(string.ascii_uppercase[:3]),columns=list('1234'))
	print(t1)
	     # 1    2    3    4
	# A  1.0  1.0  1.0  1.0
	# B  1.0  1.0  1.0  1.0
	# C  1.0  1.0  1.0  1.0
	t2 = pd.DataFrame(np.zeros((2,5)),index=list(string.ascii_uppercase[:2]),columns=list(string.ascii_uppercase[-5:]))
	print(t2)
	     # V    W    X    Y    Z
	# A  0.0  0.0  0.0  0.0  0.0
	# B  0.0  0.0  0.0  0.0  0.0

	t3 = t1.join(t2)
	print(t3)
	     # 1    2    3    4    V    W    X    Y    Z
	# A  1.0  1.0  1.0  1.0  0.0  0.0  0.0  0.0  0.0
	# B  1.0  1.0  1.0  1.0  0.0  0.0  0.0  0.0  0.0
	# C  1.0  1.0  1.0  1.0  NaN  NaN  NaN  NaN  NaN

数据合并之 merge
==================================================================================

merge: 按照指定的列把数据按照一定的方式合并到一起

.. code-block:: python

	t1 = pd.DataFrame(np.ones((3,4)),index=list('ABC'),columns=list('MNOP'))
	t1.loc["A","O"] = 'a'
	t1.loc["B","O"] = 'b'
	t1.loc["C","O"] = 'c'
	print(t1)
	     # M    N  O    P
	# A  1.0  1.0  a  1.0
	# B  1.0  1.0  b  1.0
	# C  1.0  1.0  c  1.0

	t2 = pd.DataFrame(np.zeros((2,5)),index=list('AB'),columns=list('VWXYZ'))
	t2.loc["A","X"] = 'c'
	t2.loc["B","X"] = 'd'
	print(t2)
	     # V    W  X    Y    Z
	# A  0.0  0.0  c  0.0  0.0
	# B  0.0  0.0  d  0.0  0.0

	# 默认的合并方式inner，并集
	t3 = t1.merge(t2,left_on='O',right_on='X')
	print(t3)
	     # M    N  O    P    V    W  X    Y    Z
	# 0  1.0  1.0  c  1.0  0.0  0.0  c  0.0  0.0

	t3 = t1.merge(t2,left_on='O',right_on='X',how="inner")
	print(t3)
	     # M    N  O    P    V    W  X    Y    Z
	# 0  1.0  1.0  c  1.0  0.0  0.0  c  0.0  0.0

	# merge outer，交集，NaN补全
	t3 = t1.merge(t2,left_on='O',right_on='X',how="outer")
	print(t3)
	     # M    N    O    P    V    W    X    Y    Z
	# 0  1.0  1.0    a  1.0  NaN  NaN  NaN  NaN  NaN
	# 1  1.0  1.0    b  1.0  NaN  NaN  NaN  NaN  NaN
	# 2  1.0  1.0    c  1.0  0.0  0.0    c  0.0  0.0
	# 3  NaN  NaN  NaN  NaN  0.0  0.0    d  0.0  0.0

	# merge left，左边为准，NaN补全
	t3 = t1.merge(t2,left_on='O',right_on='X',how="left")
	print(t3)
	     # M    N  O    P    V    W    X    Y    Z
	# 0  1.0  1.0  a  1.0  NaN  NaN  NaN  NaN  NaN
	# 1  1.0  1.0  b  1.0  NaN  NaN  NaN  NaN  NaN
	# 2  1.0  1.0  c  1.0  0.0  0.0    c  0.0  0.0

	# merge right，右边为准，NaN补全
	t3 = t1.merge(t2,left_on='O',right_on='X',how="right")
	print(t3)
	     # M    N    O    P    V    W  X    Y    Z
	# 0  1.0  1.0    c  1.0  0.0  0.0  c  0.0  0.0
	# 1  NaN  NaN  NaN  NaN  0.0  0.0  d  0.0  0.0

分组和聚合
**********************************************************************************

现在我们有一组关于全球星巴克店铺的统计数据，如果我想知道美国的星巴克数量和中国的哪个多，或者我想知道中国每个省份星巴克的数量的情况，那么应该怎么办？

| 思路：遍历一遍，每次加1 ？？？
| 数据来源：https://www.kaggle.com/starbucks/store-locations/data

在pandas中类似的分组的操作我们有很简单的方式来完成

	| df.groupby(by="columns_name")

那么问题来了，调用groupby方法之后返回的是什么内容？

	| grouped = df.groupby(by="columns_name")

grouped 是一个DataFrameGroupBy对象，是可迭代的 grouped 中的每一个元素是一个元组. 元组里面是（索引(分组的值)，分组之后的DataFrame）

那么，回到之前的问题：

	| 要统计美国和中国的星巴克的数量，我们应该怎么做？分组之后的每个DataFrame的长度？
	| 长度是一个思路，但是我们有更多的方法(聚合方法)来解决这个问题

要统计美国和中国的星巴克的数量，我们应该怎么做？DataFrameGroupBy 对象有很多经过优化的方法

	| 函数名.        说明
	| count.        分组中非NA值的数量
	| sum           非NA值的和
	| mean          非NA值的平均值
	| median        非NA值的算术中位数
	| std、var      无偏(分母为n-1)标准差和方差
	| min、max.     非NA值的最小值和最大值

如果我们需要对国家和省份进行分组统计，应该怎么操作呢？

	| grouped = df.groupby(by=[df["Country"],df["State/Province"]])

很多时候我们只希望对获取分组之后的某一部分数据，或者说我们只希望对某几列数据进行分组，这个时候我们应该怎么办呢？

获取分组之后的某一部分数据：

	| df.groupby(by=["Country","State/Province"])["Country"].count()

对某几列数据进行分组：

	| df["Country"].groupby(by=[df["Country"],df["State/Province"]]).count()

观察结果，由于只选择了一列数据，所以结果是一个Series类型. 如果我想返回一个DataFrame类型呢？

	| t1 = df[["Country"]].groupby(by=[df["Country"],df["State/Province"]]).count()
	| t2 = df.groupby(by=["Country","State/Province"])[["Country"]].count()

以上的两条命令结果一样. 和之前的结果的区别在于当前返回的是一个DataFrame类型

.. code-block:: python

	df = pd.read_csv(filepath_or_buffer="/Users/lilizhao/Downloads/directory.csv")
	# print(df)

	grouped = df.groupby(by="Brand")
	t1 = df[["Country"]].groupby(by=[df["Country"],df["State/Province"]]).count()
	print(t1)
	t2 = df.groupby(by=["Country","State/Province"])[["Country"]].count()
	print(t2)
	                        # Country
	# Country State/Province
	# AD      7                     1
	# AE      AJ                    2
	#         AZ                   48
	#         DU                   82
	#         FU                    2
	# ...                         ...
	# US      WV                   25
	#         WY                   23
	# VN      HN                    6
	#         SG                   19
	# ZA      GT                    3
	# [545 rows x 1 columns]

索引和复合索引
**********************************************************************************

简单的索引操作：

	| 获取 index: df.index
	| 指定 index: df.index = ['x','y']
	| 重新设置 index: df.reindex(list("abcedf"))
	| 指定某一列作为 index: df.set_index("Country",drop=False)
	| 返回index的唯一值: df.set_index("Country").index.unique()

假设 a 为一个 DataFrame, 那么当 a.set_index(["c","d"]) 即设置两个索引的时候是什么样子的结果呢？

.. code-block:: python

	a = pd.DataFrame({'a': range(7), 'b': range(7, 0, -1), 'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'],
	                  'd': list("hjklmno")})
	print(a)
	   # a  b    c  d
	# 0  0  7  one  h
	# 1  1  6  one  j
	# 2  2  5  one  k
	# 3  3  4  two  l
	# 4  4  3  two  m
	# 5  5  2  two  n
	# 6  6  1  two  o

Series 复合索引
==================================================================================

.. code-block:: python

	a = pd.DataFrame({'a': range(7), 'b': range(7, 0, -1), 'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'],
	                  'd': list("hjklmno")})
	# print(a)
	   # a  b    c  d
	# 0  0  7  one  h
	# 1  1  6  one  j
	# 2  2  5  one  k
	# 3  3  4  two  l
	# 4  4  3  two  m
	# 5  5  2  two  n
	# 6  6  1  two  o

	X = a.set_index(["c","d"])["a"]
	# print(X)
	# one  h    0
	#      j    1
	#      k    2
	# two  l    3
	#      m    4
	#      n    5
	#      o    6
	# Name: a, dtype: int64

	# print(X["one", "h"])
	# 0

	# 只取索引 h 对应值怎么办？
	x2 = X.swaplevel()
	# print(x2)
	# d  c
	# h  one    0
	# j  one    1
	# k  one    2
	# l  two    3
	# m  two    4
	# n  two    5
	# o  two    6
	# Name: a, dtype: int64

	# level相当于就是复合索引的里外层，交换了level之后，里外交换所以能够直接从h开始取值
	x3 = X.swaplevel()["h"]
	print(x3)
	# c
	# one    0
	# Name: a, dtype: int64

	X.index.levels
	# FrozenList([['one', 'two'], ['h', 'j', 'k', 'l', 'm', 'n', 'o']])

DataFrame 复合索引
==================================================================================

.. code-block:: python

	a = pd.DataFrame({'a': range(7), 'b': range(7, 0, -1), 'c': ['one', 'one', 'one', 'two', 'two', 'two', 'two'],
	                  'd': list("hjklmno")})
	# print(a)
	   # a  b    c  d
	# 0  0  7  one  h
	# 1  1  6  one  j
	# 2  2  5  one  k
	# 3  3  4  two  l
	# 4  4  3  two  m
	# 5  5  2  two  n
	# 6  6  1  two  o

	x = a.set_index(["c","d"])[["a"]]
	print(x)
	       # a
	# c   d
	# one h  0
	#     j  1
	#     k  2
	# two l  3
	#     m  4
	#     n  5
	#     o  6

	x1 = x.loc["one"]
	print(x1)
	  # a
	# d
	# h  0
	# j  1
	# k  2

	x2 = x.loc["one"].loc["h"]
	print(x2)
	# a    0
	# Name: h, dtype: int64

	x2 = x.swaplevel().loc["h"]
	print(x2)
	     # a
	# c
	# one  0

时间序列
**********************************************************************************

| 不管在什么行业，时间序列都是一种非常重要的数据形式，很多统计数据以及数据的规律也都和时间序列有着非常重要的联系
| 而且在 pandas 中处理时间序列是非常简单的











































































