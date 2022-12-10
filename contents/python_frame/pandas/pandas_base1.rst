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

	info = pd.read_csv(filepath_or_buffer="/Users/lilizhao/my_source/DataAnalysis/day05/code/starbucks_store_worldwide.csv")
	print(info)
	           # Brand  Store Number        Store Name Ownership Type  \
	# 0      Starbucks  47370-257954     Meritxell, 96       Licensed   
	# 1      Starbucks  22331-212325  Ajman Drive Thru       Licensed   
	# 2      Starbucks  47089-256771         Dana Mall       Licensed   
	# 3      Starbucks  22126-218024        Twofour 54       Licensed   
	# 4      Starbucks  17127-178586      Al Ain Tower       Licensed   
	# ...          ...           ...               ...            ...   
	# 25595  Starbucks  21401-212072               Rex       Licensed   
	# 25596  Starbucks  24010-226985          Panorama       Licensed 
	print(type(info))
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

| DataFrame和Series有什么关系呢？
| Series能够传入字典，那么DataFrame能够传入字典作为数据么？那么mongodb的数据是不是也可以这样传入呢？
| 对于一个dataframe类型，既有行索引，又有列索引，我们能够对他做什么操作呢

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

* 实战案例

| 那么回到之前我们读取的狗名字统计的数据上，我们尝试一下刚刚的方法
| 那么问题来了：很多同学肯定想知道使用次数最高的前几个名字是什么呢？

	| df.sort_values(by="Count_AnimalName",ascending=False)

那么问题又来了：如果我的数据有10列，我想按照其中的第1，第3，第8列排序，怎么办？(看ipythpn的帮助文档)

取行或者列
==================================================================================

刚刚知道了如何给数据按照某一行或者列排序，那么现在我们想单独研究使用次数前100的数据，应该如何做？

	| df_sorted = df.sort_values(by="Count_AnimalName")
	| df_sorted[:100]

那么问题来了：

| 具体要选择某一列该怎么选择呢？df["Count_AnimalName"]
| 要同时选择行和列改怎么办？df[:100]["Count_AnimalName "]













































































































