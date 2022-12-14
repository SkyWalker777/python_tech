Pandas 高级篇 3
##################################################################################

Pandas怎样找出最影响结果的那些特征？
**********************************************************************************

.. code-block:: python

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

Pandas实现groupby聚合后不同列数据统计
**********************************************************************************

.. code-block:: python

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
































































































































