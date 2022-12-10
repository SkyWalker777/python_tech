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


















































































































