前言
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

练习
==================================================================================

每隔两个小时的气温
----------------------------------------------------------------------------------














































































































































































































































