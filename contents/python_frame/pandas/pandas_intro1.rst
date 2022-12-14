Pandas 高级篇 1
##################################################################################

| 相关代码地址: https://github.com/lilizhaolilizhao/ant-learn-pandas.git

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

|image00|

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

	#### 1.3 使用 Python 字典创建 Series
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
	# 类似 Python 的字典 dict
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
	# DataFrame 是一个表格型的数据结构
	# * 每列可以是不同的值类型（数值、字符串、布尔值等）
	# * 既有行索引 index, 也有列索引 columns
	# * 可以被看做由 Series 组成的字典

	# 创建 dataframe 最常用的方法，见 02 节读取纯文本文件、excel、mysql 数据库

	#### 2.1 根据多个字典序列创建 dataframe
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

Pandas 对缺失值的处理
**********************************************************************************

.. code-block:: python

	# Pandas使用这些函数处理缺失值：
	# * isnull和notnull：检测是否是空值，可用于df和series
	# * dropna：丢弃、删除缺失值
	#   - axis : 删除行还是列，{0 or ‘index’, 1 or ‘columns’}, default 0
	#   - how : 如果等于any则任何值为空都删除，如果等于all则所有值都为空才删除
	#   - inplace : 如果为True则修改当前df，否则返回新的df
	# * fillna：填充空值
	#   - value：用于填充的值，可以是单个值，或者字典（key是列名，value是值）
	#   - method : 等于ffill使用前一个不为空的值填充forword fill；等于bfill使用后一个不为空的值填充backword fill
	#   - axis : 按行还是列填充，{0 or ‘index’, 1 or ‘columns’}
	#   - inplace : 如果为True则修改当前df，否则返回新的df

	import pandas as pd

	### 实例：特殊Excel的读取、清洗、处理
	#### 步骤1：读取excel的时候，忽略前几个空行

	studf = pd.read_excel("./datas/student_excel/student_excel.xlsx", skiprows=2)
	studf
	===========>
	Unnamed: 0	姓名	科目	分数
	0	NaN	小明	语文	85.0
	1	NaN	NaN	数学	80.0
	2	NaN	NaN	英语	90.0
	3	NaN	NaN	NaN	NaN
	4	NaN	小王	语文	85.0
	5	NaN	NaN	数学	NaN
	6	NaN	NaN	英语	90.0
	7	NaN	NaN	NaN	NaN
	8	NaN	小刚	语文	85.0
	9	NaN	NaN	数学	80.0
	10	NaN	NaN	英语	90.0

	#### 步骤2：检测空值
	studf.isnull()
		Unnamed: 0	姓名	科目	分数
	0	True	False	False	False
	1	True	True	False	False
	2	True	True	False	False
	3	True	True	True	True
	4	True	False	False	False
	5	True	True	False	True
	6	True	True	False	False
	7	True	True	True	True
	8	True	False	False	False
	9	True	True	False	False
	10	True	True	False	False

	studf["分数"].isnull()
	0     False
	1     False
	2     False
	3      True
	4     False
	5      True
	6     False
	7      True
	8     False
	9     False
	10    False
	Name: 分数, dtype: bool

	studf["分数"].notnull()
	0      True
	1      True
	2      True
	3     False
	4      True
	5     False
	6      True
	7     False
	8      True
	9      True
	10     True
	Name: 分数, dtype: bool

	# 筛选没有空分数的所有行
	studf.loc[studf["分数"].notnull(), :]
		Unnamed: 0	姓名	科目	分数
	0	NaN	小明	语文	85.0
	1	NaN	NaN	数学	80.0
	2	NaN	NaN	英语	90.0
	4	NaN	小王	语文	85.0
	6	NaN	NaN	英语	90.0
	8	NaN	小刚	语文	85.0
	9	NaN	NaN	数学	80.0
	10	NaN	NaN	英语	90.0

	#### 步骤3：删除掉全是空值的列
	studf.dropna(axis="columns", how='all', inplace=True)

	#### 步骤4：删除掉全是空值的行
	studf.dropna(axis="index", how='all', inplace=True)

	studf
	姓名	科目	分数
	0	小明	语文	85.0
	1	NaN	数学	80.0
	2	NaN	英语	90.0
	4	小王	语文	85.0
	5	NaN	数学	NaN
	6	NaN	英语	90.0
	8	小刚	语文	85.0
	9	NaN	数学	80.0
	10	NaN	英语	90.0

	### 步骤5：将分数列为空的填充为0分
	studf.fillna({"分数":0})
		姓名	科目	分数
	0	小明	语文	85.0
	1	NaN	数学	80.0
	2	NaN	英语	90.0
	4	小王	语文	85.0
	5	NaN	数学	0.0
	6	NaN	英语	90.0
	8	小刚	语文	85.0
	9	NaN	数学	80.0
	10	NaN	英语	90.0

	# 等同于
	studf.loc[:, '分数'] = studf['分数'].fillna(0)

	### 步骤6：将姓名的缺失值填充
	# 使用前面的有效值填充，用ffill：forward fill
	studf.loc[:, '姓名'] = studf['姓名'].fillna(method="ffill")

	studf
		姓名	科目	分数
	0	小明	语文	85.0
	1	小明	数学	80.0
	2	小明	英语	90.0
	4	小王	语文	85.0
	5	小王	数学	0.0
	6	小王	英语	90.0
	8	小刚	语文	85.0
	9	小刚	数学	80.0
	10	小刚	英语	90.0

	### 步骤7：将清洗好的excel保存
	studf.to_excel("./datas/student_excel/student_excel_clean.xlsx", index=False)

Pandas 的 SettingWithCopyWarning 报警
**********************************************************************************

.. code-block:: python

	## Pandas的SettingWithCopyWarning报警

	### 0、读取数据
	import pandas as pd
	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)

	df.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1
	3	2018-01-04	0℃	-8℃	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3℃	-6℃	多云~晴	西北风	1-2级	50	优	1

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	df.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1

	### 1、复现
	# 只选出3月份的数据用于分析
	condition = df["ymd"].str.startswith("2018-03")

	# 设置温差
	df[condition]["wen_cha"] = df["bWendu"]-df["yWendu"]
	A value is trying to be set on a copy of a slice from a DataFrame.
	Try using .loc[row_indexer,col_indexer] = value instead

	# 查看是否修改成功
	df[condition].head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	59	2018-03-01	8	-3	多云	西南风	1-2级	46	优	1
	60	2018-03-02	9	-1	晴~多云	北风	1-2级	95	良	2
	61	2018-03-03	13	3	多云~阴	北风	1-2级	214	重度污染	5
	62	2018-03-04	7	-2	阴~多云	东南风	1-2级	144	轻度污染	3
	63	2018-03-05	8	-3	晴	南风	1-2级	94	良	2

	### 2、原因
	# 发出警告的代码
	df[condition]["wen_cha"] = df["bWendu"]-df["yWendu"]
	# 相当于：df.get(condition).set(wen_cha)，第一步骤的get发出了报警

	# ***链式操作其实是两个步骤，先get后set，get得到的dataframe可能是view也可能是copy，pandas发出警告***

	# 官网文档：
	# https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy

	# 核心要诀：pandas的dataframe的修改写操作，只允许在源dataframe上进行，一步到位

	### 3、解决方法1
	# 将get+set的两步操作，改成set的一步操作
	df.loc[condition, "wen_cha"] = df["bWendu"]-df["yWendu"]

	df[condition].head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wen_cha
	59	2018-03-01	8	-3	多云	西南风	1-2级	46	优	1	11.0
	60	2018-03-02	9	-1	晴~多云	北风	1-2级	95	良	2	10.0
	61	2018-03-03	13	3	多云~阴	北风	1-2级	214	重度污染	5	10.0
	62	2018-03-04	7	-2	阴~多云	东南风	1-2级	144	轻度污染	3	9.0
	63	2018-03-05	8	-3	晴	南风	1-2级	94	良	2	11.0

	### 4、解决方法2
	# 如果需要预筛选数据做后续的处理分析，使用copy复制dataframe
	df_month3 = df[condition].copy()

	df_month3.head()
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wen_cha
	59	2018-03-01	8	-3	多云	西南风	1-2级	46	优	1	11.0
	60	2018-03-02	9	-1	晴~多云	北风	1-2级	95	良	2	10.0
	61	2018-03-03	13	3	多云~阴	北风	1-2级	214	重度污染	5	10.0
	62	2018-03-04	7	-2	阴~多云	东南风	1-2级	144	轻度污染	3	9.0
	63	2018-03-05	8	-3	晴	南风	1-2级	94	良	2	11.0

	df_month3["wen_cha"] = df["bWendu"]-df["yWendu"]

	df_month3.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	wen_cha
	59	2018-03-01	8	-3	多云	西南风	1-2级	46	优	1	11
	60	2018-03-02	9	-1	晴~多云	北风	1-2级	95	良	2	10
	61	2018-03-03	13	3	多云~阴	北风	1-2级	214	重度污染	5	10
	62	2018-03-04	7	-2	阴~多云	东南风	1-2级	144	轻度污染	3	9
	63	2018-03-05	8	-3	晴	南风	1-2级	94	良	2	11

	# ***总之，pandas不允许先筛选子dataframe，再进行修改写入***  
	# 要么使用.loc实现一个步骤直接修改源dataframe  
	# 要么先复制一个子dataframe再一个步骤执行修改

Pandas 数据排序
**********************************************************************************

.. code-block:: python

	## Pandas数据排序

	# Series的排序：  
	# ***Series.sort_values(ascending=True, inplace=False)***  
	# 参数说明：
	# * ascending：默认为True升序排序，为False降序排序
	# * inplace：是否修改原始Series

	# DataFrame的排序：  
	# ***DataFrame.sort_values(by, ascending=True, inplace=False)***  
	# 参数说明：
	# * by：字符串或者List<字符串>，单列排序或者多列排序
	# * ascending：bool或者List<bool>，升序还是降序，如果是list对应by的多列
	# * inplace：是否修改原始DataFrame

	import pandas as pd
	### 0、读取数据
	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	df.head()
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1

	### 1、Series的排序
	df["aqi"].sort_values()
	271     21
	281     21
	249     22
	272     22
	301     22
	      ... 
	317    266
	71     287
	91     287
	72     293
	86     387
	Name: aqi, Length: 365, dtype: int64

	df["aqi"].sort_values(ascending=False)
	86     387
	72     293
	91     287
	71     287
	317    266
	      ... 
	301     22
	272     22
	249     22
	281     21
	271     21
	Name: aqi, Length: 365, dtype: int64

	df["tianqi"].sort_values()
	225     中雨~小雨
	230     中雨~小雨
	197    中雨~雷阵雨
	196    中雨~雷阵雨
	112        多云
	        ...  
	191    雷阵雨~大雨
	219     雷阵雨~阴
	335      雾~多云
	353         霾
	348         霾
	Name: tianqi, Length: 365, dtype: object

	### 2、DataFrame的排序

	#### 2.1 单列排序
	df.sort_values(by="aqi")
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	271	2018-09-29	22	11	晴	北风	3-4级	21	优	1
	281	2018-10-09	15	4	多云~晴	西北风	4-5级	21	优	1
	249	2018-09-07	27	16	晴	西北风	3-4级	22	优	1
	272	2018-09-30	19	13	多云	西北风	4-5级	22	优	1
	301	2018-10-29	15	3	晴	北风	3-4级	22	优	1
	...	...	...	...	...	...	...	...	...	...
	317	2018-11-14	13	5	多云	南风	1-2级	266	重度污染	5
	71	2018-03-13	17	5	晴~多云	南风	1-2级	287	重度污染	5
	91	2018-04-02	26	11	多云	北风	1-2级	287	重度污染	5
	72	2018-03-14	15	6	多云~阴	东北风	1-2级	293	重度污染	5
	86	2018-03-28	25	9	多云~晴	东风	1-2级	387	严重污染	6
	365 rows × 9 columns

	df.sort_values(by="aqi", ascending=False)
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	86	2018-03-28	25	9	多云~晴	东风	1-2级	387	严重污染	6
	72	2018-03-14	15	6	多云~阴	东北风	1-2级	293	重度污染	5
	71	2018-03-13	17	5	晴~多云	南风	1-2级	287	重度污染	5
	91	2018-04-02	26	11	多云	北风	1-2级	287	重度污染	5
	317	2018-11-14	13	5	多云	南风	1-2级	266	重度污染	5
	...	...	...	...	...	...	...	...	...	...
	249	2018-09-07	27	16	晴	西北风	3-4级	22	优	1
	301	2018-10-29	15	3	晴	北风	3-4级	22	优	1
	272	2018-09-30	19	13	多云	西北风	4-5级	22	优	1
	271	2018-09-29	22	11	晴	北风	3-4级	21	优	1
	281	2018-10-09	15	4	多云~晴	西北风	4-5级	21	优	1
	365 rows × 9 columns

	#### 2.2 多列排序
	# 按空气质量等级、最高温度排序，默认升序
	df.sort_values(by=["aqiLevel", "bWendu"])
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	360	2018-12-27	-5	-12	多云~晴	西北风	3级	48	优	1
	22	2018-01-23	-4	-12	晴	西北风	3-4级	31	优	1
	23	2018-01-24	-4	-11	晴	西南风	1-2级	34	优	1
	340	2018-12-07	-4	-10	晴	西北风	3级	33	优	1
	21	2018-01-22	-3	-10	小雪~多云	东风	1-2级	47	优	1
	...	...	...	...	...	...	...	...	...	...
	71	2018-03-13	17	5	晴~多云	南风	1-2级	287	重度污染	5
	90	2018-04-01	25	11	晴~多云	南风	1-2级	218	重度污染	5
	91	2018-04-02	26	11	多云	北风	1-2级	287	重度污染	5
	85	2018-03-27	27	11	晴	南风	1-2级	243	重度污染	5
	86	2018-03-28	25	9	多云~晴	东风	1-2级	387	严重污染	6
	365 rows × 9 columns

	# 两个字段都是降序
	df.sort_values(by=["aqiLevel", "bWendu"], ascending=False)
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	86	2018-03-28	25	9	多云~晴	东风	1-2级	387	严重污染	6
	85	2018-03-27	27	11	晴	南风	1-2级	243	重度污染	5
	91	2018-04-02	26	11	多云	北风	1-2级	287	重度污染	5
	90	2018-04-01	25	11	晴~多云	南风	1-2级	218	重度污染	5
	71	2018-03-13	17	5	晴~多云	南风	1-2级	287	重度污染	5
	...	...	...	...	...	...	...	...	...	...
	362	2018-12-29	-3	-12	晴	西北风	2级	29	优	1
	22	2018-01-23	-4	-12	晴	西北风	3-4级	31	优	1
	23	2018-01-24	-4	-11	晴	西南风	1-2级	34	优	1
	340	2018-12-07	-4	-10	晴	西北风	3级	33	优	1
	360	2018-12-27	-5	-12	多云~晴	西北风	3级	48	优	1

	# 分别指定升序和降序
	df.sort_values(by=["aqiLevel", "bWendu"], ascending=[True, False])
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	178	2018-06-28	35	24	多云~晴	北风	1-2级	33	优	1
	149	2018-05-30	33	18	晴	西风	1-2级	46	优	1
	206	2018-07-26	33	25	多云~雷阵雨	东北风	1-2级	40	优	1
	158	2018-06-08	32	19	多云~雷阵雨	西南风	1-2级	43	优	1
	205	2018-07-25	32	25	多云	北风	1-2级	28	优	1
	...	...	...	...	...	...	...	...	...	...
	317	2018-11-14	13	5	多云	南风	1-2级	266	重度污染	5
	329	2018-11-26	10	0	多云	东南风	1级	245	重度污染	5
	335	2018-12-02	9	2	雾~多云	东北风	1级	234	重度污染	5
	57	2018-02-27	7	0	阴	东风	1-2级	220	重度污染	5
	86	2018-03-28	25	9	多云~晴	东风	1-2级	387	严重污染	6

Pandas 字符串处理
**********************************************************************************

.. code-block:: python

	# 前面我们已经使用了字符串的处理函数：  
	# df["bWendu"].str.replace("℃", "").astype('int32')

	# ***Pandas的字符串处理：***  
	# 1. 使用方法：先获取Series的str属性，然后在属性上调用函数；
	# 2. 只能在字符串列上使用，不能数字列上使用；
	# 3. Dataframe上没有str属性和处理方法
	# 4. Series.str并不是Python原生字符串，而是自己的一套方法，不过大部分和原生str很相似；

	# ***Series.str字符串方法列表参考文档:***  
	# https://pandas.pydata.org/pandas-docs/stable/reference/series.html#string-handling
	  
	  
	# ***本节演示内容：***  
	# 1. 获取Series的str属性，然后使用各种字符串处理函数
	# 2. 使用str的startswith、contains等bool类Series可以做条件查询
	# 3. 需要多次str处理的链式操作
	# 4. 使用正则表达式的处理

	### 0、读取北京2018年天气数据
	import pandas as pd

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)

	df.head()
		ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	0	2018-01-01	3℃	-6℃	晴~多云	东北风	1-2级	59	良	2
	1	2018-01-02	2℃	-5℃	阴~多云	东北风	1-2级	49	优	1
	2	2018-01-03	2℃	-5℃	多云	北风	1-2级	28	优	1
	3	2018-01-04	0℃	-8℃	阴	东北风	1-2级	28	优	1
	4	2018-01-05	3℃	-6℃	多云~晴	西北风	1-2级	50	优	1

	df.dtypes
	ymd          object
	bWendu       object
	yWendu       object
	tianqi       object
	fengxiang    object
	fengli       object
	aqi           int64
	aqiInfo      object
	aqiLevel      int64
	dtype: object

	### 1、获取Series的str属性，使用各种字符串处理函数
	df["bWendu"].str
	<pandas.core.strings.StringMethods at 0x1205c5710>

	# 字符串替换函数
	df["bWendu"].str.replace("℃", "")
	0       3
	1       2
	2       2
	3       0
	4       3
	       ..
	360    -5
	361    -3
	362    -3
	363    -2
	364    -2
	Name: bWendu, Length: 365, dtype: object

	# 判断是不是数字
	df["bWendu"].str.isnumeric()
	0      False
	1      False
	2      False
	3      False
	4      False
	       ...  
	360    False
	361    False
	362    False
	363    False
	364    False
	Name: bWendu, Length: 365, dtype: bool

	df["aqi"].str.len()

	### 2、使用str的startswith、contains等得到bool的Series可以做条件查询
	condition = df["ymd"].str.startswith("2018-03")

	condition
	0      False
	1      False
	2      False
	3      False
	4      False
	       ...  
	360    False
	361    False
	362    False
	363    False
	364    False
	Name: ymd, Length: 365, dtype: bool

	df[condition].head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel
	59	2018-03-01	8℃	-3℃	多云	西南风	1-2级	46	优	1
	60	2018-03-02	9℃	-1℃	晴~多云	北风	1-2级	95	良	2
	61	2018-03-03	13℃	3℃	多云~阴	北风	1-2级	214	重度污染	5
	62	2018-03-04	7℃	-2℃	阴~多云	东南风	1-2级	144	轻度污染	3
	63	2018-03-05	8℃	-3℃	晴	南风	1-2级	94	良	2

	### 3、需要多次str处理的链式操作
	# 怎样提取201803这样的数字月份？  
	# 1、先将日期2018-03-31替换成20180331的形式  
	# 2、提取月份字符串201803  

	df["ymd"].str.replace("-", "")
	0      20180101
	1      20180102
	2      20180103
	3      20180104
	4      20180105
	         ...   
	360    20181227
	361    20181228
	362    20181229
	363    20181230
	364    20181231
	Name: ymd, Length: 365, dtype: object

	# 每次调用函数，都返回一个新Series
	df["ymd"].str.replace("-", "").slice(0, 6)
	df["ymd"].str.replace("-", "").str.slice(0, 6)
	0      201801
	1      201801
	2      201801
	3      201801
	4      201801
	        ...  
	360    201812
	361    201812
	362    201812
	363    201812
	364    201812
	Name: ymd, Length: 365, dtype: object

	# slice就是切片语法，可以直接用
	df["ymd"].str.replace("-", "").str[0:6]
	0      201801
	1      201801
	2      201801
	3      201801
	4      201801
	        ...  
	360    201812
	361    201812
	362    201812
	363    201812
	364    201812
	Name: ymd, Length: 365, dtype: object

Pandas 的 axis 参数怎么理解
**********************************************************************************

.. code-block:: python

	# ## Pandas的axis参数怎么理解？

	# * axis=0或者"index"：  
	#   - 如果是单行操作，就指的是某一行
	#   - 如果是聚合操作，指的是跨行cross rows
	# * axis=1或者"columns"：
	#   - 如果是单列操作，就指的是某一列
	#   - 如果是聚合操作，指的是跨列cross columns

	# ***按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动***

	%matplotlib inline
	import pandas as pd
	import numpy as np

	df = pd.DataFrame(
	    np.arange(12).reshape(3,4),
	    columns=['A', 'B', 'C', 'D']
	)

	df
	A	B	C	D
	0	0	1	2	3
	1	4	5	6	7
	2	8	9	10	11

	### 1、单列drop，就是删除某一列
	# 代表的就是删除某列
	df.drop("A", axis=1)
	    B	C	D
	0	1	2	3
	1	5	6	7
	2	9	10	11

	### 2、单行drop，就是删除某一行
	df
		A	B	C	D
	0	0	1	2	3
	1	4	5	6	7
	2	8	9	10	11

	# 代表的就是删除某行
	df.drop(1, axis=0)
		A	B	C	D
	0	0	1	2	3
	2	8	9	10	11

	### 3、按axis=0/index执行mean聚合操作
	# 反直觉：输出的不是每行的结果，而是每列的结果
	df
	    A	B	C	D
	0	0	1	2	3
	1	4	5	6	7
	2	8	9	10	11

	# axis=0 or axis=index
	df.mean(axis=0)
	A    4.0
	B    5.0
	C    6.0
	D    7.0
	dtype: float64

	axis=0 或者 axis=index

	不是得到的是每行的结果, 而是代表按行处理、跨行 cross row 的意思

	col_data
	for row in rows:
	    col_data = row.sum/row.count

比喻：就像一把梳子往下梳

|image0|

.. code-block:: python

	# ***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动***
	### 4、按axis=1/columns执行mean聚合操作
	# 反直觉：输出的不是每行的结果，而是每列的结果

	df
	    A	B	C	D
	0	0	1	2	3
	1	4	5	6	7
	2	8	9	10	11

	# axis=1 or axis=columns
	df.mean(axis=1)
	0    1.5
	1    5.5
	2    9.5
	dtype: float64

|image1|

.. code-block:: python

	# ***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动***
	### 5、再次举例，加深理解
	def get_sum_value(x):
	    return x["A"] + x["B"] + x["C"] + x["D"]

	df["sum_value"] = df.apply(get_sum_value, axis=1)

	    A	B	C	D	sum_value
	0	0	1	2	3	6
	1	4	5	6	7	22
	2	8	9	10	11	38

	# ***指定了按哪个axis，就是这个axis要动起来(类似被for遍历)，其它的axis保持不动***

Pandas 的索引 index 的用途
**********************************************************************************

.. code-block:: python

	# 把数据存储于普通的column列也能用于数据查询，那使用index有什么好处？

	# index 的用途总结：  
	# 1. 更方便的数据查询；
	# 2. 使用 index 可以获得性能提升；
	# 3. 自动的数据对齐功能；
	# 4. 更多更强大的数据结构支持；

	import pandas as pd
	df = pd.read_csv("./datas/ml-latest-small/ratings.csv")

	df.head()
		userId	movieId	rating	timestamp
	0	1	1	4.0	964982703
	1	1	3	4.0	964981247
	2	1	6	4.0	964982224
	3	1	47	5.0	964983815
	4	1	50	5.0	964982931

	df.count()
	userId       100836
	movieId      100836
	rating       100836
	timestamp    100836
	dtype: int64

	## 1、使用index查询数据

	# drop==False，让索引列还保持在column
	df.set_index("userId", inplace=True, drop=False)

	df.head()
	userId userId movieId rating timestamp			
	1	1	1	4.0	964982703
	1	1	3	4.0	964981247
	1	1	6	4.0	964982224
	1	1	47	5.0	964983815
	1	1	50	5.0	964982931

	df.index
	Int64Index([  1,   1,   1,   1,   1,   1,   1,   1,   1,   1,
	            ...
	            610, 610, 610, 610, 610, 610, 610, 610, 610, 610],
	           dtype='int64', name='userId', length=100836)

	# 使用 index 的查询方法
	df.loc[500].head(5)
		userId	movieId	rating	timestamp
	userId				
	500	500	1	4.0	1005527755
	500	500	11	1.0	1005528017
	500	500	39	1.0	1005527926
	500	500	101	1.0	1005527980
	500	500	104	4.0	1005528065

	# 使用 column 的 condition 查询方法
	df.loc[df["userId"] == 500].head()
		userId	movieId	rating	timestamp
	userId				
	500	500	1	4.0	1005527755
	500	500	11	1.0	1005528017
	500	500	39	1.0	1005527926
	500	500	101	1.0	1005527980
	500	500	104	4.0	1005528065

	# ## 2. 使用index会提升查询性能

	# * 如果 index 是唯一的，Pandas 会使用哈希表优化，查询性能为 O(1);
	# * 如果 index 不是唯一的，但是有序，Pandas会使用二分查找算法，查询性能为 O(logN);
	# * 如果 index 是完全随机的，那么每次查询都要扫描全表，查询性能为O(N);

	### 实验1：完全随机的顺序查询
	# 将数据随机打散
	from sklearn.utils import shuffle
	df_shuffle = shuffle(df)

	df_shuffle.head()
		userId movieId rating timestamp
	userId 	
	274	274	5944	1.0	1171759788
	156	156	6297	4.0	1106882124
	177	177	3564	1.0	1435525801
	217	217	1445	1.0	955945503
	430	430	2396	5.0	962936613

	# 索引是否是递增的
	df_shuffle.index.is_monotonic_increasing
	False

	df_shuffle.index.is_unique
	False

	# 计时，查询id==500数据性能
	%timeit df_shuffle.loc[500]
	353 µs ± 21.8 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

	### 实验2：将index排序后的查询
	df_sorted = df_shuffle.sort_index()

	df_sorted.head()
		userId	movieId	rating	timestamp
	userId				
	1	1	1060	4.0	964980924
	1	1	2389	2.0	964983094
	1	1	1196	5.0	964981827
	1	1	2450	4.0	964982620
	1	1	356	4.0	964980962

	# 索引是否是递增的
	df_sorted.index.is_monotonic_increasing
	True

	## 2. 使用index会提升查询性能

	# * 如果index是唯一的，Pandas会使用哈希表优化，查询性能为O(1);
	# * 如果index不是唯一的，但是有序，Pandas会使用二分查找算法，查询性能为O(logN);
	# * 如果index是完全随机的，那么每次查询都要扫描全表，查询性能为O(N);

	### 实验1：完全随机的顺序查询
	# 将数据随机打散
	from sklearn.utils import shuffle
	df_shuffle = shuffle(df)

	df_shuffle.head()
		userId	movieId	rating	timestamp
	userId				
	352	352	590	5.0	1493932117
	89	89	122092	5.0	1520409152
	413	413	5574	5.0	1484440098
	176	176	161	4.0	840108983
	64	64	1060	3.5	1161565798

	# 索引是否是递增的
	df_shuffle.index.is_monotonic_increasing
	False

	df_shuffle.index.is_unique
	False

	# 计时，查询id==500数据性能
	%timeit df_shuffle.loc[500]
	397 µs ± 56.5 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

	df_sorted = df_shuffle.sort_index()
	df_sorted.head()
		userId	movieId	rating	timestamp
	userId				
	1	1	1030	3.0	964982903
	1	1	6	4.0	964982224
	1	1	2090	5.0	964982838
	1	1	1625	5.0	964983504
	1	1	2018	5.0	964980523

	# 索引是否是递增的
	df_sorted.index.is_monotonic_increasing
	True

	df_sorted.index.is_unique
	False

	%timeit df_sorted.loc[500]
	95 µs ± 31 µs per loop (mean ± std. dev. of 7 runs, 10000 loops each)

	## 3. 使用 index 能自动对齐数据

	# 包括 series 和 dataframe
	s1 = pd.Series([1,2,3], index=list("abc"))

	s1
	a    1
	b    2
	c    3
	dtype: int64

	s2 = pd.Series([2,3,4], index=list("bcd"))
	s2
	b    2
	c    3
	d    4
	dtype: int64

	s1+s2
	a    NaN
	b    4.0
	c    6.0
	d    NaN
	dtype: float64

	## 4. 使用index更多更强大的数据结构支持

	# ***很多强大的索引数据结构*** 
	# * CategoricalIndex，基于分类数据的Index，提升性能；
	# * MultiIndex，多维索引，用于groupby多维聚合后结果等；
	# * DatetimeIndex，时间类型索引，强大的日期和时间的方法支持；

Pandas 怎样实现 DataFrame 的 Merge
**********************************************************************************

.. code-block:: python

	# Pandas 的 Merge，相当于 Sql 的 Join，将不同的表按 key 关联到一个表

	# ### merge的语法：
	# pd.merge(left, right, how='inner', on=None, left_on=None, right_on=None,
	#          left_index=False, right_index=False, sort=True,
	#          suffixes=('_x', '_y'), copy=True, indicator=False,
	#          validate=None)  
	# * left，right：要merge的dataframe或者有name的Series
	# * how：join类型，'left', 'right', 'outer', 'inner'
	# * on：join的key，left和right都需要有这个key
	# * left_on：left的df或者series的key
	# * right_on：right的df或者seires的key
	# * left_index，right_index：使用index而不是普通的column做join
	# * suffixes：两个元素的后缀，如果列有重名，自动添加后缀，默认是('_x', '_y')

	# 文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.merge.html

	# 本次讲解提纲：
	# 1. 电影数据集的join实例
	# 2. 理解merge时一对一、一对多、多对多的数量对齐关系
	# 3. 理解left join、right join、inner join、outer join的区别
	# 4. 如果出现非Key的字段重名怎么办

	### 1、电影数据集的join实例

	#### 电影评分数据集

	# 是推荐系统研究的很好的数据集  
	# 位于本代码目录：./datas/movielens-1m

	# 包含三个文件：  
	# 1. 用户对电影的评分数据 ratings.dat
	# 2. 用户本身的信息数据 users.dat
	# 3. 电影本身的数据 movies.dat

	# 可以关联三个表，得到一个完整的大表

	# 数据集官方地址：https://grouplens.org/datasets/movielens/

	import pandas as pd
	df_ratings = pd.read_csv(
	    "./datas/movielens-1m/ratings.dat", 
	    sep="::",
	    engine='python', 
	    names="UserID::MovieID::Rating::Timestamp".split("::")
	)

	df_ratings
		UserID	MovieID	Rating	Timestamp
	0	1	1193	5	978300760
	1	1	661	3	978302109
	2	1	914	3	978301968
	3	1	3408	4	978300275
	4	1	2355	5	978824291
	...	...	...	...	...
	1000204	6040	1091	1	956716541
	1000205	6040	1094	5	956704887
	1000206	6040	562	5	956704746
	1000207	6040	1096	4	956715648
	1000208	6040	1097	4	956715569
	1000209 rows × 4 columns

	df_ratings.head()
	UserID	MovieID	Rating	Timestamp
	0	1	1193	5	978300760
	1	1	661	3	978302109
	2	1	914	3	978301968
	3	1	3408	4	978300275
	4	1	2355	5	978824291

	df_users = pd.read_csv(
	    "./datas/movielens-1m/users.dat", 
	    sep="::",
	    engine='python', 
	    names="UserID::Gender::Age::Occupation::Zip-code".split("::")
	)

	df_users.head()
	UserID	Gender	Age	Occupation	Zip-code
	0	1	F	1	10	48067
	1	2	M	56	16	70072
	2	3	M	25	15	55117
	3	4	M	45	7	02460
	4	5	M	25	20	55455

	df_movies = pd.read_csv(
	    "./datas/movielens-1m/movies.dat", 
	    sep="::",
	    engine='python', 
	    names="MovieID::Title::Genres".split("::")
	)

	df_movies.head()
		MovieID	Title	Genres
	0	1	Toy Story (1995)	Animation|Children's|Comedy
	1	2	Jumanji (1995)	Adventure|Children's|Fantasy
	2	3	Grumpier Old Men (1995)	Comedy|Romance
	3	4	Waiting to Exhale (1995)	Comedy|Drama
	4	5	Father of the Bride Part II (1995)	Comedy

	df_ratings_users = pd.merge(
	   df_ratings, df_users, left_on="UserID", right_on="UserID", how="inner"
	)

	df_ratings_users.head()
	UserID	MovieID	Rating	Timestamp	Gender	Age	Occupation	Zip-code
	0	1	1193	5	978300760	F	1	10	48067
	1	1	661	3	978302109	F	1	10	48067
	2	1	914	3	978301968	F	1	10	48067
	3	1	3408	4	978300275	F	1	10	48067
	4	1	2355	5	978824291	F	1	10	48067

	df_ratings_users_movies = pd.merge(
	    df_ratings_users, df_movies, left_on="MovieID", right_on="MovieID", how="inner"
	)

	df_ratings_users_movies.head(10)
	UserID	MovieID	Rating	Timestamp	Gender	Age	Occupation	Zip-code	Title	Genres
	0	1	1193	5	978300760	F	1	10	48067	One Flew Over the Cuckoo's Nest (1975)	Drama
	1	2	1193	5	978298413	M	56	16	70072	One Flew Over the Cuckoo's Nest (1975)	Drama
	2	12	1193	4	978220179	M	25	12	32793	One Flew Over the Cuckoo's Nest (1975)	Drama
	3	15	1193	4	978199279	M	25	7	22903	One Flew Over the Cuckoo's Nest (1975)	Drama
	4	17	1193	5	978158471	M	50	1	95350	One Flew Over the Cuckoo's Nest (1975)	Drama
	5	18	1193	4	978156168	F	18	3	95825	One Flew Over the Cuckoo's Nest (1975)	Drama
	6	19	1193	5	982730936	M	1	10	48073	One Flew Over the Cuckoo's Nest (1975)	Drama
	7	24	1193	5	978136709	F	25	7	10023	One Flew Over the Cuckoo's Nest (1975)	Drama
	8	28	1193	3	978125194	F	25	1	14607	One Flew Over the Cuckoo's Nest (1975)	Drama
	9	33	1193	5	978557765	M	45	3	55421	One Flew Over the Cuckoo's Nest (1975)	Drama

	# ### 2、理解 merge 时数量的对齐关系

	# 以下关系要正确理解：
	# * one-to-one：一对一关系，关联的key都是唯一的
	#   - 比如(学号，姓名) merge (学号，年龄)
	#   - 结果条数为：1*1
	# * one-to-many：一对多关系，左边唯一key，右边不唯一key
	#   - 比如(学号，姓名) merge (学号，[语文成绩、数学成绩、英语成绩])
	#   - 结果条数为：1*N
	# * many-to-many：多对多关系，左边右边都不是唯一的
	#   - 比如（学号，[语文成绩、数学成绩、英语成绩]） merge (学号，[篮球、足球、乒乓球])
	#   - 结果条数为：M*N

	#### 2.1 one-to-one 一对一关系的merge

	left = pd.DataFrame({'sno': [11, 12, 13, 14],
	                      'name': ['name_a', 'name_b', 'name_c', 'name_d']
	                    })
	left
		sno	name
	0	11	name_a
	1	12	name_b
	2	13	name_c
	3	14	name_d

	right = pd.DataFrame({'sno': [11, 12, 13, 14],
	                      'age': ['21', '22', '23', '24']
	                    })
	right
		sno	age
	0	11	21
	1	12	22
	2	13	23
	3	14	24

	# 一对一关系，结果中有4条
	pd.merge(left, right, on='sno')
	sno	name	age
	0	11	name_a	21
	1	12	name_b	22
	2	13	name_c	23
	3	14	name_d	24

	left = pd.DataFrame({'sno': [11, 12, 13, 14],
	                      'name': ['name_a', 'name_b', 'name_c', 'name_d']
	                    })
	left
		sno	name
	0	11	name_a
	1	12	name_b
	2	13	name_c
	3	14	name_d

	right = pd.DataFrame({'sno': [11, 11, 11, 12, 12, 13],
	                       'grade': ['语文88', '数学90', '英语75','语文66', '数学55', '英语29']
	                     })
	right
	    sno	grade
	0	11	语文88
	1	11	数学90
	2	11	英语75
	3	12	语文66
	4	12	数学55
	5	13	英语29

	# 数目以多的一边为准
	pd.merge(left, right, on='sno')
		sno	name	grade
	0	11	name_a	语文88
	1	11	name_a	数学90
	2	11	name_a	英语75
	3	12	name_b	语文66
	4	12	name_b	数学55
	5	13	name_c	英语29

	#### 2.3 many-to-many 多对多关系的merge

	# 注意：结果数量会出现乘法
	left = pd.DataFrame({'sno': [11, 11, 12, 12,12],
	                      '爱好': ['篮球', '羽毛球', '乒乓球', '篮球', "足球"]
	                    })
	left
		sno	爱好
	0	11	篮球
	1	11	羽毛球
	2	12	乒乓球
	3	12	篮球
	4	12	足球

	right = pd.DataFrame({'sno': [11, 11, 11, 12, 12, 13],
	                       'grade': ['语文88', '数学90', '英语75','语文66', '数学55', '英语29']
	                     })
	right
		sno	grade
	0	11	语文88
	1	11	数学90
	2	11	英语75
	3	12	语文66
	4	12	数学55
	5	13	英语29

	pd.merge(left, right, on='sno')
		sno	爱好	grade
	0	11	篮球	语文88
	1	11	篮球	数学90
	2	11	篮球	英语75
	3	11	羽毛球	语文88
	4	11	羽毛球	数学90
	5	11	羽毛球	英语75
	6	12	乒乓球	语文66
	7	12	乒乓球	数学55
	8	12	篮球	语文66
	9	12	篮球	数学55
	10	12	足球	语文66
	11	12	足球	数学55

	### 3、理解left join、right join、inner join、outer join的区别
	left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
	                      'A': ['A0', 'A1', 'A2', 'A3'],
	                      'B': ['B0', 'B1', 'B2', 'B3']})

	right = pd.DataFrame({'key': ['K0', 'K1', 'K4', 'K5'],
	                      'C': ['C0', 'C1', 'C4', 'C5'],
	                      'D': ['D0', 'D1', 'D4', 'D5']})

	left
		key	A	B
	0	K0	A0	B0
	1	K1	A1	B1
	2	K2	A2	B2
	3	K3	A3	B3

	right
		key	C	D
	0	K0	C0	D0
	1	K1	C1	D1
	2	K4	C4	D4
	3	K5	C5	D5

	#### 3.1 inner join，默认
	# 左边和右边的key都有，才会出现在结果里

	pd.merge(left, right, how='inner')
		key	A	B	C	D
	0	K0	A0	B0	C0	D0
	1	K1	A1	B1	C1	D1

	#### 3.2 left join
	# 左边的都会出现在结果里，右边的如果无法匹配则为Null
	pd.merge(left, right, how='left')

	key	A	B	C	D
	0	K0	A0	B0	C0	D0
	1	K1	A1	B1	C1	D1
	2	K2	A2	B2	NaN	NaN
	3	K3	A3	B3	NaN	NaN

	#### 3.3 right join
	# 右边的都会出现在结果里，左边的如果无法匹配则为Null
	pd.merge(left, right, how='right')
	key	A	B	C	D
	0	K0	A0	B0	C0	D0
	1	K1	A1	B1	C1	D1
	2	K4	NaN	NaN	C4	D4
	3	K5	NaN	NaN	C5	D5

	#### 3.4 outer join
	# 左边、右边的都会出现在结果里，如果无法匹配则为Null
	pd.merge(left, right, how='outer')
		key	A	B	C	D
	0	K0	A0	B0	C0	D0
	1	K1	A1	B1	C1	D1
	2	K2	A2	B2	NaN	NaN
	3	K3	A3	B3	NaN	NaN
	4	K4	NaN	NaN	C4	D4
	5	K5	NaN	NaN	C5	D5

	### 4、如果出现非Key的字段重名怎么办
	left = pd.DataFrame({'key': ['K0', 'K1', 'K2', 'K3'],
	                      'A': ['A0', 'A1', 'A2', 'A3'],
	                      'B': ['B0', 'B1', 'B2', 'B3']})

	right = pd.DataFrame({'key': ['K0', 'K1', 'K4', 'K5'],
	                      'A': ['A10', 'A11', 'A12', 'A13'],
	                      'D': ['D0', 'D1', 'D4', 'D5']})

	left
		key	A	B
	0	K0	A0	B0
	1	K1	A1	B1
	2	K2	A2	B2
	3	K3	A3	B3

	right
		key	A	D
	0	K0	A10	D0
	1	K1	A11	D1
	2	K4	A12	D4
	3	K5	A13	D5

	pd.merge(left, right, on='key')
		key	A_x	B	A_y	D
	0	K0	A0	B0	A10	D0
	1	K1	A1	B1	A11	D1

	pd.merge(left, right, on='key', suffixes=('_left', '_right'))
	key	A_left	B	A_right	D
	0	K0	A0	B0	A10	D0
	1	K1	A1	B1	A11	D1

.. |image0| image:: /_static/python/pandas-axis-index.png
.. |image1| image:: /_static/python/pandas-axis-columns.png
.. |image2| image:: /_static/python/pandas-index-performance.png
.. |image3| image:: /_static/python/pandas-merge-one-to-one.png
.. |image4| image:: /_static/python/pandas-merge-one-to-many.png
.. |image5| image:: /_static/python/pandas-merge-many-to-many.png
.. |image6| image:: /_static/python/pandas-leftjoin-rightjoin-outerjoin.png

.. |image00| image:: /_static/python/WX20201028-225251@2x.webp












































































































































