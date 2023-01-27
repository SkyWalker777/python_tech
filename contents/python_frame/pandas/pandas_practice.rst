Pandas 实战篇
##################################################################################

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
