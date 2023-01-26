MySQL
##################################################################################

PyMySQL 的使用
**********************************************************************************

* 思考

如何实现将100000条数据插入到MySQL数据库?

答案:

如果使用之前学习的MySQL客户端来完成这个操作，那么这个工作量无疑是巨大的，我们可以通过使用程序代码的方式去连接MySQL数据库，然后对MySQL数据库进行增删改查的方式，实现10000条数据的插入，像这样使用代码的方式操作数据库就称为数据库编程。

* Python程序操作MySQL数据库

安装pymysql第三方包: 	``sudo pip3 install pymysql``

说明:

::

	安装命令使用 sudo pip3 install 第三方包名
	卸载命令使用 sudo pip3 uninstall 第三方包
	大家现在使用的虚拟机已经安装了这个第三方包，可以使用： pip3 show pymysql 命令查看第三方包的信息
	pip3 list 查看使用pip命令安装的第三方包列表

* pymysql的使用:

| 1. 导入 pymysql 包 ``import pymysql``
| 2. 创建连接对象

调用 pymysql 模块中的 connect() 函数来创建连接对象,代码如下:

::

	 conn=connect(参数列表)

	 * 参数host：连接的mysql主机，如果本机是'localhost'
	 * 参数port：连接的mysql主机的端口，默认是3306
	 * 参数user：连接的用户名
	 * 参数password：连接的密码
	 * 参数database：数据库的名称
	 * 参数charset：通信采用的编码方式，推荐使用utf8

连接对象操作说明:

::

	关闭连接 conn.close()
	提交数据 conn.commit()
	撤销数据 conn.rollback()

3. 获取游标对象

获取游标对象的目标就是要执行sql语句，完成对数据库的增、删、改、查操作。代码如下:

::

	# 调用连接对象的cursor()方法获取游标对象
	cur =conn.cursor()

游标操作说明:

::

	使用游标执行SQL语句: execute(operation [parameters ]) 执行SQL语句，返回受影响的行数，主要用于执行insert、update、delete、select等语句
	获取查询结果集中的一条数据:cur.fetchone()返回一个元组, 如 (1,'张三')
	获取查询结果集中的所有数据: cur.fetchall()返回一个元组,如((1,'张三'),(2,'李四'))
	关闭游标: cur.close(),表示和数据库操作完成

4. pymysql完成数据的查询操作

.. code-block:: python

	import pymysql

	# 创建连接对象
	conn = pymysql.connect(host='localhost', port=13306, user='root', password='root',database='test', charset='utf8')

	# 获取游标对象
	cursor = conn.cursor()

	# 查询 SQL 语句
	sql = "select * from pay_ip;"
	# 执行 SQL 语句 返回值就是 SQL 语句在执行过程中影响的行数
	row_count = cursor.execute(sql)
	print("SQL 语句执行影响的行数%d" % row_count)

	# 取出结果集中一行数据,　例如:(1, '张三')
	# print(cursor.fetchone())

	# 取出结果集中的所有数据, 例如:((1, '张三'), (2, '李四'), (3, '王五'))
	for line in cursor.fetchall():
	    print(line)

	# 关闭游标
	cursor.close()

	# 关闭连接
	conn.close()

5. pymysql完成对数据的增删改

.. code-block:: python

	import pymysql

	# 创建连接对象
	conn = pymysql.connect(host='localhost', port=3306, user='root', password='mysql',database='python', charset='utf8')

	# 获取游标对象
	cursor = conn.cursor()

	try:
	    # 添加 SQL 语句
	    # sql = "insert into students(name) values('刘璐'), ('王美丽');"
	    # 删除 SQ L语句
	    # sql = "delete from students where id = 5;"
	    # 修改 SQL 语句
	    sql = "update students set name = '王铁蛋' where id = 6;"
	    # 执行 SQL 语句
	    row_count = cursor.execute(sql)
	    print("SQL 语句执行影响的行数%d" % row_count)
	    # 提交数据到数据库
	    conn.commit()
	except Exception as e:
	    # 回滚数据， 即撤销刚刚的SQL语句操作
	    conn.rollback()

	# 关闭游标
	cursor.close()

	# 关闭连接
	conn.close()

说明:

	| conn.commit() 表示将修改操作提交到数据库
	| conn.rollback() 表示回滚数据

防止 SQL 注入
**********************************************************************************

什么是SQL注入? 用户提交带有恶意的数据与SQL语句进行字符串方式的拼接，从而影响了SQL语句的语义，最终产生数据泄露的现象。

如何防止SQL注入? SQL语句参数化

::

	SQL语言中的参数使用%s来占位，此处不是python中的字符串格式化操作
	将SQL语句中%s占位所需要的参数存在一个列表中，把参数列表传递给execute方法中第二个参数

防止SQL注入的示例代码:

.. code-block:: python

	from pymysql import connect

	def main():

	    find_name = input("请输入物品名称：")

	    # 创建Connection连接
	    conn = connect(host='localhost',port=3306,user='root',password='mysql',database='jing_dong',charset='utf8')
	    # 获得Cursor对象
	    cs1 = conn.cursor()

	    # 非安全的方式
	    # 输入 ' or 1 = 1 or '   (单引号也要输入)
	    # sql = "select * from goods where name='%s'" % find_name
	    # print("""sql===>%s<====""" % sql)
	    # # 执行select语句，并返回受影响的行数：查询所有数据
	    # count = cs1.execute(sql)

	    # 安全的方式
	    # 构造参数列表
	    params = [find_name]
	    # 执行select语句，并返回受影响的行数：查询所有数据
	    count = cs1.execute("select * from goods where name=%s", params)
	    # 注意：
	    # 如果要是有多个参数，需要进行参数化
	    # 那么params = [数值1, 数值2....]，此时sql语句中有多个%s即可
	    # %s 不需要带引号

	    # 打印受影响的行数
	    print(count)
	    # 获取查询的结果
	    # result = cs1.fetchone()
	    result = cs1.fetchall()
	    # 打印查询的结果
	    print(result)
	    # 关闭Cursor对象
	    cs1.close()
	    # 关闭Connection对象
	    conn.close()

	if __name__ == '__main__':
	    main()

.. tip::

	说明: execute方法中的 %s 占位不需要带引号

* 小结

::

	导包 import pymysql
	创建连接对象 pymysql.connect(参数列表)
	获取游标对象 cursor =conn.cursor()
	执行SQL语句 row_count = cursor.execute(sql)
	获取查询结果集 result = cursor.fetchall()
	将修改操作提交到数据库 conn.commit()
	回滚数据 conn.rollback()
	关闭游标 cursor.close()
	关闭连接 conn.close()

事务
**********************************************************************************

* 事务的介绍

事务就是用户定义的一系列执行SQL语句的操作, 这些操作要么完全地执行，要么完全地都不执行， 它是一个不可分割的工作执行单元。

事务的使用场景: 在日常生活中，有时我们需要进行银行转账，这个银行转账操作背后就是需要执行多个SQL语句，假如这些SQL执行到一半突然停电了，那么就会导致这个功能只完成了一半，这种情况是不允许出现，要想解决这个问题就需要通过事务来完成。

* 事务的四大特性

::

	原子性(Atomicity)
	一致性(Consistency)
	隔离性(Isolation)
	持久性(Durability)

| 原子性: 一个事务必须被视为一个不可分割的最小工作单元，整个事务中的所有操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作，这就是事务的原子性
| 一致性: 数据库总是从一个一致性的状态转换到另一个一致性的状态。（在前面的例子中，一致性确保了，即使在转账过程中系统崩溃，支票账户中也不会损失200美元，因为事务最终没有提交，所以事务中所做的修改也不会保存到数据库中。）
| 隔离性: 通常来说，一个事务所做的修改操作在提交事务之前，对于其他事务来说是不可见的。（在前面的例子中，当执行完第三条语句、第四条语句还未开始时，此时有另外的一个账户汇总程序开始运行，则其看到支票帐户的余额并没有被减去200美元。）
| 持久性: 一旦事务提交，则其所做的修改会永久保存到数据库。

说明: 事务能够保证数据的完整性和一致性，让用户的操作更加安全。

* 事务的使用

在使用事务之前，先要确保表的存储引擎是 InnoDB 类型, 只有这个类型才可以使用事务，MySQL数据库中表的存储引擎默认是 InnoDB 类型。

表的存储引擎说明: 表的存储引擎就是提供存储数据一种机制，不同表的存储引擎提供不同的存储机制。

查看MySQL数据库支持的表的存储引擎:

-- 查看MySQL数据库支持的表的存储引擎
show engines;

表的存储引擎

说明:

::

	常用的表的存储引擎是 InnoDB 和 MyISAM
	InnoDB 是支持事务的
	MyISAM 不支持事务，优势是访问速度快，对事务没有要求或者以select、insert为主的都可以使用该存储引擎来创建表

查看goods表的创表语句:

.. code-block:: sql

	-- 选择数据库
	use jing_dong;
	-- 查看goods表
	show create table goods;

	mysql root@(none):jing_dong> show create table goods;
	+-------+--------------------------------------------------------+
	| Table | Create Table                                           |
	+-------+--------------------------------------------------------+
	| goods | CREATE TABLE `goods` (                                 |
	|       |   `id` int(10) unsigned NOT NULL AUTO_INCREMENT,       |
	|       |   `name` varchar(150) NOT NULL,                        |
	|       |   `cate_id` int(10) unsigned NOT NULL,                 |
	|       |   `brand_id` int(10) unsigned NOT NULL,                |
	|       |   `price` decimal(10,3) NOT NULL DEFAULT '0.000',      |
	|       |   `is_show` bit(1) NOT NULL DEFAULT b'1',              |
	|       |   `is_saleoff` bit(1) NOT NULL DEFAULT b'0',           |
	|       |   PRIMARY KEY (`id`)                                   |
	|       | ) ENGINE=InnoDB AUTO_INCREMENT=25 DEFAULT CHARSET=utf8 |
	+-------+--------------------------------------------------------+

说明:

::

	通过创表语句可以得知，goods表的存储引擎是InnoDB。
	修改表的存储引擎使用: alter table 表名 engine = 引擎类型;
	    比如: alter table students engine = 'MyISAM';

开启事务:

::

	begin;
	或者
	start transaction;

说明:

::

	开启事务后执行修改命令，变更数据会保存到MySQL服务端的缓存文件中，而不维护到物理表中
	MySQL数据库默认采用自动提交(autocommit)模式，如果没有显示的开启一个事务,那么每条sql语句都会被当作一个事务执行提交的操作

	当设置autocommit=0就是取消了自动提交事务模式，直到显示的执行commit和rollback表示该事务结束。
	    set autocommit = 0 表示取消自动提交事务模式，需要手动执行commit完成事务的提交

	set autocommit = 0;
	insert into students(name) values('刘三峰');
	-- 需要执行手动提交，数据才会真正添加到表中, 验证的话需要重新打开一个连接窗口查看表的数据信息，因为是临时关闭自动提交模式
	commit

	-- 重新打开一个终端窗口，连接MySQL数据库服务端
	mysql -uroot -p

	-- 然后查询数据,如果上个窗口执行了commit，这个窗口才能看到数据
	select * from students;

	提交事务:

	将本地缓存文件中的数据提交到物理表中，完成数据的更新。

	commit;

	回滚事务:

	放弃本地缓存文件中的缓存数据, 表示回到开始事务前的状态

	rollback;

	事务演练的SQL语句:

	begin;
	insert into students(name) values('李白');
	-- 查询数据，此时有新增的数据, 注意: 如果这里后续没有执行提交事务操作，那么数据是没有真正的更新到物理表中
	select * from students;
	-- 只有这里提交事务，才把数据真正插入到物理表中
	commit;

	-- 新打开一个终端，重新连接MySQL数据库，查询students表,这时没有显示新增的数据，说明之前的事务没有提交，这就是事务的隔离性
	-- 一个事务所做的修改操作在提交事务之前，对于其他事务来说是不可见的
	select * from students;

* 小结

::

	事务的特性:
	    原子性: 强调事务中的多个操作时一个整体
	    一致性: 强调数据库中不会保存不一致状态
	    隔离性: 强调数据库中事务之间相互不可见
	    持久性: 强调数据库能永久保存数据，一旦提交就不可撤销
	MySQL数据库默认采用自动提交(autocommit)模式, 也就是说修改数据(insert、update、delete)的操作会自动的触发事务,完成事务的提交或者回滚
	开启事务使用 begin 或者 start transaction;
	回滚事务使用 rollback;
	pymysql 里面的 conn.commit() 操作就是提交事务
	pymysql 里面的 conn.rollback() 操作就是回滚事务

索引
**********************************************************************************

* 索引的介绍

索引在MySQL中也叫做“键”，它是一个特殊的文件，它保存着数据表里所有记录的位置信息，更通俗的来说，数据库索引好比是一本书前面的目录，能加快数据库的查询速度。

应用场景: 当数据库中数据量很大时，查找数据会变得很慢，我们就可以通过索引来提高数据库的查询效率。

* 索引的使用

查看表中已有索引: ``show index from 表名;``

说明: 主键列会自动创建索引

索引的创建:

.. code-block:: sql

	-- 创建索引的语法格式
	-- alter table 表名 add index 索引名[可选](列名, ..)
	-- 给name字段添加索引
	alter table classes add index my_name (name);

说明: 索引名不指定，默认使用字段名

索引的删除:

.. code-block:: sql

	-- 删除索引的语法格式
	-- alter table 表名 drop index 索引名
	-- 如果不知道索引名，可以查看创表sql语句
	show create table classes;
	alter table classes drop index my_name;

* 案例-验证索引查询性能

创建测试表testindex: ``create table test_index(title varchar(10));``

向表中插入十万条数据:

.. code-block:: sql

	from pymysql import connect

	def main():
	    # 创建Connection连接
	    conn = connect(host='localhost',port=3306,database='python',user='root',password='mysql',charset='utf8')
	    # 获得Cursor对象
	    cursor = conn.cursor()
	    # 插入10万次数据
	    for i in range(100000):
	        cursor.execute("insert into test_index values('ha-%d')" % i)
	    # 提交数据
	    conn.commit()

	if __name__ == "__main__":
	    main()

验证索引性能操作：

.. code-block:: sql

	-- 开启运行时间监测：
	set profiling=1;
	-- 查找第1万条数据ha-99999
	select * from test_index where title='ha-99999';
	-- 查看执行的时间：
	show profiles;
	-- 给title字段创建索引：
	alter table test_index add index (title);
	-- 再次执行查询语句
	select * from test_index where title='ha-99999';
	-- 再次查看执行的时间
	show profiles;

* 联合索引

联合索引又叫复合索引，即一个索引覆盖表中两个或者多个字段，一般用在多个字段一起查询的时候。

.. code-block:: sql

	-- 创建teacher表
	create table teacher
	(
	    id int not null primary key auto_increment,
	    name varchar(10),
	    age int
	);

	-- 创建联合索引
	alter table teacher add index (name,age);

联合索引的好处: 减少磁盘空间开销，因为每创建一个索引，其实就是创建了一个索引文件，那么会增加磁盘空间的开销。

* 联合索引的最左原则

在使用联合索引的时候，我们要遵守一个最左原则,即index(name,age)支持 name 、name 和 age 组合查询,而不支持单独 age 查询，因为没有用到创建的联合索引。

最左原则示例:

.. code-block:: sql

	-- 下面的查询使用到了联合索引
	select * from stu where name='张三' -- 这里使用了联合索引的name部分
	select * from stu where name='李四' and age=10 -- 这里完整的使用联合索引，包括 name 和 age 部分
	-- 下面的查询没有使用到联合索引
	select * from stu where age=10 -- 因为联合索引里面没有这个组合，只有 name | name age 这两种组合

说明: 在使用联合索引的查询数据时候一定要保证联合索引的最左侧字段出现在查询条件里面，否则联合索引失效

* MySQL中索引的优点和缺点和使用原则

优点：加快数据的查询速度

缺点：创建索引会耗费时间和占用磁盘空间，并且随着数据量的增加所耗费的时间也会增加

使用原则：

::

	通过优缺点对比，不是索引越多越好，而是需要自己合理的使用。
	对经常更新的表就避免对其进行过多索引的创建，对经常用于查询的字段应该创建索引，
	数据量小的表最好不要使用索引，因为由于数据较少，可能查询全部数据花费的时间比遍历索引的时间还要短，索引就可能不会产生优化效果。
	在一字段上相同值比较多不要建立索引，比如在学生表的"性别"字段上只有男，女两个不同值。相反的，在一个字段上不同值较多可是建立索引。

* 小结

::

	索引是加快数据库的查询速度的一种手段
	创建索引使用: alter table 表名 add index 索引名[可选] (字段名, xxx);
	删除索引使用: alter table 表名 drop index 索引名;

闭包和装饰器
##################################################################################

闭包
**********************************************************************************

* 闭包的介绍

我们前面已经学过了函数，我们知道当函数调用完，函数内定义的变量都销毁了，但是我们有时候需要保存函数内的这个变量，每次在这个变量的基础上完成一些列的操作，比如: 每次在这个变量的基础上和其它数字进行求和计算，那怎么办呢?

我们就可以通过咱们今天学习的闭包来解决这个需求。

闭包的定义: 在函数嵌套的前提下，内部函数使用了外部函数的变量，并且外部函数返回了内部函数，我们把这个使用外部函数变量的内部函数称为闭包。

* 闭包的构成条件

通过闭包的定义，我们可以得知闭包的形成条件:

::

	在函数嵌套(函数里面再定义函数)的前提下
	内部函数使用了外部函数的变量(还包括外部函数的参数)
	外部函数返回了内部函数

* 简单闭包的示例代码

.. code-block:: python

	# 定义一个外部函数
	def func_out(num1):
	    # 定义一个内部函数
	    def func_inner(num2):
	        # 内部函数使用了外部函数的变量(num1)
	        result = num1 + num2
	        print("结果是:", result)
	    # 外部函数返回了内部函数，这里返回的内部函数就是闭包
	    return func_inner

	# 创建闭包实例
	f = func_out(1)
	# 执行闭包
	f(2)
	f(3)

	运行结果:

	结果是: 3
	结果是: 4

闭包执行结果的说明: 通过上面的输出结果可以看出闭包保存了外部函数内的变量num1，每次执行闭包都是在num1 = 1 基础上进行计算。

* 闭包的作用

闭包可以保存外部函数内的变量，不会随着外部函数调用完而销毁。

.. tip::

	由于闭包引用了外部函数的变量，则外部函数的变量没有及时释放，消耗内存。

* 小结

| 当返回的内部函数使用了外部函数的变量就形成了闭包
| 闭包可以对外部函数的变量进行保存
| 实现闭包的标准格式:

.. code-block:: python

	 # 外部函数
	 def test1(a):
	     b = 10
	     # 内部函数
	     def test2():
	         # 内部函数使用了外部函数的变量或者参数
	         print(a, b)
	     # 返回内部函数, 这里返回的内部函数就是闭包实例
	     return test2

闭包的使用
**********************************************************************************

* 案例

需求: 根据配置信息使用闭包实现不同人的对话信息，例如对话:

张三: 到北京了吗? 李四: 已经到了，放心吧。

* 实现步骤说明

::

	定义外部函数接收不同的配置信息参数，参数是人名
	定义内部函数接收对话信息参数
	在内部函数里面把配置信息和对话信息进行拼接输出

* 功能代码的实现

.. code-block:: python

	# 外部函数
	def config_name(name):
	    # 内部函数
	    def say_info(info):
	        print(name + ": " + info)

	    return say_info

	tom = config_name("Tom")

	tom("你好!")
	tom("你好, 在吗?")

	jerry = config_name("jerry")
	jerry("不在, 不和玩!")

	运行结果:

	Tom: 你好!
	Tom: 你好, 在吗?
	jerry: 不在, 不和玩!

闭包案例说明: 闭包还可以提高代码的可重用性，不需要再手动定义额外的功能函数。

.. tip::

	闭包不仅可以保存外部函数的变量还可以提高代码的可重用行。

修改闭包内使用的外部变量
**********************************************************************************

* 修改闭包内使用的外部变量

修改闭包内使用的外部变量的错误示例:

.. code-block:: python

	# 定义一个外部函数
	def func_out(num1):

	    # 定义一个内部函数
	    def func_inner(num2):
	        # 这里本意想要修改外部num1的值，实际上是在内部函数定义了一个局部变量num1
	        num1 = 10
	        # 内部函数使用了外部函数的变量(num1)
	        result = num1 + num2
	        print("结果是:", result)

	    print(num1)
	    func_inner(1)
	    print(num1)

	    # 外部函数返回了内部函数，这里返回的内部函数就是闭包
	    return func_inner

	# 创建闭包实例
	f = func_out(1)
	# 执行闭包
	f(2)

修改闭包内使用的外部变量的错误示例:

.. code-block:: python

	def func_out(num1):

	    # 定义一个内部函数
	    def func_inner(num2):
	        # 这里本意想要修改外部num1的值，实际上是在内部函数定义了一个局部变量num1
	        nonlocal num1  # 告诉解释器，此处使用的是 外部变量a
	        # 修改外部变量num1
	        num1 = 10
	        # 内部函数使用了外部函数的变量(num1)
	        result = num1 + num2
	        print("结果是:", result)

	    print(num1)
	    func_inner(1)
	    print(num1)

	    # 外部函数返回了内部函数，这里返回的内部函数就是闭包
	    return func_inner

	# 创建闭包实例
	f = func_out(1)
	# 执行闭包
	f(2)# 定义一个外部函数

.. tip::

	修改闭包内使用的外部函数变量使用 nonlocal 关键字来完成。

装饰器
**********************************************************************************

* 装饰器的定义

就是给已有函数增加额外功能的函数，它本质上就是一个闭包函数。

装饰器的功能特点:

::

	不修改已有函数的源代码
	不修改已有函数的调用方式
	给已有函数增加额外的功能

* 装饰器的示例代码

.. code-block:: python

	# 添加一个登录验证的功能
	def check(fn):
	    def inner():
	        print("请先登录....")
	        fn()
	    return inner


	def comment():
	    print("发表评论")

	# 使用装饰器来装饰函数
	comment = check(comment)
	comment()

	# 装饰器的基本雏形
	# def decorator(fn): # fn:目标函数.
	#     def inner():
	#         '''执行函数之前'''
	#         fn() # 执行被装饰的函数
	#         '''执行函数之后'''
	#     return inner

代码说明:

::

	闭包函数有且只有一个参数，必须是函数类型，这样定义的函数才是装饰器。
	写代码要遵循开放封闭原则，它规定已经实现的功能代码不允许被修改，但可以被扩展。

	执行结果:

	请先登录....
	发表评论

* 装饰器的语法糖写法

如果有多个函数都需要添加登录验证的功能，每次都需要编写func = check(func)这样代码对已有函数进行装饰，这种做法还是比较麻烦。

Python给提供了一个装饰函数更加简单的写法，那就是语法糖，语法糖的书写格式是: @装饰器名字，通过语法糖的方式也可以完成对已有函数的装饰

.. code-block:: python

	# 添加一个登录验证的功能
	def check(fn):
	    print("装饰器函数执行了")
	    def inner():
	        print("请先登录....")
	        fn()
	    return inner

	# 使用语法糖方式来装饰函数
	@check
	def comment():
	    print("发表评论")

	comment()

说明:

::

	@check 等价于 comment = check(comment)
	装饰器的执行时间是加载模块时立即执行。

执行结果:

::

	请先登录....
	发表评论

.. tip::

	| 装饰器本质上就是一个闭包函数，它可以对已有函数进行额外的功能扩展。
	| 装饰器的语法格式:

	| # 装饰器
	| # def decorator(fn): # fn:被装饰的目标函数.
	| #     def inner():
	| #         '''执行函数之前'''
	| #         fn() # 执行被装饰的目标函数
	| #         '''执行函数之后'''
	| #     return inner

	| 装饰器的语法糖用法: @装饰器名称，同样可以完成对已有函数的装饰操作。

装饰器的使用
**********************************************************************************

* 装饰器的使用场景

::

	函数执行时间的统计
	输出日志信息

* 装饰器实现已有函数执行时间的统计

.. code-block:: python

	import time

	# 装饰器函数
	def get_time(func):
	    def inner():
	        begin = time.time()
	        func()
	        end = time.time()
	        print("函数执行花费%f" % (end-begin))
	    return inner

	@get_time
	def func1():
	    for i in range(100000):
	        print(i)

	func1()

执行结果:

::

	...
	99995
	99996
	99997
	99998
	99999
	函数执行花费0.329066

.. tip::

	通过上面的示例代码可以得知装饰器的作用: 在不改变已有函数源代码及调用方式的前提下，对已有函数进行功能的扩展。

通用装饰器的使用
**********************************************************************************

* 装饰带有参数的函数

.. code-block:: python

	# 添加输出日志的功能
	def logging(fn):
	    def inner(num1, num2):
	        print("--正在努力计算--")
	        fn(num1, num2)

	    return inner

	# 使用装饰器装饰函数
	@logging
	def sum_num(a, b):
	    result = a + b
	    print(result)

	sum_num(1, 2)

运行结果:

::

	--正在努力计算--
	3

* 装饰带有返回值的函数

.. code-block:: python

	# 添加输出日志的功能
	def logging(fn):
	    def inner(num1, num2):
	        print("--正在努力计算--")
	        result = fn(num1, num2)
	        return result
	    return inner

	# 使用装饰器装饰函数
	@logging
	def sum_num(a, b):
	    result = a + b
	    return result

	result = sum_num(1, 2)
	print(result)

运行结果:

::

	--正在努力计算--
	3

* 装饰带有不定长参数的函数

.. code-block:: python

	# 添加输出日志的功能
	def logging(fn):
	    def inner(*args, **kwargs):
	        print("--正在努力计算--")
	        fn(*args, **kwargs)

	    return inner

	# 使用语法糖装饰函数
	@logging
	def sum_num(*args, **kwargs):
	    result = 0
	    for value in args:
	        result += value

	    for value in kwargs.values():
	        result += value

	    print(result)

	sum_num(1, 2, a=10)

运行结果:

::

	--正在努力计算--
	13

* 通用装饰器

.. code-block:: python

	# 添加输出日志的功能
	def logging(fn):
	    def inner(*args, **kwargs):
	        print("--正在努力计算--")
	        result = fn(*args, **kwargs)
	        return result

	    return inner

	# 使用语法糖装饰函数
	@logging
	def sum_num(*args, **kwargs):
	    result = 0
	    for value in args:
	        result += value

	    for value in kwargs.values():
	        result += value

	    return result

	@logging
	def subtraction(a, b):
	    result = a - b
	    print(result)

	result = sum_num(1, 2, a=10)
	print(result)

	subtraction(4, 2)

运行结果:

::

	--正在努力计算--
	13
	--正在努力计算--
	2

* 小结

通用装饰器的语法格式:

::

	# 通用装饰器
	def logging(fn):
	  def inner(*args, **kwargs):
	      print("--正在努力计算--")
	      result = fn(*args, **kwargs)
	      return result

	  return inner

多个装饰器的使用
**********************************************************************************

* 多个装饰器的使用示例代码

.. code-block:: python

	def make_div(func):
	    """对被装饰的函数的返回值 div标签"""
	    def inner(*args, **kwargs):
	        return "<div>" + func() + "</div>"
	    return inner

	def make_p(func):
	    """对被装饰的函数的返回值 p标签"""
	    def inner(*args, **kwargs):
	        return "<p>" + func() + "</p>"
	    return inner

	# 装饰过程: 1 content = make_p(content) 2 content = make_div(content)
	# content = make_div(make_p(content))
	@make_div
	@make_p
	def content():
	    return "人生苦短"

	result = content()

	print(result)

代码说明: 多个装饰器的装饰过程是: 离函数最近的装饰器先装饰，然后外面的装饰器再进行装饰，由内到外的装饰过程

* 小结

多个装饰器可以对函数进行多个功能的装饰，装饰顺序是由内到外的进行装饰

带有参数的装饰器
**********************************************************************************

* 带有参数的装饰器介绍

带有参数的装饰器就是使用装饰器装饰函数的时候可以传入指定参数，语法格式: @装饰器(参数,...)

错误写法:

.. code-block:: python

	def decorator(fn, flag):
	    def inner(num1, num2):
	        if flag == "+":
	            print("--正在努力加法计算--")
	        elif flag == "-":
	            print("--正在努力减法计算--")
	        result = fn(num1, num2)
	        return result
	    return inner

	@decorator('+')
	def add(a, b):
	    result = a + b
	    return result

	result = add(1, 3)
	print(result)

执行结果:

::

	Traceback (most recent call last):
	  File "/home/python/Desktop/test/hho.py", line 12, in <module>
	    @decorator('+')
	TypeError: decorator() missing 1 required positional argument: 'flag'

代码说明: 装饰器只能接收一个参数，并且还是函数类型。

正确写法: 在装饰器外面再包裹上一个函数，让最外面的函数接收参数，返回的是装饰器，因为@符号后面必须是装饰器实例。

.. code-block:: python

	# 添加输出日志的功能
	def logging(flag):
	    def decorator(fn):
	        def inner(num1, num2):
	            if flag == "+":
	                print("--正在努力加法计算--")
	            elif flag == "-":
	                print("--正在努力减法计算--")
	            result = fn(num1, num2)
	            return result
	        return inner

	    # 返回装饰器
	    return decorator

	# 使用装饰器装饰函数
	@logging("+")
	def add(a, b):
	    result = a + b
	    return result

	@logging("-")
	def sub(a, b):
	    result = a - b
	    return result

	result = add(1, 2)
	print(result)

	result = sub(1, 2)
	print(result)

* 小结

使用带有参数的装饰器，其实是在装饰器外面又包裹了一个函数，使用该函数接收参数，返回是装饰器，因为 @ 符号需要配合装饰器实例使用

类装饰器的使用
**********************************************************************************

* 类装饰器的介绍

装饰器还有一种特殊的用法就是类装饰器，就是通过定义一个类来装饰函数。

类装饰器示例代码:

.. code-block:: python

	class Check(object):
	    def __init__(self, fn):
	        # 初始化操作在此完成
	        self.__fn = fn

	    # 实现__call__方法，表示对象是一个可调用对象，可以像调用函数一样进行调用。
	    def __call__(self, *args, **kwargs):
	        # 添加装饰功能
	        print("请先登陆...")
	        self.__fn()

	@Check
	def comment():
	    print("发表评论")

	comment()

代码说明:

::

	@Check 等价于 comment = Check(comment), 所以需要提供一个init方法，并多增加一个fn参数。
	要想类的实例对象能够像函数一样调用，需要在类里面使用call方法，把类的实例变成可调用对象(callable)，也就是说可以像调用函数一样进行调用。
	在call方法里进行对fn函数的装饰，可以添加额外的功能。

执行结果:

::

	请先登陆...
	发表评论

* 小结

::

	想要让类的实例对象能够像函数一样进行调用，需要在类里面使用call方法，把类的实例变成可调用对象(callable)
	类装饰器装饰函数功能在call方法里面进行添加
