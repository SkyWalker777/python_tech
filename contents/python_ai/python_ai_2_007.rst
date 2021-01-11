正则表达式
##################################################################################

property 属性
**********************************************************************************

* property 属性的介绍

property属性就是负责把一个方法当做属性进行使用，这样做可以简化代码使用。

定义property属性有两种方式

::

	装饰器方式
	类属性方式

* 装饰器方式

.. code-block:: python
	:linenos:

	class Person(object):

	    def __init__(self):
	        self.__age = 0

	    # 装饰器方式的property, 把age方法当做属性使用, 表示当获取属性时会执行下面修饰的方法
	    @property
	    def age(self):
	        return self.__age

	    # 把age方法当做属性使用, 表示当设置属性时会执行下面修饰的方法
	    @age.setter
	    def age(self, new_age):
	        if new_age >= 150:
	            print("成精了")
	        else:
	            self.__age = new_age

	# 创建person
	p = Person()
	print(p.age)
	p.age = 100
	print(p.age)
	p.age = 1000

	运行结果:

	0
	100
	成精了

代码说明:

::

	@property 表示把方法当做属性使用, 表示当获取属性时会执行下面修饰的方法
	@方法名.setter 表示把方法当做属性使用,表示当设置属性时会执行下面修饰的方法
	装饰器方式的property属性修饰的方法名一定要一样。

* 类属性方式

.. code-block:: python
	:linenos:

	class Person(object):

	    def __init__(self):
	        self.__age = 0

	    def get_age(self):
	        """当获取age属性的时候会执行该方法"""
	        return self.__age

	    def set_age(self, new_age):
	        """当设置age属性的时候会执行该方法"""
	        if new_age >= 150:
	            print("成精了")
	        else:
	            self.__age = new_age

	    # 类属性方式的property属性
	    age = property(get_age, set_age)

	# 创建person
	p = Person()
	print(p.age)
	p.age = 100
	print(p.age)
	p.age = 1000

	运行结果:

	0
	100
	成精了

property的参数说明:

::

	第一个参数是获取属性时要执行的方法
	第二个参数是设置属性时要执行的方法

* 小结

::

	定义property属性有两种方式:
		装饰器方式
		类属性方式
	装饰器方式:
		@property 修饰获取值的方法
		@方法名.setter 修饰设置值的方法
	类属性方式:
		类属性 = property(获取值方法, 设置值方法)

with 语句和上下文管理器
**********************************************************************************

* with 语句的使用

基础班向文件中写入数据的示例代码:

.. code-block:: python
	:linenos:

	# 1、以写的方式打开文件
	f = open("1.txt", "w")
	# 2、写入文件内容
	f.write("hello world")
	# 3、关闭文件
	f.close()

代码说明: 文件使用完后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的

这种写法可能出现一定的安全隐患，错误代码如下:

.. code-block:: python
	:linenos:

	# 1、以读的方式打开文件
	f = open("1.txt", "r")
	# 2、读取文件内容
	f.write("hello world")
	# 3、关闭文件
	f.close()

	运行结果:

	Traceback (most recent call last):
	  File "/home/python/Desktop/test/xxf.py", line 4, in <module>
	    f.write("hello world")
	io.UnsupportedOperation: not writable

代码说明:

::

	由于文件读写时都有可能产生IOError，一旦出错，后面的f.close()就不会调用。
	为了保证无论是否出错都能正确地关闭文件，我们可以使用try ... finally来解决

安全写法, 代码如下:

.. code-block:: python
	:linenos:

	try:
	    # 1、以读的方式打开文件
	    f = open("1.txt", "r")
	    # 2、读取文件内容
	    f.write("xxxxx")

	except IOError as e:
	    print("文件操作出错", e)

	finally:
	    # 3、关闭文件
	    f.close()

	运行结果: 文件操作出错 not writable

这种方法虽然代码运行良好,但是缺点就是代码过于冗长,并且需要添加try-except-finally语句,不是很方便,也容易忘记.

在这种情况下,Python提供了 with 语句的这种写法，既简单又安全，并且 with 语句执行完成以后自动调用关闭文件操作，即使出现异常也会自动调用关闭文件操作。

with 语句的示例代码:

.. code-block:: python
	:linenos:

	# 1、以写的方式打开文件
	with open("1.txt", "w") as f:
	    # 2、读取文件内容
	    f.write("hello world")

* 上下文管理器

一个类只要实现了__enter__()和__exit__()这个两个方法，通过该类创建的对象我们就称之为上下文管理器。

上下文管理器可以使用 with 语句，with语句之所以这么强大，背后是由上下文管理器做支撑的，也就是说刚才使用 open 函数创建的文件对象就是就是一个上下文管理器对象。

自定义上下文管理器类,模拟文件操作:

定义一个File类，实现 __enter__() 和 __exit__()方法，然后使用 with 语句来完成操作文件， 示例代码:

.. code-block:: python
	:linenos:

	class File(object):

	    # 初始化方法
	    def __init__(self, file_name, file_model):
	        # 定义变量保存文件名和打开模式
	        self.file_name = file_name
	        self.file_model = file_model

	    # 上文方法
	    def __enter__(self):
	        print("进入上文方法")
	        # 返回文件资源
	        self.file = open(self.file_name,self.file_model)
	        return self.file

	    # 下文方法
	    def __exit__(self, exc_type, exc_val, exc_tb):
	        print("进入下文方法")
	        self.file.close()

	if __name__ == '__main__':

	    # 使用with管理文件
	    with File("1.txt", "r") as file:
	        file_data = file.read()
	        print(file_data)

	运行结果:

	进入上文方法
	hello world
	进入下文方法

代码说明:

::

	__enter__表示上文方法，需要返回一个操作文件对象
	__exit__表示下文方法，with语句执行完成会自动执行，即使出现异常也会执行该方法。

* 上下文管理器的另外一种实现方式

假如想要让一个函数成为上下文管理器，Python 还提供了一个 @contextmanager 的装饰器，更进一步简化了上下文管理器的实现方式。通过 yield 将函数分割成两部分，yield 上面的语句在 __enter__ 方法中执行，yield 下面的语句在 __exit__ 方法中执行，紧跟在 yield 后面的参数是函数的返回值。

.. code-block:: python
	:linenos:

	# 导入装饰器
	from contextlib import contextmanager

	# 装饰器装饰函数，让其称为一个上下文管理器对象
	@contextmanager
	def my_open(path, mode):
	    try:
	        # 打开文件
	        file = open(file_name, file_mode)
	        # yield之前的代码好比是上文方法
	        yield file
	    except Exception as e:
	        print(e)
	    finally:
	        print("over")
	        # yield下面的代码好比是下文方法
	        file.close()

	# 使用with语句
	with my_open('out.txt', 'w') as f:
	    f.write("hello , the simplest context manager")

* 小结

::

	Python 提供了 with 语句用于简化资源释放的操作，使用 with 语句操作建立在上下文管理器(实现__enter__和__exit__)的基础上
	Python 还提供了一个 @contextmanager 装饰器，更进一步简化上下管理器的实现，让一个函数可以成为上下文管理器，结合 with 语句来使用

生成器的创建方式
**********************************************************************************

* 生成器的介绍

根据程序员制定的规则循环生成数据，当条件不成立时则生成数据结束。数据不是一次性全部生成处理，而是使用一个，再生成一个，可以节约大量的内存。

* 创建生成器的方式

::

	生成器推导式
	yield 关键字

生成器推导式: 与列表推导式类似，只不过生成器推导式使用小括号

.. code-block:: python
	:linenos:

	# 创建生成器
	my_generator = (i * 2 for i in range(5))
	print(my_generator)

	# next获取生成器下一个值
	# value = next(my_generator)
	# print(value)

	# 遍历生成器
	for value in my_generator:
	    print(value)

	运行结果:

	<generator object <genexpr> at 0x101367048>
	0
	2
	4
	6
	8

代码说明:

::

	next 函数获取生成器中的下一个值
	for 循环遍历生成器中的每一个值

yield 关键字: 只要在def函数里面看到有 yield 关键字那么就是生成器

.. code-block:: python
	:linenos:

	def mygenerater(n):
	    for i in range(n):
	        print('开始生成...')
	        yield i
	        print('完成一次...')

	if __name__ == '__main__':

	    g = mygenerater(2)
	    # 获取生成器中下一个值
	    # result = next(g)
	    # print(result)

	    # while True:
	    #     try:
	    #         result = next(g)
	    #         print(result)
	    #     except StopIteration as e:
	    #         break

	    # # for遍历生成器, for 循环内部自动处理了停止迭代异常，使用起来更加方便
	    for i in g:
	        print(i)

	运行结果:

	开始生成...
	0
	完成一次...
	开始生成...
	1
	完成一次...

代码说明:

::

	代码执行到 yield 会暂停，然后把结果返回出去，下次启动生成器会在暂停的位置继续往下执行
	生成器如果把数据生成完成，再次获取生成器中的下一个数据会抛出一个StopIteration 异常，表示停止迭代异常
	while 循环内部没有处理异常操作，需要手动添加处理异常操作
	for 循环内部自动处理了停止迭代异常，使用起来更加方便，推荐大家使用。

* 生成器的使用场景

数学中有个著名的斐波拉契数列（Fibonacci），数列中第一个数为0，第二个数为1，其后的每一个数都可由前两个数相加得到：

0, 1, 1, 2, 3, 5, 8, 13, 21, 34, ...

现在我们使用生成器来实现这个斐波那契数列，每次取值都通过算法来生成下一个数据, 生成器每次调用只生成一个数据，可以节省大量的内存。

.. code-block:: python
	:linenos:

	def fibonacci(num):
	    a = 0
	    b = 1

	    # 记录生成fibonacci数字的下标
	    current_index = 0

	    while current_index < num:
	        result = a
	        a, b = b, a + b
	        current_index += 1
	        # 代码执行到yield会暂停，然后把结果返回出去，下次启动生成器会在暂停的位置继续往下执行
	        yield result

	fib = fibonacci(5)
	# 遍历生成的数据
	for value in fib:
	    print(value)

	运行结果:

	0
	1
	1
	2
	3

* 小结

::

	生成器是根据算法生成数据的一种机制，每次调用生成器只生成一个值，可以节省大量内存。
	生成器的创建有两种方式:
	生成器推导式
	yield 关键字

深拷贝和浅拷贝
**********************************************************************************

* 浅拷贝

copy函数是浅拷贝，只对可变类型的第一层对象进行拷贝，对拷贝的对象开辟新的内存空间进行存储，不会拷贝对象内部的子对象。

不可变类型的浅拷贝示例代码:

import copy  # 使用浅拷贝需要导入copy模块

# 不可变类型有: 数字、字符串、元组

.. code-block:: python
	:linenos:

	a1 = 123123
	b1 = copy.copy(a1)  # 使用copy模块里的copy()函数就是浅拷贝了
	# 查看内存地址
	print(id(a1))
	print(id(b1))

	print("-" * 10)
	a2 = "abc"
	b2 = copy.copy(a2)
	# 查看内存地址
	print(id(a2))
	print(id(b2))

	print("-" * 10)
	a3 = (1, 2, ["hello", "world"])
	b3 = copy.copy(a3)
	# 查看内存地址
	print(id(a3))
	print(id(b3))
	运行结果:

	140459558944048
	140459558944048
	----------
	140459558648776
	140459558648776
	----------
	140459558073328
	140459558073328

不可变类型的浅拷贝说明: 通过上面的执行结果可以得知，不可变类型进行浅拷贝不会给拷贝的对象开辟新的内存空间，而只是拷贝了这个对象的引用。

可变类型的浅拷贝示例代码:

.. code-block:: python
	:linenos:

	import copy # 使用浅拷贝需要导入copy模块

	# 可变类型有: 列表、字典、集合

	a1 = [1, 2]
	b1 = copy.copy(a1) # 使用copy模块里的copy()函数就是浅拷贝了
	# 查看内存地址
	print(id(a1))
	print(id(b1))
	print("-" * 10)
	a2 = {"name": "张三", "age": 20}
	b2 = copy.copy(a2)
	# 查看内存地址
	print(id(a2))
	print(id(b2))
	print("-" * 10)
	a3 = {1, 2, "王五"}
	b3 = copy.copy(a3)
	# 查看内存地址
	print(id(a3))
	print(id(b3))

	print("-" * 10)
	a4 = [1, 2, [4, 5]]
	# 注意：浅拷贝只会拷贝父对象，不会对子对象进行拷贝
	b4 = copy.copy(a4) # 使用copy模块里的copy()函数就是浅拷贝了
	# 查看内存地址
	print(id(a4))
	print(id(b4))
	print("-" * 10)
	# 查看内存地址
	print(id(a4[2]))
	print(id(b4[2]))

	# 修改数据
	a4[2][0] = 6

	# 子对象的数据会受影响
	print(a4)
	print(b4)
	运行结果:

	139882899585608
	139882899585800
	----------
	139882919626432
	139882919626504
	----------
	139882919321672
	139882899616264
	----------
	139882899587016
	139882899586952
	----------
	139882899693640
	139882899693640
	[1, 2, [6, 5]]
	[1, 2, [6, 5]]

可变类型的浅拷贝说明: 通过上面的执行结果可以得知，可变类型进行浅拷贝只对可变类型的第一层对象进行拷贝，对拷贝的对象会开辟新的内存空间进行存储，子对象不进行拷贝。

* 深拷贝

deepcopy 函数是深拷贝, 只要发现对象有可变类型就会对该对象到最后一个可变类型的每一层对象就行拷贝, 对每一层拷贝的对象都会开辟新的内存空间进行存储。

不可变类型的深拷贝示例代码:

import copy  # 使用深拷贝需要导入copy模块

# 不可变类型有: 数字、字符串、元组

.. code-block:: python
	:linenos:

	a1 = 1
	b1 = copy.deepcopy(a1)  # 使用copy模块里的deepcopy()函数就是深拷贝了
	# 查看内存地址
	print(id(a1))
	print(id(b1))
	print("-" * 10)
	a2 = "张三"
	b2 = copy.deepcopy(a2)
	# 查看内存地址
	print(id(a2))
	print(id(b2))
	print("-" * 10)
	a3 = (1, 2)
	b3 = copy.deepcopy(a3)
	# 查看内存地址
	print(id(a3))
	print(id(b3))
	print("-" * 10)

	# 注意: 元组里面要是有可变类型对象，发现对象有可变类型就会该对象到最后一个可变类型的每一层对象进行拷贝
	a4 = (1, ["李四"])
	b4 = copy.deepcopy(a4)
	# 查看内存地址
	print(id(a4))
	print(id(b4))
	# 元组里面的可变类型子对象也会进行拷贝
	print(id(a4[1]))
	print(id(b4[1]))
	运行结果:

	9289120
	9289120
	----------
	140115621848320
	140115621848320
	----------
	140115621859592
	140115621859592
	----------
	140115602480584
	140115621834568
	140115602328136
	140115602436168

不可变类型的深拷贝说明: 通过上面的执行结果可以得知,不可变类型进行深拷贝如果子对象没有可变类型则不会进行拷贝，而只是拷贝了这个对象的引用，否则会对该对象到最后一个可变类型的每一层对象就行拷贝, 对每一层拷贝的对象都会开辟新的内存空间进行存储

可变类型的深拷贝示例代码:
 
import copy  # 使用深拷贝需要导入copy模块

# 可变类型有: 列表、字典、集合

.. code-block:: python
	:linenos:

	a1 = [1, 2]
	b1 = copy.deepcopy(a1)  # 使用copy模块里的deepcopy()函数就是深拷贝了
	# 查看内存地址
	print(id(a1))
	print(id(b1))
	print("-" * 10)
	a2 = {"name": "张三"}
	b2 = copy.deepcopy(a2)
	# 查看内存地址
	print(id(a2))
	print(id(b2))
	print("-" * 10)
	a3 = {1, 2}
	b3 = copy.deepcopy(a3)
	# 查看内存地址
	print(id(a3))
	print(id(b3))
	print("-" * 10)

	a4 = [1, 2, ["李四", "王五"]]
	b4 = copy.deepcopy(a4)  # 使用copy模块里的deepcopy()函数就是深拷贝了
	# 查看内存地址
	print(id(a4))
	print(id(b4))

	# 查看内存地址
	print(id(a4[2]))
	print(id(b4[2]))
	a4[2][0] = "王五"
	# 因为列表的内存地址不同，所以数据不会收到影响
	print(a4)
	print(b4)
	运行结果:

	140348291721736
	140348291721928
	----------
	140348311762624
	140348311221592
	----------
	140348311457864
	140348291752456
	----------
	140348291723080
	140348291723144
	140348291723208
	140348291723016
	[1, 2, ['王五', '王五']]
	[1, 2, ['李四', '王五']]

可变类型的深拷贝说明: 通过上面的执行结果可以得知, 可变类型进行深拷贝会对该对象到最后一个可变类型的每一层对象就行拷贝, 对每一层拷贝的对象都会开辟新的内存空间进行存储。

* 浅拷贝和深拷贝的区别

::

	浅拷贝最多拷贝对象的一层
	深拷贝可能拷贝对象的多层

* 总结

::

	浅拷贝使用copy.copy函数
	深拷贝使用copy.deepcopy函数
	不管是给对象进行深拷贝还是浅拷贝，只要拷贝成功就会开辟新的内存空间存储拷贝的对象。
	浅拷贝和深拷贝的区别是:
	浅拷贝最多拷贝对象的一层，深拷贝可能拷贝对象的多层。

正则表达式的概述
**********************************************************************************

* 正则表达式的介绍

在实际开发过程中经常会有查找符合某些复杂规则的字符串的需要，比如:邮箱、图片地址、手机号码等，这时候想匹配或者查找符合某些规则的字符串就可以使用正则表达式了。

* 正则表达式概念

正则表达式就是记录文本规则的代码

* 正则表达式的样子

0\d{2}-\d{8} 这个就是一个正则表达式，表达的意思是匹配的是座机号码

* 正则表达式的特点

| 正则表达式的语法很令人头疼，可读性差
| 正则表达式通用行很强，能够适用于很多编程语言

* 小结

正则表达式是匹配符合某些规则的字符串数据

re 模块介绍
**********************************************************************************

* re 模块的介绍

在Python中需要通过正则表达式对字符串进行匹配的时候，可以使用一个 re 模块

.. code-block:: python
	:linenos:

	# 导入re模块
	import re

	# 使用match方法进行匹配操作
	result = re.match(正则表达式,要匹配的字符串)

	# 如果上一步匹配到数据的话，可以使用group方法来提取数据
	result.group()

* re 模块的使用

.. code-block:: python
	:linenos:

	import re

	# 使用match方法进行匹配操作
	result = re.match("itcast","itcast.cn")
	# 获取匹配结果
	info = result.group()
	print(info)
	运行结果:

	itcast

* 小结

re.match() 根据正则表达式从头开始匹配字符串数据

匹配单个字符
**********************************************************************************

* 匹配单个字符

在上一小节中，了解到通过re模块能够完成使用正则表达式来匹配字符串

本小节，将要讲解正则表达式的单字符匹配

::

	代码	功能
	.	匹配任意1个字符（除了\n）
	[ ]	匹配[ ]中列举的字符
	\d	匹配数字，即0-9
	\D	匹配非数字，即不是数字
	\s	匹配空白，即 空格，tab键
	\S	匹配非空白
	\w	匹配非特殊字符，即a-z、A-Z、0-9、_、汉字
	\W	匹配特殊字符，即非字母、非数字、非汉字

示例1: 

.. code-block:: python
	:linenos:

	import re

	ret = re.match(".","M")
	print(ret.group())

	ret = re.match("t.o","too")
	print(ret.group())

	ret = re.match("t.o","two")
	print(ret.group())

	运行结果：
	M
	too
	two

示例2:

.. code-block:: python
	:linenos:

	import re

	# 如果hello的首字符小写，那么正则表达式需要小写的h
	ret = re.match("h","hello Python") 
	print(ret.group())

	# 如果hello的首字符大写，那么正则表达式需要大写的H
	ret = re.match("H","Hello Python") 
	print(ret.group())

	# 大小写h都可以的情况
	ret = re.match("[hH]","hello Python")
	print(ret.group())
	ret = re.match("[hH]","Hello Python")
	print(ret.group())
	ret = re.match("[hH]ello Python","Hello Python")
	print(ret.group())

	# 匹配0到9第一种写法
	ret = re.match("[0123456789]Hello Python","7Hello Python")
	print(ret.group())

	# 匹配0到9第二种写法
	ret = re.match("[0-9]Hello Python","7Hello Python")
	print(ret.group())

	ret = re.match("[0-35-9]Hello Python","7Hello Python")
	print(ret.group())

	# 下面这个正则不能够匹配到数字4，因此ret为None
	ret = re.match("[0-35-9]Hello Python","4Hello Python")
	# print(ret.group())

	运行结果：

	h
	H
	h
	H
	Hello Python
	7Hello Python
	7Hello Python
	7Hello Python

示例3：\d

.. code-block:: python
	:linenos:

	import re

	# 普通的匹配方式
	ret = re.match("嫦娥1号","嫦娥1号发射成功") 
	print(ret.group())

	ret = re.match("嫦娥2号","嫦娥2号发射成功") 
	print(ret.group())

	ret = re.match("嫦娥3号","嫦娥3号发射成功") 
	print(ret.group())

	# 使用\d进行匹配
	ret = re.match("嫦娥\d号","嫦娥1号发射成功") 
	print(ret.group())

	ret = re.match("嫦娥\d号","嫦娥2号发射成功") 
	print(ret.group())

	ret = re.match("嫦娥\d号","嫦娥3号发射成功") 
	print(ret.group())
	运行结果：

	嫦娥1号
	嫦娥2号
	嫦娥3号
	嫦娥1号
	嫦娥2号
	嫦娥3号

示例4：\D

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("\D", "f")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果:

	f

示例5：\s

.. code-block:: python
	:linenos:

	import re

	# 空格属于空白字符
	match_obj = re.match("hello\sworld", "hello world")
	if match_obj:
	    result = match_obj.group()
	    print(result)
	else:
	    print("匹配失败")

	# \t 属于空白字符
	match_obj = re.match("hello\sworld", "hello\tworld")
	if match_obj:
	    result = match_obj.group()
	    print(result)
	else:
	    print("匹配失败")
	运行结果:

	hello world
	hello world

示例6：\S

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("hello\Sworld", "hello&world")
	if match_obj:
	result = match_obj.group()
	print(result)
	else:
	print("匹配失败")

	match_obj = re.match("hello\Sworld", "hello$world")
	if match_obj:
	result = match_obj.group()
	print(result)
	else:
	print("匹配失败")
	运行结果:

	hello&world  
	hello$world

示例7：\w

.. code-block:: python
	:linenos:

	import re

	# 匹配非特殊字符中的一位
	match_obj = re.match("\w", "A")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	执行结果:

	A

示例8：\W

.. code-block:: python
	:linenos:

	# 匹配特殊字符中的一位
	match_obj = re.match("\W", "&")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	执行结果:

	&

* 小结

::

	. 表示匹配任意1个字符（除了\n）
	[ ] 表示匹配[ ]中列举的1个字符
	\d 表示匹配一个数字，即0-9
	\D 表示匹配一个非数字，即不是数字
	\s 表示匹配一个空白字符，即 空格，tab键
	\S | 匹配一个非空白字符
	\w | 匹配一个非特殊字符，即a-z、A-Z、0-9、_、汉字
	\W | 匹配一个特殊字符，即非字母、非数字、非汉字

匹配多个字符
**********************************************************************************

* 匹配多个字符

::

	代码	功能
	*	匹配前一个字符出现0次或者无限次，即可有可无
	+	匹配前一个字符出现1次或者无限次，即至少有1次
	?	匹配前一个字符出现1次或者0次，即要么有1次，要么没有
	{m}	匹配前一个字符出现m次
	{m,n}	匹配前一个字符出现从m到n次

示例1：*

需求：匹配出一个字符串第一个字母为大小字符，后面都是小写字母并且这些小写字母可 有可无

.. code-block:: python
	:linenos:

	import re

	ret = re.match("[A-Z][a-z]*","M")
	print(ret.group())

	ret = re.match("[A-Z][a-z]*","MnnM")
	print(ret.group())

	ret = re.match("[A-Z][a-z]*","Aabcdef")
	print(ret.group())
	运行结果：

	M
	Mnn
	Aabcdef

示例2：+

需求：匹配一个字符串，第一个字符是t,最后一个字符串是o,中间至少有一个字符

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("t.+o", "two")
	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")

	运行结果：

	two

示例3：?

需求：匹配出这样的数据，但是https 这个s可能有，也可能是http 这个s没有

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("https?", "http")
	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果：

	https

示例4：{m}、{m,n}

需求：匹配出，8到20位的密码，可以是大小写英文字母、数字、下划线

.. code-block:: python
	:linenos:

	import re

	ret = re.match("[a-zA-Z0-9_]{6}","12a3g45678")
	print(ret.group())

	ret = re.match("[a-zA-Z0-9_]{8,20}","1ad12f23s34455ff66")
	print(ret.group())
	运行结果：

	12a3g4
	1ad12f23s34455ff66

* 小结

::

	*表示匹配前一个字符出现0次或者无限次，即可有可无
	+表示匹配前一个字符出现1次或者无限次，即至少有1次
	?表示匹配前一个字符出现1次或者0次，即要么有1次，要么没有
	{m}表示匹配前一个字符出现m次
	{m,n}表示匹配前一个字符出现从m到n次

匹配开头和结尾
**********************************************************************************

* 匹配开头和结尾

::

	代码	功能
	^	匹配字符串开头
	$	匹配字符串结尾

示例1：^

需求：匹配以数字开头的数据

.. code-block:: python
	:linenos:

	import re

	# 匹配以数字开头的数据
	match_obj = re.match("^\d.*", "3hello")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果:

	3hello

示例2：$

需求: 匹配以数字结尾的数据

.. code-block:: python
	:linenos:

	import re
	# 匹配以数字结尾的数据
	match_obj = re.match(".*\d$", "hello5")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果：

	hello5

示例3：^ 和 $

需求: 匹配以数字开头中间内容不管以数字结尾

.. code-block:: python
	:linenos:

	match_obj = re.match("^\d.*\d$", "4hello4")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果:

	4hello4

* 除了指定字符以外都匹配

[^指定字符]: 表示除了指定字符都匹配

需求: 第一个字符除了aeiou的字符都匹配

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("[^aeiou]", "h")
	if match_obj:
	    # 获取匹配结果
	    print(match_obj.group())
	else:
	    print("匹配失败")
	执行结果

	h

* 小结

::

	^ 表示匹配字符串开头
	$ 表示匹配字符串结尾

匹配分组
**********************************************************************************

* 匹配分组相关正则表达式

::

	代码	功能
	|	匹配左右任意一个表达式
	(ab)	将括号中字符作为一个分组
	\num	引用分组num匹配到的字符串
	(?P<name>)	分组起别名
	(?P=name)	引用别名为name分组匹配到的字符串

示例1：|

需求：在列表中["apple", "banana", "orange", "pear"]，匹配apple和pear

.. code-block:: python
	:linenos:

	import re

	# 水果列表
	fruit_list = ["apple", "banana", "orange", "pear"]

	# 遍历数据
	for value in fruit_list:
	    # |    匹配左右任意一个表达式
	    match_obj = re.match("apple|pear", value)
	    if match_obj:
	        print("%s是我想要的" % match_obj.group())
	    else:
	        print("%s不是我要的" % value)
	执行结果:

	apple是我想要的
	banana不是我要的
	orange不是我要的
	pear是我想要的

示例2：( )

需求：匹配出163、126、qq等邮箱

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("[a-zA-Z0-9_]{4,20}@(163|126|qq|sina|yahoo)\.com", "hello@163.com")
	if match_obj:
	    print(match_obj.group())
	    # 获取分组数据
	    print(match_obj.group(1))
	else:
	    print("匹配失败")
	执行结果:

	hello@163.com
	163

需求: 匹配qq:10567这样的数据，提取出来qq文字和qq号码

.. code-block:: python
	:linenos:

	import re

	match_obj = re.match("(qq):([1-9]\d{4,10})", "qq:10567")

	if match_obj:
	    print(match_obj.group())
	    # 分组:默认是1一个分组，多个分组从左到右依次加1
	    print(match_obj.group(1))
	    # 提取第二个分组数据
	    print(match_obj.group(2))
	else:
	    print("匹配失败")
	执行结果:

	qq
	10567

示例3：\num

需求：匹配出<html>hh</html>

.. code-block:: python
	:linenos:

	match_obj = re.match("<[a-zA-Z1-6]+>.*</[a-zA-Z1-6]+>", "<html>hh</div>")

	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")

	match_obj = re.match("<([a-zA-Z1-6]+)>.*</\\1>", "<html>hh</html>")

	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果：

	<html>hh</div>
	<html>hh</html>

需求：匹配出<html><h1>www.itcast.cn</h1></html>

.. code-block:: python
	:linenos:

	match_obj = re.match("<([a-zA-Z1-6]+)><([a-zA-Z1-6]+)>.*</\\2></\\1>", "<html><h1>www.itcast.cn</h1></html>")

	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果：

	<html><h1>www.itcast.cn</h1></html>

示例4：(?P<name>) (?P=name)

需求：匹配出<html><h1>www.itcast.cn</h1></html>

.. code-block:: python
	:linenos:

	match_obj = re.match("<(?P<name1>[a-zA-Z1-6]+)><(?P<name2>[a-zA-Z1-6]+)>.*</(?P=name2)></(?P=name1)>", "<html><h1>www.itcast.cn</h1></html>")

	if match_obj:
	    print(match_obj.group())
	else:
	    print("匹配失败")
	运行结果：

	<html><h1>www.itcast.cn</h1></html>

* 小结

::

	| 表示匹配左右任意一个表达式
	(ab) 表示将括号中字符作为一个分组
	\num 表示引用分组num匹配到的字符串
	(?P<name>) 表示分组起别名
	(?P=name) 表示引用别名为name分组匹配到的字符串
	(分组数据)：分组数是从左到右的方式进行分配的，最左边的是第一个分组，依次类推







































































