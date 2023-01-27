日期函数
##################################################################################

所有日期、时间的 api 都在 datetime 模块内。

简单示例如下：

.. code-block:: python

	##  %Y年，%m月，%d日，%H时，%M分，%S秒，%f毫秒
	import datetime
	print(datetime.datetime.now())  # 2022-01-28 11:09:01.529864
	print(datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f'))  # 2022-01-28 11:09:01.529864
	print(datetime.datetime.now().strftime('%Y%m%d%H%M%S%f'))  # 20220128110901529864

日期输出格式化 datetime => string
**********************************************************************************

.. code-block:: python

	import datetime
	now = datetime.datetime.now()
	print( now.strftime('%Y-%m-%d %H:%M:%S')  )
	# 输出 '2022-04-07 19:11:21'

strftime 是 datetime 类的实例方法。

日期输出格式化 string => datetime
**********************************************************************************

.. code-block:: python

	import datetime

	t_str = '2022-04-07 19:11:21'
	d = datetime.datetime.strptime(t_str, '%Y-%m-%d %H:%M:%S')

strptime 是 datetime 类的静态方法。

日期比较操作
**********************************************************************************

在 datetime 模块中有 timedelta 类，这个类的对象用于表示一个时间间隔，比如两个日期或者时间的差别。

构造方法：

.. code-block:: python

	import datetime

	datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)  

所有的参数都有默认值 0，这些参数可以是 int 或 float，正的或负的。可以通过 timedelta.days、tiemdelta.seconds 等获取相应的时间值。

timedelta 类的实例，支持加、减、乘、除等操作，所得的结果也是 timedelta 类的实例。比如：

.. code-block:: python

	import datetime

	year = datetime.timedelta(days=365)
	ten_years = year *10
	nine_years = ten_years - year  

同时，date、time 和 datetime 类也支持与 timedelta 的加、减运算。

.. code-block:: python

	datetime1 = datetime2 + timedelta
	timedelta = datetime1 - datetime2

这样，可以很方便的实现一些功能。

两个日期相差多少天
**********************************************************************************

.. code-block:: python

	import datetime

	d1 = datetime.datetime.strptime('2022-03-05 17:41:20', '%Y-%m-%d %H:%M:%S')
	d2 = datetime.datetime.strptime('2022-03-02 17:41:20', '%Y-%m-%d %H:%M:%S')

	delta = d1 - d2
	print( delta.days  )
	# 输出：3

今天的 n 天后的日期
**********************************************************************************

.. code-block:: python

	import datetime

	now = datetime.datetime.now()
	delta = datetime.timedelta(days=3)

	n_days = now + delta
	print(n_days.strftime('%Y-%m-%d %H:%M:%S'))

	输出：2022-04-10 19:16:34

	#coding=utf-8

	import datetime
	now=datetime.datetime.now()
	print(now)


















































