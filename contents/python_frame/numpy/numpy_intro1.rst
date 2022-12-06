numpy 入门篇
##################################################################################

什么是 numpy
**********************************************************************************

为什么要学习 numpy
==================================================================================

	| 快速
	| 方便
	| 科学计算的基础库

numpy 是什么
==================================================================================

一个在 Python 中做科学计算的基础库，重在数值计算，也是大部分 PYTHON 科学计算库的基础库，多用于在大型、多维数组上执行数值运算。

numpy 基础
**********************************************************************************

numpy 创建数组(矩阵)
==================================================================================

.. code-block:: python

	# 创建数组
	import numpy as np

	a = np.array([1,2,3,4,5])
	b = np.array(range(1,6))
	c = np.arange(1,6)
	print(a)
	print(b)
	print(c)

	# [1 2 3 4 5]
	# [1 2 3 4 5]
	# [1 2 3 4 5]

	# 上面 a,b,c, 内容相同, 注意 arange 和 range 的区别

	# 数组的类名
	a = np.array([1,2,3,4,5])
	type(a)
	# ---> numpy.ndarray

	# 数据的类型
	print(a.dtype)
	# ---> int64

numpy 中常见的更多数据类型
==================================================================================

	| 类型 类型代码 说明
	| int8、uint8 i1、u1 有符号和无符号8位整型（1字节）
	| int16、uint16 i2、u2 有符号和无符号16位整型（2字节）
	| int32、uint32 i4、u4 有符号和无符号32位整型（4字节）
	| int64、uint64 i8、u8 有符号和无符号64位整型（8字节）
	| float16 f2 半精度浮点数
	| float32 f4、f 单精度浮点数
	| float64 f8、d 双精度浮点数
	| float128 f16、g 扩展精度浮点数
	| complex64 c8 分别用两个32位表示的复数
	| complex128 c16 分别用两个64位表示的复数
	| complex256 c32 分别用两个128位表示的复数
	| bool b 布尔型
	| object O python对象
	| string Sn 固定长度字符串，每个字符1字节，如S10
	| unicode Un 固定长度Unicode，字节数由系统决定，如U10

数据类型的操作
==================================================================================

.. code-block:: python

	# 指定创建的数组的数据类型
	a = np.array([1,0,1,0],dtype=bool) # 或者使用 dtype='?'
	print(a)

	# ---> [ True False  True False]

	# 指定创建的数组的数据类型
	a = np.array([1,0,1,0],dtype=bool) # 或者使用 dtype='?'
	print(a)
	# ---> [ True False  True False]

	# 修改数组的数据类型
	b = a.astype("i1")
	print(b)
	# --> [1 0 1 0]

	# 或者使用 a.asType(np.int8)
	c = a.astype(np.int8)
	print(c)
	# --> [1 0 1 0]

	# 修改浮点型的小数位数
	#numpy中的小数
	t7 = np.array([random.random() for i in range(10)])
	print(t7)
	print(t7.dtype)

	t8 = np.round(t7,2)
	print(t8)

	# [0.23509756 0.18365565 0.42878215 0.17844451 0.28498626 0.03637158
	#  0.81981437 0.51776255 0.74482902 0.91590774]
	# float64
	# [0.24 0.18 0.43 0.18 0.28 0.04 0.82 0.52 0.74 0.92] 

.. tip::

	综合案例

	.. code-block:: python

		# coding=utf-8
		import numpy as np
		import random

		#使用numpy生成数组,得到ndarray的类型
		t1 = np.array([1,2,3,])
		print(t1)
		print(type(t1))

		t2 = np.array(range(10))
		print(t2)
		print(type(t2))

		t3 = np.arange(4,10,2)
		print(t3)
		print(type(t3))

		print(t3.dtype)
		print("*"*100)
		#numpy中的数据类型

		t4 = np.array(range(1,4),dtype="i1")
		print(t4)
		print(t4.dtype)

		##numpy中的bool类型
		t5 = np.array([1,1,0,1,0,0],dtype=bool)
		print(t5)
		print(t5.dtype)

		#调整数据类型
		t6 = t5.astype("int8")
		print(t6)
		print(t6.dtype)

		#numpy中的小数
		t7 = np.array([random.random() for i in range(10)])
		print(t7)
		print(t7.dtype)

		t8 = np.round(t7,2)
		print(t8)

数组的形状
==================================================================================

.. code-block:: python

	a = np.array([[3,4,5,6,7,8],[4,5,6,7,8,9]])
	print(a)
	# [[3 4 5 6 7 8]
	#  [4 5 6 7 8 9]]

	# 查看数组的形状
	print(a.shape)
	# (2, 6)

	# 修改数组的形状
	print(a.reshape(3,4))
	# [[3 4 5 6]
	#  [7 8 4 5]
	#  [6 7 8 9]]

	print(a.shape)
	# (2, 6) a 的形状没有改变

	b = a.reshape(3,4)
	print(b.shape)
	# (3, 4)
	print(b)
	# [[3 4 5 6]
	#  [7 8 4 5]
	#  [6 7 8 9]]

	# 把数组转化为 1 维度数据
	c = b.reshape(1,12)
	print(c)
	# [[3 4 5 6 7 8 4 5 6 7 8 9]]  # 这是一维度数组吗？

	print(b.flatten())
	# [3 4 5 6 7 8 4 5 6 7 8 9]

数组和数的计算
==================================================================================

.. code-block:: python



numpy 常用方法
**********************************************************************************

numpy 常用统计方法
**********************************************************************************































































































































