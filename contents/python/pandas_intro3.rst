Pandas 篇 3
##################################################################################

Pandas 实现数据的合并 concat
**********************************************************************************

.. code-block:: python

	#### 使用场景：
	批量合并相同格式的Excel、给DataFrame添加行、给DataFrame添加列

	#### 一句话说明concat语法：  
	* 使用某种合并方式(inner/outer)
	* 沿着某个轴向(axis=0/1)
	* 把多个Pandas对象(DataFrame/Series)合并成一个。

	#### concat语法：pandas.concat(objs, axis=0, join='outer', ignore_index=False)
	* objs：一个列表，内容可以是DataFrame或者Series，可以混合
	* axis：默认是0代表按行合并，如果等于1代表按列合并
	* join：合并的时候索引的对齐方式，默认是outer join，也可以是inner join
	* ignore_index：是否忽略掉原来的数据索引

	#### append语法：DataFrame.append(other, ignore_index=False)
	append只有按行合并，没有按列合并，相当于concat按行的简写形式  
	* other：单个dataframe、series、dict，或者列表
	* ignore_index：是否忽略掉原来的数据索引

	#### 参考文档：
	* pandas.concat的api文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html
	* pandas.concat的教程：https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html
	* pandas.append的api文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.append.html

	import pandas as pd

	import warnings
	warnings.filterwarnings('ignore')

	### 一、使用pandas.concat合并数据
	df1 = pd.DataFrame({'A': ['A0', 'A1', 'A2', 'A3'],
	                    'B': ['B0', 'B1', 'B2', 'B3'],
	                    'C': ['C0', 'C1', 'C2', 'C3'],
	                    'D': ['D0', 'D1', 'D2', 'D3'],
	                    'E': ['E0', 'E1', 'E2', 'E3']
	                   })
	df1
		A	B	C	D	E
	0	A0	B0	C0	D0	E0
	1	A1	B1	C1	D1	E1
	2	A2	B2	C2	D2	E2
	3	A3	B3	C3	D3	E3

	df2 = pd.DataFrame({ 'A': ['A4', 'A5', 'A6', 'A7'],
	                     'B': ['B4', 'B5', 'B6', 'B7'],
	                     'C': ['C4', 'C5', 'C6', 'C7'],
	                     'D': ['D4', 'D5', 'D6', 'D7'],
	                     'F': ['F4', 'F5', 'F6', 'F7']
	                   })
	df2
	A	B	C	D	F
	0	A4	B4	C4	D4	F4
	1	A5	B5	C5	D5	F5
	2	A6	B6	C6	D6	F6
	3	A7	B7	C7	D7	F7

	***1、默认的concat，参数为axis=0、join=outer、ignore_index=False***

	pd.concat([df1,df2])
		A	B	C	D	E	F
	0	A0	B0	C0	D0	E0	NaN
	1	A1	B1	C1	D1	E1	NaN
	2	A2	B2	C2	D2	E2	NaN
	3	A3	B3	C3	D3	E3	NaN
	0	A4	B4	C4	D4	NaN	F4
	1	A5	B5	C5	D5	NaN	F5
	2	A6	B6	C6	D6	NaN	F6
	3	A7	B7	C7	D7	NaN	F7

	***2、使用ignore_index=True可以忽略原来的索引***
	pd.concat([df1,df2], ignore_index=True)
		A	B	C	D	E	F
	0	A0	B0	C0	D0	E0	NaN
	1	A1	B1	C1	D1	E1	NaN
	2	A2	B2	C2	D2	E2	NaN
	3	A3	B3	C3	D3	E3	NaN
	4	A4	B4	C4	D4	NaN	F4
	5	A5	B5	C5	D5	NaN	F5
	6	A6	B6	C6	D6	NaN	F6
	7	A7	B7	C7	D7	NaN	F7

	***3、使用join=inner过滤掉不匹配的列***
	pd.concat([df1,df2], ignore_index=True, join="inner")
	A	B	C	D
	0	A0	B0	C0	D0
	1	A1	B1	C1	D1
	2	A2	B2	C2	D2
	3	A3	B3	C3	D3
	4	A4	B4	C4	D4
	5	A5	B5	C5	D5
	6	A6	B6	C6	D6
	7	A7	B7	C7	D7

	***4、使用axis=1相当于添加新列***
	df1
	A	B	C	D	E
	0	A0	B0	C0	D0	E0
	1	A1	B1	C1	D1	E1
	2	A2	B2	C2	D2	E2
	3	A3	B3	C3	D3	E3

	***A：添加一列Series***
	s1 = pd.Series(list(range(4)), name="F")
	pd.concat([df1,s1], axis=1)
	A	B	C	D	E	F
	0	A0	B0	C0	D0	E0	0
	1	A1	B1	C1	D1	E1	1
	2	A2	B2	C2	D2	E2	2
	3	A3	B3	C3	D3	E3	3

	***B：添加多列Series***
	s2 = df1.apply(lambda x:x["A"]+"_GG", axis=1)
	s2
	0    A0_GG
	1    A1_GG
	2    A2_GG
	3    A3_GG
	dtype: object

	s2.name="G"
	pd.concat([df1,s1,s2], axis=1)
		A	B	C	D	E	F	G
	0	A0	B0	C0	D0	E0	0	A0_GG
	1	A1	B1	C1	D1	E1	1	A1_GG
	2	A2	B2	C2	D2	E2	2	A2_GG
	3	A3	B3	C3	D3	E3	3	A3_GG

	# 列表可以只有Series
	pd.concat([s1,s2], axis=1)
		F	G
	0	0	A0_GG
	1	1	A1_GG
	2	2	A2_GG
	3	3	A3_GG

	# 列表是可以混合顺序的
	pd.concat([s1,df1,s2], axis=1)
	F	A	B	C	D	E	G
	0	0	A0	B0	C0	D0	E0	A0_GG
	1	1	A1	B1	C1	D1	E1	A1_GG
	2	2	A2	B2	C2	D2	E2	A2_GG
	3	3	A3	B3	C3	D3	E3	A3_GG

	### 二、使用DataFrame.append按行合并数据
	df1 = pd.DataFrame([[1, 2], [3, 4]], columns=list('AB'))
	df1
		A	B
	0	1	2
	1	3	4

	df2 = pd.DataFrame([[5, 6], [7, 8]], columns=list('AB'))
	df2
	    A	B
	0	5	6
	1	7	8

	***1、给1个dataframe添加另一个dataframe***
	df1.append(df2)
		A	B
	0	1	2
	1	3	4
	0	5	6
	1	7	8

	***2、忽略原来的索引ignore_index=True***
	df1.append(df2, ignore_index=True)
		A	B
	0	1	2
	1	3	4
	2	5	6
	3	7	8

	***3、可以一行一行的给DataFrame添加数据***
	# 一个空的df
	df = pd.DataFrame(columns=['A'])
	df
	A

	***A：低性能版本***
	for i in range(5):
	    # 注意这里每次都在复制
	    df = df.append({'A': i}, ignore_index=True)
	df
		A
	0	0
	1	1
	2	2
	3	3
	4	4

	***B：性能好的版本***
	# 第一个入参是一个列表，避免了多次复制
	pd.concat(
	    [pd.DataFrame([i], columns=['A']) for i in range(5)],
	    ignore_index=True
	)
		A
	0	0
	1	1
	2	2
	3	3
	4	4

Pandas 批量拆分与合并 Excel
**********************************************************************************

.. code-block:: python

	## Pandas批量拆分Excel与合并Excel

	实例演示：
	1. 将一个大Excel等份拆成多个Excel
	2. 将多个小Excel合并成一个大Excel并标记来源

	work_dir="./course_datas/c15_excel_split_merge"
	splits_dir=f"{work_dir}/splits"

	import os
	if not os.path.exists(splits_dir):
	    os.mkdir(splits_dir)

	### 0、读取源Excel到Pandas
	import pandas as pd
	df_source = pd.read_excel(f"{work_dir}/crazyant_blog_articles_source.xlsx")

	df_source.head()
	     id	title	tags
	0	2585	Tensorflow怎样接收变长列表特征	python,tensorflow,特征工程
	1	2583	Pandas实现数据的合并concat	pandas,python,数据分析
	2	2574	Pandas的Index索引有什么用途？	pandas,python,数据分析
	3	2564	机器学习常用数据集大全	python,机器学习
	4	2561	一个数据科学家的修炼路径	数据分析

	df_source.index
	RangeIndex(start=0, stop=258, step=1)

	df_source.shape
	(258, 3)

	total_row_count = df_source.shape[0]
	total_row_count
	258

	### 一、将一个大Excel等份拆成多个Excel

	1. 使用df.iloc方法，将一个大的dataframe，拆分成多个小dataframe
	2. 将使用dataframe.to_excel保存每个小Excel

	#### 1、计算拆分后的每个excel的行数
	# 这个大excel，会拆分给这几个人
	user_names = ["xiao_shuai", "xiao_wang", "xiao_ming", "xiao_lei", "xiao_bo", "xiao_hong"]

	# 每个人的任务数目
	split_size = total_row_count // len(user_names)
	if total_row_count % len(user_names) != 0:
	    split_size += 1

	split_size
	43

	#### 2、拆分成多个dataframe
	df_subs = []
	for idx, user_name in enumerate(user_names):
	    # iloc的开始索引
	    begin = idx*split_size
	    # iloc的结束索引
	    end = begin+split_size
	    # 实现df按照iloc拆分
	    df_sub = df_source.iloc[begin:end]
	    # 将每个子df存入列表
	    df_subs.append((idx, user_name, df_sub))

	#### 3、将每个datafame存入excel
	for idx, user_name, df_sub in df_subs:
	    file_name = f"{splits_dir}/crazyant_blog_articles_{idx}_{user_name}.xlsx"
	    df_sub.to_excel(file_name, index=False)

	### 二、合并多个小Excel到一个大Excel
	1. 遍历文件夹，得到要合并的Excel文件列表
	2. 分别读取到dataframe，给每个df添加一列用于标记来源
	3. 使用pd.concat进行df批量合并
	4. 将合并后的dataframe输出到excel

	#### 1. 遍历文件夹，得到要合并的Excel名称列表
	import os
	excel_names = []
	for excel_name in os.listdir(splits_dir):
	    excel_names.append(excel_name)
	excel_names
	['crazyant_blog_articles_1_xiao_wang.xlsx',
	 'crazyant_blog_articles_0_xiao_shuai.xlsx',
	 'crazyant_blog_articles_5_xiao_hong.xlsx',
	 'crazyant_blog_articles_4_xiao_bo.xlsx',
	 'crazyant_blog_articles_3_xiao_lei.xlsx',
	 'crazyant_blog_articles_2_xiao_ming.xlsx']

	 #### 2. 分别读取到dataframe
	 df_list = []

	for excel_name in excel_names:
	    # 读取每个excel到df
	    excel_path = f"{splits_dir}/{excel_name}"
	    df_split = pd.read_excel(excel_path)
	    # 得到username
	    username = excel_name.replace("crazyant_blog_articles_", "").replace(".xlsx", "")[2:]
	    print(excel_name, username)
	    # 给每个df添加1列，即用户名字
	    df_split["username"] = username
	    
	    df_list.append(df_split)

	crazyant_blog_articles_1_xiao_wang.xlsx xiao_wang
	crazyant_blog_articles_0_xiao_shuai.xlsx xiao_shuai
	crazyant_blog_articles_5_xiao_hong.xlsx xiao_hong
	crazyant_blog_articles_4_xiao_bo.xlsx xiao_bo
	crazyant_blog_articles_3_xiao_lei.xlsx xiao_lei
	crazyant_blog_articles_2_xiao_ming.xlsx xiao_ming

	#### 3. 使用pd.concat进行合并
	df_merged = pd.concat(df_list)
	df_merged.shape
	(258, 4)

	df_merged.head()
	id	title	tags	username
	0	2120	Zookeeper并不保证读取的是最新数据	zookeeper	xiao_wang
	1	2089	Mybatis源码解读-初始化过程详解	mybatis	xiao_wang
	2	2076	怎样借助Python爬虫给宝宝起个好名字	python,爬虫	xiao_wang
	3	2022	Mybatis源码解读-设计模式总结	mybatis,设计模式	xiao_wang
	4	2012	打工者心态、主人公意识、个人公司品牌	程序人生	xiao_wang

	df_merged["username"].value_counts()
	xiao_bo       43
	xiao_ming     43
	xiao_hong     43
	xiao_lei      43
	xiao_wang     43
	xiao_shuai    43
	Name: username, dtype: int64

	#### 4. 将合并后的dataframe输出到excel
	df_merged.to_excel(f"{work_dir}/crazyant_blog_articles_merged.xlsx", index=False)

Pandas 的分组聚合 groupby
**********************************************************************************

.. code-block:: python

	## Pandas怎样实现groupby分组统计

	类似SQL：  
	select city,max(temperature) from city_weather group by city;

	groupby：先对数据分组，然后在每个分组上应用聚合函数、转换函数

	本次演示：  
	一、分组使用聚合函数做数据统计  
	二、遍历groupby的结果理解执行流程  
	三、实例分组探索天气数据

	import pandas as pd
	import numpy as np
	# 加上这一句，能在jupyter notebook展示matplot图表
	%matplotlib inline

	df = pd.DataFrame({'A': ['foo', 'bar', 'foo', 'bar', 'foo', 'bar', 'foo', 'foo'],
	                   'B': ['one', 'one', 'two', 'three', 'two', 'two', 'one', 'three'],
	                   'C': np.random.randn(8),
	                   'D': np.random.randn(8)})
	df
	A	B	C	D
	0	foo	one	-0.265986	-1.612982
	1	bar	one	-0.615903	0.722317
	2	foo	two	-0.697261	0.282532
	3	bar	three	-1.697145	1.104218
	4	foo	two	0.037168	0.910201
	5	bar	two	0.718036	-1.350090
	6	foo	one	-0.276452	-2.225983
	7	foo	three	-1.213616	-1.927561

	### 一、分组使用聚合函数做数据统计
	#### 1、单个列groupby，查询所有数据列的统计
	df.groupby('A').sum()
		C	D
	A		
	bar	-1.595012	0.476444
	foo	-2.416147	-4.573793

	我们看到：
	1. groupby中的'A'变成了数据的索引列
	2. 因为要统计sum，但B列不是数字，所以被自动忽略掉

	#### 2、多个列groupby，查询所有数据列的统计
	df.groupby(['A','B']).mean()
			C	D
	A	B		
	bar	one	-0.615903	0.722317
	three	-1.697145	1.104218
	two	0.718036	-1.350090
	foo	one	-0.271219	-1.919482
	three	-1.213616	-1.927561
	two	-0.330047	0.596366

	我们看到：('A','B')成对变成了二级索引

	df.groupby(['A','B'], as_index=False).mean()
	A	B	C	D
	0	bar	one	-0.615903	0.722317
	1	bar	three	-1.697145	1.104218
	2	bar	two	0.718036	-1.350090
	3	foo	one	-0.271219	-1.919482
	4	foo	three	-1.213616	-1.927561
	5	foo	two	-0.330047	0.596366

	#### 3、同时查看多种数据统计
	df.groupby('A').agg([np.sum, np.mean, np.std])
	C	D
	sum	mean	std	sum	mean	std
	A						
	bar	-1.595012	-0.531671	1.209792	0.476444	0.158815	1.320628
	foo	-2.416147	-0.483229	0.484778	-4.573793	-0.914759	1.413911

	我们看到：列变成了多级索引
	#### 4、查看单列的结果数据统计
	# 方法1：预过滤，性能更好
	df.groupby('A')['C'].agg([np.sum, np.mean, np.std])
	sum	mean	std
	A			
	bar	-1.595012	-0.531671	1.209792
	foo	-2.416147	-0.483229	0.484778

	# 方法2
	df.groupby('A').agg([np.sum, np.mean, np.std])['C']
		sum	mean	std
	A			
	bar	-1.595012	-0.531671	1.209792
	foo	-2.416147	-0.483229	0.484778

	#### 5、不同列使用不同的聚合函数
	df.groupby('A').agg({"C":np.sum, "D":np.mean})
	C	D
	A		
	bar	-1.595012	0.158815
	foo	-2.416147	-0.914759

	### 二、遍历groupby的结果理解执行流程
	for循环可以直接遍历每个group
	##### 1、遍历单个列聚合的分组
	g = df.groupby('A')
	g
	<pandas.core.groupby.generic.DataFrameGroupBy object at 0x118fa3d50>


	for name,group in g:
	    print(name)
	    print(group)
	    print()
	bar
	     A      B         C         D
	1  bar    one -0.615903  0.722317
	3  bar  three -1.697145  1.104218
	5  bar    two  0.718036 -1.350090

	foo
	     A      B         C         D
	0  foo    one -0.265986 -1.612982
	2  foo    two -0.697261  0.282532
	4  foo    two  0.037168  0.910201
	6  foo    one -0.276452 -2.225983
	7  foo  three -1.213616 -1.927561

	***可以获取单个分组的数据***
	g.get_group('bar')
		A	B	C	D
	1	bar	one	-0.615903	0.722317
	3	bar	three	-1.697145	1.104218
	5	bar	two	0.718036	-1.350090

	##### 2、遍历多个列聚合的分组
	g = df.groupby(['A', 'B'])

	for name,group in g:
	    print(name)
	    print(group)
	    print()
	('bar', 'one')
	     A    B         C         D
	1  bar  one -0.615903  0.722317

	('bar', 'three')
	     A      B         C         D
	3  bar  three -1.697145  1.104218

	('bar', 'two')
	     A    B         C        D
	5  bar  two  0.718036 -1.35009

	('foo', 'one')
	     A    B         C         D
	0  foo  one -0.265986 -1.612982
	6  foo  one -0.276452 -2.225983

	('foo', 'three')
	     A      B         C         D
	7  foo  three -1.213616 -1.927561

	('foo', 'two')
	     A    B         C         D
	2  foo  two -0.697261  0.282532
	4  foo  two  0.037168  0.910201

	# 可以看到，name是一个2个元素的tuple，代表不同的列
	g.get_group(('foo', 'one'))
	A	B	C	D
	0	foo	one	-0.265986	-1.612982
	6	foo	one	-0.276452	-2.225983

	***可以直接查询group后的某几列，生成Series或者子DataFrame***
	g['C']
	<pandas.core.groupby.generic.SeriesGroupBy object at 0x11912d2d0>


	for name, group in g['C']:
	    print(name)
	    print(group)
	    print(type(group))
	    print()
	('bar', 'one')
	1   -0.615903
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	('bar', 'three')
	3   -1.697145
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	('bar', 'two')
	5    0.718036
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	('foo', 'one')
	0   -0.265986
	6   -0.276452
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	('foo', 'three')
	7   -1.213616
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	('foo', 'two')
	2   -0.697261
	4    0.037168
	Name: C, dtype: float64
	<class 'pandas.core.series.Series'>

	其实所有的聚合统计，都是在dataframe和series上进行的；

	### 三、实例分组探索天气数据
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

	# 新增一列为月份
	df['month'] = df['ymd'].str[:7]
	df.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	month
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	2018-01
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	2018-01
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	2018-01
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	2018-01
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	2018-01

	#### 1、查看每个月的最高温度
	data = df.groupby('month')['bWendu'].max()
	data
	month
	2018-01     7
	2018-02    12
	2018-03    27
	2018-04    30
	2018-05    35
	2018-06    38
	2018-07    37
	2018-08    36
	2018-09    31
	2018-10    25
	2018-11    18
	2018-12    10
	Name: bWendu, dtype: int32

	type(data)
	pandas.core.series.Series

	data.plot()
	<AxesSubplot:xlabel='month'>

	|image0|

	#### 2、查看每个月的最高温度、最低温度、平均空气质量指数
	df.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	month
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	2018-01
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	2018-01
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	2018-01
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	2018-01
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	2018-01

	group_data = df.groupby('month').agg({"bWendu":np.max, "yWendu":np.min, "aqi":np.mean})
	group_data
		bWendu	yWendu	aqi
	month			
	2018-01	7	-12	60.677419
	2018-02	12	-10	78.857143
	2018-03	27	-4	130.322581
	2018-04	30	1	102.866667
	2018-05	35	10	99.064516
	2018-06	38	17	82.300000
	2018-07	37	22	72.677419
	2018-08	36	20	59.516129
	2018-09	31	11	50.433333
	2018-10	25	1	67.096774
	2018-11	18	-4	105.100000
	2018-12	10	-12	77.354839

	group_data.plot()
	<AxesSubplot:xlabel='month'>

	|image1|

Pandas 的分层索引 MultiIndex
**********************************************************************************

.. code-block:: python

	为什么要学习分层索引MultiIndex？
	* 分层索引：在一个轴向上拥有多个索引层级，可以表达更高维度数据的形式；
	* 可以更方便的进行数据筛选，如果有序则性能更好；
	* groupby等操作的结果，如果是多KEY，结果是分层索引，需要会使用
	* 一般不需要自己创建分层索引(MultiIndex有构造函数但一般不用)

	演示数据：百度、阿里巴巴、爱奇艺、京东四家公司的10天股票数据  
	数据来自：英为财经  
	https://cn.investing.com/

	本次演示提纲：  
	一、Series的分层索引MultiIndex  
	二、Series有多层索引怎样筛选数据？  
	三、DataFrame的多层索引MultiIndex  
	四、DataFrame有多层索引怎样筛选数据？ 

	import pandas as pd
	%matplotlib inline

	stocks = pd.read_excel('./datas/stocks/互联网公司股票.xlsx')
	stocks.shape
	(12, 8)

	stocks.head(3)
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01

	stocks["公司"].unique()
	array(['BIDU', 'BABA', 'IQ', 'JD'], dtype=object)

	stocks.index
	RangeIndex(start=0, stop=12, step=1)

	stocks.groupby('公司')["收盘"].mean()
	公司
	BABA    166.80
	BIDU    102.98
	IQ       15.90
	JD       28.35
	Name: 收盘, dtype: float64

	### 一、Series的分层索引MultiIndex
	ser = stocks.groupby(['公司', '日期'])['收盘'].mean()
	ser
	公司    日期        
	BABA  2019-10-01    165.15
	      2019-10-02    165.77
	      2019-10-03    169.48
	BIDU  2019-10-01    102.00
	      2019-10-02    102.62
	      2019-10-03    104.32
	IQ    2019-10-01     15.92
	      2019-10-02     15.72
	      2019-10-03     16.06
	JD    2019-10-01     28.19
	      2019-10-02     28.06
	      2019-10-03     28.80
	Name: 收盘, dtype: float64

	# 多维索引中，空白的意思是：使用上面的值
	ser.index
	MultiIndex([('BABA', '2019-10-01'),
	            ('BABA', '2019-10-02'),
	            ('BABA', '2019-10-03'),
	            ('BIDU', '2019-10-01'),
	            ('BIDU', '2019-10-02'),
	            ('BIDU', '2019-10-03'),
	            (  'IQ', '2019-10-01'),
	            (  'IQ', '2019-10-02'),
	            (  'IQ', '2019-10-03'),
	            (  'JD', '2019-10-01'),
	            (  'JD', '2019-10-02'),
	            (  'JD', '2019-10-03')],
	           names=['公司', '日期'])

	 # unstack把二级索引变成列
	ser.unstack()
	日期	2019-10-01	2019-10-02	2019-10-03
	公司			
	BABA	165.15	165.77	169.48
	BIDU	102.00	102.62	104.32
	IQ	15.92	15.72	16.06
	JD	28.19	28.06	28.80

	ser
	公司    日期        
	BABA  2019-10-01    165.15
	      2019-10-02    165.77
	      2019-10-03    169.48
	BIDU  2019-10-01    102.00
	      2019-10-02    102.62
	      2019-10-03    104.32
	IQ    2019-10-01     15.92
	      2019-10-02     15.72
	      2019-10-03     16.06
	JD    2019-10-01     28.19
	      2019-10-02     28.06
	      2019-10-03     28.80
	Name: 收盘, dtype: float64

	ser.reset_index()
	公司	日期	收盘
	0	BABA	2019-10-01	165.15
	1	BABA	2019-10-02	165.77
	2	BABA	2019-10-03	169.48
	3	BIDU	2019-10-01	102.00
	4	BIDU	2019-10-02	102.62
	5	BIDU	2019-10-03	104.32
	6	IQ	2019-10-01	15.92
	7	IQ	2019-10-02	15.72
	8	IQ	2019-10-03	16.06
	9	JD	2019-10-01	28.19
	10	JD	2019-10-02	28.06
	11	JD	2019-10-03	28.80

	### 二、Series有多层索引MultiIndex怎样筛选数据
	ser
	公司    日期        
	BABA  2019-10-01    165.15
	      2019-10-02    165.77
	      2019-10-03    169.48
	BIDU  2019-10-01    102.00
	      2019-10-02    102.62
	      2019-10-03    104.32
	IQ    2019-10-01     15.92
	      2019-10-02     15.72
	      2019-10-03     16.06
	JD    2019-10-01     28.19
	      2019-10-02     28.06
	      2019-10-03     28.80
	Name: 收盘, dtype: float64

	ser.loc['BIDU']
	日期
	2019-10-01    102.00
	2019-10-02    102.62
	2019-10-03    104.32
	Name: 收盘, dtype: float64

	# 多层索引，可以用元组的形式筛选
	ser.loc[('BIDU', '2019-10-02')]
	102.62

	ser.loc[:, '2019-10-02']
	公司
	BABA    165.77
	BIDU    102.62
	IQ       15.72
	JD       28.06
	Name: 收盘, dtype: float64

	### 三、DataFrame的多层索引MultiIndex
	stocks.head()

	日期	公司	收盘	开盘	高	低	交易量	涨跌幅
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00

	stocks.set_index(['公司', '日期'], inplace=True)
	stocks
		收盘	开盘	高	低	交易量	涨跌幅
	公司	日期						
	BIDU	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	2019-10-01	102.00	102.80	103.26	101.00	1.78	-0.01
	BABA	2019-10-03	169.48	166.65	170.18	165.00	10.39	0.02
	2019-10-02	165.77	162.82	166.88	161.90	11.60	0.00
	2019-10-01	165.15	168.01	168.23	163.64	14.19	-0.01
	IQ	2019-10-03	16.06	15.71	16.38	15.32	10.08	0.02
	2019-10-02	15.72	15.85	15.87	15.12	8.10	-0.01
	2019-10-01	15.92	16.14	16.22	15.50	11.65	-0.01
	JD	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03
	2019-10-02	28.06	28.00	28.22	27.53	9.53	0.00
	2019-10-01	28.19	28.22	28.57	27.97	10.64	0.00

	stocks.index
	MultiIndex([('BIDU', '2019-10-03'),
	            ('BIDU', '2019-10-02'),
	            ('BIDU', '2019-10-01'),
	            ('BABA', '2019-10-03'),
	            ('BABA', '2019-10-02'),
	            ('BABA', '2019-10-01'),
	            (  'IQ', '2019-10-03'),
	            (  'IQ', '2019-10-02'),
	            (  'IQ', '2019-10-01'),
	            (  'JD', '2019-10-03'),
	            (  'JD', '2019-10-02'),
	            (  'JD', '2019-10-01')],
	           names=['公司', '日期'])

	stocks.sort_index(inplace=True)
	stocks
		收盘	开盘	高	低	交易量	涨跌幅
	公司	日期						
	BABA	2019-10-01	165.15	168.01	168.23	163.64	14.19	-0.01
	2019-10-02	165.77	162.82	166.88	161.90	11.60	0.00
	2019-10-03	169.48	166.65	170.18	165.00	10.39	0.02
	BIDU	2019-10-01	102.00	102.80	103.26	101.00	1.78	-0.01
	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	IQ	2019-10-01	15.92	16.14	16.22	15.50	11.65	-0.01
	2019-10-02	15.72	15.85	15.87	15.12	8.10	-0.01
	2019-10-03	16.06	15.71	16.38	15.32	10.08	0.02
	JD	2019-10-01	28.19	28.22	28.57	27.97	10.64	0.00
	2019-10-02	28.06	28.00	28.22	27.53	9.53	0.00
	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03

	### 四、DataFrame有多层索引MultiIndex怎样筛选数据？
	【***重要知识***】在选择数据时： 
	* 元组(key1,key2)代表筛选多层索引，其中key1是索引第一级，key2是第二级，比如key1=JD, key2=2019-10-02
	* 列表[key1,key2]代表同一层的多个KEY，其中key1和key2是并列的同级索引，比如key1=JD, key2=BIDU

	stocks.loc['BIDU']
	收盘	开盘	高	低	交易量	涨跌幅
	日期						
	2019-10-01	102.00	102.80	103.26	101.00	1.78	-0.01
	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02

	stocks.loc[('BIDU', '2019-10-02'), '开盘']
	100.85

	stocks.loc[['BIDU', 'JD'], :]
		收盘	开盘	高	低	交易量	涨跌幅
	公司	日期						
	BIDU	2019-10-01	102.00	102.80	103.26	101.00	1.78	-0.01
	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	JD	2019-10-01	28.19	28.22	28.57	27.97	10.64	0.00
	2019-10-02	28.06	28.00	28.22	27.53	9.53	0.00
	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03

	stocks.loc[(['BIDU', 'JD'], '2019-10-03'), :]
	收盘	开盘	高	低	交易量	涨跌幅
	公司	日期						
	BIDU	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	JD	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03

	stocks.loc[(['BIDU', 'JD'], '2019-10-03'), '收盘']
	公司    日期        
	BIDU  2019-10-03    104.32
	JD    2019-10-03     28.80
	Name: 收盘, dtype: float64

	stocks.loc[('BIDU', ['2019-10-02', '2019-10-03']), '收盘']
	公司    日期        
	BIDU  2019-10-02    102.62
	      2019-10-03    104.32
	Name: 收盘, dtype: float64

	# slice(None)代表筛选这一索引的所有内容
	stocks.loc[(slice(None), ['2019-10-02', '2019-10-03']), :]
	收盘	开盘	高	低	交易量	涨跌幅
	公司	日期						
	BABA	2019-10-02	165.77	162.82	166.88	161.90	11.60	0.00
	2019-10-03	169.48	166.65	170.18	165.00	10.39	0.02
	BIDU	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	IQ	2019-10-02	15.72	15.85	15.87	15.12	8.10	-0.01
	2019-10-03	16.06	15.71	16.38	15.32	10.08	0.02
	JD	2019-10-02	28.06	28.00	28.22	27.53	9.53	0.00
	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03

	stocks.reset_index()
	公司	日期	收盘	开盘	高	低	交易量	涨跌幅
	0	BABA	2019-10-01	165.15	168.01	168.23	163.64	14.19	-0.01
	1	BABA	2019-10-02	165.77	162.82	166.88	161.90	11.60	0.00
	2	BABA	2019-10-03	169.48	166.65	170.18	165.00	10.39	0.02
	3	BIDU	2019-10-01	102.00	102.80	103.26	101.00	1.78	-0.01
	4	BIDU	2019-10-02	102.62	100.85	103.24	99.50	2.69	0.01
	5	BIDU	2019-10-03	104.32	102.35	104.73	101.15	2.24	0.02
	6	IQ	2019-10-01	15.92	16.14	16.22	15.50	11.65	-0.01
	7	IQ	2019-10-02	15.72	15.85	15.87	15.12	8.10	-0.01
	8	IQ	2019-10-03	16.06	15.71	16.38	15.32	10.08	0.02
	9	JD	2019-10-01	28.19	28.22	28.57	27.97	10.64	0.00
	10	JD	2019-10-02	28.06	28.00	28.22	27.53	9.53	0.00
	11	JD	2019-10-03	28.80	28.11	28.97	27.82	8.77	0.03 

Pandas 的数据转换函数 map、apply、applymap
**********************************************************************************

.. code-block:: python

	### 1. map用于Series值的转换
	实例：将股票代码英文转换成中文名字
	Series.map(dict) or Series.map(function)均可

	import pandas as pd
	stocks = pd.read_excel('./datas/stocks/互联网公司股票.xlsx')
	stocks.head()
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00

	stocks["公司"].unique()
	array(['BIDU', 'BABA', 'IQ', 'JD'], dtype=object)

	# 公司股票代码到中文的映射，注意这里是小写
	dict_company_names = {
	    "bidu": "百度",
	    "baba": "阿里巴巴",
	    "iq": "爱奇艺", 
	    "jd": "京东"
	}

	#### 方法1：Series.map(dict)
	stocks["公司中文1"] = stocks["公司"].str.lower().map(dict_company_names)

	stocks.head()
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅	公司中文1
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02	百度
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01	百度
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01	百度
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02	阿里巴巴
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00	阿里巴巴

	#### 方法2：Series.map(function)
	function的参数是Series的每个元素的值
	stocks["公司中文2"] = stocks["公司"].map(lambda x : dict_company_names[x.lower()])
	stocks.head()
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅	公司中文1	公司中文2
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02	百度	百度
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01	百度	百度
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01	百度	百度
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02	阿里巴巴	阿里巴巴
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00	阿里巴巴	阿里巴巴

	### 2. apply用于Series和DataFrame的转换

	* Series.apply(function), 函数的参数是每个值
	* DataFrame.apply(function), 函数的参数是Series
	#### Series.apply(function)
	function的参数是Series的每个值
	stocks["公司中文3"] = stocks["公司"].apply(
	    lambda x : dict_company_names[x.lower()])

	stocks.head()
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅	公司中文1	公司中文2	公司中文3
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02	百度	百度	百度
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01	百度	百度	百度
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01	百度	百度	百度
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02	阿里巴巴	阿里巴巴	阿里巴巴
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00	阿里巴巴	阿里巴巴	阿里巴巴

	#### DataFrame.apply(function)
	function的参数是对应轴的Series
	stocks["公司中文4"] = stocks.apply(
	    lambda x : dict_company_names[x["公司"].lower()], 
	    axis=1)

	stocks["公司中文4"] = stocks.apply(
	    lambda x : dict_company_names[x["公司"].lower()], 
	    axis=1)

	stocks.head()
	日期	公司	收盘	开盘	高	低	交易量	涨跌幅	公司中文1	公司中文2	公司中文3	公司中文4
	0	2019-10-03	BIDU	104.32	102.35	104.73	101.15	2.24	0.02	百度	百度	百度	百度
	1	2019-10-02	BIDU	102.62	100.85	103.24	99.50	2.69	0.01	百度	百度	百度	百度
	2	2019-10-01	BIDU	102.00	102.80	103.26	101.00	1.78	-0.01	百度	百度	百度	百度
	3	2019-10-03	BABA	169.48	166.65	170.18	165.00	10.39	0.02	阿里巴巴	阿里巴巴	阿里巴巴	阿里巴巴
	4	2019-10-02	BABA	165.77	162.82	166.88	161.90	11.60	0.00	阿里巴巴	阿里巴巴	阿里巴巴	阿里巴巴

	### 3. applymap用于DataFrame所有值的转换
	sub_df = stocks[['收盘', '开盘', '高', '低', '交易量']]
	sub_df.head()
	收盘	开盘	高	低	交易量
	0	104.32	102.35	104.73	101.15	2.24
	1	102.62	100.85	103.24	99.50	2.69
	2	102.00	102.80	103.26	101.00	1.78
	3	169.48	166.65	170.18	165.00	10.39
	4	165.77	162.82	166.88	161.90	11.60

	# 将这些数字取整数，应用于所有元素
	sub_df.applymap(lambda x : int(x))
		收盘	开盘	高	低	交易量
	0	104	102	104	101	2
	1	102	100	103	99	2
	2	102	102	103	101	1
	3	169	166	170	165	10
	4	165	162	166	161	11
	5	165	168	168	163	14
	6	16	15	16	15	10
	7	15	15	15	15	8
	8	15	16	16	15	11
	9	28	28	28	27	8
	10	28	28	28	27	9
	11	28	28	28	27	10

	# 直接修改原df的这几列
	stocks.loc[:, ['收盘', '开盘', '高', '低', '交易量']] = sub_df.applymap(lambda x : int(x))
	stocks.head()

	日期	公司	收盘	开盘	高	低	交易量	涨跌幅	公司中文1	公司中文2	公司中文3	公司中文4
	0	2019-10-03	BIDU	104	102	104	101	2	0.02	百度	百度	百度	百度
	1	2019-10-02	BIDU	102	100	103	99	2	0.01	百度	百度	百度	百度
	2	2019-10-01	BIDU	102	102	103	101	1	-0.01	百度	百度	百度	百度
	3	2019-10-03	BABA	169	166	170	165	10	0.02	阿里巴巴	阿里巴巴	阿里巴巴	阿里巴巴
	4	2019-10-02	BABA	165	162	166	161	11	0.00	阿里巴巴	阿里巴巴	阿里巴巴	阿里巴巴

.. |image0| image:: /_static/python/python_222.png
.. |image1| image:: /_static/python/python_334.png






























































































































