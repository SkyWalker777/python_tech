机器学习 3 机器学习模型
#######################################################################

主要内容

1. 监督学习

::

	回归模型
		线性回归
	分类模型
		k 近邻 (KNN)
		决策树
		逻辑斯蒂回归

2. 无监督学习

::

	聚类
		k 均值 (k-means)
	降维

监督学习 —— 回归模型
***********************************************************************

线性回归模型
=======================================================================

|image0|

- 线性回归(linear regression)是一种线性模型，它假设输入变量x 和单个输出变量y之间存在线性关系

- 具体来说，利用线性回归模型，可以从一组输入变量X的线性组合中, 计算输出变量y

|image1|

* 线性方程求解

假设有一个如下的二元一次方程：

::

	y = ax + b

已知两组数据：　　

::

	x = l时，y = 3,即(1,3)
	x = 2 时,y = 5,即(2,5)

将数据输入方程中，可得：

::

	a + b = 3
	2a + b = 5

解得： ``a = 2, b = 1``; 即方程为： ``2x + 1 = y``

当有任意一个x时，输入方程，就可以得到对应的y. 例如x=5时,y = 11。

* 线性回归模型

|image2|

给定有d个属性（特征）描述的示例x=（x1;x2;…；xd）,其中 xi 是 x 在第 i 个属性（特征）上的取值，线性模型（linear model）试图学得一个通过 属性（特征）的线性组合来进行预测的函数，即：

|image3|

—般用向量形式写成： 

|image4|

|image5|

| 假设特征和结果都满足线性，即不大于一次方。

| w和b学得之后，模型就得以确定。

| 许多功能更为强大的非线性模型可在线性模型的基础上通过引入层级结构 或高维映射而得。

* 最小二乘法

基于均方误差最小化来进行模型求解的方法称为“最小二乘法” (least square method)

它的主要思想就是选择未知参数使得理论值与观测值之差的平方和达到最小。

|image6|

- 我们假设输入属性（特征）的数目只有一个：

|image7|

- 在线性回归中，最小二乘法就是试图找到一条直线，使所有样本到 直线上的欧式距离之和最小。

|image8|

求解w和b,使得  最小化的过程，称为线性回归 模型的“最小二乘参数估计”

|image9|

将 E(w, b) 分别对w和b求导，可以得到

|image10|

令偏导数都为0,可以得到

|image11|

——其中：

|image12|

* 代码实现： 

.. code-block:: python
	:linenos:

	0.引入依赖
	import numpy as np
	import matplotlib.pyplot as plt

	1.导入数据（data.csv）
	points = np.genfromtxt("data.csv", delimiter=",")
	#points

	# 提取 points 中的两列数据，分别作为 x，y
	x = points[:, 0]
	y = points[:, 1]

	# 用 plt 画出散点图
	plt.scatter(x, y)
	plt.show()
	 
	2.定义损失函数
	# 损失函数是系数的函数
	def compute_cost(w, b, points):
	    total_cost = 0
	    M = len(points)
	    
	    # 逐点计算平方损失误差，然而求平均数
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        total_cost += ( y - w * x - b ) ** 2
	        
	    return total_cost/M

	3.定义核心算法拟合函数
	# 先定义一个求平均值的函数
	def average(data):
	    sum = 0
	    num = len(data)
	    for i in range(num):
	        sum += data[i]
	    return sum/num

	# 定义核心拟合函数
	def fit(points):
	    M = len(points)
	    x_bar = average(points[:, 0])
	    
	    sum_yx = 0
	    sum_x2 = 0
	    sum_delta = 0
	    
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        sum_yx += y * (x - x_bar)
	        sum_x2 += x ** 2
	    # 根据公式计算 W
	    w = sum_yx / (sum_x2 - M * (x_bar**2))
	    
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        sum_delta += (y - w * x)
	    b = sum_delta / M
	    
	    return w, b

	4.测试
	w, b = fit(points)

	print("w is：", w)
	print("b is：", b)

	cost = compute_cost(w, b, points)
	print("cost is：", cost)
	5.画出拟合曲线
	plt.scatter(x, y)

	# 针对每一个 x，计算出预测的 y 值
	pred_y = w * x + b
	plt.plot(x, pred_y, c='r')
	plt.show()

散点图:

|image13|

拟合图:

|image14|

多元线性回归
=======================================================================

- 如果有两个或两个以上的自变量，这样的线性回归分析就称为多元线 性回归

- 实际问题中，一个现象往往是受多个因素影响的，所以多元线性回归 比一元线性回归的实际应用更广

|image15|

* 梯度下降法求解线性回归

|image16|

|image17|

• a在梯度下降算法中被称作为学习率或者步长

• 这意味着我们可以通过a来控制每一步走的距离，以保证不要走太快，错 过了最低点；同时也要保证收敛速度不要太慢

• 所以a的选择在梯度下降法中往往是很重要的，不能太大也不能太小

|image18|

简单线性回归（梯度下降法）

.. code-block:: python
	:linenos:

	0.引入依赖
	import numpy as np
	import matplotlib.pyplot as plt

	1.导入数据（data.csv）
	points = np.genfromtxt("data.csv", delimiter=",")
	#points

	# 提取 points 中的两列数据，分别作为 x，y
	x = points[:, 0]
	y = points[:, 1]

	# 用 plt 画出散点图
	plt.scatter(x, y)
	plt.show()
	2.定义损失函数
	# 损失函数是系数的函数
	def compute_cost(w, b, points):
	    total_cost = 0
	    M = len(points)
	    
	    # 逐点计算平方损失误差，然而求平均数
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        total_cost += ( y - w * x - b ) ** 2
	        
	    return total_cost/M

	3.定义模型的超参数
	alpha = 0.0001
	initial_w = 0
	initial_b = 0
	num_iter = 10

	4.定义核心梯度下降算法函数
	def grad_desc(points, initial_b, initial_w, alpha, num_iter):
	    w = initial_w
	    b = initial_b
	    
	    # 定义一个列表保存所有的损失函数值，用来显示下降的过程
	    cost_list = []
	    for i in range(num_iter):
	        cost_list.append(compute_cost(w, b, points))
	        w, b = step_grad_desc(w, b, alpha, points)
	    return [w, b, cost_list]

	def step_grad_desc(current_w, current_b, alpha, points):
	    sum_grad_w = 0
	    sum_grad_b = 0
	    M = len(points)
	    
	    # 对每个点，带入公式求和
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        sum_grad_w += (current_w * x + current_b - y) * x
	        sum_grad_b += current_w * x + current_b - y
	        
	    # 用公式求当前梯度
	    grad_w = 2/M * sum_grad_w
	    grad_b = 2/M * sum_grad_b
	    
	    # 梯度下降，更新当前的 w 和 b 
	    updated_w = current_w - alpha * grad_w
	    updated_b = current_b - alpha * grad_b
	    
	    return updated_w, updated_b

	5.测试：运行梯度下降算法计算最优的 w 和 b
	w, b, cost_list = grad_desc(points, initial_b, initial_w, alpha, num_iter)

	print("w is：", w)
	print("b is：", b)
	cost = compute_cost(w, b, points)
	print("cost is：", cost)
	plt.plot(cost_list)
	plt.show()
	 
	6.画出拟合曲线
	plt.scatter(x, y)

	# 针对每一个 x，计算出预测的 y 值
	pred_y = w * x + b
	plt.plot(x, pred_y, c='r')
	plt.show()

散点图:

|image19|

迭代多次之后, 损失函数接近不变: 

|image20|

拟合曲线:

|image21|

* 梯度下降法和最小二乘法

相同点

::

	- 本质和目标相同：两种方法都是经典的学习算法，在给定已知数据的前提下利用求导算
	- 出一个模型（函数），使得损失函数最小，然后对给定的新数据进行估算预测

不同点

::

	- 损失函数：梯度下降可以选取其它损失函数，而最小二乘一定是平方损失函数
	- 实现方法：最小二乘法是直接求导找出全局最小；而梯度下降是一种迭代法
	- 效果：最小二乘找到的一定是全局最小，但计算繁琐，且复杂情况下未必有解；
		梯度下 降迭代计算简单，但找到的一般是局部最小，只有在目标函数是凸函数时才是全局最小;
		到最小点附近时收敛速度会变慢，且对初始点的选择极为敏感

Python 自带线性回归库（sklearn）（最小二乘法）
=======================================================================

.. code-block:: python
	:linenos:

	import numpy as np
	import matplotlib.pyplot as plt

	points = np.genfromtxt("data.csv", delimiter=",")
	#points

	# 提取 points 中的两列数据，分别作为 x，y
	x = points[:, 0]
	y = points[:, 1]

	# 用 plt 画出散点图
	plt.scatter(x, y)
	plt.show()


	# 损失函数是系数的函数
	def compute_cost(w, b, points):
	    total_cost = 0
	    M = len(points)
	    
	    # 逐点计算平方损失误差，然而求平均数
	    for i in range(M):
	        x = points[i, 0]
	        y = points[i, 1]
	        total_cost += ( y - w * x - b ) ** 2
	        
	    return total_cost/M

	from sklearn.linear_model import LinearRegression
	lr = LinearRegression()

	x_new = x.reshape(-1, 1)
	y_new = y.reshape(-1, 1)

	lr.fit(x_new, y_new)
	 
	# 从训练好的模型中提取系数和截距
	w = lr.coef_
	b = lr.intercept_

	print("w is：", w)
	print("b is：", b)
	cost = compute_cost(w, b, points)
	print("cost is：", cost)


	w = w[0][0]
	b = b[0]

	plt.scatter(x, y)

	# 针对每一个 x，计算出预测的 y 值
	pred_y = w * x + b
	plt.plot(x, pred_y, c='r')
	plt.show()

散点图:

|image22|

拟合图:

|image23|

监督学习 —— 分类模型
***********************************************************************

* K 近邻
* 逻辑斯蒂回归
* 决策树

|image24|

K近邻（KNN）
=======================================================================

• 最简单最初级的分类器，就是将全部的训练数据所对应的类别都记录下 来，

　　当测试对象的属性和某个训练对象的属性完全匹配时，便可以对其 进行分类

• K近邻（k-nearest neighbour, KNN）是一种基本分类方法，通过测量不同特征值之间的距离进行分类。

　　它的思路是：如果一个样本在特征空间 中的k个最相似（即特征空间中最邻近）的样本中的大多数属于某一个类 另L则该样本也属于这个类别，其中K通常是不大于20的整数

• KNN算法中，所选择的邻居都是已经正确分类的对象

KNN 示例
------------------------------------------------------------------------

• 绿色圆要被决定赋予哪个类，是红色三 角形还是蓝色四方形？

• 如果K=3，由于红色三角形所占比例为 2/3，绿色圆将被赋予红色三角形那个类,

• 如果K=5，由于蓝色四方形比例为3/5, 因此绿色圆被赋予蓝色四方形类

• KNN 算法的结果很大程度取决于K的选择

|image25|

KNN 距离计算
------------------------------------------------------------------------

• KNN中，通过计算对象间距离来作为各个对象之间的非相似性指标，避免了对象之间的匹配I可题，在这里距蜀一般使用欧氏距离或曼哈顿距离：

|image26|

KNN 算法
------------------------------------------------------------------------

- 在训练集中数据和标签已知的情况下，输入测试数据，将测试数据的特征与训练集中对应的特征进行相互比较，找到训练集中与之最为相似的前K个数据, 则该测试数据对应的类别就是K个数据中出现次数最多的那个分类，其算法的描述为：

::

	a） 计算测试数据与各个训练数据之间的距离；
	b） 按照距离的递增关系进行排序；
	c） 选取距离最小的K个点；
	d） 确定前K个点所在类别的出现频率；
	e） 返回前K个点中出现频率最高的类别作为测试数据的预测分类。

* 代码实现

.. code-block:: python
	:linenos:

	0.引入依赖
	import numpy as np
	import pandas as pd

	# 这里直接引入 sklearn 里的数据集，iris 鸢尾花
	from sklearn.datasets import load_iris
	from sklearn.model_selection import train_test_split # 切分数据集为训练集和测试集
	from sklearn.metrics import accuracy_score # 计算分类预测的准确率

	1.数据加载和预处理
	iris = load_iris()
	df = pd.DataFrame(iris.data, columns = iris.feature_names)
	df['class'] = iris.target
	df['class'] = df['class'].map({0: iris.target_names[0], 1: iris.target_names[1], 2: iris.target_names[2]})
	df.describe()

	//测试输出数据
	sepal length (cm)	sepal width (cm)	petal length (cm)	petal width (cm)
	count	150.000000	150.000000	150.000000	150.000000
	mean	5.843333	3.057333	3.758000	1.199333
	std	0.828066	0.435866	1.765298	0.762238
	min	4.300000	2.000000	1.000000	0.100000
	25%	5.100000	2.800000	1.600000	0.300000
	50%	5.800000	3.000000	4.350000	1.300000
	75%	6.400000	3.300000	5.100000	1.800000
	max	7.900000	4.400000	6.900000	2.500000

	x = iris.data

	# 将 y 转化为二维数组
	y = iris.target.reshape(-1, 1)
	print(x.shape, y.shape)

	//输出数据
	(150, 4) (150, 1)

	# 划分训练集和测试集
	x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.3, random_state=35, stratify=y)

	# print(x_train)
	print(x_train.shape, y_train.shape)
	print(x_test.shape, y_test.shape)

	//训练集和测试集大小划分
	(105, 4) (105, 1)
	(45, 4) (45, 1)
	 
	2.核心算法实现
	# 距离函数定义
	def l1_distance(a, b):
	    return np.sum(np.abs(a-b), axis=1)

	def l2_distance(a, b):
	    return np.sqrt(np.sum((a-b) ** 2, axis=1))

	#x_test[0].reshape(1, -1).shape
	#np.sum(np.abs(x_train - x_test[0].reshape(1, -1)), axis=1)

	# 分类器的实现
	class KNN(object):
	    # 定义一个初始化方法：__init__ 是类的构造方法
	    def __init__(self, n_neighbors = 1, dist_func = l1_distance):
	        self.n_neighbors = n_neighbors
	        self.dist_func = dist_func
	        
	    # 训练模型的方法
	    def fit(self, x, y):
	        self.x_train = x
	        self.y_train = y
	        
	    # 模型预测方法
	    def predict(self, x):
	        # 初始化预测分类数组
	        y_pred = np.zeros((x.shape[0], 1), dtype=self.y_train.dtype)
	        
	        # 遍历输入的 x 数据点，取出每一个数据点的序号 i 和数据 x_test
	        for i, x_test in enumerate(x):
	            # x_test 跟所有训练数据计算距离
	            distances = self.dist_func(self.x_train, x_test)
	            
	            # 得到的距离按照由近到远排序,取出索引值
	            nn_index = np.argsort(distances)
	            
	            # 西选取最近的 k 个点，保存它们对应的分类类别
	            nn_y = self.y_train[nn_index[:self.n_neighbors]].ravel()
	            
	            # 统计类别出现频率最高的那个，赋给 y_pred[i]
	            y_pred[i] = np.argmax(np.bincount(nn_y))
	        
	        return y_pred

	3.测试
	# 定义一个实例
	knn = KNN(n_neighbors = 5)

	# 训练模型
	knn.fit(x_train, y_train)

	# 传入测试数据，做预测
	y_pred = knn.predict(x_test)

	# 求出预测准确率
	accracy = accuracy_score(y_test, y_pred)

	print("预测准确率：", accracy)

	//预测准确率： 0.9777777777777777
	 
	4.测试集
	# 定义一个实例
	knn = KNN(n_neighbors = 3)

	# 训练模型
	knn.fit(x_train, y_train)

	# 保存结果 list
	result_list = []

	# 针对不同的参数选取，做预测
	for p in [1, 2]:
	    knn.dist_func = l1_distance if p ==1 else l2_distance
	    
	    # 考虑不同的 k 取值
	    for k in range(1, 10, 2):
	        knn.n_neighbors = k
	        
	        # 传入测试数据，做预测
	        y_pred = knn.predict(x_test)
	        
	        # 求出预测准确率
	        accracy = accuracy_score(y_test, y_pred)
	        
	        # 保存准确率
	        result_list.append([k, 'l1_distance' if p ==1 else 'l2_distance', accracy])
	        
	df = pd.DataFrame(result_list, columns=['k', '距离函数', '预测准确率'])
	df

	//输出结果
	k	距离函数	预测准确率
	0	1	l1_distance	0.933333
	1	3	l1_distance	0.933333
	2	5	l1_distance	0.977778
	3	7	l1_distance	0.955556
	4	9	l1_distance	0.955556
	5	1	l2_distance	0.933333
	6	3	l2_distance	0.933333
	7	5	l2_distance	0.977778
	8	7	l2_distance	0.977778
	9	9	l2_distance	0.977778

逻辑斯蒂回归
=======================================================================

- 线性回归的问题 一一 怎样判断肿瘤是否恶性?

|image27|

- 线性回归健壮性不够，一旦有噪声，立刻“投降”

|image28|

逻辑斯蒂回归 —— 分类问题
------------------------------------------------------------------------

|image29|

Sigmoid函数（压缩函数）
------------------------------------------------------------------------

|image30|

|image31|

- 将线性回归拟合出来的值用压缩函数进行压缩，压缩完成后; 用0.5做一个概率的判定边界，就能把样本分成两类，即正样本

|image32|

|image33|

• sigmoid函数中， e (-Z 次方) 中 z 的正负决定了 g(z)的值最后是大于0.5还是小于0.5； 即z大于0时,g(z)大于0.5, z小于0时,g(z)小于0.5

• 当z对应的表达式为分类边界时，恰好有分类边界两侧对应z正负不同，也就使得分类边界两边分别对应g(z)>0.5和g(z)<0.5,因此根据g(z)与0.5的大小关系，就可以实现分类

逻辑斯谛回归损失函数
------------------------------------------------------------------------

-平方损失函数的问题

|image34|

-损失函数

|image35|

|image36|

|image37|

|image38|

|image39|

这样，就获得了一个凸函数。

梯度下降法求解
------------------------------------------------------------------------

|image40|

决策树
=======================================================================

-决策树是一种简单高效并且具有强解释性的模型，广泛应用于数据分析领域。其本质是一颗自上而下的由多个判断节点组成的树

|image41|

决策树示例
------------------------------------------------------------------------

-预测小明今天是否会出门打球

|image42|

|image43|

|image44|

决策树与 if-then 规则
------------------------------------------------------------------------

• 决策树可以看作一个if-then规则的集合

• 由决策树的根节点到叶节点的每一条路径，构建一条规则：路径上内部节点 的特征对应着规则的条件(condition),叶节点对应规则的结论

• 决策树的if-then规则集合有一^重要性质：互斥并且完备。这就是说，每个实例都被一条规则(一条路径)所覆盖，并且只被这一条规则覆盖

|image45|

决策树的目标
------------------------------------------------------------------------

- 决策树学习的本质，是从训练数据集中归纳出一组if-then分类规则

- 与训练集不相矛盾的决策树，可能有很多个，也可能一个也没有；所以我们 需要选择一个与训练数据集矛盾较小的决策树

- 另一角度，我们可以把决策树看成一个条件概率模型，我们的目标是将实例 分配到条件概率更大的那一类中去

- 从所有可能的情况中选择最优决策树，是一个NP完全问题，所以我们通常采 用启发式算法求解决策树，得到一个次最优解

- 采用的算法通常是递归地进行以下过程：选择最优特征，并根据该特征对训 练数据进行分割，使得各个子数据集都有一个最好的分类

特征选择
------------------------------------------------------------------------

- 特征选择就是决定用哪个特征来划分特征空间

|image46|

随机变量
------------------------------------------------------------------------

• 随机变量(random variable)的本质是一个函数，是从样本空间的子集到实 数的映射，将事件转换成一个数值

|image47|

• 根据样本空间中的元素不同(即不同的实验结果)，随机变量的值也将随机产生。 可以说，随机变量是“数值化”的实验结果

• 在现实生活中，实验结果是描述性的词汇，比如“硬币的正面”、“反面”。 在数学家眼里，这些文字化的叙述太过繁琐，所以拿数字来代表它们

熵
------------------------------------------------------------------------

• 熵(entropy)用来衡量随机变量的不确定性

• 变量的不确定性越大，炳也就越大 设X是一个取有限个值的离散随机变量，其概率分布为：

|image48|

则随机变量X的炳定义为：

|image49|

通常，上式中的对数以2为底或者以e为底(自然对数) ，称为比特(bit)或纳特(nat) 。当随机变量只取两个值，例如1,0时，则X的分布为：

|image50|

熵为：

|image51|

这时，熵H(p)随概率p变化的曲线如下图所示(单位为比特)：

|image52|

熵的示例
------------------------------------------------------------------------

- 给三个球分类

　　显然一眼就可以看出把红球独自一组，黑球一组;

　　那么从熵的观点来看，是什么情况呢？

|image53|

初始状态的熵：

|image54|

- 第一种分类方法是一个红球、一个黑球一组，另一个黑球自己一组。

　　在红黑一组中有红球和黑球，红黑球各自出现的概率是1/2.

　　在另一组100%出现黑球,红球的概率是0

|image55|

|image56|

• 第二种分法就是红球自己一组，剩下两个黑球一组

　　- 在红球组中出现黑球的概率是0,在黑球组中出现红球的概率是0,这样的

　　分类已经“纯” 了，也就是分类后子集中的随机变量已经变成确定性的了

|image57|

|image58|

决策树的目标
------------------------------------------------------------------------

- 使用决策树模型的最终目的是利用决策树模型进行分类预测，预测我们 给出的一组数据最终属于哪一种类别，这是一个由不确定到确定的过程

- 最终理想的分类是，每一组数据，都能确定性地按照决策树分支找到对应的 类别

- 所以就选择使数据信息炳下降最快的特征作为分类节点，使得决策树尽快地趋于确定

* 条件熵(conditional entropy )

- 条件熵 H(Y|X) 表示在已知随机变量X的条件下随机变量Y的不确定性:

|image59|

- 熵 H(D) 表示对数据集 D 进行分类的不确定性。

- 条件熵 H(D|A) 指在给定特征 A 的条件下数据集分类的不确定性

- 当炳和条件炳的概率由数据估计得到时，所对应的炳与条件炳分别称为经验熵 (empirical entropy) 和经验条件炳 (empirical conditional entropy)

* 信息增益

-特征A对训练数据集D的信息增益g(D, A)，定义为集合D的经验炳H(D)与特征: A给定条件下D的条件炳H(D|A)之差，即

|image60|

-决策树学习应用信息增益准则选择特征

- 经验嫡H(D)表示对数据集D进行分类的不确定性。而经验条件嫡H(D|A)表示在特征A给定的条件下对数据集D进行分类的不确定性。那么它们的差，即信息增益，就表示由于 特征A而使得对数据集D的分类的不确定性减少的程度

- 对于数据集D而言，信息增益依赖于特征，不同的特征往往具有不同的信息增益

- 信息增益大的特征具有更强的分类能力

决策树的生成算法
------------------------------------------------------------------------

• ID3

　　- 决策树（ID3）的训练过程就是找到信息增益最大的特征，然后按照此特征进行分类，然后再找到各类型子集中信息增益最大的特征，然后按照此特征进行分类， 最终得到符合要求的模型。

• C4.5

　　- C4.5算法在ID3基础上做了改进，用信息增益比来选择特征

• 分类与回归树（CART）

　　- 由特征选择、树的生成和剪枝三部分组成，既可以用于分类也可以用于回归

无监督学习 -- 聚类
***********************************************************************

相对监督学习（输入进x，有对应的y），没有标注

::

	聚类
		k均值
		基于密度的聚类
		最大期望聚类
	降维
		潜语义分析（LSA）
		主成分分析（PCA）
		奇异值分解（SVD）

|image61|

| k 均值（k-means）是聚类算法中最为简单、高效的，属于无监督学习算法
| 核心思想：由用户指定k个初始质心（initial centroids)，以作为聚类的类别（cluster），重复迭代直至算法收敛
| 基本算法流程：

::

	- 选取k个初始质心（作为初始cluster）；
	- repeat：
		- 对每个样本点，计算得到距其最近的质心，将其类别标为该质心所对应的cluster；
		- 重新计算k个cluser对应的质心；
	- until 质心不再发生变化或迭代达到上限

* kmeans代码实现

.. code-block:: python
	:linenos:

	import numpy as np
	import matplotlib.pyplot as plt

	# 从sklearn中直接生成聚类数据
	from sklearn.datasets.samples_generator import make_blobs

	x, y = make_blobs( n_samples=100, centers=6, random_state=1234, cluster_std=0.6 )
	#100个样本，centers生成的样本中心（类别）数; random_state --seed used by the random number generator;
	#cluster_std为每个类别设置不同的方差
	#x.shape #(100, 2)
	plt.figure(figsize=(6,6))
	plt.scatter(x[:,0], x[:,1], c=y)  #c=y 6类，变得有颜色
	plt.show()

	# 引入scipy中的距离函数，默认计算欧式距离
	from scipy.spatial.distance import cdist

	class K_Means(object):
	    # 初始化，参数 n_clusters（K）、迭代次数max_iter、初始质心 centroids
	    def __init__(self, n_clusters=5, max_iter=300, centroids=[]):
	        self.n_clusters = n_clusters
	        self.max_iter = max_iter
	        self.centroids = np.array( centroids, dtype=np.float )
	        
	    # 训练模型方法，k-means聚类过程，传入原始数据
	    def fit(self, data):
	        # 假如没有指定初始质心，就随机选取data中的点作为初始质心
	        if( self.centroids.shape == (0,) ):
	            # 从data中随机生成0到data行数的6个整数，作为索引值
	            self.centroids = data[ np.random.randint( 0, data.shape[0], self.n_clusters ) ,: ]
	            
	        # 开始迭代
	        for i in range(self.max_iter):
	            # 1. 计算距离矩阵，得到的是一个100*6的矩阵
	            distances = cdist(data, self.centroids)
	            
	            # 2. 对距离按有近到远排序，选取最近的质心点的类别，作为当前点的分类
	            c_ind = np.argmin( distances, axis=1 ) #axis=1保留最近一列
	            
	            # 3. 对每一类数据进行均值计算，更新质心点坐标
	            for i in range(self.n_clusters ):
	                # 排除掉没有出现在c_ind里的类别
	                if i in c_ind:
	                    # 选出所有类别是i的点，取data里面坐标的均值，更新第i个质心
	                    #data[c_ind==i]布尔索引，拿到为true的值
	                    self.centroids[i] = np.mean( data[c_ind==i], axis=0 )
	    
	    # 实现预测方法
	    def predict(self, samples):
	        # 跟上面一样，先计算距离矩阵，然后选取距离最近的那个质心的类别
	        distances = cdist(samples, self.centroids)
	        c_ind = np.argmin( distances, axis=1 )
	        
	        return c_ind
	##测试下，二维数组5 * 4（4个质心点），行数代表有几个点，一行数是跟每一个质心的距离
	dist = np.array([[121,221,32,43],
	                [121,1,12,23],
	                [65,21,2,43],
	                [1,221,32,43],
	                [21,11,22,3],])
	c_ind = np.argmin( dist, axis=1 )
	print(c_ind) #每一个元素跟哪一类最近 [2 1 2 0 3]
	x_new=x[0:5] 
	print(x_new)#
	print(c_ind==2) #[ True False  True False False]
	print(x_new[c_ind==2])
	np.mean(x_new[c_ind==2], axis=0) #每一个坐标每一列对应的平均值

	//输出数据
	[2 1 2 0 3]
	[[-0.02708305  5.0215929 ]
	 [-5.49252256  6.27366991]
	 [-5.37691608  1.51403209]
	 [-5.37872006  2.16059225]
	 [ 9.58333171  8.10916554]]
	[ True False  True False False]
	[[-0.02708305  5.0215929 ]
	 [-5.37691608  1.51403209]]

	# 定义一个绘制子图函数
	def plotKMeans(x, y, centroids, subplot, title):
	    # 分配子图，121表示1行2列的子图中的第一个
	    plt.subplot(subplot)
	    plt.scatter(x[:,0], x[:,1], c='r')
	    # 画出质心点
	    plt.scatter(centroids[:,0], centroids[:,1], c=np.array(range(5)), s=100)
	    plt.title(title)
	#centroids指定初始点
	kmeans = K_Means(max_iter=300, centroids=np.array([[2,1],[2,2],[2,3],[2,4],[2,5]]))

	plt.figure(figsize=(16, 6))
	#初始状态的图
	plotKMeans( x, y, kmeans.centroids, 121, 'Initial State' )

	# 开始聚类
	kmeans.fit(x)

	plotKMeans( x, y, kmeans.centroids, 122, 'Final State' )

	# 预测新数据点的类别
	x_new = np.array([[0,0],[10,7]])
	y_pred = kmeans.predict(x_new)

	print(kmeans.centroids)
	print(y_pred)

	plt.scatter(x_new[:,0], x_new[:,1], s=100, c='black') 

	//输出数据
	[[ 5.76444812 -4.67941789]
	 [-2.89174024 -0.22808556]
	 [-5.89115978  2.33887408]
	 [-2.8455246   5.87376915]
	 [ 9.20551979  7.56124841]]
	[1 4]

|image62|

|image63|

.. |image0| image:: /_static/machine_learn_intro/1604514-20190723012250797-1299937709.png
.. |image1| image:: /_static/machine_learn_intro/1604514-20190723012347497-1186254805.png
.. |image2| image:: /_static/machine_learn_intro/1604514-20190723012728839-860497705.png
.. |image3| image:: /_static/machine_learn_intro/1604514-20190723012903129-1323346657.png
.. |image4| image:: /_static/machine_learn_intro/1604514-20190723012924478-1922724232.png
.. |image5| image:: /_static/machine_learn_intro/1604514-20190723012933461-1040353286.png
.. |image6| image:: /_static/machine_learn_intro/1604514-20190723013841813-1107679314.png
.. |image7| image:: /_static/machine_learn_intro/1604514-20190723013916600-1475505172.png
.. |image8| image:: /_static/machine_learn_intro/1604514-20190723014008095-1683753160.png
.. |image9| image:: /_static/machine_learn_intro/1604514-20190723014359151-920595108.png
.. |image10| image:: /_static/machine_learn_intro/1604514-20190723014531309-948386531.png
.. |image11| image:: /_static/machine_learn_intro/1604514-20190723014627491-1994641169.png
.. |image12| image:: /_static/machine_learn_intro/1604514-20190723014651795-775598261.png
.. |image13| image:: /_static/machine_learn_intro/machine_learn_intro_001.png
.. |image14| image:: /_static/machine_learn_intro/machine_learn_intro_002.png
.. |image15| image:: /_static/machine_learn_intro/1604514-20190723154602995-7203349.png
.. |image16| image:: /_static/machine_learn_intro/1604514-20190723160345687-247486614.png
.. |image17| image:: /_static/machine_learn_intro/1604514-20190723160414479-326695820.png
.. |image18| image:: /_static/machine_learn_intro/1604514-20190723160526827-1443426554.png
.. |image19| image:: /_static/machine_learn_intro/1604514-20190723185141331-2033148846.png
.. |image20| image:: /_static/machine_learn_intro/1604514-20190723185210893-930350575.png
.. |image21| image:: /_static/machine_learn_intro/1604514-20190723185225597-181581141.png
.. |image22| image:: /_static/machine_learn_intro/1604514-20190723192444245-779507566.png
.. |image23| image:: /_static/machine_learn_intro/1604514-20190723192344512-2114153534.png
.. |image24| image:: /_static/machine_learn_intro/WX20200815-105332@2x.webp
.. |image25| image:: /_static/machine_learn_intro/1604514-20190723194829099-1316540633.png
.. |image26| image:: /_static/machine_learn_intro/1604514-20190723194957075-665331862.png
.. |image27| image:: /_static/machine_learn_intro/1604514-20190723195352219-1093274092.png
.. |image28| image:: /_static/machine_learn_intro/1604514-20190723195536489-1782448707.png
.. |image29| image:: /_static/machine_learn_intro/1604514-20190723195446746-1237938856.png
.. |image30| image:: /_static/machine_learn_intro/1604514-20190723195732820-1467697485.png
.. |image31| image:: /_static/machine_learn_intro/1604514-20190723195825416-156593133.png
.. |image32| image:: /_static/machine_learn_intro/1604514-20190723200011579-179073114.png
.. |image33| image:: /_static/machine_learn_intro/1604514-20190723200127258-1186479355.png
.. |image34| image:: /_static/machine_learn_intro/1604514-20190723201014705-862643560.png
.. |image35| image:: /_static/machine_learn_intro/1604514-20190723201042183-1862938839.png
.. |image36| image:: /_static/machine_learn_intro/1604514-20190723201105843-551042109.png
.. |image37| image:: /_static/machine_learn_intro/1604514-20190723201131522-2099230104.png
.. |image38| image:: /_static/machine_learn_intro/1604514-20190723201149915-367595888.png
.. |image39| image:: /_static/machine_learn_intro/1604514-20190723201211320-232540569.png
.. |image40| image:: /_static/machine_learn_intro/1604514-20190723201301100-114574049.png
.. |image41| image:: /_static/machine_learn_intro/1604514-20190723204703671-712066171.png
.. |image42| image:: /_static/machine_learn_intro/1604514-20190724023904244-1480467400.png
.. |image43| image:: /_static/machine_learn_intro/1604514-20190724023927850-1867719665.png
.. |image44| image:: /_static/machine_learn_intro/1604514-20190724023941883-1270505604.png
.. |image45| image:: /_static/machine_learn_intro/1604514-20190724024100725-1011487618.png
.. |image46| image:: /_static/machine_learn_intro/1604514-20190724024248009-720342402.png
.. |image47| image:: /_static/machine_learn_intro/1604514-20190724024330103-1069453808.png
.. |image48| image:: /_static/machine_learn_intro/1604514-20190724024520258-1158443547.png
.. |image49| image:: /_static/machine_learn_intro/1604514-20190724024552576-1677933481.png
.. |image50| image:: /_static/machine_learn_intro/1604514-20190724024653192-1065012874.png
.. |image51| image:: /_static/machine_learn_intro/1604514-20190724024716101-2015641211.png
.. |image52| image:: /_static/machine_learn_intro/1604514-20190724024756027-930393927.png
.. |image53| image:: /_static/machine_learn_intro/1604514-20190724024953766-1503927518.png
.. |image54| image:: /_static/machine_learn_intro/1604514-20190724025038007-251581871.png
.. |image55| image:: /_static/machine_learn_intro/1604514-20190724025203894-438002260.png
.. |image56| image:: /_static/machine_learn_intro/1604514-20190724025246615-1432322847.png
.. |image57| image:: /_static/machine_learn_intro/1604514-20190724025341917-1542319751.png
.. |image58| image:: /_static/machine_learn_intro/1604514-20190724025425009-314991163.png
.. |image59| image:: /_static/machine_learn_intro/1604514-20190724025656232-437238913.png
.. |image60| image:: /_static/machine_learn_intro/1604514-20190724025955972-70376690.png
.. |image61| image:: /_static/machine_learn_intro/1247221-20190907200202607-1738912840.png
.. |image62| image:: /_static/machine_learn_intro/machine_make_blobs.png
.. |image63| image:: /_static/machine_learn_intro/machine_plotKMeans.png

















