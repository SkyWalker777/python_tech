Pandas 篇 5
##################################################################################

Pandas怎样找出最影响结果的那些特征？
**********************************************************************************

.. code-block:: python
	:linenos:

	应用场景：  
	* 机器学习的特征选择，去除无用的特征，可以提升模型效果、降低训练时间等等  
	* 数据分析领域，找出收入波动的最大因素！！

	#%% md

	## 实例演示：泰坦尼克沉船事件中，最影响生死的因素有哪些？

	#%% md

	### 1、导入相关的包

	#%%

	import pandas as pd
	import numpy as np

	# 特征最影响结果的K个特征
	from sklearn.feature_selection import SelectKBest

	# 卡方检验，作为SelectKBest的参数
	from sklearn.feature_selection import chi2

	#%% md

	### 2、导入泰坦尼克号的数据

	#%%

	df = pd.read_csv("./datas/titanic/titanic_train.csv")
	df.head()

	#%%

	df = df[["PassengerId", "Survived", "Pclass", "Sex", "Age", "SibSp", "Parch", "Fare", "Embarked"]].copy()
	df.head()

	#%% md

	### 3、数据清理和转换

	#%% md

	#### 3.1 查看是否有空值列

	#%%

	df.info()

	#%% md

	#### 3.2 给Age列填充平均值

	#%%

	df["Age"] = df["Age"].fillna(df["Age"].median())

	#%%

	df.head()

	#%% md

	#### 3.2 将性别列变成数字

	#%%

	# 性别
	df.Sex.unique()

	#%%

	df.loc[df["Sex"] == "male", "Sex"] = 0
	df.loc[df["Sex"] == "female", "Sex"] = 1

	#%%

	df.head()

	#%% md

	#### 3.3 给Embarked列填充空值，字符串转换成数字

	#%%

	# Embarked
	df.Embarked.unique()

	#%%

	# 填充空值
	df["Embarked"] = df["Embarked"].fillna(0)

	# 字符串变成数字
	df.loc[df["Embarked"] == "S", "Embarked"] = 1
	df.loc[df["Embarked"] == "C", "Embarked"] = 2
	df.loc[df["Embarked"] == "Q", "Embarked"] = 3

	#%%

	df.head()

	#%% md

	### 4、将特征列和结果列拆分开

	#%%

	y = df.pop("Survived")
	X = df

	#%%

	X.head()

	#%%

	y.head()

	#%% md

	### 5、使用卡方检验选择topK的特征

	#%%

	# 选择所有的特征，目的是看到特征重要性排序
	bestfeatures = SelectKBest(score_func=chi2, k=len(X.columns))
	fit = bestfeatures.fit(X, y)

	#%% md

	### 6、按照重要性顺序打印特征列表

	#%%

	df_scores = pd.DataFrame(fit.scores_)
	df_scores

	#%%

	df_columns = pd.DataFrame(X.columns)
	df_columns

	#%%

	# 合并两个df
	df_feature_scores = pd.concat([df_columns,df_scores],axis=1)
	# 列名
	df_feature_scores.columns = ['feature_name','Score']  #naming the dataframe columns

	# 查看
	df_feature_scores

	#%%

	df_feature_scores.sort_values(by="Score", ascending=False)

Pandas的Categorical数据类型可以降低数据存储提升计算速度
**********************************************************************************

.. code-block:: python
	:linenos:

	<img src="./other_files/pandas-categorical.png" style="width:70%; margin-left:0px"/>

	#%% md

	### 1、读取数据

	#%%

	import pandas as pd

	#%%

	df = pd.read_csv("./datas/movielens-1m/users.dat",
	                 sep="::",
	                 engine="python",
	                 header=None,
	                 names="UserID::Gender::Age::Occupation::Zip-code".split("::"))

	#%%

	df.head()

	#%%

	df.info()

	#%%

	df.info(memory_usage="deep")

	#%%

	df_cat = df.copy()
	df_cat.head()

	#%% md

	### 2、使用categorical类型降低存储量

	#%%

	df_cat["Gender"] = df_cat["Gender"].astype("category")

	#%%

	df_cat.info(memory_usage="deep")

	#%%

	df_cat.head()

	#%%

	df_cat["Gender"].value_counts()

	#%% md

	### 3、提升运算速度

	#%%

	%timeit df.groupby("Gender").size()

	#%%

	%timeit df_cat.groupby("Gender").size()

Pandas的get_dummies用于机器学习的特征处理
**********************************************************************************

.. code-block:: python
	:linenos:

	分类特征有两种：
	* 普通分类：性别、颜色
	* 顺序分类：评分、级别

	对于评分，可以把这个分类直接转换成1、2、3、4、5表示，因为它们之间有顺序、大小关系

	但是对于颜色这种分类，直接用1/2/3/4/5/6/7表达，是不合适的，因为机器学习会误以为这些数字之间有大小关系

	get_dummies就是用于颜色、性别这种特征的处理，也叫作one-hot-encoding处理

	比如：
	* 男性：1 0
	* 女性：0 1

	这就叫做one-hot-encoding，是机器学习对类别的特征处理

	#%% md

	### 1、读取泰坦尼克数据集

	#%%

	import pandas as pd

	#%%

	df_train = pd.read_csv("./datas/titanic/titanic_train.csv")
	df_train.head()

	#%%

	df_train.drop(columns=["Name", "Ticket", "Cabin"], inplace=True)
	df_train.head()

	#%%

	df_train.info()

	#%% md

	#### 特征说明：
	* 数值特征：Fare
	* 分类-有序特征：Age
	* 分类-普通特征：PassengerId、Pclass、Sex、SibSp、Parch、Embarked

	Survived为要预测的Label

	#%% md

	### 2、分类有序特征可以用数字的方法处理

	#%%

	# 使用年龄的平均值，填充空值
	df_train["Age"] = df_train["Age"].fillna(df_train["Age"].mean())

	#%%

	df_train.info()

	#%% md

	### 3、普通无序分类特征可以用get_dummies编码

	其实就是one-hot编码

	#%%

	# series
	pd.get_dummies(df_train["Sex"]).head()

	#%% md

	***注意，One-hot-Encoding一般要去掉一列，不然会出现dummy variable trap，因为一个人不是male就是femal，它俩有推导关系***
	https://www.geeksforgeeks.org/ml-dummy-variable-trap-in-regression-models/

	#%%

	# 便捷方法，用df全部替换
	needcode_cat_columns = ["Pclass","Sex","SibSp","Parch","Embarked"]
	df_coded = pd.get_dummies(
	    df_train,
	    # 要转码的列
	    columns=needcode_cat_columns,
	    # 生成的列名的前缀
	    prefix=needcode_cat_columns,
	    # 把空值也做编码
	    dummy_na=True,
	    # 把1 of k移除（dummy variable trap）
	    drop_first=True
	)

	#%%

	df_coded.head()

	#%% md

	### 4、机器学习模型训练

	#%%

	y = df_coded.pop("Survived")
	y.head()

	#%%

	X = df_coded
	X.head()

	#%%

	from sklearn.linear_model import LogisticRegression
	# 创建模型对象
	logreg = LogisticRegression(solver='liblinear')

	# 实现模型训练
	logreg.fit(X, y)

	#%%

	logreg.score(X, y)

Pandas使用explode实现一行变多行统计
**********************************************************************************

.. code-block:: python
	:linenos:

	解决实际问题：一个字段包含多个值，怎样将这个值拆分成多行，然后实现统计

	比如：一个电影有多个分类、一个人有多个喜好，需要按分类、喜好做统计

	#%% md

	### 1、读取数据

	#%%

	import pandas as pd

	#%%

	df = pd.read_csv(
	    "./datas/movielens-1m/movies.dat",
	    header=None,
	    names="MovieID::Title::Genres".split("::"),
	    sep="::",
	    engine="python"
	)

	#%%

	df.head()

	#%% md

	***问题：怎样实现这样的统计，每个题材有多少部电影？***

	解决思路：
	* 将Genres按照分隔符|拆分
	* 按Genres拆分成多行
	* 统计每个Genres下的电影数目

	#%% md

	### 2、将Genres字段拆分成列表

	#%%

	df.info()

	#%%

	# 当前的Genres字段是字符串类型
	type(df.iloc[0]["Genres"])

	#%%

	# 新增一列
	df["Genre"] = df["Genres"].map(lambda x:x.split("|"))

	#%%

	df.head()

	#%%

	# Genre的类型是列表
	print(df["Genre"][0])
	print(type(df["Genre"][0]))

	#%%

	df.info()

	#%% md

	### 3、使用explode将一行拆分成多行

	#%% md

	语法：pandas.DataFrame.explode(column)  
	将dataframe的一个list-like的元素按行复制，index索引随之复制

	#%%

	df_new = df.explode("Genre")

	#%%

	df_new.head(10)

	#%% md

	### 4、实现拆分后的题材的统计

	#%%

	%matplotlib inline
	df_new["Genre"].value_counts().plot.bar()

Pandas借助Python爬虫读取HTML网页表格存储到Excel文件
**********************************************************************************

.. code-block:: python
	:linenos:

	实现目标：
	* 网易有道词典可以用于英语单词查询，可以将查询的单词加入到单词本;
	* 当前没有导出全部单词列表的功能。为了复习方便，可以爬取所有的单词列表，存入Excel方便复习

	涉及技术：
	* Pandas：Python语言最强大的数据处理和数据分析库
	* Python爬虫：可以将网页下载下来然后解析，使用requests库实现，需要绕过登录验证


	#%%

	import requests
	import requests.cookies
	import json
	import time
	import pandas as pd

	#%% md

	### 0. 处理流程

	#%% md

	<h4>输入网页：有道词典-单词本</h4>
	<img src="./course_datas/c32_read_html/youdao_cidian.png" style="width:50%; margin-left:0px;"/>

	#%% md

	<h4>处理流程</h4>
	<img src="./course_datas/c32_read_html/ppt_flow.png" style="width:70%; margin-left:0px;"/>

	#%% md

	<h4>数据结果到Excel文件（方便打印复习）：</h4>
	<img src="./course_datas/c32_read_html/output_excel.png" style="width:70%; margin-left:0px;"/>

	#%% md

	### 1. 登录网易有道词典的PC版，微信扫码登录，复制cookies到文件

	* PC版地址：http://dict.youdao.com/  
	* Chrome插件可以复制Cookies为Json格式：http://www.editthiscookie.com/

	#%%

	cookie_jar = requests.cookies.RequestsCookieJar()

	with open("./course_datas/c32_read_html/cookie.txt") as fin:
	    cookiejson = json.loads(fin.read())
	    for cookie in cookiejson:
	        cookie_jar.set(
	            name=cookie["name"],
	            value=cookie["value"],
	            domain=cookie["domain"],
	            path=cookie["path"]
	        )

	#%%

	cookie_jar

	#%% md

	### 2. 将html都下载下来存入列表

	#%%

	htmls = []
	url = "http://dict.youdao.com/wordbook/wordlist?p={idx}&tags="
	for idx in range(6):
	    time.sleep(1)
	    print("**爬数据：第%d页" % idx)
	    r = requests.get(url.format(idx=idx), cookies=cookie_jar)
	    htmls.append(r.text)

	#%%

	htmls[0]

	#%% md

	### 3. 使用Pandas解析网页中的表格

	#%%

	df = pd.read_html(htmls[0])

	#%%

	print(len(df))
	print(type(df))

	#%%

	df[0].head(3)

	#%%

	df[1].head(3)

	#%%

	df_cont = df[1]

	#%%

	df_cont.columns = df[0].columns

	#%%

	df_cont.head(3)

	#%%

	# 收集6个网页的表格
	df_list = []
	for html in htmls:
	    df = pd.read_html(html)
	    df_cont = df[1]
	    df_cont.columns = df[0].columns
	    df_list.append(df_cont)

	#%%

	# 合并多个表格
	df_all = pd.concat(df_list)

	#%%

	df_all.head(3)

	#%%

	df_all.shape

	#%% md

	### 4. 将结果数据输出到Excel文件

	#%%

	df_all[["单词", "音标", "解释"]].to_excel("./course_datas/c32_read_html/网易有道单词本列表.xlsx", index=False)

Pandas计算同比环比指标的3种方法
**********************************************************************************

.. code-block:: python
	:linenos:

	#### 同比和环比：环比和同比用于描述统计数据的变化情况
	* 环比：表示本次统计段与相连的上次统计段之间的比较。
	    - 比如2010年中国第一季度GDP为G2010Q1亿元，第二季度GDP为G2010Q2亿元，则第二季度GDP环比增长（G2010Q2-G2010Q1)/G2010Q1；
	* 同比：即同期相比，表示某个特定统计段今年与去年之间的比较。
	    - 比如2009年中国第一季度GDP为G2009Q1亿元，则2010年第一季度的GDP同比增长为（G2010Q1-G2009Q1)/G2009Q1。

	<img src="./other_files/tongbi_huanbi.jpg" style="margin-left:0px; "/>


	演示步骤：
	0. 读取连续3年的天气数据
	1. 方法1：pandas.Series.pct_change
	2. 方法2：pandas.Series.shift
	3. 方法3：pandas.Series.diff

	pct_change、shift、diff，都实现了跨越多行的数据计算

	#%% md

	### 0. 读取连续3年的天气数据

	#%%

	import pandas as pd
	%matplotlib inline

	#%%

	fpath = "./datas/beijing_tianqi/beijing_tianqi_2017-2019.csv"
	df = pd.read_csv(fpath, index_col="ymd", parse_dates=True)

	#%%

	df.head(3)

	#%%

	# 替换掉温度的后缀℃
	df["bWendu"] = df["bWendu"].str.replace("℃", "").astype('int32')

	#%%

	df.head(3)

	#%%

	# 新的df，为每个月的平均最高温
	df = df[["bWendu"]].resample("M").mean()

	#%%

	# 将索引按照日期升序排列
	df.sort_index(ascending=True, inplace=True)

	#%%

	df.head()

	#%%

	df.index

	#%%

	df.plot()

	#%% md

	### 方法1：pandas.Series.pct_change

	pct_change方法直接算好了"(新-旧)/旧"的百分比

	官方文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.pct_change.html

	#%%

	df["bWendu_way1_huanbi"] = df["bWendu"].pct_change(periods=1)
	df["bWendu_way1_tongbi"] = df["bWendu"].pct_change(periods=12)

	#%%

	df.head(15)

	#%% md

	### 方法2：pandas.Series.shift

	shift用于移动数据，但是保持索引不变

	官方文档地址：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.shift.html

	#%%

	# 见识一下shift做了什么事情
	# 使用pd.concat合并Series列表变成一个大的df
	pd.concat(
	    [df["bWendu"], 
	     df["bWendu"].shift(periods=1), 
	     df["bWendu"].shift(periods=12)],
	    axis=1
	).head(15)

	#%%

	# 环比
	series_shift1 = df["bWendu"].shift(periods=1)
	df["bWendu_way2_huanbi"] = (df["bWendu"]-series_shift1)/series_shift1

	# 同比
	series_shift2 = df["bWendu"].shift(periods=12)
	df["bWendu_way2_tongbi"] = (df["bWendu"]-series_shift2)/series_shift2

	#%%

	df.head(15)

	#%% md

	### 方法3. pandas.Series.diff

	pandas.Series.diff用于新值减去旧值

	官方文档：https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.diff.html

	#%%

	pd.concat(
	    [df["bWendu"], 
	     df["bWendu"].diff(periods=1), 
	     df["bWendu"].diff(periods=12)],
	    axis=1
	).head(15)

	#%%

	# 环比
	series_diff1 = df["bWendu"].diff(periods=1)
	df["bWendu_way3_huanbi"] = series_diff1/(df["bWendu"]-series_diff1)

	# 同比
	series_diff2 = df["bWendu"].diff(periods=12)
	df["bWendu_way3_tongbi"] = series_diff2/(df["bWendu"]-series_diff2)

	#%%

	df.head(15)

Pandas和数据库查询语言SQL的对比
**********************************************************************************

.. code-block:: python
	:linenos:

	* Pandas：Python最流行的数据处理与数据分析的类库
	* SQL：结构化查询语言，用于对MySQL、Oracle等关系型数据库的增删改查

	两者都是对“表格型”数据的操作和查询，所以很多语法都能对应起来

	对比列表：
	1. SELECT数据查询
	2. WHERE按条件查询
	3. in和not in的条件查询
	4. groupby分组统计
	5. JOIN数据关联
	6. UNION数据合并
	7. Order Limit先排序后分页
	8. 取每个分组group的top n
	9. UPDATE数据更新
	10. DELETE删除数据

	#%% md

	### 0. 读取泰坦尼克数据集

	#%%

	import pandas as pd
	import numpy as np

	#%%

	df = pd.read_csv("./datas/titanic/titanic_train.csv")
	df.head()

	#%% md

	### 1. SELECT数据查询

	#%%

	# SQL：
	sql = """
	    SELECT PassengerId, Sex, Age, Survived
	    FROM titanic
	    LIMIT 5;
	"""

	#%%

	# Pandas
	df[["PassengerId", "Sex", "Age", "Survived"]].head(5)

	#%% md

	df.head(5)类似select * from table limit 5，查询所有的字段

	#%% md

	### 2. WHERE按条件查询

	#%%

	# SQL：
	sql = """
	    SELECT *
	    FROM titanic
	    where Sex='male' and Age>=20.0 and Age<=40.0
	    LIMIT 5;
	"""

	#%%

	# 使用括号的方式，级联多个条件|
	condition = (df["Sex"]=="male") & (df["Age"]>=20.0) & (df["Age"]<=40.0)
	condition.value_counts()

	#%%

	df[condition].head(5)

	#%% md

	### 3. in和not in的条件查询

	#%%

	df["Pclass"].unique()

	#%%

	# SQL：
	sql = """
	    SELECT *
	    FROM titanic
	    where Pclass in (1,2)
	    LIMIT 5;
	"""

	#%%

	# in 
	df[df["Pclass"].isin((1,2))].head()

	#%%

	# not in 
	df[~df["Pclass"].isin((1,2))].head()

	#%% md

	### 4. groupby分组统计

	#%% md

	#### 4.1 单个列的聚合

	#%%

	# SQL：
	sql = """
	    SELECT 
	        -- 分性别的存活人数
	        sum(Survived),
	        -- 分性别的平均年龄
	        mean(Age)
	        -- 分性别的平均票价
	        mean(Fare)
	    FROM titanic
	    group by Sex
	"""

	#%%

	df.groupby("Sex").agg({"Survived":np.sum, "Age":np.mean, "Fare":np.mean})

	#%% md

	#### 4.2 多个列的聚合

	#%%

	# SQL：
	sql = """
	    SELECT 
	        -- 不同存活和性别分组的，平均年龄
	        mean(Age)
	        -- 不同存活和性别分组的，平均票价
	        mean(Fare)
	    FROM titanic
	    group by Survived, Sex
	"""

	#%%

	df.groupby(["Survived", "Sex"]).agg({"Age":np.mean, "Fare":np.mean})

	#%% md

	### 5. JOIN数据关联

	#%%

	# 电影评分数据集，评分表
	df_rating = pd.read_csv("./datas/ml-latest-small/ratings.csv")
	df_rating.head(5)

	#%%

	# 电影评分数据集，电影信息表
	df_movies = pd.read_csv("./datas/ml-latest-small/movies.csv")
	df_movies.head(5)

	#%%

	# SQL：
	sql = """
	    SELECT *
	    FROM 
	        rating join movies 
	        on(rating.movieId=movies.movieId)
	    limit 5
	"""

	#%%

	df_merged = pd.merge(left=df_rating, right=df_movies, on="movieId")
	df_merged.head(5)

	#%% md

	### 6. UNION数据合并

	#%%

	df1 = pd.DataFrame({'city': ['Chicago', 'San Francisco', 'New York City'],
	                    'rank': range(1, 4)}) 
	df1

	#%%

	df2 = pd.DataFrame({'city': ['Chicago', 'Boston', 'Los Angeles'],
	                    'rank': [1, 4, 5]})
	df2

	#%%

	# SQL：
	sql = """
	    SELECT city, rank
	    FROM df1
	    
	    UNION ALL
	    
	    SELECT city, rank
	    FROM df2;
	"""

	#%%

	# pandas
	pd.concat([df1, df2])

	#%% md

	### 7. Order Limit先排序后分页

	#%%

	# SQL：
	sql = """
	    SELECT *
	    from titanic
	    order by Fare
	    limit 5
	"""

	#%%

	df.sort_values("Fare", ascending=False).head(5)

	#%% md

	### 8. 取每个分组group的top n

	#%%

	# MYSQL不支持
	# Oracle有ROW_NUMBER语法

	#%%

	# 按（Survived，Sex）分组，取Age的TOP 2
	df.groupby(["Survived", "Sex"]).apply(
	    lambda df:df.sort_values("Age", ascending=False).head(2))

	#%% md

	### 9. UPDATE数据更新

	#%%

	df.info()

	#%%

	# SQL：
	sql = """
	    UPDATE titanic
	    set Age=0
	    where Age is null
	"""

	#%%

	condition = df["Age"].isna()
	condition.value_counts()

	#%%

	df[condition] = 0

	#%%

	df["Age"].isna().value_counts()

	#%% md

	### 10. DELETE删除数据

	#%%

	# SQL：
	sql = """
	    DELETE FROM titanic
	    where Age=0
	"""

	#%%

	df_new = df[df["Age"]!=0]

	#%%

	df_new[df_new["Age"]==0]




































































































