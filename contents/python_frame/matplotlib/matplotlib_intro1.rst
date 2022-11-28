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

对比常用统计图
==================================================================================

	| 折线图:以折线的上升或下降来表示统计数量的增减变化的统计图
	| 特点:能够显示数据的变化趋势，反映事物的变化情况。(变化)
	| 
	| 直方图:由一系列高度不等的纵向条纹或线段表示数据分布的情况。一般用横轴表示数据范围，纵轴表示分布情况。
	| 特点:绘制连续性的数据,展示一组或者多组数据的分布状况(统计)
	| 
	| 条形图:排列在工作表的列或行中的数据可以绘制到条形图中。
	| 特点:绘制连离散的数据,能够一眼看出各个数据的大小,比较数据之间的差别。(统计)
	| 
	| 散点图:用两组数据构成多个坐标点，考察坐标点的分布,判断两变量之间是否存在某种关联或总结坐标点的分布模式。
	| 特点:判断变量之间是否存在数量关联趋势,展示离群点(分布规律)

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

.. tabs::

	.. tab:: 男女朋友统计 v1

		假设大家在30岁的时候,根据自己的实际情况,统计出来了从11岁到30岁每年交的女(男)朋友的数量如列表a,请绘制出该数据的折线图,以便分析自己每年交女(男)朋友的数量走势 

			| a = [1,0,1,1,2,4,3,2,3,4,4,5,6,5,4,3,3,1,1,1]

		要求:

			| y	轴表示个数
			| x	轴表示岁数,比如11岁,12岁等

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

		假设大家在30岁的时候,根据自己的实际情况,统计出来了你和你同桌各自从11岁到30岁每年交的女(男)朋友的数量如列表a和b,请在一个图中绘制出该数据的折线图,以便比较自己和同桌20年间的差异,同时分析每年交女(男)朋友的数量走势

			| a = [1,0,1,1,2,4,3,2,3,4,4,5,6,5,4,3,3,1,1,1]
			| b = [1,0,3,1,2,2,3,3,2,1 ,2,1,1,1,1,1,1,1,1,1]

		要求:

		    | y轴表示个数
		    | x轴表示岁数,比如11岁,12岁等

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

.. tip::

	总结:

		| 绘制了折线图(plt.plot)
		| 设置了图片的大小和分辨率(plt.figure)
		| 实现了图片的保存(plt.savefig)
		| 设置了xy轴上的刻度和字符串(xticks)
		| 解决了刻度稀疏和密集的问题(xticks)
		| 设置了标题,xy轴的lable(title,xlable,ylable)
		| 设置了字体(font_manager. fontProperties,matplotlib.rc)
		| 在一个图上绘制多个图形(plt多次plot即可)
		| 为不同的图形添加图例

应用场景
----------------------------------------------------------------------------------

	| 呈现公司产品(不同区域)每天活跃用户数
	| 呈现app每天下载数量
	| 呈现产品新功能上线后,用户点击次数随时间的变化
	| 呈现员工每天上下班时间
	| ......

散点图
==================================================================================

气温变化散点图
----------------------------------------------------------------------------------

假设通过爬虫你获取到了北京2016年3,10月份每天白天的最高气温(分别位于列表a,b),那么此时如何寻找出气温和随时间(天)变化的某种规律?

	| a = [11,17,16,11,12,11,12,6,6,7,8,9,12,15,14,17,18,21,16,17,20,14,15,15,15,19,21,22,22,22,23]
	| b = [26,26,28,19,21,17,16,19,18,20,20,19,22,23,17,20,21,20,22,15,11,15,5,13,17,10,11,13,12,13,6]

数据来源: ``http://lishi.tianqi.com/beijing/index.html``

.. code-block:: python

	# coding=utf-8
	from matplotlib import pyplot as plt
	from matplotlib import font_manager

	my_font = font_manager.FontProperties(fname="/System/Library/Fonts/Hiragino Sans GB.ttc")
	y_3 = [11,17,16,11,12,11,12,6,6,7,8,9,12,15,14,17,18,21,16,17,20,14,15,15,15,19,21,22,22,22,23]
	y_10 = [26,26,28,19,21,17,16,19,18,20,20,19,22,23,17,20,21,20,22,15,11,15,5,13,17,10,11,13,12,13,6]

	x_3 = range(1,32)
	x_10 = range(51,82)

	#设置图形大小
	plt.figure(figsize=(20,8),dpi=80)

	#使用scatter方法绘制散点图,和之前绘制折线图的唯一区别
	plt.scatter(x_3,y_3,label="3月份")
	plt.scatter(x_10,y_10,label="10月份")

	#调整x轴的刻度
	_x = list(x_3)+list(x_10)
	_xtick_labels = ["3月{}日".format(i) for i in x_3]
	_xtick_labels += ["10月{}日".format(i-50) for i in x_10]
	plt.xticks(_x[::3],_xtick_labels[::3],fontproperties=my_font,rotation=45)

	#添加图例
	plt.legend(loc="upper left",prop=my_font)

	#添加描述信息
	plt.xlabel("时间",fontproperties=my_font)
	plt.ylabel("温度",fontproperties=my_font)
	plt.title("标题",fontproperties=my_font)
	#展示
	plt.show()

应用场景
----------------------------------------------------------------------------------

	| 不同条件(维度)之间的内在关联关系
	| 观察数据的离散聚合程度

条形图
==================================================================================

电影票房
----------------------------------------------------------------------------------

假设你获取到了2017年内地电影票房前20的电影(列表a)和电影票房数据(列表b),那么如何更加直观的展示该数据?

	| a = ["战狼2","速度与激情8","功夫瑜伽","西游伏妖篇","变形金刚5：最后的骑士","摔跤吧！爸爸","加勒比海盗5：死无对证","金刚：骷髅岛","极限特工：终极回归","生化危机6：终章","乘风破浪","神偷奶爸3","智取威虎山","大闹天竺","金刚狼3：殊死一战","蜘蛛侠：英雄归来","悟空传","银河护卫队2","情圣","新木乃伊",]
	| b=[56.01,26.94,17.53,16.49,15.45,12.96,11.8,11.61,11.28,11.12,10.49,10.3,8.75,7.55,7.32,6.99,6.88,6.86,6.58,6.23] 单位:亿

数据来源: ``http://58921.com/alltime/2017``

.. tabs::

	.. tab:: 垂直条形图

		.. code-block:: python

			# coding=utf-8
			from matplotlib import pyplot as plt
			from matplotlib import font_manager

			my_font = font_manager.FontProperties(fname="/System/Library/Fonts/Hiragino Sans GB.ttc")

			a = ["战狼2", "速度与激情8", "功夫瑜伽", "西游伏妖篇", "变形金刚5：最后的骑士", "摔跤吧！爸爸", "加勒比海盗5：死无对证",
			     "金刚：骷髅岛", "极限特工：终极回归", "生化危机6：终章", "乘风破浪", "神偷奶爸3", "智取威虎山", "大闹天竺",
			     "金刚狼3：殊死一战", "蜘蛛侠：英雄归来", "悟空传", "银河护卫队2", "情圣", "新木乃伊", ]

			b = [56.01, 26.94, 17.53, 16.49, 15.45, 12.96, 11.8, 11.61, 11.28, 11.12, 10.49, 10.3, 8.75, 7.55, 7.32, 6.99, 6.88,
			     6.86, 6.58, 6.23]

			#设置图形大小
			plt.figure(figsize=(20, 15), dpi=80)
			#绘制条形图
			plt.bar(range(len(a)), b, width=0.3)
			#设置字符串到x轴
			plt.xticks(range(len(a)), a, fontproperties=my_font, rotation=90)

			plt.savefig("./movie.png")

			plt.show()

	.. tab:: 水平条形图

		.. code-block:: python

			from matplotlib import pyplot as plt
			from matplotlib import font_manager

			my_font = font_manager.FontProperties(fname="/System/Library/Fonts/Hiragino Sans GB.ttc")

			a = ["战狼2", "速度与激情8", "功夫瑜伽", "西游伏妖篇", "变形金刚5：最后的骑士", "摔跤吧！爸爸", "加勒比海盗5：死无对证",
			     "金刚：骷髅岛", "极限特工：终极回归", "生化危机6：终章", "乘风破浪", "神偷奶爸3", "智取威虎山", "大闹天竺",
			     "金刚狼3：殊死一战", "蜘蛛侠：英雄归来", "悟空传", "银河护卫队2", "情圣", "新木乃伊", ]

			b = [56.01, 26.94, 17.53, 16.49, 15.45, 12.96, 11.8, 11.61, 11.28, 11.12, 10.49, 10.3, 8.75, 7.55, 7.32, 6.99, 6.88,
			     6.86, 6.58, 6.23]

			#设置图形大小
			plt.figure(figsize=(20, 8), dpi=80)
			#绘制条形图
			plt.barh(range(len(a)), b, height=0.3, color="orange")
			#设置字符串到x轴
			plt.yticks(range(len(a)), a, fontproperties=my_font)

			plt.grid(alpha=0.3)
			# plt.savefig("./movie.png")

			plt.show()

电影票房对比
----------------------------------------------------------------------------------

假设你知道了列表a中电影分别在2017-09-14(b_14), 2017-09-15(b_15), 2017-09-16(b_16)三天的票房,为了展示列表中电影本身的票房以及同其他电影的数据对比情况,应该如何更加直观的呈现该数据?

	| a = ["猩球崛起3：终极之战","敦刻尔克","蜘蛛侠：英雄归来","战狼2"]
	| b_16 = [15746,312,4497,319]
	| b_15 = [12357,156,2045,168]
	| b_14 = [2358,399,2358,362]

数据来源: ``http://www.cbooo.cn/movieday``

.. code-block:: python

	# coding=utf-8
	from matplotlib import pyplot as plt
	from matplotlib import font_manager
	my_font = font_manager.FontProperties(fname="/System/Library/Fonts/Hiragino Sans GB.ttc")

	a = ["猩球崛起3：终极之战", "敦刻尔克", "蜘蛛侠：英雄归来", "战狼2"]
	b_16 = [15746, 312, 4497, 319]
	b_15 = [12357, 156, 2045, 168]
	b_14 = [2358, 399, 2358, 362]

	bar_width = 0.2

	x_14 = list(range(len(a)))
	x_15 = [i + bar_width for i in x_14]
	x_16 = [i + 2 * bar_width for i in x_14]

	#设置图形大小
	plt.figure(figsize=(20,8),dpi=80)

	plt.bar(range(len(a)),b_14,width=bar_width,label="9月14日")
	plt.bar(x_15,b_15,width=bar_width,label="9月15日")
	plt.bar(x_16,b_16,width=bar_width,label="9月16日")

	#设置图例
	plt.legend(prop=my_font)

	#设置x轴的刻度
	plt.xticks(x_15,a,fontproperties=my_font)

	plt.show()

应用场景
----------------------------------------------------------------------------------

	| 数量统计
	| 频率统计(市场饱和度)

直方图
==================================================================================

电影分布直方图
----------------------------------------------------------------------------------

假设你获取了 250 部电影的时长(列表 a 中),希望统计出这些电影时长的分布状态(比如时长为 100 分钟到 120 分钟电影的数量,出现的频率)等信息,你应该如何呈现这些数据?

	| a=[131,  98, 125, 131, 124, 139, 131, 117, 128, 108, 135, 138, 131, 102, 107, 114, 119, 128, 121, 142, 127, 130, 124, 101, 110, 116, 117, 110, 128, 128, 115,  99, 136, 126, 134,  95, 138, 117, 111,78, 132, 124, 113, 150, 110, 117,  86,  95, 144, 105, 126, 130,126, 130, 126, 116, 123, 106, 112, 138, 123,  86, 101,  99, 136,123, 117, 119, 105, 137, 123, 128, 125, 104, 109, 134, 125, 127,105, 120, 107, 129, 116, 108, 132, 103, 136, 118, 102, 120, 114,105, 115, 132, 145, 119, 121, 112, 139, 125, 138, 109, 132, 134,156, 106, 117, 127, 144, 139, 139, 119, 140,  83, 110, 102,123,107, 143, 115, 136, 118, 139, 123, 112, 118, 125, 109, 119, 133,112, 114, 122, 109, 106, 123, 116, 131, 127, 115, 118, 112, 135,115, 146, 137, 116, 103, 144,  83, 123, 111, 110, 111, 100, 154,136, 100, 118, 119, 133, 134, 106, 129, 126, 110, 111, 109, 141,120, 117, 106, 149, 122, 122, 110, 118, 127, 121, 114, 125, 126,114, 140, 103, 130, 141, 117, 106, 114, 121, 114, 133, 137,  92,121, 112, 146,  97, 137, 105,  98, 117, 112,  81,  97, 139, 113,134, 106, 144, 110, 137, 137, 111, 104, 117, 100, 111, 101, 110,105, 129, 137, 112, 120, 113, 133, 112,  83,  94, 146, 133, 101,131, 116, 111,  84, 137, 115, 122, 106, 144, 109, 123, 116, 111,111, 133, 150]

.. code-block:: python

	# coding=utf-8
	from matplotlib import pyplot as plt
	from matplotlib import font_manager

	a=[131,  98, 125, 131, 124, 139, 131, 117, 128, 108, 135, 138, 131, 102, 107, 114, 119, 128, 121, 142, 127, 130, 124, 101, 110, 116, 117, 110, 128, 128, 115,  99, 136, 126, 134,  95, 138, 117, 111,78, 132, 124, 113, 150, 110, 117,  86,  95, 144, 105, 126, 130,126, 130, 126, 116, 123, 106, 112, 138, 123,  86, 101,  99, 136,123, 117, 119, 105, 137, 123, 128, 125, 104, 109, 134, 125, 127,105, 120, 107, 129, 116, 108, 132, 103, 136, 118, 102, 120, 114,105, 115, 132, 145, 119, 121, 112, 139, 125, 138, 109, 132, 134,156, 106, 117, 127, 144, 139, 139, 119, 140,  83, 110, 102,123,107, 143, 115, 136, 118, 139, 123, 112, 118, 125, 109, 119, 133,112, 114, 122, 109, 106, 123, 116, 131, 127, 115, 118, 112, 135,115, 146, 137, 116, 103, 144,  83, 123, 111, 110, 111, 100, 154,136, 100, 118, 119, 133, 134, 106, 129, 126, 110, 111, 109, 141,120, 117, 106, 149, 122, 122, 110, 118, 127, 121, 114, 125, 126,114, 140, 103, 130, 141, 117, 106, 114, 121, 114, 133, 137,  92,121, 112, 146,  97, 137, 105,  98, 117, 112,  81,  97, 139, 113,134, 106, 144, 110, 137, 137, 111, 104, 117, 100, 111, 101, 110,105, 129, 137, 112, 120, 113, 133, 112,  83,  94, 146, 133, 101,131, 116, 111,  84, 137, 115, 122, 106, 144, 109, 123, 116, 111,111, 133, 150]

	#计算组数
	d = 3  #组距
	num_bins = (max(a) - min(a)) // d
	print(max(a),min(a),max(a)-min(a))
	print(num_bins)

	#设置图形的大小
	plt.figure(figsize=(20,8),dpi=80)
	plt.hist(a,num_bins)
	#设置x轴的刻度
	plt.xticks(range(min(a),max(a)+d,d))

	plt.grid()

	plt.show()

使用条形图模拟直方图(直方图解决不了的时候)
----------------------------------------------------------------------------------

在美国2004年人口普查发现有124 million的人在离家相对较远的地方工作。根据他们从家到上班地点所需要的时间,通过抽样统计(最后一列)出了下表的数据,这些数据能够绘制成直方图么?

	| interval = [0,5,10,15,20,25,30,35,40,45,60,90]
	| width = [5,5,5,5,5,5,5,5,5,15,30,60]
	| quantity = [836,2737,3723,3926,3596,1438,3273,642,824,613,215,47]
	| 
	| 数据来源: ``https://en.wikipedia.org/wiki/Histogram``
	| 普查报告地址: ``https://www.census.gov/prod/2004pubs/c2kbr-33.pdf``

.. code-block:: python

	# coding=utf-8
	from matplotlib import pyplot as plt
	from matplotlib import font_manager

	interval = [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 60, 90]
	width = [5, 5, 5, 5, 5, 5, 5, 5, 5, 15, 30, 60]
	quantity = [836, 2737, 3723, 3926, 3596, 1438, 3273, 642, 824, 613, 215, 47]
	print(len(interval), len(width), len(quantity))

	#设置图形大小
	plt.figure(figsize=(20, 8), dpi=80)

	plt.bar(range(12), quantity, width=1)

	#设置x轴的刻度
	_x = [i - 0.5 for i in range(13)]
	_xtick_labels = interval + [150]
	plt.xticks(_x, _xtick_labels)

	plt.grid(alpha=0.4)
	plt.show()

应用场景
----------------------------------------------------------------------------------

	| 用户的年龄分布状态
	| 一段时间内用户点击次数的分布状态
	| 用户活跃时间的分布状态

总结
**********************************************************************************

常见问题总结
==================================================================================

	| 应该选择那种图形来呈现数据
	| matplotlib.plot(x,y)
	| matplotlib.bar(x,y)
	| matplotlib.scatter(x,y)
	| matplotlib.hist(data,bins,normed)
	| xticks和yticks的设置
	| label和titile,grid的设置
	| 绘图的大小和保存图片

使用流程总结
==================================================================================

	| 明确问题
	| 选择图形的呈现方式
	| 准备数据
	| 绘图和图形完善

更多的图形样式
==================================================================================

matplotlib 支持的图形是非常多的，如果有其他的需求，可以查看一下 url 地址：``https://matplotlib.org/stable/plot_types/index``






























































































































































































































