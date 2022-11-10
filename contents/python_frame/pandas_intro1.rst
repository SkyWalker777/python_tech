Pandas 篇 1
##################################################################################

什么是 Pandas
**********************************************************************************

* 本课程适用于谁？

::

	需要使用Python做数据分析，期待一个强大的数据分析工具
	本课程要求有一定的Python基础
	适用于零基础入门Pandas，或者提升自己的Pandas水平

* 什么是Pandas?

一个开源的Python类库：用于数据分析、数据处理、数据可视化

::

	高性能
	容易使用的数据结构
	容易使用的数据分析工具

很方便和其它类库一起使用：

::

	numpy：用于数学计算
	scikit-learn：用于机器学习

* 怎样下载安装Pandas

| 1、下载使用Python类库集成安装包：anaconda

::

	https://www.anaconda.com
	当今最流行的Python数据分析发行版
	已经安装了数据分析需要的几乎所有的类库

| 2、pip install pandas

* 演示方式

| 1、使用jupyter notebook演示

::

	jupyter：交互性、探索性的开发神器，适合学习语法、数据分析；
	pycharm：大而全的集成开发环境，适合复杂项目的开发；

| 2、使用真实数据集做演示
| 3、jupyter代码、数据集，提供 github 仓库下载

Pandas 数据读取
**********************************************************************************

Pandas 需要先读取表格类型的数据，然后进行分析

::

	数据类型         说明                         Pandas读取方法
	csv、tsv、txt    用逗号分隔、tab分割的纯文本文件 pd.read_csv
	excel           微软xls或者xlsx文件           pd.read_excel
	mysql           关系型数据库表                 pd.read_sql

本节示例内容

::

	1. pandas读取纯文本文件
	  * 读取csv文件
	  * 读取txt文件
	2. pandas读取xlsx格式excel文件
	3. pandas读取mysql数据表

.. code-block:: python

	import pandas as pd

	## 1、读取纯文本文件

	### 1.1 读取CSV，使用默认的标题行、逗号分隔符
	fpath = "./datas/ml-latest-small/ratings.csv"
	# 使用pd.read_csv读取数据
	ratings = pd.read_csv(fpath)

	# 查看前几行数据
	ratings.head()
	# 输出
	userId	movieId	rating	timestamp
	0	1	1	4.0	964982703
	1	1	3	4.0	964981247
	2	1	6	4.0	964982224
	3	1	47	5.0	964983815
	4	1	50	5.0	964982931

	# 查看数据的形状，返回(行数、列数)
	ratings.shape
	# 输出
	(100836, 4)

	# 查看列名列表
	ratings.columns
	# 输出
	Index(['userId', 'movieId', 'rating', 'timestamp'], dtype='object')

	# 查看索引列
	ratings.index
	# 输出
	RangeIndex(start=0, stop=100836, step=1)

	# 查看每列的数据类型
	ratings.dtypes
	# 输出
	userId         int64
	movieId        int64
	rating       float64
	timestamp      int64
	dtype: object

	### 1.2 读取txt文件，自己指定分隔符、列名
	fpath = "./datas/crazyant/access_pvuv.txt"

	pvuv = pd.read_csv(
	    fpath,
	    sep="\t",
	    header=None,
	    names=['pdate', 'pv', 'uv']
	)

	pvuv
	# 输出
		pdate	pv	uv
	0	2019-09-10	139	92
	1	2019-09-09	185	153
	2	2019-09-08	123	59
	3	2019-09-07	65	40
	4	2019-09-06	157	98
	5	2019-09-05	205	151
	6	2019-09-04	196	167
	7	2019-09-03	216	176
	8	2019-09-02	227	148
	9	2019-09-01	105	61

	## 2、读取excel文件
	fpath = "./datas/crazyant/access_pvuv.xlsx"
	pvuv = pd.read_excel(fpath)

	pvuv
	# 输出
	日期	PV	UV
	0	2019-09-10	139	92
	1	2019-09-09	185	153
	2	2019-09-08	123	59
	3	2019-09-07	65	40
	4	2019-09-06	157	98
	5	2019-09-05	205	151
	6	2019-09-04	196	167
	7	2019-09-03	216	176
	8	2019-09-02	227	148
	9	2019-09-01	105	61

	## 3、读取MySQL数据库
	import pymysql
	conn = pymysql.connect(
	        host='127.0.0.1',
	        user='root',
	        password='12345678',
	        database='test',
	        charset='utf8'
	    )

	mysql_page = pd.read_sql("select * from crazyant_pvuv", con=conn)
	mysql_page
	# 输出
	time	ip	period	total_orders	success_orders	fail_orders	total_money	success_money	fail_money	success_orders_per	success_money_per	created	param1	param2	param3
	0	2020-02-12 00:00:00	host-10-191-94-110	2	9306	0	0	93060.0	0.0	0.0	0.0	0.0	2020-06-10 02:25:40	None	None	None
	1	2020-02-12 16:00:00	host-10-191-94-110	1	9306	0	0	93060.0	0.0	0.0	0.0	0.0	2020-06-10 02:25:40	None	None	None
	2	2020-02-12 16:05:00	host-10-191-94-110	0	9306	0	0	93060.0	0.0	0.0	0.0	0.0	2020-06-10 02:25:40	None	None	None

Pandas 数据结构
**********************************************************************************

DataFrame & Series

DataFrame：二维数据，整个表格，多行多列

|image0|

.. code-block:: python

	## 03. Pandas数据结构
	# 1. Series
	# 2. DataFrame
	# 3. 从DataFrame中查询出Series

	import pandas as pd
	import numpy as np

	### 1. Series
	# Series是一种类似于一维数组的对象，它由一组数据（不同数据类型）以及一组与之相关的数据标签（即索引）组成。

	#### 1.1 仅有数据列表即可产生最简单的Series
	s1 = pd.Series([1,'a',5.2,7])

	# 左侧为索引，右侧是数据
	s1
	输出 ===> 
	0      1
	1      a
	2    5.2
	3      7
	dtype: object

	# 获取索引
	s1.index
	输出 ===> 
	RangeIndex(start=0, stop=4, step=1)

	# 获取数据
	s1.values
	输出 ===> 
	array([1, 'a', 5.2, 7], dtype=object)

	#### 1.2 创建一个具有标签索引的Series
	s2 = pd.Series([1, 'a', 5.2, 7], index=['d','b','a','c'])

	s2
	输出 ===> 
	d      1
	b      a
	a    5.2
	c      7
	dtype: object

	s2.index
	输出 ===> 
	Index(['d', 'b', 'a', 'c'], dtype='object')

	#### 1.3 使用Python字典创建Series
	sdata={'Ohio':35000,'Texas':72000,'Oregon':16000,'Utah':5000}
	输出 ===> 
	{'Ohio': 35000, 'Texas': 72000, 'Oregon': 16000, 'Utah': 5000}

	s3=pd.Series(sdata)
	s3
	输出 ===> 
	Ohio      35000
	Texas     72000
	Oregon    16000
	Utah       5000
	dtype: int64

	#### 1.4 根据标签索引查询数据
	# 类似Python的字典dict
	输出 ===> 
	d      1
	b      a
	a    5.2
	c      7
	dtype: object

	s2['a']
	输出 ===> 
	5.2

	type(s2['a'])
	输出 ===> 
	float

	s2[['b','a']]
	输出 ===> 
	b      a
	a    5.2
	dtype: object

	type(s2[['b','a']])
	输出 ===> 
	pandas.core.series.Series


	### 2. DataFrame
	# DataFrame是一个表格型的数据结构
	# * 每列可以是不同的值类型（数值、字符串、布尔值等）
	# * 既有行索引index,也有列索引columns
	# * 可以被看做由Series组成的字典

	# 创建dataframe最常用的方法，见02节读取纯文本文件、excel、mysql数据库

	#### 2.1 根据多个字典序列创建dataframe
	data={
	        'state':['Ohio','Ohio','Ohio','Nevada','Nevada'],
	        'year':[2000,2001,2002,2001,2002],
	        'pop':[1.5,1.7,3.6,2.4,2.9]
	    }
	df = pd.DataFrame(data)

	df
	输出 ===> 
		state	year	pop
	0	Ohio	2000	1.5
	1	Ohio	2001	1.7
	2	Ohio	2002	3.6
	3	Nevada	2001	2.4
	4	Nevada	2002	2.9

	df.dtypes
	输出 ===> 
	state     object
	year       int64
	pop      float64
	dtype: object

	df.columns
	输出 ===> 
	Index(['state', 'year', 'pop'], dtype='object')

	df.index
	输出 ===> 
	RangeIndex(start=0, stop=5, step=1)

	### 3. 从DataFrame中查询出Series

	# * 如果只查询一行、一列，返回的是pd.Series
	# * 如果查询多行、多列，返回的是pd.DataFrame
	df
	输出 ===> 
		state	year	pop
	0	Ohio	2000	1.5
	1	Ohio	2001	1.7
	2	Ohio	2002	3.6
	3	Nevada	2001	2.4
	4	Nevada	2002	2.9

	#### 3.1 查询一列，结果是一个pd.Series
	df['year']
	输出 ===> 
	0    2000
	1    2001
	2    2002
	3    2001
	4    2002
	Name: year, dtype: int64

	type(df['year'])
	pandas.core.series.Series

	#### 3.2 查询多列，结果是一个pd.DataFrame
	df[['year', 'pop']]
	输出 ===> 
		year	pop
	0	2000	1.5
	1	2001	1.7
	2	2002	3.6
	3	2001	2.4
	4	2002	2.9

	type(df[['year', 'pop']])
	输出 ===> 
	pandas.core.frame.DataFrame

	#### 3.3 查询一行，结果是一个pd.Series
	df.loc[1]
	输出 ===> 
	state    Ohio
	year     2001
	pop       1.7
	Name: 1, dtype: object

	type(df.loc[1])
	输出 ===> 
	pandas.core.series.Series

	#### 3.4 查询多行，结果是一个pd.DataFrame
	df.loc[1:3]
	输出 ===> 
	state	year	pop
	1	Ohio	2001	1.7
	2	Ohio	2002	3.6
	3	Nevada	2001	2.4

	type(df.loc[1:3])
	输出 ===> 
	pandas.core.frame.DataFrame

Pandas 数据查询
**********************************************************************************

按数值、列表、区间、条件、函数五种方法

.. code-block:: python

	## Pandas查询数据的几种方法
	# 1. df.loc方法，根据行、列的标签值查询
	# 2. df.iloc方法，根据行、列的数字位置查询
	# 3. df.where方法
	# 4. df.query方法

	# .loc既能查询，又能覆盖写入，强烈推荐！

	# ## Pandas使用df.loc查询数据的方法
	# 1. 使用单个label值查询数据
	# 2. 使用值列表批量查询
	# 3. 使用数值区间进行范围查询
	# 4. 使用条件表达式查询
	# 5. 调用函数查询

	# ## 注意
	# * 以上查询方法，既适用于行，也适用于列
	# * 注意观察降维dataFrame>Series>值

	import pandas as pd
	print(pd.__version__)
	========>
	1.1.3

	## 0、读取数据
	# 数据为北京2018年全年天气预报  
	df = pd.read_csv("./datas/beijing_tianqi/beijing_tianqi_2018.csv")

	df.head()
	========>
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1
	3	2018-01-04	0℃	-8℃	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3℃	-6℃	多云~晴	西北风	1-2级	50	优	1

	# 设定索引为日期，方便按日期筛选
	df.set_index('ymd', inplace=True)

	# 时间序列见后续课程，本次按字符串处理
	df.index
	========>
	Index(['2018-01-01', '2018-01-02', '2018-01-03', '2018-01-04', '2018-01-05',
	       '2018-01-06', '2018-01-07', '2018-01-08', '2018-01-09', '2018-01-10',
	       ...
	       '2018-12-22', '2018-12-23', '2018-12-24', '2018-12-25', '2018-12-26',
	       '2018-12-27', '2018-12-28', '2018-12-29', '2018-12-30', '2018-12-31'],
	      dtype='object', name='ymd', length=365)

	df.head()
	========>
		bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1
	2018-01-04	0℃	-8℃	阴	东北风	1-2级	28	优	1
	2018-01-05	3℃	-6℃	多云~晴	西北风	1-2级	50	优	1

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	df.dtypes
	========>
	bWendu        int32
	yWendu        int32
	tianqi       object
	fengxiang    object
	fengli       object
	aqi           int64
	aqiInfo      object
	aqiLevel      int64
	dtype: object

	df.head()
	========>
		bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2
	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1
	2018-01-03	2	-5	多云	北风	1-2级	28	优	1
	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1
	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1

	## 1、使用单个label值查询数据
	# 行或者列，都可以只传入单个值，实现精确匹配

	# 得到单个值
	df.loc['2018-01-03', 'bWendu']
	========>
	2

	# 得到一个Series
	df.loc['2018-01-03', ['bWendu', 'yWendu']]
	========>
	bWendu     2
	yWendu    -5
	Name: 2018-01-03, dtype: object

	## 2、使用值列表批量查询
	# 得到Series
	df.loc[['2018-01-03','2018-01-04','2018-01-05'], 'bWendu']
	========>
	ymd
	2018-01-03    2
	2018-01-04    0
	2018-01-05    3
	Name: bWendu, dtype: int32

	# 得到DataFrame
	df.loc[['2018-01-03','2018-01-04','2018-01-05'], ['bWendu', 'yWendu']]
	========>
	bWendu	yWendu
	ymd		
	2018-01-03	2	-5
	2018-01-04	0	-8
	2018-01-05	3	-6

	## 3、使用数值区间进行范围查询
	# 注意：区间既包含开始，也包含结束
	# 行index按区间
	df.loc['2018-01-03':'2018-01-05', 'bWendu']
	========>
	ymd
	2018-01-03    2
	2018-01-04    0
	2018-01-05    3
	Name: bWendu, dtype: int32

	# 列index按区间
	df.loc['2018-01-03', 'bWendu':'fengxiang']
	========>
	bWendu        2
	yWendu       -5
	tianqi       多云
	fengxiang    北风
	Name: 2018-01-03, dtype: object

	# 行和列都按区间查询
	df.loc['2018-01-03':'2018-01-05', 'bWendu':'fengxiang']
	========>
		bWendu	yWendu	tianqi	fengxiang
	ymd				
	2018-01-03	2	-5	多云	北风
	2018-01-04	0	-8	阴	东北风
	2018-01-05	3	-6	多云~晴	西北风

	## 4、使用条件表达式查询
	# bool列表的长度得等于行数或者列数

	#### 简单条件查询，最低温度低于-10度的列表
	df.loc[df["yWendu"]<-10, :]
	========>
	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-01-23	-4	-12	晴	西北风	3-4级	31	优	1
	2018-01-24	-4	-11	晴	西南风	1-2级	34	优	1
	2018-01-25	-3	-11	多云	东北风	1-2级	27	优	1
	2018-12-26	-2	-11	晴~多云	东北风	2级	26	优	1
	2018-12-27	-5	-12	多云~晴	西北风	3级	48	优	1
	2018-12-28	-3	-11	晴	西北风	3级	40	优	1
	2018-12-29	-3	-12	晴	西北风	2级	29	优	1
	2018-12-30	-2	-11	晴~多云	东北风	1级	31	优	1

	# 观察一下这里的boolean条件
	df["yWendu"]<-10
	========>
	ymd
	2018-01-01    False
	2018-01-02    False
	2018-01-03    False
	2018-01-04    False
	2018-01-05    False
	              ...  
	2018-12-27     True
	2018-12-28     True
	2018-12-29     True
	2018-12-30     True
	2018-12-31    False
	Name: yWendu, Length: 365, dtype: bool

	#### 复杂条件查询，查一下我心中的完美天气
	# 注意，组合条件用&符号合并，每个条件判断都得带括号

	## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
	df.loc[(df["bWendu"]<=30) & (df["yWendu"]>=15) & (df["tianqi"]=='晴') & (df["aqiLevel"]==1), :]
	========>
	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-08-24	30	20	晴	北风	1-2级	40	优	1
	2018-09-07	27	16	晴	西北风	3-4级	22	优	1

	# 北京好天气这么稀少！！

	# 再次观察这里的boolean条件
	(df["bWendu"]<=30) & (df["yWendu"]>=15) & (df["tianqi"]=='晴') & (df["aqiLevel"]==1)
	================================================>
	ymd
	2018-01-01    False
	2018-01-02    False
	2018-01-03    False
	2018-01-04    False
	2018-01-05    False
	              ...  
	2018-12-27    False
	2018-12-28    False
	2018-12-29    False
	2018-12-30    False
	2018-12-31    False
	Length: 365, dtype: bool

	## 5、调用函数查询

	# 直接写lambda表达式
	df.loc[lambda df : (df["bWendu"]<=30) & (df["yWendu"]>=15), :]
	================================================>
		bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-04-28	27	17	晴	西南风	3-4级	125	轻度污染	3
	2018-04-29	30	16	多云	南风	3-4级	193	中度污染	4
	2018-05-04	27	16	晴~多云	西南风	1-2级	86	良	2
	2018-05-09	29	17	晴~多云	西南风	3-4级	79	良	2
	2018-05-10	26	18	多云	南风	3-4级	118	轻度污染	3
	...	...	...	...	...	...	...	...	...
	2018-09-15	26	15	多云	北风	3-4级	42	优	1
	2018-09-17	27	17	多云~阴	北风	1-2级	37	优	1
	2018-09-18	25	17	阴~多云	西南风	1-2级	50	优	1
	2018-09-19	26	17	多云	南风	1-2级	52	良	2
	2018-09-20	27	16	多云	西南风	1-2级	63	良	2
	64 rows × 8 columns

	# 编写自己的函数，查询9月份，空气质量好的数据
	def query_my_data(df):
	    return df.index.str.startswith("2018-09") & (df["aqiLevel"]==1)
	    
	df.loc[query_my_data, :]

		bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	ymd								
	2018-09-01	27	19	阴~小雨	南风	1-2级	50	优	1
	2018-09-04	31	18	晴	西南风	3-4级	24	优	1
	2018-09-05	31	19	晴~多云	西南风	3-4级	34	优	1
	2018-09-06	27	18	多云~晴	西北风	4-5级	37	优	1
	2018-09-07	27	16	晴	西北风	3-4级	22	优	1
	2018-09-08	27	15	多云~晴	北风	1-2级	28	优	1
	2018-09-15	26	15	多云	北风	3-4级	42	优	1
	2018-09-16	25	14	多云~晴	北风	1-2级	29	优	1
	2018-09-17	27	17	多云~阴	北风	1-2级	37	优	1
	2018-09-18	25	17	阴~多云	西南风	1-2级	50	优	1
	2018-09-21	25	14	晴	西北风	3-4级	50	优	1
	2018-09-22	24	13	晴	西北风	3-4级	28	优	1
	2018-09-23	23	12	晴	西北风	4-5级	28	优	1
	2018-09-24	23	11	晴	北风	1-2级	28	优	1
	2018-09-25	24	12	晴~多云	南风	1-2级	44	优	1
	2018-09-29	22	11	晴	北风	3-4级	21	优	1
	2018-09-30	19	13	多云	西北风	4-5级	22	优	1

Pandas 新增数据列
**********************************************************************************

直接赋值、apply、assign、分条件赋值

.. code-block:: python

	## Pandas怎样新增数据列？
	# 在进行数据分析时，经常需要按照一定条件创建新的数据列，然后进行进一步分析。

	# 1. 直接赋值
	# 2. df.apply方法
	# 3. df.assign方法
	# 4. 按条件选择分组分别赋值

	import pandas as pd

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)

	df.head()
	========>
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1
	3	2018-01-04	0℃	-8℃	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3℃	-6℃	多云~晴	西北风	1-2级	50	优	1

	### 1、直接赋值的方法  
	# 实例：清理温度列，变成数字类型

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	df.head()
	========>
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1

	# 注意，df["bWendu"]其实是一个Series，后面的减法返回的是Series
	df.loc[:, "wencha"] = df["bWendu"] - df["yWendu"]

	df.head()
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wencha
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	9
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	7
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	7
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	8
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	9

	### 2、df.apply方法

	# Apply a function along an axis of the DataFrame.

	# Objects passed to the function are Series objects whose index is either the DataFrame’s index (axis=0) or the DataFrame’s columns (axis=1). 

	# 实例：添加一列温度类型：  
	# 1. 如果最高温度大于33度就是高温
	# 2. 低于-10度是低温
	# 3. 否则是常温
	def get_wendu_type(x):
	    if x["bWendu"] > 33:
	        return '高温'
	    if x["yWendu"] < -10:
	        return '低温'
	    return '常温'

	# 注意需要设置axis==1，这是series的index是columns
	df.loc[:, "wendu_type"] = df.apply(get_wendu_type, axis=1)

	df
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wencha	wendu_type
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	9	常温
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	7	常温
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	7	常温
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	8	常温
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	9	常温
	...	...	...	...	...	...	...	...	...	...	...	...
	360	2018-12-27	-5	-12	多云~晴	西北风	3级	48	优	1	7	低温
	361	2018-12-28	-3	-11	晴	西北风	3级	40	优	1	8	低温
	362	2018-12-29	-3	-12	晴	西北风	2级	29	优	1	9	低温
	363	2018-12-30	-2	-11	晴~多云	东北风	1级	31	优	1	9	低温
	364	2018-12-31	-2	-10	多云	东北风	1级	56	良	2	8	常温

	# 查看温度类型的计数
	df["wendu_type"].value_counts()
	常温    328
	高温     29
	低温      8
	Name: wendu_type, dtype: int64

	### 3、df.assign方法
	# Assign new columns to a DataFrame.
	# Returns a new object with all original columns in addition to new ones. 

	# 实例：将温度从摄氏度变成华氏度

	# 可以同时添加多个新的列
	df.assign(
	    yWendu_huashi = lambda x : x["yWendu"] * 9 / 5 + 32,
	    # 摄氏度转华氏度
	    bWendu_huashi = lambda x : x["bWendu"] * 9 / 5 + 32
	)
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wencha	wendu_type	yWendu_huashi	bWendu_huashi
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	9	常温	21.2	37.4
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	7	常温	23.0	35.6
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	7	常温	23.0	35.6
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	8	常温	17.6	32.0
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	9	常温	21.2	37.4
	...	...	...	...	...	...	...	...	...	...	...	...	...	...
	360	2018-12-27	-5	-12	多云~晴	西北风	3级	48	优	1	7	低温	10.4	23.0
	361	2018-12-28	-3	-11	晴	西北风	3级	40	优	1	8	低温	12.2	26.6
	362	2018-12-29	-3	-12	晴	西北风	2级	29	优	1	9	低温	10.4	26.6
	363	2018-12-30	-2	-11	晴~多云	东北风	1级	31	优	1	9	低温	12.2	28.4
	364	2018-12-31	-2	-10	多云	东北风	1级	56	良	2	8	常温	14.0	28.4

	### 4、按条件选择分组分别赋值
	# 按条件先选择数据，然后对这部分数据赋值新列  
	# 实例：高低温差大于10度，则认为温差大

	# 先创建空列（这是第一种创建新列的方法）
	df['wencha_type'] = ''
	df.loc[df["bWendu"]-df["yWendu"]>10, "wencha_type"] = "温差大"
	df.loc[df["bWendu"]-df["yWendu"]<=10, "wencha_type"] = "温差正常"

	df["wencha_type"].value_counts()
	温差正常    187
	温差大     178
	Name: wencha_type, dtype: int64

Pandas 数据统计函数
**********************************************************************************

平均值、最大值、最小值、去重数、协方差、相关系数

.. code-block:: python

	## Pandas数据统计函数

	import pandas as pd

	### 0、读取csv数据
	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)

	df.head(3)
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	df.head(3)
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1

	### 1、汇总类统计
	# 一下子提取所有数字列统计结果
	df.describe()

	bWendu	yWendu	aqi	aqiLevel
	count	365.000000	365.000000	365.000000	365.000000
	mean	18.665753	8.358904	82.183562	2.090411
	std	11.858046	11.755053	51.936159	1.029798
	min	-5.000000	-12.000000	21.000000	1.000000
	25%	8.000000	-3.000000	46.000000	1.000000
	50%	21.000000	8.000000	69.000000	2.000000
	75%	29.000000	19.000000	104.000000	3.000000
	max	38.000000	27.000000	387.000000	6.000000

	## 查看单个Series的数据
	df["bWendu"].mean()
	18.665753424657535

	# 最高温
	df["bWendu"].max()
	38

	# 最低温
	df["bWendu"].min()
	-5

	### 2、唯一去重和按值计数
	#### 2.1 唯一性去重
	# 一般不用于数值列，而是枚举、分类列

	df["fengxiang"].unique()
	array(['东北风', '北风', '西北风', '西南风', '南风', '东南风', '东风', '西风'], dtype=object)

	df["tianqi"].unique()
	array(['晴~多云', '阴~多云', '多云', '阴', '多云~晴', '多云~阴', '晴', '阴~小雪', '小雪~多云',
	       '小雨~阴', '小雨~雨夹雪', '多云~小雨', '小雨~多云', '大雨~小雨', '小雨', '阴~小雨',
	       '多云~雷阵雨', '雷阵雨~多云', '阴~雷阵雨', '雷阵雨', '雷阵雨~大雨', '中雨~雷阵雨', '小雨~大雨',
	       '暴雨~雷阵雨', '雷阵雨~中雨', '小雨~雷阵雨', '雷阵雨~阴', '中雨~小雨', '小雨~中雨', '雾~多云',
	       '霾'], dtype=object)

	df["fengli"].unique()
	array(['1-2级', '4-5级', '3-4级', '2级', '1级', '3级'], dtype=object)

	#### 2.2 按值计数
	df["fengxiang"].value_counts()
	南风     92
	西南风    64
	北风     54
	西北风    51
	东南风    46
	东北风    38
	东风     14
	西风      6
	Name: fengxiang, dtype: int64

	df["tianqi"].value_counts()
	晴         101
	多云         95
	多云~晴       40
	晴~多云       34
	多云~雷阵雨     14
	多云~阴       10
	小雨~多云       8
	阴~多云        8
	雷阵雨         8
	雷阵雨~多云      7
	小雨          6
	多云~小雨       5
	阴           4
	雷阵雨~中雨      4
	霾           2
	中雨~雷阵雨      2
	阴~小雨        2
	中雨~小雨       2
	小雪~多云       1
	大雨~小雨       1
	小雨~阴        1
	雾~多云        1
	小雨~大雨       1
	小雨~中雨       1
	阴~雷阵雨       1
	小雨~雨夹雪      1
	雷阵雨~阴       1
	阴~小雪        1
	暴雨~雷阵雨      1
	小雨~雷阵雨      1
	雷阵雨~大雨      1
	Name: tianqi, dtype: int64

	df["fengli"].value_counts()
	1-2级    236
	3-4级     68
	1级       21
	4-5级     20
	2级       13
	3级        7
	Name: fengli, dtype: int64

	### 3、相关系数和协方差

	# 用途（超级厉害）：
	# 1. 两只股票，是不是同涨同跌？程度多大？正相关还是负相关？
	# 2. 产品销量的波动，跟哪些因素正相关、负相关，程度有多大？

	# 来自知乎，对于两个变量X、Y：
	# 1. 协方差：***衡量同向反向程度***，如果协方差为正，说明X，Y同向变化，协方差越大说明同向程度越高；如果协方差为负，说明X，Y反向运动，协方差越小说明反向程度越高。
	# 2. 相关系数：***衡量相似度程度***，当他们的相关系数为1时，说明两个变量变化时的正向相似度最大，当相关系数为－1时，说明两个变量变化的反向相似度最大

	# 协方差矩阵：
	df.cov()
		bWendu	yWendu	aqi	aqiLevel
	bWendu	140.613247	135.529633	47.462622	0.879204
	yWendu	135.529633	138.181274	16.186685	0.264165
	aqi	47.462622	16.186685	2697.364564	50.749842
	aqiLevel	0.879204	0.264165	50.749842	1.060485

	# 相关系数矩阵
	df.corr()
		bWendu	yWendu	aqi	aqiLevel
	bWendu	1.000000	0.972292	0.077067	0.071999
	yWendu	0.972292	1.000000	0.026513	0.021822
	aqi	0.077067	0.026513	1.000000	0.948883
	aqiLevel	0.071999	0.021822	0.948883	1.000000

	# 单独查看空气质量和最高温度的相关系数
	df["aqi"].corr(df["bWendu"])
	0.07706705916811069

	df["aqi"].corr(df["yWendu"])
	0.02651328267296889

	# 空气质量和温差的相关系数
	df["aqi"].corr(df["bWendu"]-df["yWendu"])
	0.2165225757638205

.. |image0| image:: /_static/python/WX20201028-225251@2x.webp












































































































































