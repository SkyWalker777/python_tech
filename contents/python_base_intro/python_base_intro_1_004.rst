文件操作
##################################################################################

文件操作的作用
**********************************************************************************

思考：什么是文件？目录和文件类别。

思考：文件操作包含什么？答：打开、关闭、读、写、复制....

思考：文件操作的的作用是什么？答：读取内容、写入内容、备份内容......

> 总结：文件操作的作用就是==把一些内容(数据)存储存放起来，可以让程序下一次执行的时候直接使用，而不必重新制作一份，省时省力==。

文件的基本操作
**********************************************************************************

文件操作步骤
==================================================================================

::

	1. 打开文件
	2. 读写等操作
	3. 关闭文件

.. tip::

	注意：可以只打开和关闭文件，不进行任何读写操作。

* 打开

在python，使用open函数，可以打开一个已经存在的文件，或者创建一个新文件，语法如下：

``open(name, mode)``

| name：是要打开的目标文件名的字符串(可以包含文件所在的具体路径)。
| mode：设置打开文件的模式(访问模式)：只读、写入、追加等。

* 打开文件模式

::

	| 模式 | 描述                                                         |
	| :--: | ------------------------------------------------------------ |
	|  r   | 以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。 |
	|  rb  | 以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。 |
	|  r+  | 打开一个文件用于读写。文件指针将会放在文件的开头。           |
	| rb+  | 以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。 |
	|  w   | 打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
	|  wb  | 以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
	|  w+  | 打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
	| wb+  | 以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。 |
	|  a   | 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
	|  ab  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。 |
	|  a+  | 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。 |
	| ab+  | 以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。 |

* 快速体验

::

	f = open('test.txt', 'w')

.. note::

	注意：此时的`f`是`open`函数的文件对象。

* 文件对象方法

写方法

.. code-block:: python

	# 语法: 对象对象.write('内容')
	# 1. 打开文件
	f = open('test.txt', 'w')

	# 2.文件写入
	f.write('hello world')

	# 3. 关闭文件
	f.close()

.. tip::

	| `w	`和`a`模式：如果文件不存在则创建该文件；如果文件存在，`w`模式先清空再写入，`a`模式直接末尾追加。
	| `r`模式：如果文件不存在则报错。

读方法

- read()

``语法: 文件对象.read(num)``

num表示要从文件中读取的数据的长度（单位是字节），如果没有传入num，那么就表示读取文件中所有的数据。

- readlines()

readlines可以按照行的方式把整个文件中的内容进行一次性读取，并且返回的是一个列表，其中每一行的数据为一个元素。

.. code-block:: python

	f = open('test.txt')
	content = f.readlines()

	# ['hello world\n', 'abcdefg\n', 'aaa\n', 'bbb\n', 'ccc']
	print(content)

	# 关闭文件
	f.close()

- readline()

readline()一次读取一行内容。

.. code-block:: python

	f = open('test.txt')

	content = f.readline()
	print(f'第一行：{content}')

	content = f.readline()
	print(f'第二行：{content}')

	# 关闭文件
	f.close()

seek()

作用：用来移动文件指针。

语法: 文件对象.seek(偏移量, 起始位置)

::

	起始位置：

	 - 0：文件开头
	 - 1：当前位置
	 - 2：文件结尾

关闭

文件对象.close()

文件备份
**********************************************************************************

需求：用户输入当前目录下任意文件名，程序完成对该文件的备份功能(备份文件名为xx[备份]后缀，例如：test[备份].txt)。

* 步骤

	| 1. 接收用户输入的文件名
	| 2. 规划备份文件名
	| 3. 备份文件写入数据

.. code-block:: python

	old_name = input('请输入您要备份的文件名：')

	# 2.1 提取文件后缀点的下标
	index = old_name.rfind('.')

	print(index)  # 后缀中.的下标

	print(old_name[:index])  # 源文件名（无后缀）

	# 2.2 组织新文件名 旧文件名 + [备份] + 后缀
	new_name = old_name[:index] + '[备份]' + old_name[index:]

	# 打印新文件名（带后缀）
	print(new_name)

	old_f = open(old_name, 'rb')
	new_f = open(new_name, 'wb')

	# 3.2 将源文件数据写入备份文件
	while True:
	    con = old_f.read(1024)
	    if len(con) == 0:
	        break
	    new_f.write(con)

	# 3.3 关闭文件
	old_f.close()
	new_f.close()

如果用户输入`.txt`，这是一个无效文件，程序如何更改才能限制只有有效的文件名才能备份？答：添加条件判断即可。

.. code-block:: python

	old_name = input('请输入您要备份的文件名：')

	index = old_name.rfind('.')

	if index > 0:
	    postfix = old_name[index:]

	new_name = old_name[:index] + '[备份]' + postfix

	old_f = open(old_name, 'rb')
	new_f = open(new_name, 'wb')

	while True:
	    con = old_f.read(1024)
	    if len(con) == 0:
	        break
	    new_f.write(con)

	old_f.close()
	new_f.close()

文件和文件夹的操作
**********************************************************************************

在Python中文件和文件夹的操作要借助os模块里面的相关功能，具体步骤如下：

1. 导入os模块

.. code-block:: python

	import os

2. 使用`os`模块相关功能

.. code-block:: python

	os.函数名()

* 文件重命名

.. code-block:: python

	os.rename(目标文件名, 新文件名)

* 删除文件

.. code-block:: python

	os.remove(目标文件名)

* 创建文件夹

.. code-block:: python

	os.mkdir(文件夹名字)

* 删除文件夹

.. code-block:: python

	os.rmdir(文件夹名字)

* 获取当前目录

.. code-block:: python

	os.getcwd()

* 改变默认目录

.. code-block:: python

	os.chdir(目录)

* 获取目录列表

.. code-block:: python

	os.listdir(目录)

应用案例
**********************************************************************************

需求：批量修改文件名，既可添加指定字符串，又能删除指定字符串。

- 步骤

::

	1. 设置添加删除字符串的的标识
	2. 获取指定目录的所有文件
	3. 将原有文件名添加/删除指定字符串，构造新名字
	4. os.rename()重命名

- 代码

.. code-block:: python

	import os

	# 设置重命名标识：如果为1则添加指定字符，flag取值为2则删除指定字符
	flag = 1

	# 获取指定目录
	dir_name = './'

	# 获取指定目录的文件列表
	file_list = os.listdir(dir_name)
	# print(file_list)


	# 遍历文件列表内的文件
	for name in file_list:

	    # 添加指定字符
	    if flag == 1:
	        new_name = 'Python-' + name
	    # 删除指定字符
	    elif flag == 2:
	        num = len('Python-')
	        new_name = name[num:]

	    # 打印新文件名，测试程序正确性
	    print(new_name)
	    
	    # 重命名
	    os.rename(dir_name+name, dir_name+new_name)

面向对象
##################################################################################

面向对象基础
**********************************************************************************

* 理解面向对象

面向对象是一种抽象化的编程思想，很多编程语言中都有的一种思想。例如：洗衣服

| 思考：几种途径可以完成洗衣服？答： 手洗 和 机洗。
| 手洗：找盆 - 放水 - 加洗衣粉 - 浸泡 - 搓洗 - 拧干水 - 倒水 - 漂洗N次 - 拧干 - 晾晒。
| 机洗：打开洗衣机 - 放衣服 - 加洗衣粉 - 按下开始按钮 - 晾晒。
| 思考：对比两种洗衣服途径，同学们发现了什么？答：机洗更简单
| 思考：机洗，只需要找到一台洗衣机，加入简单操作就可以完成洗衣服的工作，而不需要关心洗衣机内部发生了什么事情。

| 总结：==面向对象就是将编程当成是一个事物，对外界来说，事物是直接使用的，不用去管他内部的情况。而编程就是设置事物能够做什么事。==

* 类和对象

| 思考：洗衣机洗衣服描述过程中，洗衣机其实就是一个事物，即对象，洗衣机对象哪来的呢？
| 答：洗衣机是由工厂工人制作出来。
| 思考：工厂工人怎么制作出的洗衣机？
| 答：工人根据设计师设计的功能图纸制作洗衣机。
| 总结：图纸  → 洗衣机 → 洗衣服。

在面向对象编程过程中，有两个重要组成部分：==类== 和 ==对象==。

==类和对象的关系：用类去创建一个对象。==

1 理解类和对象

1.1 类

类是对一系列具有相同==特征==和==行为==的事物的统称，是一个==抽象的概念==，不是真实存在的事物。

::

	- 特征即是属性
	- 行为即是方法

类比如是制造洗衣机时要用到的图纸，也就是说==类是用来创建对象==。

1.2 对象

对象是类创建出来的真实存在的事物，例如：洗衣机。

2 面向对象实现方法

2.1 定义类

Python2中类分为：经典类 和 新式类

class 类名():
    代码
    ......
注意：类名要满足标识符命名规则，同时遵循==大驼峰命名习惯==。

- 体验

.. code-block:: python

	class Washer():
	    def wash(self):
	        print('我会洗衣服')

- 拓展：经典类

不由任意内置类型派生出的类，称之为经典类

.. code-block:: python

	class 类名:
	    代码
	    ......

2.2 创建对象

对象又名实例。

- 语法

对象名 = 类名()

- 体验

.. code-block:: python

	# 创建对象
	haier1 = Washer()

	# <__main__.Washer object at 0x0000018B7B224240>
	print(haier1)

	# haier对象调用实例方法
	haier1.wash()

注意：创建对象的过程也叫实例化对象。

2.3 self

self指的是调用该函数的对象。

.. code-block:: python

	# 1. 定义类
	class Washer():
	    def wash(self):
	        print('我会洗衣服')
	        # <__main__.Washer object at 0x0000024BA2B34240>
	        print(self)

	# 2. 创建对象
	haier1 = Washer()
	# <__main__.Washer object at 0x0000018B7B224240>
	print(haier1)
	# haier1对象调用实例方法
	haier1.wash()

	haier2 = Washer()
	# <__main__.Washer object at 0x0000022005857EF0>
	print(haier2)

.. tip::

	注意：打印对象和self得到的结果是一致的，都是当前对象的内存中存储地址。

* 添加和获取对象属性

属性即是特征，比如：洗衣机的宽度、高度、重量...

对象属性既可以在类外面添加和获取，也能在类里面添加和获取。

1 类外面添加对象属性

- 语法

.. code-block:: python

	对象名.属性名 = 值

- 体验

.. code-block:: python

	haier1.width = 500
	haier1.height = 800

2 类外面获取对象属性

- 语法

.. code-block:: python

	对象名.属性名

- 体验

.. code-block:: python

	print(f'haier1洗衣机的宽度是{haier1.width}')
	print(f'haier1洗衣机的高度是{haier1.height}')

3 类里面获取对象属性

- 语法

.. code-block:: python

	self.属性名

- 体验

.. code-block:: python

	# 定义类
	class Washer():
	    def print_info(self):
	        # 类里面获取实例属性
	        print(f'haier1洗衣机的宽度是{self.width}')
	        print(f'haier1洗衣机的高度是{self.height}')

	# 创建对象
	haier1 = Washer()

	# 添加实例属性
	haier1.width = 500
	haier1.height = 800

	haier1.print_info()

* 魔法方法

在Python中，`__xx__()`的函数叫做魔法方法，指的是具有特殊功能的函数。

1 `__init__()`

1.1 体验`__init__()`

思考：洗衣机的宽度高度是与生俱来的属性，可不可以在生产过程中就赋予这些属性呢？

答：理应如此。==`__init__()`方法的作用：初始化对象。==

.. code-block:: python

	class Washer():
	    
	    # 定义初始化功能的函数
	    def __init__(self):
	        # 添加实例属性
	        self.width = 500
	        self.height = 800

	    def print_info(self):
	        # 类里面调用实例属性
	        print(f'洗衣机的宽度是{self.width}, 高度是{self.height}')

	haier1 = Washer()
	haier1.print_info()

.. tip::

	| `__init__()`方法，在创建一个对象时默认被调用，不需要手动调用
	| `__init__(self)`中的self参数，不需要开发者传递，python解释器会自动把当前的对象引用传递过去。

1.2 带参数的`__init__()`

思考：一个类可以创建多个对象，如何对不同的对象设置不同的初始化属性呢？

答：传参数。

.. code-block:: python

	class Washer():
	    def __init__(self, width, height):
	        self.width = width
	        self.height = height

	    def print_info(self):
	        print(f'洗衣机的宽度是{self.width}')
	        print(f'洗衣机的高度是{self.height}')

	haier1 = Washer(10, 20)
	haier1.print_info()

	haier2 = Washer(30, 40)
	haier2.print_info()

2  `__str__()`

当使用print输出对象的时候，默认打印对象的内存地址。如果类定义了`__str__`方法，那么就会打印从在这个方法中 return 的数据。

.. code-block:: python

	class Washer():
	    def __init__(self, width, height):
	        self.width = width
	        self.height = height

	    def __str__(self):
	        return '这是海尔洗衣机的说明书'

	haier1 = Washer(10, 20)
	# 这是海尔洗衣机的说明书
	print(haier1)

3  `__del__()`

当删除对象时，python解释器也会默认调用`__del__()`方法。

.. code-block:: python

	class Washer():
	    def __init__(self, width, height):
	        self.width = width
	        self.height = height

	    def __del__(self):
	        print(f'{self}对象已经被删除')


	haier1 = Washer(10, 20)

	# <__main__.Washer object at 0x0000026118223278>对象已经被删除
	del haier1

* 综合应用

1 烤地瓜

需求主线：

	| 1. 被烤的时间和对应的地瓜状态：
	| 	0-3分钟：生的
	| 	3-5分钟：半生不熟
	| 	5-8分钟：熟的
	| 	超过8分钟：烤糊了
	| 2. 添加的调料：
	| 	用户可以按自己的意愿添加调料

.. code-block:: python

	# 定义类
	class SweetPotato():
	    def __init__(self):
	        # 被烤的时间
	        self.cook_time = 0
	        # 地瓜的状态
	        self.cook_static = '生的'
	        # 调料列表
	        self.condiments = []

	    def cook(self, time):
	        """烤地瓜的方法"""
	        self.cook_time += time
	        if 0 <= self.cook_time < 3:
	            self.cook_static = '生的'
	        elif 3 <= self.cook_time < 5:
	            self.cook_static = '半生不熟'
	        elif 5 <= self.cook_time < 8:
	            self.cook_static = '熟了'
	        elif self.cook_time >= 8:
	            self.cook_static = '烤糊了'

	    def add_condiments(self, condiment):
	        """添加调料"""
	        self.condiments.append(condiment)

	    def __str__(self):
	        return f'这个地瓜烤了{self.cook_time}分钟, 状态是{self.cook_static}, 添加的调料有{self.condiments}'

	digua1 = SweetPotato()
	print(digua1)

	digua1.cook(2)
	digua1.add_condiments('酱油')
	print(digua1)

	digua1.cook(2)
	digua1.add_condiments('辣椒面儿')
	print(digua1)

	digua1.cook(2)
	print(digua1)

	digua1.cook(2)
	print(digua1)

2 搬家具

将小于房子剩余面积的家具摆放到房子中, 求涉及两个事物：房子 和 家具，故被案例涉及两个类：房子类 和 家具类。

	| - 房子类
	|   - 实例属性
	|     - 房子地理位置
	|     - 房子占地面积
	|     - 房子剩余面积
	|     - 房子内家具列表
	|   - 实例方法
	|     - 容纳家具
	|   - 显示房屋信息
	|
	| - 家具类
	|   - 家具名称
	|   - 家具占地面积

.. code-block:: python

	class Furniture():
	    def __init__(self, name, area):
	        # 家具名字
	        self.name = name
	        # 家具占地面积
	        self.area = area

	# - 房子类

	class Home():
	    def __init__(self, address, area):
	        # 地理位置
	        self.address = address
	        # 房屋面积
	        self.area = area
	        # 剩余面积
	        self.free_area = area
	        # 家具列表
	        self.furniture = []

	    def __str__(self):
	        return f'房子坐落于{self.address}, 占地面积{self.area}, 剩余面积{self.free_area}, 家具有{self.furniture}'

	    def add_furniture(self, item):
	        """容纳家具"""
	        if self.free_area >= item.area:
	            self.furniture.append(item.name)
	            # 家具搬入后，房屋剩余面积 = 之前剩余面积 - 该家具面积
	            self.free_area -= item.area
	        else:
	            print('家具太大，剩余面积不足，无法容纳')

	# - 创建对象并调用实例属性和方法

	bed = Furniture('双人床', 6)
	jia1 = Home('北京', 1200)
	print(jia1)

	jia1.add_furniture(bed)
	print(jia1)

	sofa = Furniture('沙发', 10)
	jia1.add_furniture(sofa)
	print(jia1)

	ball = Furniture('篮球场', 1500)
	jia1.add_furniture(ball)
	print(jia1)

面向对象-继承
**********************************************************************************

目标
==================================================================================

	| - 继承的概念
	| - 单继承
	| - 多继承
	| - 子类重写父类的同名属性和方法
	| - 子类调用父类的同名属性和方法
	| - 多层继承
	| - super()
	| - 私有属性和私有方法

继承的概念
==================================================================================

生活中的继承，一般指的是子女继承父辈的财产。

- 拓展1：经典类或旧式类

不由任意内置类型派生出的类，称之为经典类。

.. code-block:: python

	class 类名:
	    代码
	    ......

- 拓展2：新式类

.. code-block:: python

	class 类名(object):
	  代码

Python面向对象的继承指的是多个类之间的所属关系，即子类默认继承父类的所有属性和方法，具体如下：

.. code-block:: python

	# 父类A
	class A(object):
	    def __init__(self):
	        self.num = 1

	    def info_print(self):
	        print(self.num)

	# 子类B
	class B(A):
	    pass

	result = B()
	result.info_print()  # 1

.. important::

	在Python中，所有类默认继承object类，object类是顶级类或基类；其他子类叫做派生类。

单继承
==================================================================================

故事主线：一个煎饼果子老师傅，在煎饼果子界摸爬滚打多年，研发了一套精湛的摊煎饼果子的技术。师父要把这套技术传授给他的唯一的最得意的徒弟。

分析：徒弟是不是要继承师父的所有技术？

.. code-block:: python

	# 1. 师父类
	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')
	 
	# 2. 徒弟类
	class Prentice(Master):
	    pass

	# 3. 创建对象daqiu
	daqiu = Prentice()
	# 4. 对象访问实例属性
	print(daqiu.kongfu)
	# 5. 对象调用实例方法
	daqiu.make_cake()

多继承
==================================================================================

故事推进：daqiu是个爱学习的好孩子，想学习更多的煎饼果子技术，于是，在百度搜索到黑马程序员，报班学习煎饼果子技术。

所谓多继承意思就是一个类同时继承了多个父类。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	# 创建学校类
	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class Prentice(School, Master):
	    pass

	daqiu = Prentice()
	print(daqiu.kongfu)
	daqiu.make_cake()

.. tip::

	注意：当一个类有多个父类的时候，默认使用第一个父类的同名属性和方法。

子类重写父类同名方法和属性
==================================================================================

故事：daqiu掌握了师父和培训的技术后，自己潜心钻研出自己的独门配方的一套全新的煎饼果子技术。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	# 独创配方
	class Prentice(School, Master):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	daqiu = Prentice()
	print(daqiu.kongfu)
	daqiu.make_cake()

	print(Prentice.__mro__)

.. tip::

	子类和父类具有同名属性和方法，默认使用子类的同名属性和方法。

子类调用父类的同名方法和属性
==================================================================================

故事：很多顾客都希望也能吃到古法和黑马的技术的煎饼果子。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class Prentice(School, Master):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子配方]'

	    def make_cake(self):
	        # 如果是先调用了父类的属性和方法，父类属性会覆盖子类属性，故在调用属性前，先调用自己子类的初始化
	        self.__init__()
	        print(f'运用{self.kongfu}制作煎饼果子')

	    # 调用父类方法，但是为保证调用到的也是父类的属性，必须在调用方法前调用父类的初始化
	    def make_master_cake(self):
	        Master.__init__(self)
	        Master.make_cake(self)

	    def make_school_cake(self):
	        School.__init__(self)
	        School.make_cake(self)

	daqiu = Prentice()
	daqiu.make_cake()
	daqiu.make_master_cake()
	daqiu.make_school_cake()
	daqiu.make_cake()

多层继承
==================================================================================

故事：N年后，daqiu老了，想要把所有技术传承给自己的徒弟。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class Prentice(School, Master):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子配方]'

	    def make_cake(self):
	        self.__init__()
	        print(f'运用{self.kongfu}制作煎饼果子')

	    def make_master_cake(self):
	        Master.__init__(self)
	        Master.make_cake(self)

	    def make_school_cake(self):
	        School.__init__(self)
	        School.make_cake(self)

	# 徒孙类
	class Tusun(Prentice):
	    pass

	xiaoqiu = Tusun()
	xiaoqiu.make_cake()
	xiaoqiu.make_school_cake()
	xiaoqiu.make_master_cake()

super()调用父类方法
==================================================================================

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(Master):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	        # 方法2.1
	        # super(School, self).__init__()
	        # super(School, self).make_cake()

	        # 方法2.2
	        super().__init__()
	        super().make_cake()

	class Prentice(School):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子技术]'

	    def make_cake(self):
	        self.__init__()
	        print(f'运用{self.kongfu}制作煎饼果子')

	    # 子类调用父类的同名方法和属性：把父类的同名属性和方法再次封装
	    def make_master_cake(self):
	        Master.__init__(self)
	        Master.make_cake(self)

	    def make_school_cake(self):
	        School.__init__(self)
	        School.make_cake(self)

	    # 一次性调用父类的同名属性和方法
	    def make_old_cake(self):
	        # 方法一：代码冗余；父类类名如果变化，这里代码需要频繁修改
	        # Master.__init__(self)
	        # Master.make_cake(self)
	        # School.__init__(self)
	        # School.make_cake(self)

	        # 方法二: super()
	        # 方法2.1 super(当前类名, self).函数()
	        # super(Prentice, self).__init__()
	        # super(Prentice, self).make_cake()

	        # 方法2.2 super().函数()
	        super().__init__()
	        super().make_cake()

	daqiu = Prentice()
	daqiu.make_old_cake()

.. tip::

	注意：使用super() 可以自动查找父类。调用顺序遵循 `__mro__` 类属性的顺序。比较适合单继承使用。

私有权限
==================================================================================

* 定义私有属性和方法

在Python中，可以为实例属性和方法设置私有权限，即设置某个实例属性或实例方法不继承给子类。

故事：daqiu把技术传承给徒弟的同时，不想把自己的钱(2000000个亿)继承给徒弟，这个时候就要为`钱`这个实例属性设置私有权限。

设置私有权限的方法：在属性名和方法名 前面 加上两个下划线 __。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class Prentice(School, Master):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子配方]'
	        # 定义私有属性
	        self.__money = 2000000

	    # 定义私有方法
	    def __info_print(self):
	        print(self.kongfu)
	        print(self.__money)

	    def make_cake(self):
	        self.__init__()
	        print(f'运用{self.kongfu}制作煎饼果子')

	    def make_master_cake(self):
	        Master.__init__(self)
	        Master.make_cake(self)

	    def make_school_cake(self):
	        School.__init__(self)
	        School.make_cake(self)

	# 徒孙类
	class Tusun(Prentice):
	    pass

	daqiu = Prentice()
	# 对象不能访问私有属性和私有方法
	# print(daqiu.__money)
	# daqiu.__info_print()

	xiaoqiu = Tusun()
	# 子类无法继承父类的私有属性和私有方法
	# print(xiaoqiu.__money)  # 无法访问实例属性__money
	# xiaoqiu.__info_print()

获取和修改私有属性值
==================================================================================

在Python中，一般定义函数名`get_xx`用来获取私有属性，定义`set_xx`用来修改私有属性值。

.. code-block:: python

	class Master(object):
	    def __init__(self):
	        self.kongfu = '[古法煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class School(object):
	    def __init__(self):
	        self.kongfu = '[黑马煎饼果子配方]'

	    def make_cake(self):
	        print(f'运用{self.kongfu}制作煎饼果子')

	class Prentice(School, Master):
	    def __init__(self):
	        self.kongfu = '[独创煎饼果子配方]'
	        self.__money = 2000000

	    # 获取私有属性
	    def get_money(self):
	        return self.__money

	    # 修改私有属性
	    def set_money(self):
	        self.__money = 500

	    def __info_print(self):
	        print(self.kongfu)
	        print(self.__money)

	    def make_cake(self):
	        self.__init__()
	        print(f'运用{self.kongfu}制作煎饼果子')

	    def make_master_cake(self):
	        Master.__init__(self)
	        Master.make_cake(self)

	    def make_school_cake(self):
	        School.__init__(self)
	        School.make_cake(self)

	# 徒孙类
	class Tusun(Prentice):
	    pass

	daqiu = Prentice()

	xiaoqiu = Tusun()
	# 调用get_money函数获取私有属性money的值
	print(xiaoqiu.get_money())
	# 调用set_money函数修改私有属性money的值
	xiaoqiu.set_money()
	print(xiaoqiu.get_money())

面向对象-其他
**********************************************************************************

面向对象三大特性
==================================================================================

	| - 封装
	|   - 将属性和方法书写到类的里面的操作即为封装
	|   - 封装可以为属性和方法添加私有权限
	| - 继承
	|   - 子类默认继承父类的所有属性和方法
	|   - 子类可以重写父类属性和方法
	| - 多态
	|   - 传入不同的对象，产生不同的结果

多态
==================================================================================

多态指的是一类事物有多种形态，（一个抽象类有多个子类，因而多态的概念依赖于继承）。

	| - 定义：多态是一种使用对象的方式，子类重写父类方法，调用不同子类对象的相同父类方法，可以产生不同的执行结果
	| - 好处：调用灵活，有了多态，更容易编写出通用的代码，做出通用的编程，以适应需求的不断变化！
	| - 实现步骤：
	|   - 定义父类，并提供公共方法
	|   - 定义子类，并重写父类方法
	|   - 传递子类对象给调用者，可以看到不同子类执行效果不同

.. code-block:: python

	class Dog(object):
	    def work(self):  # 父类提供统一的方法，哪怕是空方法
	        print('指哪打哪...')

	class ArmyDog(Dog):  # 继承Dog类
	    def work(self):  # 子类重写父类同名方法
	        print('追击敌人...')

	class DrugDog(Dog):
	    def work(self):
	        print('追查毒品...')

	class Person(object):
	    def work_with_dog(self, dog):  # 传入不同的对象，执行不同的代码，即不同的work函数
	        dog.work()

	ad = ArmyDog()
	dd = DrugDog()

	daqiu = Person()
	daqiu.work_with_dog(ad)
	daqiu.work_with_dog(dd)

类属性和实例属性
==================================================================================

* 设置和访问类属性

| - 类属性就是 **类对象** 所拥有的属性，它被 **该类的所有实例对象 所共有**。
| - 类属性可以使用 **类对象** 或 **实例对象** 访问。

.. code-block:: python

	class Dog(object):
	    tooth = 10

	wangcai = Dog()
	xiaohei = Dog()

	print(Dog.tooth)  # 10
	print(wangcai.tooth)  # 10
	print(xiaohei.tooth)  # 10

.. tip::

	| 类属性的优点
	| 	**记录的某项数据 始终保持一致时**，则定义类属性。
	| 	**实例属性** 要求 **每个对象** 为其 **单独开辟一份内存空间** 来记录数据，而 **类属性** 为全类所共有 ，**仅占用一份内存**，**更加节省内存空间**。

* 修改类属性

类属性只能通过类对象修改，不能通过实例对象修改，如果通过实例对象修改类属性，表示的是创建了一个实例属性。

.. code-block:: python

	class Dog(object):
	    tooth = 10

	wangcai = Dog()
	xiaohei = Dog()

	# 修改类属性
	Dog.tooth = 12
	print(Dog.tooth)  # 12
	print(wangcai.tooth)  # 12
	print(xiaohei.tooth)  # 12

	# 不能通过对象修改属性，如果这样操作，实则是创建了一个实例属性
	wangcai.tooth = 20
	print(Dog.tooth)  # 12
	print(wangcai.tooth)  # 20
	print(xiaohei.tooth)  # 12

* 实例属性

.. code-block:: python

	class Dog(object):
	    def __init__(self):
	        self.age = 5

	    def info_print(self):
	        print(self.age)

	wangcai = Dog()
	print(wangcai.age)  # 5
	# print(Dog.age)  # 报错：实例属性不能通过类访问
	wangcai.info_print()  # 5

类方法和静态方法
==================================================================================

* 类方法特点

- 需要用装饰器`@classmethod`来标识其为类方法，对于类方法，**第一个参数必须是类对象**，一般以`cls`作为第一个参数。

* 类方法使用场景

| - 当方法中 **需要使用类对象** (如访问私有类属性等)时，定义类方法
| - 类方法一般和类属性配合使用

.. code-block:: python

	class Dog(object):
	    __tooth = 10

	    @classmethod
	    def get_tooth(cls):
	        return cls.__tooth

	wangcai = Dog()
	result = wangcai.get_tooth()
	print(result)  # 10

* 静态方法特点

| - 需要通过装饰器`@staticmethod`来进行修饰，**静态方法既不需要传递类对象也不需要传递实例对象（形参没有self/cls）**。
| - 静态方法 也能够通过 **实例对象** 和 **类对象** 去访问。

静态方法使用场景

| - 当方法中 **既不需要使用实例对象**(如实例对象，实例属性)，**也不需要使用类对象** (如类属性、类方法、创建实例等)时，定义静态方法
| - **取消不需要的参数传递**，有利于 **减少不必要的内存占用和性能消耗**

.. code-block:: python

	class Dog(object):
	    @staticmethod
	    def info_print():
	        print('这是一个狗类，用于创建狗实例....')

	wangcai = Dog()
	# 静态方法既可以使用对象访问又可以使用类访问
	wangcai.info_print()
	Dog.info_print()

模块_包_异常
##################################################################################

异常
**********************************************************************************

* 目标

::

	- 了解异常
	- 捕获异常
	- 异常的else
	- 异常finally
	- 异常的传递
	- 自定义异常

* 了解异常

当检测到一个错误时，解释器就无法继续执行了，反而出现了一些错误的提示，这就是所谓的"异常"。

例如：以`r`方式打开一个不存在的文件。``open('test.txt', 'r')``

* 异常的写法

1 语法

.. code-block:: python

	try:
	    可能发生错误的代码
	except:
	    如果出现异常执行的代码

2 快速体验

需求：尝试以`r`模式打开文件，如果文件不存在，则以`w`方式打开。

.. code-block:: python

	try:
	    f = open('test.txt', 'r')
	except:
	    f = open('test.txt', 'w')

3 捕获指定异常

	| try:
	|     可能发生错误的代码
	| except 异常类型:
	|     如果捕获到该异常类型执行的代码

.. code-block:: python

	try:
	    print(num)
	except NameError:
	    print('有错误')

.. tip::

	| 如果尝试执行的代码的异常类型和要捕获的异常类型不一致，则无法捕获异常。
	| 一般 try 下方只放一行尝试执行的代码。

4 捕获多个指定异常

当捕获多个异常时，可以把要捕获的异常类型的名字，放到 except 后，并使用元组的方式进行书写。

.. code-block:: python

	try:
	    print(1/0)
	except (NameError, ZeroDivisionError):
	    print('有错误')

5 捕获异常描述信息

.. code-block:: python

	try:
	    print(num)
	except (NameError, ZeroDivisionError) as result:
	    print(result)

6 捕获所有异常

Exception 是所有程序异常类的父类。

.. code-block:: python

	try:
	    print(num)
	except Exception as result:
	    print(result)

7 异常的 else

else 表示的是如果没有异常要执行的代码。

.. code-block:: python

	try:
	    print(1)
	except Exception as result:
	    print(result)
	else:
	    print('我是 else，是没有异常的时候执行的代码')

8 异常的 finally

finally 表示的是无论是否异常都要执行的代码，例如关闭文件。

.. code-block:: python

	try:
	    f = open('test.txt', 'r')
	except Exception as result:
	    f = open('test.txt', 'w')
	else:
	    print('没有异常，真开心')
	finally:
	    f.close()

* 异常的传递

需求：

	| 尝试只读方式打开 test.txt 文件，如果文件存在则读取文件内容，文件不存在则提示用户即可。
	| 读取内容要求：尝试循环读取内容，读取过程中如果检测到用户意外终止程序，则 except 捕获异常并提示用户。

.. code-block:: python

	import time
	try:
	    f = open('test.txt')
	    try:
	        while True:
	            content = f.readline()
	            if len(content) == 0:
	                break
	            time.sleep(2)
	            print(content)
	    except:
	        # 如果在读取文件的过程中，产生了异常，那么就会捕获到
	        # 比如 按下了 ctrl+c
	        print('意外终止了读取数据')
	    finally:
	        f.close()
	        print('关闭文件')
	except:
	    print("没有这个文件")

* 自定义异常

在 Python 中，抛出自定义异常的语法为 `raise 异常类对象`。

需求：密码长度不足，则报异常（用户输入密码，如果输入的长度不足 3 位，则报错，即抛出自定义异常，并捕获该异常）。

.. code-block:: python

	# 自定义异常类，继承 Exception
	class ShortInputError(Exception):
	    def __init__(self, length, min_len):
	        self.length = length
	        self.min_len = min_len

	    # 设置抛出异常的描述信息
	    def __str__(self):
	        return f'你输入的长度是{self.length}, 不能少于{self.min_len}个字符'

	def main():
	    try:
	        con = input('请输入密码：')
	        if len(con) < 3:
	            raise ShortInputError(len(con), 3)
	    except Exception as result:
	        print(result)
	    else:
	        print('密码已经输入完成')

	main()

* 总结

- 1. 异常语法

.. code-block:: python

	try:
	  	可能发生异常的代码
	except:
	  	如果出现异常执行的代码
	else:
	  	没有异常执行的代码
	finally:
	  	无论是否异常都要执行的代码

- 2. 捕获异常

.. code-block:: python

	except 异常类型:
	  	代码
	except 异常类型 as xx:
		代码

- 3. 自定义异常

.. code-block:: python

	# 1. 自定义异常类
	class 异常类类名(Exception):
	  	代码
	    
	    # 设置抛出异常的描述信息
	    def __str__(self):
	      return ...

	# 2. 抛出异常
	raise 异常类名()

	# 捕获异常
	except Exception...

模块和包
**********************************************************************************

目标
==================================================================================

	| 了解模块
	| 导入模块
	| 制作模块
	| __all__
	| 包的使用方法

模块
==================================================================================

Python 模块 Module，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和 Python 语句。

模块能定义函数，类和变量，模块里也能包含可执行的代码。

* 导入模块

1 导入模块的方式

.. code-block:: python

	import 模块名
	from 模块名 import 功能名
	from 模块名 import *
	import 模块名 as 别名
	from 模块名 import 功能名 as 别名

2 导入方式详解

2.1 import

- 语法

	| # 1. 导入模块
	| import 模块名
	| import 模块名1, 模块名2...
	| 
	| # 2. 调用功能
	| 模块名.功能名()

- 体验

.. code-block:: python

	import math
	print(math.sqrt(9))  # 3.0

2.2 from..import..

- 语法

	| from 模块名 import 功能1, 功能2, 功能3...

- 体验

.. code-block:: python

	from math import sqrt
	print(sqrt(9))

2.3 from .. import *

- 语法

	| from 模块名 import *

- 体验

.. code-block:: python

	from math import *
	print(sqrt(9))

2.4 as 定义别名

- 语法

	| # 模块定义别名
	| import 模块名 as 别名
	| 
	| # 功能定义别名
	| from 模块名 import 功能 as 别名

- 体验

.. code-block:: python

	# 模块别名
	import time as tt

	tt.sleep(2)
	print('hello')

	# 功能别名
	from time import sleep as sl
	sl(2)
	print('hello')

*  制作模块

在 Python 中，每个 Python 文件都可以作为一个模块，模块的名字就是文件的名字。**也就是说自定义模块名必须要符合标识符命名规则。**

1 定义模块

新建一个 Python 文件，命名为`my_module1.py`，并定义`testA`函数。

.. code-block:: python

	def testA(a, b):
	    print(a + b)

2 测试模块

在实际开中，当一个开发人员编写完一个模块后，为了让模块能够在项目中达到想要的效果，这个开发人员会自行在 py 文件中添加一些测试信息.，例如，在`my_module1.py`文件中添加测试代码。

.. code-block:: python

	def testA(a, b):
	    print(a + b)

	testA(1, 1)

此时，无论是当前文件，还是其他已经导入了该模块的文件，在运行的时候都会自动执行`testA`函数的调用。

解决办法如下：

.. code-block:: python

	def testA(a, b):
	    print(a + b)

	# 只在当前文件中调用该函数，其他导入的文件内不符合该条件，则不执行testA函数调用
	if __name__ == '__main__':
	    testA(1, 1)

3 调用模块

.. code-block:: python

	import my_module1
	my_module1.testA(1, 1)

4 注意事项

如果使用 `from .. import ..` 或 `from .. import *` 导入多个模块的时候，且模块内有同名功能。当调用这个同名功能的时候，调用到的是后面导入的模块的功能。

- 体验

.. code-block:: python

	# 模块 1 代码
	def my_test(a, b):
	    print(a + b)

	# 模块 2 代码
	def my_test(a, b):
	    print(a - b)
	   
	# 导入模块和调用功能代码
	from my_module1 import my_test
	from my_module2 import my_test

	# my_test函数是模块2中的函数
	my_test(1, 1)

*  模块定位顺序

当导入一个模块，Python 解析器对模块位置的搜索顺序是：

::

	1. 当前目录
	2. 如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
	3. 如果都找不到，Python 会察看默认路径。UNIX 下，默认路径一般为 /usr/local/lib/python/

模块搜索路径存储在 system 模块的 sys.path 变量中。变量里包含当前目录，PYTHONPATH 和由安装过程决定的默认目录。

.. tip::

	| - 注意
	|   - 自己的文件名不要和已有模块名重复，否则导致模块功能无法使用
	|   - `使用 from 模块名 import 功能`的时候，如果功能名字重复，调用到的是最后定义或导入的功能。

* `__all__`

如果一个模块文件中有 `__all__` 变量，当使用 `from xxx import *` 导入时，只能导入这个列表中的元素。

- my_module1 模块代码

.. code-block:: python

	__all__ = ['testA']

	def testA():
	    print('testA')

	def testB():
	    print('testB')

- 导入模块的文件代码

.. code-block:: python

	from my_module1 import *
	testA()
	testB()  //报错

包
==================================================================================

包将有联系的模块组织在一起，即放到同一个文件夹下，并且在这个文件夹创建一个名字为 `__init__.py` 文件，那么这个文件夹就称之为包。

* 制作包

[New] — [Python Package] — 输入包名 — [OK] — 新建功能模块(有联系的模块)。

注意：新建包后，包内部会自动创建 `__init__.py` 文件，这个文件控制着包的导入行为。

1 快速体验

::

	1. 新建包`mypackage`
	2. 新建包内模块：`my_module1` 和 `my_module2`
	3. 模块内代码如下

.. code-block:: python

	# my_module1
	print(1)

	def info_print1():
	    print('my_module1')

	# my_module2
	print(2)

	def info_print2():
	    print('my_module2')

2 导入包

2.1 方法一

.. code-block:: python

	import 包名.模块名

	包名.模块名.目标

	代码示例: 
	import my_package.my_module1
	my_package.my_module1.info_print1()

2.2 方法二

注意：必须在`__init__.py`文件中添加`__all__ = []`，控制允许导入的模块列表。

.. code-block:: python

	from 包名 import *
	模块名.目标

	from my_package import *

	my_module1.info_print1()

总结
==================================================================================

- 导入模块方法

.. code-block:: python

	import 模块名

	from 模块名 import 目标

	from 模块名 import *

- 导入包

.. code-block:: python

	import 包名.模块名

	from 包名 import *

- `__all__ = []` ：允许导入的模块或功能列表
















































































































