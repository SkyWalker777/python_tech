Pandas 篇 6
##################################################################################

Pandas实现groupby聚合后不同列数据统计
**********************************************************************************

.. code-block:: python
	:linenos:

	电影评分数据集（UserID，MovieID，Rating，Timestamp）  

	<b>聚合后单列-单指标统计：每个MovieID的平均评分</b>  
	df.groupby("MovieID")["Rating"].mean()

	<b>聚合后单列-多指标统计：每个MoiveID的最高评分、最低评分、平均评分</b>  
	df.groupby("MovieID")["Rating"].agg(mean="mean", max="max", min=np.min)  
	df.groupby("MovieID").agg({"Rating":['mean', 'max', np.min]})

	<b>聚合后多列-多指标统计：每个MoiveID的评分人数，最高评分、最低评分、平均评分</b>  
	df.groupby("MovieID").agg(
	    rating_mean=("Rating", "mean"),
	    user_count=("UserID", lambda x : x.nunique())  
	df.groupby("MovieID").agg(
	    {"Rating": ['mean', 'min', 'max'],
	    "UserID": lambda x :x.nunique()})  
	df.groupby("MovieID").apply(
	    lambda x: pd.Series(
	        {"min": x["Rating"].min(), "mean": x["Rating"].mean()}))  

	<b>记忆：</b>agg(新列名=函数)、agg(新列名=(原列名，函数))、agg({"原列名"：函数/列表})  
	agg函数的两种形式，等号代表“把结果赋值给新列”，字典/元组代表“对这个列运用这些函数”

	官网文档：https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.core.groupby.DataFrameGroupBy.agg.html

	#%% md

	### 读取数据

	#%%

	import pandas as pd
	import numpy as np

	#%%

	df = pd.read_csv(
	    "./datas/movielens-1m/ratings.dat", 
	    sep="::",
	    engine='python', 
	    names="UserID::MovieID::Rating::Timestamp".split("::")
	)

	#%%

	df.head(3)

	#%% md

	### 聚合后单列-单指标统计

	#%%

	# 每个MovieID的平均评分
	result = df.groupby("MovieID")["Rating"].mean()
	result.head()

	#%%

	type(result)

	#%% md

	### 聚合后单列-多指标统计

	#%% md

	每个MoiveID的最高评分、最低评分、平均评分

	#%% md

	#### 方法1：agg函数传入多个结果列名=函数名形式

	#%%

	result = df.groupby("MovieID")["Rating"].agg(
	    mean="mean", max="max", min=np.min
	)
	result.head()

	#%% md

	#### 方法2：agg函数传入字典，key是column名，value是函数列表

	#%%

	# 每个MoiveID的最高评分、最低评分、平均评分
	result = df.groupby("MovieID").agg(
	    {"Rating":['mean', 'max', np.min]}
	)
	result.head()

	#%%

	result.columns = ['age_mean', 'age_min', 'age_max']
	result.head()

	#%% md

	### 聚合后多列-多指标统计

	每个MoiveID的评分人数，最高评分、最低评分、平均评分

	#%% md

	#### 方法1：agg函数传入字典，key是原列名，value是原列名和函数元组

	#%%

	# 回忆：agg函数的两种形式，等号代表“把结果赋值给新列”，字典/元组代表“对这个列运用这些函数”
	result = df.groupby("MovieID").agg(
	        rating_mean=("Rating", "mean"),
	        rating_min=("Rating", "min"),
	        rating_max=("Rating", "max"),
	        user_count=("UserID", lambda x : x.nunique())
	)
	result.head()

	#%% md

	#### 方法2：agg函数传入字典，key是原列名，value是函数列表

	统计后是二级索引，需要做索引处理

	#%%

	result = df.groupby("MovieID").agg(
	    {
	        "Rating": ['mean', 'min', 'max'],
	        "UserID": lambda x :x.nunique()
	    }
	)
	result.head()

	#%%

	result["Rating"].head(3)

	#%%

	result.columns = ["rating_mean", "rating_min","rating_max","user_count"]
	result.head()

	#%% md

	#### 方法3：使用groupby之后apply对每个子df单独统计

	#%%

	def agg_func(x):
	    """注意，这个x是子DF"""
	    
	    # 这个Series会变成一行，字典KEY是列名
	    return pd.Series({
	        "rating_mean": x["Rating"].mean(),
	        "rating_min": x["Rating"].min(),
	        "rating_max": x["Rating"].max(),
	        "user_count": x["UserID"].nunique()
	    })

	result = df.groupby("MovieID").apply(agg_func)
	result.head()

Python使用Pandas将Excel存入MySQL
**********************************************************************************

.. code-block:: python
	:linenos:

	一个典型的数据处理流：
	1. Pandas从多方数据源读取数据，比如excel、csv、mysql、爬虫等等
	2. Pandas对数据做过滤、统计分析
	3. Pandas将数据存储到MySQL，用于Web页面展示、后续的进一步SQL分析等等

	官网文档：  
	https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_sql.html#pandas.DataFrame.to_sql

	#%% md

	### 数据准备：学生信息Excel表

	#%%

	import pandas as pd

	#%%

	df = pd.read_excel("./course_datas/c23_excel_vlookup/学生信息表.xlsx")
	df.head()

	#%%

	# 展示索引的name
	df.index.name

	#%%

	df.index.name = "id"
	df.head()

	#%% md

	### 创建sqlalchemy对象连接MySQL

	#%% md

	SQLAlchemy是Python中的ORM框架，
	Object-Relational Mapping，把关系数据库的表结构映射到对象上。

	* 官网：https://www.sqlalchemy.org/
	* 如果sqlalchemy包不存在，用这个命令安装：pip install sqlalchemy
	* 需要安装依赖Python库：pip install mysql-connector-python

	可以直接执行SQL语句

	#%%

	from sqlalchemy import create_engine

	#%%

	engine = create_engine("mysql+mysqlconnector://root:123456@127.0.0.1:3306/test", echo=False)

	#%% md

	### 方法1：当数据表不存在时，每次覆盖整个表

	每次运行会drop table，新建表

	#%%

	df.to_sql(name='student', con=engine, if_exists="replace")

	#%%

	engine.execute("show create table student").first()[1]

	#%%

	print(engine.execute("show create table student").first()[1])

	#%%

	engine.execute("select count(1) from student").first()

	#%%

	engine.execute("select * from student limit 5").fetchall()

	#%% md

	### 方法2：当数据表存在时，每次新增数据

	场景：每天会新增一部分数据，要添加到数据表，怎么处理？

	#%%

	df_new = df.loc[:4, :]
	df_new

	#%%

	df_new.to_sql(name='student', con=engine, if_exists="append")

	#%%

	engine.execute("SELECT * FROM student where id<5 ").fetchall()

	#%% md

	#### 问题解决：先根据数据KEY删除旧数据

	#%%

	df_new.index

	#%%

	for id in df_new.index:
	    ## 先删除要新增的数据
	    delete_sql = f"delete from student where id={id}"
	    print(delete_sql)
	    engine.execute(delete_sql)

	#%%

	engine.execute("SELECT * FROM student where id<5 ").fetchall()

	#%%

	engine.execute("select count(1) from student").first()

	#%%

	# 新增数据到表中
	df_new.to_sql(name='student', con=engine, if_exists="append")

	#%%

	engine.execute("SELECT * FROM student where id<5 ").fetchall()

	#%%

	engine.execute("SELECT count(1) FROM student").first()

Python批量翻译英语单词
**********************************************************************************

.. code-block:: python
	:linenos:

	***用途：***   
	对批量的英语文本，生成英语-汉语翻译的单词本，提供Excel下载

	***本代码实现：***
	1. 提供一个英文文章URL，自动下载网页；
	2. 实现网页中所有英语单词的翻译；
	3. 下载翻译结果的Excel

	***涉及技术：***
	1. pandas的读取csv、多数据merge、输出Excel
	2. requests库下载HTML网页
	3. BeautifulSoup解析HTML网页
	4. Python正则表达式实现英文分词

	#%% md

	### 1. 读取英语-汉语翻译词典文件

	#%% md

	词典文件来自：https://github.com/skywind3000/ECDICT
	使用步骤：
	1. 下载代码打包：https://github.com/skywind3000/ECDICT/archive/master.zip
	2. 解压master.zip，然后解压其中的‪stardict.csv文件

	#%%

	import pandas as pd

	#%%

	# 注意：stardict.csv的地址需要替换成你自己的文件地址
	df_dict = pd.read_csv("D:/tmp/ECDICT-master/stardict.csv")

	#%%

	df_dict.shape

	#%%

	df_dict.sample(10).head()

	#%%

	# 把word、translation之外的列扔掉
	df_dict = df_dict[["word", "translation"]]
	df_dict.head()

	#%% md

	### 2. 下载网页，得到网页内容

	#%%

	import requests

	#%%

	# Pandas官方文档中的一个URL
	url = "https://pandas.pydata.org/docs/user_guide/indexing.html"

	#%%

	html_cont = requests.get(url).text

	#%%

	html_cont[:100]

	#%% md

	### 3. 提取HTML的正文内容
	即：去除HTML标签，获取正文

	#%%

	from bs4 import BeautifulSoup
	soup = BeautifulSoup(html_cont)
	html_text = soup.get_text()

	#%%

	html_text[:500]

	#%% md

	### 4. 英文分词和数据清洗

	#%%

	# 分词
	import re
	word_list = re.split("""[ ,.\(\)/\n|\-:=\$\["']""",html_text)
	word_list[:10]

	#%%

	# 读取停用词表，从网上复制的，位于当前目录下
	with open("./datas/stop_words/stop_words.txt") as fin:
	    stop_words=set(fin.read().split("\n"))
	list(stop_words)[:10]

	#%%

	# 数据清洗
	word_list_clean = []
	for word in word_list:
	    word = str(word).lower().strip()
	    # 过滤掉空词、数字、单个字符的词、停用词
	    if not word or word.isnumeric() or len(word)<=1 or word in stop_words:
	        continue
	    word_list_clean.append(word)
	word_list_clean[:20]

	#%% md

	### 5. 分词结果构造成一个DataFrame

	#%%

	df_words = pd.DataFrame({
	    "word": word_list_clean
	})
	df_words.head()

	#%%

	df_words.shape

	#%%

	# 统计词频
	df_words = (
	    df_words
	    .groupby("word")["word"]
	    .agg(count="size")
	    .reset_index()
	    .sort_values(by="count", ascending=False)
	)
	df_words.head(10)

	#%% md

	### 6. 和单词词典实现merge

	#%%

	df_merge = pd.merge(
	    left = df_dict,
	    right = df_words,
	    left_on = "word",
	    right_on = "word"
	)

	#%%

	df_merge.sample(10)

	#%%

	df_merge.shape

	#%% md

	### 7. 存入Excel

	#%%

	df_merge.to_excel("./38. batch_chinese_english.xlsx", index=False)

	#%% md

	### 后续升级：
	1. 可以提供txt/excel/word/pdf的批量输入，生成单词本；
	2. 可以做成网页、微信小程序的形式，在线访问和使用
	3. 用户可以标记或上传“已经认识的词语”，每次过滤掉

Pandas处理Excel - 复杂多列到多行转换
**********************************************************************************

.. code-block:: python
	:linenos:

	#### 用户需求图片
	<img src="./course_datas/c39_explode_to_manyrows/用户需求图片.png" />

	#%% md

	#### 分析：
	1. 一行变多行，可以用explode实现；
	2. 要使用explode，需要先将多列变成一列；
	3. 注意有的列为空，需要做空值过滤；

	#%% md

	### 1. 读取数据

	#%%

	import pandas as pd

	#%%

	file_path = "./course_datas/c39_explode_to_manyrows/读者提供的数据-输入.xlsx"
	df = pd.read_excel(file_path)

	#%%

	df

	#%% md

	### 2. 把多列合并到一列

	#%%

	# 提取待合并的所有列名，一会可以把它们drop掉
	merge_names = list(df.loc[:, "Supplier":].columns.values)
	merge_names

	#%%

	def merge_cols(x):
	    """
	    x是一个行Series，把它们按分隔符合并
	    """
	    # 删除为空的列
	    x = x[x.notna()]
	    # 使用x.values用于合并
	    y = x.values
	    # 合并后的列表，每个元素是"Supplier" + "Supplier PN"对
	    result = []
	    # range的步长为2，目的是每两列做合并
	    for idx in range(0, len(y), 2):
	        # 使用竖线作为"Supplier" + "Supplier PN"之间的分隔符
	        result.append(f"{y[idx]}|{y[idx+1]}")
	    # 将所有两两对，用#分割，返回一个大字符串
	    return "#".join(result)

	# 添加新列，把待合并的所有列变成一个大字符串
	df["merge"] = df.loc[:, "Supplier":].apply(merge_cols, axis=1)
	df

	#%%

	# 把不用的列删除掉
	df.drop(merge_names, axis=1, inplace=True)
	df

	#%% md

	### 3. 使用explode把一列变多行

	#%%

	# 先将merge列变成list的形式
	df["merge"] = df["merge"].str.split("#")
	df

	#%%

	# 执行explode变成多行
	df_explode = df.explode("merge")
	df_explode

	#%% md

	### 4. 将一列还原成结果的多列

	#%%

	# 分别从merge中提取两列
	df_explode["Supplier"]=df_explode["merge"].str.split("|").str[0]
	df_explode["Supplier PN"]=df_explode["merge"].str.split("|").str[1]
	df_explode

	#%%

	# 把merge列删除掉，得到最终数据
	df_explode.drop("merge", axis=1, inplace=True)
	df_explode

	#%% md

	### 5. 输出到结果Excel

	#%%

	df_explode.to_excel("./course_datas/c39_explode_to_manyrows/读者提供的数据-输出.xlsx", index=False)

Python自动翻译英语论文PDF
**********************************************************************************

.. code-block:: python
	:linenos:

	***涉及技术：***
	1. Python读取PDF文本
	2. pandas的读取csv、多数据merge、输出Excel
	3. Python正则表达式实现英文分词

	#%% md

	### 1. 读取PDF文本内容

	#%%

	!pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pdfplumber

	#%%

	import pdfplumber
	def read_pdf(pdf_fpath):
	    pdf = pdfplumber.open(pdf_fpath)
	    page_conts = []
	    for page in pdf.pages:
	        page_conts.append(page.extract_text())
	    pdf.close()
	    return " ".join(page_conts)

	#%%

	pdf_fpath = "D:/tmp/Wide & Deep Learning for Recommender Systems.pdf"
	pdf_cont = read_pdf(pdf_fpath)

	#%%

	print(pdf_cont[:2000])

	#%% md

	### 2. 读取英语-汉语翻译词典文件

	#%% md

	词典文件来自：https://github.com/skywind3000/ECDICT
	使用步骤：
	1. 下载代码打包：https://github.com/skywind3000/ECDICT/archive/master.zip
	2. 解压master.zip，然后解压其中的‪stardict.csv文件

	#%%

	import pandas as pd

	#%%

	# 注意：stardict.csv的地址需要替换成你自己的文件地址
	df_dict = pd.read_csv("D:/tmp/ECDICT-master/stardict.csv")

	#%%

	df_dict.shape

	#%%

	df_dict.sample(10).head()

	#%%

	# 把word、translation之外的列扔掉
	df_dict = df_dict[["word", "translation"]]
	df_dict.head()

	#%% md

	### 3. 英文分词和数据清洗

	#%%

	# 分词
	import re
	word_list = re.split("""[ ,.\(\)/\n|\-:=\$\["']""", pdf_cont)
	word_list[:10]

	#%%

	# 数据清洗
	word_list_clean = []
	for word in word_list:
	    word = str(word).lower().strip()
	    # 过滤掉空词、数字、单个字符的词、停用词
	    if not word or word.isnumeric() or len(word)<=1:
	        continue
	    word_list_clean.append(word)
	word_list_clean[:20]

	#%% md

	### 4. 分词结果构造成一个DataFrame

	#%%

	df_words = pd.DataFrame({
	    "word": word_list_clean
	})
	df_words.head()

	#%%

	df_words.shape

	#%%

	# 统计词频
	df_words = (
	    df_words
	    .groupby("word")["word"]
	    .agg(count="size")
	    .reset_index()
	    .sort_values(by="count", ascending=False)
	)
	df_words.head(10)

	#%% md

	### 5. 和单词词典实现merge

	#%%

	df_merge = pd.merge(
	    left = df_dict,
	    right = df_words,
	    left_on = "word",
	    right_on = "word"
	)

	#%%

	df_merge.sample(10)

	#%%

	df_merge.shape

	#%% md

	### 6. 存入Excel

	#%%

	df_merge.to_excel("./39. pdf_chinese_english.xlsx", index=False)

Pandas怎样实现groupby聚合后字符串列的合并
**********************************************************************************

.. code-block:: python
	:linenos:

	#### 需求：  
	计算每个月的最高温度、最低温度、出现的风向列表、出现的空气质量列表

	#### 数据输入
	<img src="./other_files/grouby_join_input.png" style="margin-left:0px; width:60%"/>

	#### 数据输出
	<img src="./other_files/grouby_join_output.png" style="margin-left:0px; width:60%"/>

	#%% md

	### 读取数据

	#%%

	import pandas as pd

	#%%

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2018.csv"
	df = pd.read_csv(fpath)
	df.head(3)

	#%% md

	#### 知识：使用df.info()可以查看每列的类型

	#%%

	df.info()

	#%% md

	#### 知识：series怎样从str类型变成int

	#%%

	df["bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df["yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')
	df.head(3)

	#%% md

	#### 知识：进行日期列解析，可以方便提取月份

	#%%

	df["ymd"] = pd.to_datetime(df["ymd"])

	#%%

	df["ymd"].dt.month

	#%% md

	#### 知识：series可以用Series.unique去重

	#%%

	df["fengxiang"].unique()

	#%% md

	#### 知识：可以用",".join(series)实现数组合并成大字符串

	#%%

	",".join(df["fengxiang"].unique())

	#%% md

	### 方法1

	#%%

	result = (
	    df.groupby(df["ymd"].dt.month)
	      .agg(
	          # 新列名 = (原列名，函数)
	          最高温度=("bWendu", "max"),
	          最低温度=("yWendu", "min"),
	          风向列表=("fengxiang", lambda x : ",".join(x.unique())),
	          空气质量列表=("aqiInfo", lambda x : ",".join(x.unique()))
	      )
	      .reset_index()
	      .rename(columns={"ymd":"月份"})
	)

	#%%

	result

	#%% md

	### 方法2

	#%%

	def agg_func(x):
	    """注意，这个x是每个分组的dataframe"""
	    return pd.Series({
	        "最高温度": x["bWendu"].max(),
	        "最低温度": x["yWendu"].min(),
	        "风向列表": ",".join(x["fengxiang"].unique()),
	        "空气质量列表": ",".join(x["aqiInfo"].unique())
	    })

	result = df \
	        .groupby(df["ymd"].dt.month) \
	        .apply(agg_func) \
	        .reset_index() \
	        .rename(columns={"ymd":"月份"})

	#%%

	result

Pandas读取Excel绘制直方图
**********************************************************************************

.. code-block:: python
	:linenos:

	***直方图(Histogram)：***  
	直方图是数值数据分布的精确图形表示，是一个连续变量（定量变量）的概率分布的估计，它是一种条形图。   
	为了构建直方图，第一步是将值的范围分段，即将整个值的范围分成一系列间隔，然后计算每个间隔中有多少值。 

	#%% md

	### 1. 读取数据

	#%% md

	波斯顿房价数据集

	#%%

	import pandas as pd
	import numpy as np

	#%%

	df = pd.read_excel("./datas/boston-house-prices/housing.xlsx")

	#%%

	df

	#%%

	df.info()

	#%%

	df["MEDV"]

	#%% md

	### 2. 使用matplotlib画直方图

	#%% md

	matplotlib直方图文档：https://matplotlib.org/3.2.0/api/_as_gen/matplotlib.pyplot.hist.html

	#%%

	import matplotlib.pyplot as plt
	%matplotlib inline

	#%%

	plt.figure(figsize=(12, 5))
	plt.hist(df["MEDV"], bins=100)
	plt.show()

	#%% md

	### 3. 使用pyecharts画直方图

	#%% md

	pyecharts直方图文档：http://gallery.pyecharts.org/#/Bar/bar_histogram  
	numpy直方图文档：https://docs.scipy.org/doc/numpy/reference/generated/numpy.histogram.html

	#%%

	from pyecharts import options as opts
	from pyecharts.charts import Bar

	#%%

	# 需要自己计算有多少个间隔、以及每个间隔有多少个值
	hist,bin_edges =  np.histogram(df["MEDV"], bins=100)

	#%%

	# 这是每个间隔的分割点
	bin_edges

	#%%

	len(bin_edges)

	#%%

	# 这是间隔的计数
	hist

	#%%

	len(hist)

	#%% md

	#### 对bin_edges的解释，为什么是101个？比hist计数多1个？

	举例：如果bins是[1, 2, 3, 4]，那么会分成3个区间：[1, 2)、[2, 3)、[3, 4]；  
	其中bins的第一个值是数组的最小值，bins的最后一个元素是数组的最大值

	#%%

	# 注意观察，min是bins的第一个值，max是bins的最后一个元素
	df["MEDV"].describe()

	#%%

	# 查看bins每一个值和前一个值的差值，可以看到这是等分的数据
	np.diff(bin_edges)

	#%%

	# 这些间隔的数目，刚好等于计数hist的数目
	len(np.diff(bin_edges))

	#%%

	# pyecharts的直方图使用bar实现
	# 取bins[:-1]，意思是用每个区间的左边元素作为x轴的值
	bar = (
	    Bar()
	    .add_xaxis([str(x) for x in bin_edges[:-1]])
	    .add_yaxis("价格分布", [float(x) for x in hist], category_gap=0)
	    .set_global_opts(
	        title_opts=opts.TitleOpts(title="波斯顿房价-价格分布-直方图", pos_left="center"),
	        legend_opts=opts.LegendOpts(is_show=False)
	    )
	)

	#%%

	bar.render_notebook()

	#%% md

	<b>小作业：</b>   
	获取你们产品的销量数据、价格数据，提取得到一个一数组，画一个直方图看一下数据分布

Python处理Excel一列变多列
**********************************************************************************

.. code-block:: python
	:linenos:

	### 1. 读取数据

	#%%

	import pandas as pd

	#%%

	df = pd.read_excel("./course_datas/c42_split_onecolumn_tomany/学生数据表.xlsx")

	#%%

	df.head()

	#%% md

	### 2. 实现拆分

	#%%

	def split_func(line):
	    line["姓名"], line["性别"], line["年龄"], line["城市"] = line["数据"].split(":")
	    return line

	df = df.apply(split_func, axis=1)

	#%%

	df.head()

	#%%

	df.drop(["数据"], axis=1, inplace=True)

	#%%

	df.head()

	#%% md

	### 3. 输出到结果Excel

	#%%

	df.to_excel("./course_datas/c42_split_onecolumn_tomany/学生数据表_拆分后.xlsx", index=False)

Pandas查询数据的简便方法df.query
**********************************************************************************

.. code-block:: python
	:linenos:

	怎样进行复杂组合条件对数据查询：
	* 方式1. 使用df[(df["a"] > 3) & (df["b"]<5)]的方式；
	* 方式2. 使用df.query("a>3 & b<5")的方式；

	方法2的语法更加简洁

	性能对比：
	* 当数据量小时，方法1更快；
	* 当数据量大时，因为方法2直接用C语言实现，节省方法1临时数组的多次复制，方法2更快；

	#%%

	import pandas as pd
	print(pd.__version__)

	#%% md

	### 0、读取数据
	数据为北京2018年全年天气预报  

	#%%

	df = pd.read_csv("./datas/beijing_tianqi/beijing_tianqi_2018.csv")

	#%%

	df.head()

	#%%

	# 替换掉温度的后缀℃
	df.loc[:, "bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')
	df.loc[:, "yWendu"] = df["yWendu"].str.replace("℃", "").astype('int32')

	#%% md

	### 1、使用dataframe条件表达式查询

	#%% md

	#### 最低温度低于-10度的列表

	#%%

	df[df["yWendu"] < -10].head()

	#%% md

	#### 复杂条件查询
	注意，组合条件用&符号合并，每个条件判断都得带括号

	#%%

	## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
	df[
	    (df["bWendu"]<=30) 
	    & (df["yWendu"]>=15) 
	    & (df["tianqi"]=='晴') 
	    & (df["aqiLevel"]==1)]

	#%% md

	### 2、使用df.query可以简化查询

	形式：DataFrame.query(expr, inplace=False, **kwargs)

	其中expr为要返回boolean结果的字符串表达式

	形如：
	* df.query('a<100')
	* df.query('a < b & b < c')，或者df.query('(a<b)&(b<c)')

	df.query可支持的表达式语法：
	* 逻辑操作符: &, |, ~
	* 比较操作符: <, <=, ==, !=, >=, >
	* 单变量操作符: -
	* 多变量操作符: +, -, *, /, %

	df.query中可以使用@var的方式传入外部变量

	df.query支持的语法来自NumExpr，地址：   
	https://numexpr.readthedocs.io/projects/NumExpr3/en/latest/index.html


	#%% md

	#### 查询最低温度低于-10度的列表

	#%%

	df.query("yWendu < 3").head(3)

	#%% md

	#### 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据

	#%%

	## 查询最高温度小于30度，并且最低温度大于15度，并且是晴天，并且天气为优的数据
	df.query("bWendu<=30 & yWendu>=15 & tianqi=='晴' & aqiLevel==1")

	#%% md

	#### 查询温差大于15度的日子

	#%%

	df.query("bWendu-yWendu >= 15").head()

	#%% md

	#### 可以使用外部的变量

	#%%

	# 查询温度在这两个温度之间的数据
	high_temperature = 15
	low_temperature = 13

	#%%

	df.query("yWendu<=@high_temperature & yWendu>=@low_temperature").head()

.. tip::

	Pandas GUI 神器，自动转代码: "https://blog.csdn.net/SeizeeveryDay/article/details/121173429"





























































































