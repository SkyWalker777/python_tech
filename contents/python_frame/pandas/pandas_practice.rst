Pandas 实战篇
##################################################################################

Java 版本 Pandas
**********************************************************************************

.. tip::

	java 版本的 Pandas 框架: joinery/tablesaw, kotlin dataframe 

Pandas 将 SQL 数据查询转化为 DataFrame
**********************************************************************************

.. tabs::

	.. tab:: 方式一

		.. code-block:: python

			import warnings
			import pymysql
			import pandas as pd
			warnings.filterwarnings('ignore')


			def connect_database(database, password):
			     db = pymysql.connect(host ="XXX",user ="root", password = password, database = database)
			     return db


			def read_data(db, table):
			    cursor = db.cursor() # 使用cursor()方法获取用于执行SQL语句的游标

			    sql = "SELECT * FROM " + table # SQL语句
			    cursor.execute(sql)  # 执行SQL语句

			    data = cursor.fetchall()

			    #下面为将获取的数据转化为dataframe格式
			    columnDes = cursor.description #获取连接对象的描述信息
			    columnNames = [columnDes[i][0] for i in range(len(columnDes))] #获取列名
			    df = pd.DataFrame([list(i) for i in data],columns=columnNames) #得到的data为二维元组，逐行取出，转化为列表，再转化为df

			    """
			    使用完成之后需关闭游标和数据库连接，减少资源占用,cursor.close(),db.close()
			    db.commit()若对数据库进行了修改，需进行提交之后再关闭
			    """
			    cursor.close()
			    db.close()

			    print("cursor.description中的内容：",columnDes)
			    return df

	.. tab:: 方式二

		.. code-block:: python

			import pymysql
			import warnings
			import pandas as pd
			warnings.filterwarnings('ignore')

			import pymysql
			conn = pymysql.connect(
			        host='127.0.0.1',
			        user='root',
			        password='12345678',
			        database='test',
			        charset='utf8'
			    )

			mysql_page = pd.read_sql("select * from crazyant_pvuv", con=conn)


DataFrame 数据遍历
**********************************************************************************

对 Pandas 对象进行基本迭代的行为取决于类型。在遍历一个 Series 时，它被视为类似数组，并且基本迭代产生这些值。其他数据结构（如 DataFrame 和 Panel）遵循类似于字典的惯例，即迭代对象的键。总之，基本的迭代产生

	| Series - 值
	| DataFrame - 列标签
	| Panel - 项目标签

迭代 DataFrame
=================================================================================

迭代 DataFrame 会给出列名称。让我们考虑下面的例子来理解相同的情况。

.. code-block:: python

	import pandas as pd
	import numpy as np

	N=20

	df = pd.DataFrame({
	    'A': pd.date_range(start='2021-01-01',periods=N,freq='D'),
	    'x': np.linspace(0,stop=N-1,num=N),
	    'y': np.random.rand(N),
	    'C': np.random.choice(['Low','Medium','High'],N).tolist(),
	    'D': np.random.normal(100, 10, size=(N)).tolist()
	    })

	for col in df:
	   print(col)

其 输出 如下

	| A
	| C
	| D
	| x
	| y

要迭代DataFrame的行，我们可以使用以下函数 -

	| iteritems（） - 遍历（键，值）对
	| iterrows（） - 遍历行（索引，序列）对
	| itertuples（） - 遍历 行为 namedtuples

iteritems()
=================================================================================

将每列作为关键字值进行迭代，并将标签作为键和列值作为 Series 对象进行迭代。

.. code-block:: python

	import pandas as pd
	import numpy as np

	df = pd.DataFrame(np.random.randn(4,3),columns=['col1','col2','col3'])
	for key,value in df.iteritems():
	   print(key,value)

其 输出 如下 :

	| col1 0    0.265778
	| 1   -0.814620
	| 2   -2.384911
	| 3    0.525155
	| Name: col1, dtype: float64
	| col2 0    2.580894
	| 1   -0.408090
	| 2    0.641011
	| 3    0.591557
	| Name: col2, dtype: float64
	| col3 0   -0.830860
	| 1    0.413565
	| 2   -2.251128
	| 3   -0.392120
	| Name: col3, dtype: float64

请注意，每个列在 Series 中作为键值对单独迭代。

iterrows()
=================================================================================

iterrows() 返回产生每个索引值的迭代器以及包含每行数据的序列。

.. code-block:: python

	import pandas as pd
	import numpy as np

	df = pd.DataFrame(np.random.randn(4,3),columns = ['col1','col2','col3'])
	for row_index,row in df.iterrows():
	   print(row_index,row)

其 输出 如下

	| 0 col1   -0.536180
	| col2   -0.422245
	| col3   -0.049302
	| Name: 0, dtype: float64
	| 1 col1   -0.577882
	| col2    0.546570
	| col3    1.210293
	| Name: 1, dtype: float64
	| 2 col1    0.593660
	| col2    0.621967
	| col3    0.456040
	| Name: 2, dtype: float64
	| 3 col1    0.874323
	| col2    0.303070
	| col3   -0.107727
	| Name: 3, dtype: float64

注 - 由于 iterrows（） 遍历行，因此它不会保留行中的数据类型。0,1,2是行索引，col1，col2，col3是列索引。

itertuples()
=================================================================================

itertuples（）方法将返回一个迭代器，为 DataFrame 中的每一行生成一个命名的元组。元组的第一个元素将是行的相应索引值，而其余值是行值。

.. code-block:: python

	import pandas as pd
	import numpy as np

	df = pd.DataFrame(np.random.randn(4,3),columns = ['col1','col2','col3'])
	for row in df.itertuples():
	    print(row)

其 输出 如下

	| Pandas(Index=0, col1=-0.4029137277161786, col2=1.3034737750584355, col3=0.8197109653411052)
	| Pandas(Index=1, col1=-0.43013422882386704, col2=-0.2536252662252256, col3=0.9102527012477817)
	| Pandas(Index=2, col1=0.25877683462048057, col2=-0.7725072659033637, col3=-0.013946376730006241)
	| Pandas(Index=3, col1=0.3611368595844501, col2=-0.2777909818571997, col3=0.9396027945103758)

注 : 不要在迭代时尝试修改任何对象。 迭代是为了读取而迭代器返回原始对象（视图）的副本，因此这些更改不会反映到原始对象上。

.. code-block:: python

	import pandas as pd
	import numpy as np

	df = pd.DataFrame(np.random.randn(4,3),columns = ['col1','col2','col3'])

	for index, row in df.iterrows():
	   row['col1'] = 10
	print(df)

其 输出 如下

.. code-block:: python

	       col1      col2      col3
	0  0.579118  0.444899 -0.693009
	1  0.479294  0.080658 -0.126600
	2  0.095121 -1.870492  0.596165
	3  1.885483 -0.122502 -1.531169

pandas Timestamp 类型转化
**********************************************************************************

pandas._libs.tslibs.timestamps.Timestamp 时间戳类型转化(转化是毫秒级/秒级时间戳)

.. code-block:: python

	print(type(row['top_time']))
	<class 'pandas._libs.tslibs.timestamps.Timestamp'>

	print(row['top_time'])
	2023-01-26 21:55:20

	print(row['top_time'].value // 10**9 - 8*60*60) # 8 小时时差
	1674770120
































































































