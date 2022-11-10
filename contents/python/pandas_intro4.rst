Pandas 篇 4
##################################################################################

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

Pandas 怎样对每个分组应用 apply 函数?
**********************************************************************************

.. code-block:: python

	## Pandas怎样对每个分组应用apply函数?

	#### 知识：Pandas的GroupBy遵从split、apply、combine模式

	<div style="text-align:left; width:700px;"><img src="./other_files/pandas-split-apply-combine.png" style=""/></div>

	这里的split指的是pandas的groupby，我们自己实现apply函数，apply返回的结果由pandas进行combine得到结果

	#### GroupBy.apply(function)  
	* function的第一个参数是dataframe
	* function的返回结果，可是dataframe、series、单个值，甚至和输入dataframe完全没关系

	#### 本次实例演示：
	1. 怎样对数值列按分组的归一化？
	2. 怎样取每个分组的TOPN数据？

	### 实例1：怎样对数值列按分组的归一化？

	# 将不同范围的数值列进行归一化，映射到[0,1]区间：
	# * 更容易做数据横向对比，比如价格字段是几百到几千，增幅字段是0到100
	# * 机器学习模型学的更快性能更好

	# 归一化的公式：

	%%html
	<div style="text-align:left; width:500px;"><img src="./other_files/Normalization-Formula.jpg" style=""/></div>

	#### 演示：用户对电影评分的归一化

	# 每个用户的评分不同，有的乐观派评分高，有的悲观派评分低，按用户做归一化
	import pandas as pd
	ratings = pd.read_csv(
	    "./datas/movielens-1m/ratings.dat", 
	    sep="::",
	    engine='python', 
	    names="UserID::MovieID::Rating::Timestamp".split("::")
	)
	ratings.head()
	UserID	MovieID	Rating	Timestamp
	0	1	1193	5	978300760
	1	1	661	3	978302109
	2	1	914	3	978301968
	3	1	3408	4	978300275
	4	1	2355	5	978824291

	# 实现按照用户ID分组，然后对其中一列归一化
	def ratings_norm(df):
	    """
	    @param df：每个用户分组的dataframe
	    """
	    min_value = df["Rating"].min()
	    max_value = df["Rating"].max()
	    df["Rating_norm"] = df["Rating"].apply(
	        lambda x: (x-min_value)/(max_value-min_value))
	    return df

	ratings = ratings.groupby("UserID").apply(ratings_norm)
	ratings[ratings["UserID"]==1].head()
	UserID	MovieID	Rating	Timestamp	Rating_norm
	0	1	1193	5	978300760	1.0
	1	1	661	3	978302109	0.0
	2	1	914	3	978301968	0.0
	3	1	3408	4	978300275	0.5
	4	1	2355	5	978824291	1.0

	# 可以看到UserID==1这个用户，Rating==3是他的最低分，是个乐观派，我们归一化到0分；
	### 实例2：怎样取每个分组的TOPN数据？
	# 获取2018年每个月温度最高的2天数据

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)
	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
	# 新增一列为月份
	df['month'] = df['ymd'].str[:7]
	df.head()
	ymd	bWendu	yWendu	tianqi	fengxiang	fengli	aqi	aqiInfo	aqiLevel	month
	0	2018-01-01	3	-6	晴~多云	东北风	1-2级	59	良	2	2018-01
	1	2018-01-02	2	-5	阴~多云	东北风	1-2级	49	优	1	2018-01
	2	2018-01-03	2	-5	多云	北风	1-2级	28	优	1	2018-01
	3	2018-01-04	0	-8	阴	东北风	1-2级	28	优	1	2018-01
	4	2018-01-05	3	-6	多云~晴	西北风	1-2级	50	优	1	2018-01

	def getWenduTopN(df, topn):
	    """
	    这里的df，是每个月份分组group的df
	    """
	    return df.sort_values(by="bWendu")[["ymd", "bWendu"]][-topn:]

	df.groupby("month").apply(getWenduTopN, topn=1).head()
			ymd	bWendu
	month			
	2018-01	18	2018-01-19	7
	2018-02	56	2018-02-26	12
	2018-03	85	2018-03-27	27
	2018-04	118	2018-04-29	30
	2018-05	150	2018-05-31	35

	# 我们看到，grouby的apply函数返回的dataframe，其实和原来的dataframe其实可以完全不一样

Pandas 的 stack 和 pivot 实现数据透视
**********************************************************************************

.. code-block:: python

	<img src="./other_files/reshaping_example.png" style="margin-left:0px; width:600px" />

	1. 经过统计得到多维度指标数据
	2. 使用unstack实现数据二维透视
	3. 使用pivot简化透视
	4. stack、unstack、pivot的语法

	#%% md

	###  1. 经过统计得到多维度指标数据

	非常常见的统计场景，指定多个维度，计算聚合后的指标  

	实例：统计得到“电影评分数据集”，每个月份的每个分数被评分多少次：（月份、分数1~5、次数）

	#%%

	import pandas as pd
	import numpy as np
	%matplotlib inline

	#%%

	df = pd.read_csv(
	    "./datas/movielens-1m/ratings.dat",
	    header=None,
	    names="UserID::MovieID::Rating::Timestamp".split("::"),
	    sep="::",
	    engine="python"
	)

	#%%

	df.head()

	#%%

	df["pdate"] = pd.to_datetime(df["Timestamp"], unit='s')

	#%%

	df.head()

	#%%

	df.dtypes

	#%%

	# 实现数据统计
	df_group = df.groupby([df["pdate"].dt.month, "Rating"])["UserID"].agg(pv=np.size)

	#%%

	df_group.head(20)

	#%%



	#%% md

	对这样格式的数据，我想查看按月份，不同评分的次数趋势，是没法实现的

	需要将数据变换成每个评分是一列才可以实现

	#%% md

	### 2. 使用unstack实现数据二维透视

	目的：想要画图对比按照月份的不同评分的数量趋势

	#%%

	df_stack = df_group.unstack()
	df_stack

	#%%

	df_stack.plot()

	#%%

	# unstack和stack是互逆操作
	df_stack.stack().head(20)

	#%% md

	### 3. 使用pivot简化透视

	#%%

	df_group.head(20)

	#%%

	df_reset = df_group.reset_index()
	df_reset.head()

	#%%

	df_pivot = df_reset.pivot("pdate", "Rating", "pv")

	#%%

	df_pivot.head()

	#%%

	df_pivot.plot()

	#%% md

	***pivot方法相当于对df使用set_index创建分层索引，然后调用unstack***

	#%% md

	### 4. stack、unstack、pivot的语法

	#### stack：DataFrame.stack(level=-1, dropna=True)，将column变成index，类似把横放的书籍变成竖放

	level=-1代表多层索引的最内层，可以通过==0、1、2指定多层索引的对应层

	<img src="./other_files/reshaping_stack.png" style="margin-left:0px; width:600px" />

	#### unstack：DataFrame.unstack(level=-1, fill_value=None)，将index变成column，类似把竖放的书籍变成横放

	<img src="./other_files/reshaping_unstack.png" style="margin-left:0px; width:600px" />

	#### pivot：DataFrame.pivot(index=None, columns=None, values=None)，指定index、columns、values实现二维透视

	<img src="./other_files/reshaping_pivot.png" style="margin-left:0px; width:600px" />

Pandas 怎样快捷方便的处理日期数据
**********************************************************************************

.. code-block:: python

	Pandas日期处理的作用：将2018-01-01、1/1/2018等多种日期格式映射成统一的格式对象，在该对象上提供强大的功能支持

	几个概念：
	1. pd.to_datetime：pandas的一个函数，能将字符串、列表、series变成日期形式
	2. Timestamp：pandas表示日期的对象形式
	3. DatetimeIndex：pandas表示日期的对象列表形式

	其中：
	* DatetimeIndex是Timestamp的列表形式
	* pd.to_datetime对单个日期字符串处理会得到Timestamp
	* pd.to_datetime对日期字符串列表处理会得到DatetimeIndex

	<img src="./other_files/pandas-todatetime-timestamp-datetimeindex.png" style="margin-left:0px"/>

	#%% md

	### 问题：怎样统计每周、每月、每季度的最高温度？

	#%% md

	### 1、读取天气数据到dataframe

	#%%

	import pandas as pd
	%matplotlib inline

	#%%

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)
	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
	df.head()

	#%% md

	### 2、将日期列转换成pandas的日期

	#%%

	df.set_index(pd.to_datetime(df["ymd"]), inplace=True)

	#%%

	df.head()

	#%%

	df.index

	#%%

	# DatetimeIndex是Timestamp的列表形式
	df.index[0]

	#%% md

	### 3、 方便的对DatetimeIndex进行查询

	#%%

	# 筛选固定的某一天
	df.loc['2018-01-05']

	#%%

	# 日期区间
	df.loc['2018-01-05':'2018-01-10']

	#%%

	# 按月份前缀筛选
	df.loc['2018-03']

	#%%

	# 按月份前缀筛选
	df.loc["2018-07":"2018-09"].index

	#%%

	# 按年份前缀筛选
	df.loc["2018"].head()

	#%% md

	### 4、方便的获取周、月、季度

	Timestamp、DatetimeIndex支持大量的属性可以获取日期分量：  
	https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#time-date-components

	#%%

	# 周数字列表
	df.index.week

	#%%

	# 月数字列表
	df.index.month

	#%%

	# 季度数字列表
	df.index.quarter

	#%% md

	### 5、统计每周、每月、每个季度的最高温度

	#%% md

	#### 统计每周的数据

	#%%

	df.groupby(df.index.week)["bWendu"].max().head()

	#%%

	df.groupby(df.index.week)["bWendu"].max().plot()

	#%% md

	#### 统计每个月的数据

	#%%

	df.groupby(df.index.month)["bWendu"].max()

	#%%

	df.groupby(df.index.month)["bWendu"].max().plot()

	#%% md

	#### 统计每个季度的数据

	#%%

	df.groupby(df.index.quarter)["bWendu"].max()

	#%%

	df.groupby(df.index.quarter)["bWendu"].max().plot()

Pandas 怎么处理日期索引的缺失
**********************************************************************************

.. code-block:: python

	问题：按日期统计的数据，缺失了某天，导致数据不全该怎么补充日期？

	公众号：蚂蚁学Python

	可以用两种方法实现：  
	1、DataFrame.reindex，调整dataframe的索引以适应新的索引  
	2、DataFrame.resample，可以对时间序列重采样，支持补充缺失值

	#%% md

	## 问题：如果缺失了索引该怎么填充？

	#%%

	import pandas as pd
	%matplotlib inline

	#%%

	df = pd.DataFrame({
	    "pdate": ["2019-12-01", "2019-12-02", "2019-12-04", "2019-12-05"],
	    "pv": [100, 200, 400, 500],
	    "uv": [10, 20, 40, 50],
	})

	df

	#%%

	df.set_index("pdate").plot()

	#%% md

	***问题，这里缺失了2019-12-03的数据，导致数据不全该怎么补充？***

	#%% md

	## 方法1：使用pandas.reindex方法

	#%% md

	### 1、将df的索引变成日期索引

	#%%

	df_date = df.set_index("pdate")
	df_date

	#%%

	df_date.index

	#%%

	# 将df的索引设置为日期索引
	df_date = df_date.set_index(pd.to_datetime(df_date.index))
	df_date

	#%%

	df_date.index

	#%% md

	### 2、使用pandas.reindex填充缺失的索引

	#%%

	# 生成完整的日期序列
	pdates = pd.date_range(start="2019-12-01", end="2019-12-05")
	pdates

	#%%

	df_date_new = df_date.reindex(pdates, fill_value=0)
	df_date_new

	#%%

	df_date_new.plot()

	#%% md

	## 方法2：使用pandas.resample方法

	#%% md

	### 1、先将索引变成日期索引

	#%%

	df

	#%%

	df_new2 = df.set_index(pd.to_datetime(df["pdate"])).drop("pdate", axis=1)
	df_new2

	#%%

	df_new2.index

	#%% md

	### 2、使用dataframe的resample的方法按照天重采样

	#%% md

	resample的含义：  
	改变数据的时间频率，比如把天数据变成月份，或者把小时数据变成分钟级别

	resample的语法：    
	(DataFrame or Series).resample(arguments).(aggregate function)

	resample的采样规则参数：  
	https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#offset-aliases

	#%%

	# 由于采样会让区间变成一个值，所以需要指定mean等采样值的设定方法
	df_new2 = df_new2.resample("D").mean().fillna(0)
	df_new2

	#%%

	# resample的使用方式
	df_new2.resample("2D").mean()

Pandas怎样实现Excel的vlookup并且在指定列后面输出？
**********************************************************************************

.. code-block:: python

	背景：  
	1. 有两个excel，他们有相同的一个列；  
	2. 按照这个列合并成一个大的excel，即vlookup功能，要求：  
	    * 只需要第二个excel的少量的列，比如从40个列中挑选2个列  
	    * 新增的来自第二个excel的列需要放到第一个excel指定的列后面；  
	3. 将结果输出到一个新的excel;

	微信公众号：蚂蚁学Python

	#%% md

	### 步骤1：读取两个数据表

	#%%

	import pandas as pd

	#%%

	# 学生成绩表
	df_grade = pd.read_excel("./course_datas/c23_excel_vlookup/学生成绩表.xlsx") 
	df_grade.head()

	#%%

	# 学生信息表
	df_sinfo = pd.read_excel("./course_datas/c23_excel_vlookup/学生信息表.xlsx") 
	df_sinfo.head()

	#%% md

	***目标：怎样将第二个“学生信息表”的姓名、性别两列，添加到第一个表“学生成绩表”，并且放在第一个表的“学号”列后面？***

	#%% md

	### 步骤2：实现两个表的关联

	即excel的vloopup功能

	#%%

	# 只筛选第二个表的少量的列
	df_sinfo = df_sinfo[["学号", "姓名", "性别"]]
	df_sinfo.head()

	#%%

	df_merge = pd.merge(left=df_grade, right=df_sinfo, left_on="学号", right_on="学号")
	df_merge.head()

	#%% md

	### 步骤3：调整列的顺序

	#%%

	df_merge.columns

	#%% md

	#### 问题：怎样将'姓名', '性别'两列，放到'学号'的后面？

	接下来需要用Python的语法实现列表的处理

	#%%

	# 将columns变成python的列表形式
	new_columns = df_merge.columns.to_list()
	new_columns

	#%%

	# 按逆序insert，会将"姓名"，"性别"放到"学号"的后面
	for name in ["姓名", "性别"][::-1]:
	    new_columns.remove(name)
	    new_columns.insert(new_columns.index("学号")+1, name)

	#%%

	new_columns

	#%%

	df_merge = df_merge.reindex(columns=new_columns)
	df_merge.head()

	#%% md

	### 步骤4：输出最终的Excel文件

	#%%

	df_merge.to_excel("./course_datas/c23_excel_vlookup/合并后的数据表.xlsx", index=False)

Pandas怎样结合Pyecharts绘制交互性折线图？
**********************************************************************************

.. code-block:: python

	背景：  
	* Pandas是Python用于数据分析领域的超级牛的库
	* Echarts是百度开源的非常好用强大的可视化图表库，Pyecharts是它的Python库版本

	#%% md

	## 1、读取数据

	#%%

	import pandas as pd

	#%%

	xlsx_path = "./datas/stocks/baidu_stocks.xlsx"
	df = pd.read_excel(xlsx_path, index_col="datetime", parse_dates=True)
	df.head()

	#%%

	df.index

	#%%

	df.sort_index(inplace=True)
	df.head()

	#%% md

	## 2、使用Pyecharts绘制折线图

	#%%

	# 如果没有安装，使用pip install pyecharts安装
	from pyecharts.charts import Line
	from pyecharts import options as opts

	#%%

	# 折线图
	line = Line()

	# x轴
	line.add_xaxis(df.index.to_list())

	# 每个y轴
	line.add_yaxis("开盘价", df["open"].round(2).to_list())
	line.add_yaxis("收盘价", df["close"].round(2).to_list())

	# 图表配置
	line.set_global_opts(
	    title_opts=opts.TitleOpts(title="百度股票2019年"),
	    tooltip_opts=opts.TooltipOpts(trigger="axis", axis_pointer_type="cross")
	)

	#%%

	# 渲染数据
	line.render_notebook()

Pandas结合Sklearn实现泰坦尼克存活率预测
**********************************************************************************

.. code-block:: python

	### 实例目标：实现泰坦尼克存活预测

	处理步骤：  
	1、输入数据：使用Pandas读取训练数据(历史数据，特点是已经知道了这个人最后有没有活下来)  
	2、训练模型：使用Sklearn训练模型  
	3、使用模型：对于一个新的不知道存活的人，预估他存活的概率   

	#%% md

	### 步骤1：读取训练数据

	#%%

	import pandas as pd

	#%%

	df_train = pd.read_csv("./datas/titanic/titanic_train.csv")
	df_train.head()

	#%% md

	***其中，Survived==1代表这个人活下来了、==0代表没活下来；其他的都是这个人的信息和当时的仓位、票务情况***

	#%%

	# 我们只挑选两列，作为预测需要的特征
	feature_cols = ['Pclass', 'Parch']
	X = df_train.loc[:, feature_cols]
	X.head()

	#%%

	# 单独提取是否存活的列，作为预测的目标
	y = df_train.Survived
	y.head()

	#%% md

	### 步骤2：训练模型

	#%%

	from sklearn.linear_model import LogisticRegression
	# 创建模型对象
	logreg = LogisticRegression()

	# 实现模型训练
	logreg.fit(X, y)

	#%%

	logreg.score(X, y)

	#%% md

	### 步骤3：对于未知数据使用模型

	机器学习的核心目标，是使用模型预测未知的事物

	比如预测股票明天是涨还是跌、一套新的二手房成交价大概多少钱、用户打开APP最可能看那些视频等问题

	#%%

	# 找一个历史数据中不存在的数据
	X.drop_duplicates().sort_values(by=["Pclass", "Parch"])

	#%%

	# 预测这个数据存活的概率
	logreg.predict([[2, 4]])

	#%%

	logreg.predict_proba([[2, 4]])

Pandas处理分析网站原始访问日志
**********************************************************************************

.. code-block:: python

	目标：真实项目的实战，探索Pandas的数据处理与分析

	实例：  
	数据来源：我自己的wordpress博客http://www.crazyant.net/ 的访问日志    

	实现步骤：  
	1、读取数据、清理、格式化  
	2、统计爬虫spider的访问比例，输出柱状图  
	3、统计http状态码的访问占比，输出饼图  
	4、统计按小时、按天的PV/UV流量趋势，输出折线图  

	#%% md

	### 1、读取数据并清理格式化

	#%%

	import pandas as pd
	import numpy as np

	pd.set_option('display.max_colwidth', -1)

	from pyecharts import options as opts
	from pyecharts.charts import Bar,Pie,Line

	#%%

	# 读取整个目录，将所有的文件合并到一个dataframe
	data_dir = "./datas/crazyant/blog_access_log"

	df_list = []

	import os
	for fname in os.listdir(f"{data_dir}"):
	    df_list.append(pd.read_csv(f"{data_dir}/{fname}", sep=" ", header=None, error_bad_lines=False))

	df = pd.concat(df_list)

	#%%

	df.head()

	#%%

	df = df[[0, 3, 6, 9]].copy()
	df.head()

	#%%

	df.columns = ["ip", "stime", "status", "client"]
	df.head()

	#%%

	df.dtypes

	#%% md

	### 2、统计spider的比例

	#%%

	df["is_spider"] = df["client"].str.lower().str.contains("spider")
	df.head()

	#%%

	df_spider = df["is_spider"].value_counts()
	df_spider

	#%%

	bar = (
	        Bar()
	        .add_xaxis([str(x) for x in df_spider.index])
	        .add_yaxis("是否Spider", df_spider.values.tolist())
	        .set_global_opts(title_opts=opts.TitleOpts(title="爬虫访问量占比"))
	)
	bar.render_notebook()

	#%% md

	### 3、访问状态码的数量对比

	#%%

	df_status = df.groupby("status").size()
	df_status

	#%%

	list(zip(df_status.index, df_status))

	#%%

	pie = (
	        Pie()
	        .add("状态码比例", list(zip(df_status.index, df_status)))
	        .set_series_opts(label_opts=opts.LabelOpts(formatter="{b}: {c}"))
	    )
	pie.render_notebook()

	#%% md

	### 4、实现按小时、按天粒度的流量统计

	#%%

	df.head()

	#%%

	df["stime"] = pd.to_datetime(df["stime"].str[1:], format="%d/%b/%Y:%H:%M:%S")
	df.head()

	#%%

	df.set_index("stime", inplace=True)
	df.sort_index(inplace=True)
	df.head()

	#%%

	df.index

	#%%

	# 按小时统计
	#df_pvuv = df.resample("H")["ip"].agg(pv=np.size, uv=pd.Series.nunique)

	# 按每6个小时统计
	#df_pvuv = df.resample("6H")["ip"].agg(pv=np.size, uv=pd.Series.nunique)

	# 按天统计
	df_pvuv = df.resample("D")["ip"].agg(pv=np.size, uv=pd.Series.nunique)

	df_pvuv.head()

	#%%

	line = (
	        Line()
	        .add_xaxis(df_pvuv.index.to_list())
	        .add_yaxis("PV", df_pvuv["pv"].to_list())
	        .add_yaxis("UV", df_pvuv["uv"].to_list())
	        .set_global_opts(
	            title_opts=opts.TitleOpts(title="PVUV数据对比"),
	            tooltip_opts=opts.TooltipOpts(trigger="axis", axis_pointer_type="cross")
	        )
	    )
	line.render_notebook()

.. |image0| image:: /_static/python/python_222.png
.. |image1| image:: /_static/python/python_334.png






























































































































