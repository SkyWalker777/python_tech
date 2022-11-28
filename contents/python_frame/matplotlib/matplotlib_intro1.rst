matplotlib 入门
##################################################################################

数据分析是用适当的方法对收集来的大量数据进行分析,用图像分析数据可以使数据分析更生动.matplotlib 是常用的绘图模块,

CONDA 安装
**********************************************************************************

	| conda : data science package & environment manager
	| 创建环境:  conda create --name python3 python=3
	| 切换环境:  windows :activate python3     linux/macos : source activate python3
	| 官方地址:  https://www.anaconda.com/download/

matplotlib
**********************************************************************************

什么是 matplotlib
==================================================================================

最流行的 Python 底层绘图库，主要做数据可视化图表, 名字取材于 MATLAB，模仿 MATLAB 构建, 能将数据进行可视化,更直观的呈现, 使数据更加客观、更具说服力.

matplotlib 基本要点
==================================================================================

基本语法
----------------------------------------------------------------------------------

.. code-block:: python

	from matplotlib import pyplot as plt #导入pyplot
	x = range (2,26,2)
	y = [15,13,14.5,17,20,25,26,26,24,22,18,15] #数据在x,y轴的位置,是一个可迭代对象,x轴和y轴的数据一起组成了所有要绘制出的坐标
	plt.plot(x,y,color='r',linestyle='--',linewidth=5，alpha=0.5，#透明度)   #传入x和y,通过plot绘制出折线图scatte绘制散点图,设置颜色,线条风格,线条粗细,透明度
	bin_width = 3   #设置组距为3
	plt.bar(_x,b,width=0.2,color="orange" )   #bar绘制条形图,只能接受含数字的可迭代对象,width表示长条的宽度,默认0.8
	num_bins = int( (max(a)-min(a))/bin_width)   #分为多少组plt.hist(a，num_bins)
	plt.hist(a，num_bins,normed=1)   #normed : bool是否绘制频率分布直方图,默认为频数直方图
	fig = plt.figure(figsize=(20,8) ,dpi=80) #通过实例化一个figure并且传递参数,能够在后台自动使用该fgure实例—>在图像模糊的时候可以传入dpi参数,让图片更加清晰
	plt.savefig( "./sig_size.png")   #保存图片,可以保存为svg这种矢量图格式,放大不会有锯齿
	plt.xticks(x)   #设置x的刻度#
	plt.xticks(x[::2])  #当刻度太密集时候使用列表的步长(间隔取值)来解决,matplotlib会自动帮我们对应
	plt.legend (prop=my_font,loc="best")   #prop指定图例的字体,loc指定图例的位置,默认右上角
	plt.xlabel("时间", fontproperties=my_font)  #设置x轴的label
	plt.ylabel()   #设置y轴的label
	plt.title()   #设置标题的label
	plt.show()   #在执行程序的时候展示图形

设置中文
----------------------------------------------------------------------------------

Windows 和 Linux 可用:

.. code-block:: python

	font = {'family': 'Microsoft Yahei','weight': '10'}
	matplotlib.rc("font",**font)

全部有效:

.. code-block:: python

	from matplotlib import font_manager
	my_font = font_manager. FontProperties(fname=" /System/Library/Fonts/PingFang.ttc")

折线图
==================================================================================

每隔两个小时的气温
----------------------------------------------------------------------------------

.. code-block:: python

	from matplotlib import pyplot as plt

	x = range(2, 26, 2)
	y = [15, 13, 14, 17, 20, 25, 26, 26, 24, 22, 18, 15]

	#调整图片大小
	plt.figure(figsize=(40,8),dpi=80)

	plt.plot(x,y)

	#设置x刻度
	x_labels = [i/2 for i in range(4,49)]
	plt.xticks(x_labels[::2])

	plt.savefig("/Users/lilizhao/Downloads/test.png")
	plt.show()

10 点到 12 点每分钟的气温变化情况
----------------------------------------------------------------------------------

.. code-block:: python

	# coding=utf-8
	from matplotlib import pyplot as plt
	import random
	import matplotlib
	from matplotlib import font_manager

	#windws和linux设置字体的放
	# font = {'family' : 'MicroSoft YaHei',
	#         'weight': 'bold',
	#         'size': 'larger'}
	# matplotlib.rc("font",**font)
	# matplotlib.rc("font",family='MicroSoft YaHei',weight="bold")

	#另外一种设置字体的方式
	my_font = font_manager.FontProperties(fname="/System/Library/Fonts/PingFang.ttc")

	x = range(0, 120)
	y = [random.randint(20, 35) for i in range(120)]

	plt.figure(figsize=(20,8),dpi=80)

	plt.plot(x,y)

	#调整x轴的刻度
	_xtick_labels = ["10点{}分".format(i) for i in range(60)]
	_xtick_labels += ["11点{}分".format(i) for i in range(60)]
	#取步长，数字和字符串一一对应，数据的长度一样
	plt.xticks(list(x)[::3],_xtick_labels[::3],rotation=45,fontproperties=my_font) #rotaion旋转的度数

	#添加描述信息
	plt.xlabel("时间",fontproperties=my_font)
	plt.ylabel("温度 单位(℃)",fontproperties=my_font)
	plt.title("10点到12点每分钟的气温变化情况",fontproperties=my_font)

	plt.show()

男女朋友统计
----------------------------------------------------------------------------------

假设大家在30岁的时候,根据自己的实际情况,统计出来了从11岁到30岁每年交的女(男)朋友的数量如列表a,请绘制出该数据的折线图,以便分析自己每年交女(男)朋友的数量走势 a = [1,0,1,1,2,4,3,2,3,4,4,5,6,5,4,3,3,1,1,1]

要求:

	| y	轴表示个数
	| x	轴表示岁数,比如11岁,12岁等

.. tabs::

	.. tab:: 男女朋友统计 v1

		.. code-block:: python

			# coding=utf-8
			from matplotlib import pyplot as plt
			from matplotlib import font_manager

			my_font = font_manager.FontProperties(fname="/System/Library/Fonts/PingFang.ttc")

			y = [1,0,1,1,2,4,3,2,3,4,4,5,6,5,4,3,3,1,1,1]
			x = range(11,31)

			#设置图形大小
			plt.figure(figsize=(20,8),dpi=80)

			plt.plot(x,y)

			#设置x轴刻度
			_xtick_labels = ["{}岁".format(i) for i in x]
			plt.xticks(x,_xtick_labels,fontproperties=my_font)
			plt.yticks(range(0,9))

			#绘制网格
			plt.grid(alpha=0.1)

			#展示
			plt.show()

	.. tab:: 男女朋友统计 v2

		.. code-block:: python

			# coding=utf-8
			from matplotlib import pyplot as plt
			from matplotlib import font_manager

			my_font = font_manager.FontProperties(fname="/System/Library/Fonts/PingFang.ttc")

			y_1 = [1,0,1,1,2,4,3,2,3,4,4,5,6,5,4,3,3,1,1,1]
			y_2 = [1,0,3,1,2,2,3,3,2,1,2,1,1,1,1,1,1,1,1,1]

			x = range(11,31)

			#设置图形大小
			plt.figure(figsize=(20,8),dpi=80)

			plt.plot(x,y_1,label="自己",color="#F08080")
			plt.plot(x,y_2,label="同桌",color="#DB7093",linestyle="--")

			#设置x轴刻度
			_xtick_labels = ["{}岁".format(i) for i in x]
			plt.xticks(x,_xtick_labels,fontproperties=my_font)
			plt.yticks(range(0,9))

			#绘制网格
			plt.grid(alpha=0.4,linestyle=":")

			#添加图例
			plt.legend(prop=my_font,loc="upper left")

			#展示
			plt.show()

散点图
==================================================================================








































































































































































































































