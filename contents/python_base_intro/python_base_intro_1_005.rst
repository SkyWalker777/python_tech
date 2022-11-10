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

例如：以`r`方式打开一个不存在的文件。

``open('test.txt', 'r')``

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

.. code-block:: python

	语法
	try:
	    可能发生错误的代码
	except 异常类型:
	    如果捕获到该异常类型执行的代码

	代码示例
	try:
	    print(num)
	except NameError:
	    print('有错误')

.. tip::

	| 如果尝试执行的代码的异常类型和要捕获的异常类型不一致，则无法捕获异常。
	| 一般try下方只放一行尝试执行的代码。

4 捕获多个指定异常

当捕获多个异常时，可以把要捕获的异常类型的名字，放到except 后，并使用元组的方式进行书写。

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

Exception是所有程序异常类的父类。

.. code-block:: python

	try:
	    print(num)
	except Exception as result:
	    print(result)

7 异常的else

else表示的是如果没有异常要执行的代码。

.. code-block:: python

	try:
	    print(1)
	except Exception as result:
	    print(result)
	else:
	    print('我是else，是没有异常的时候执行的代码')

8 异常的finally

finally表示的是无论是否异常都要执行的代码，例如关闭文件。

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

::

​	尝试只读方式打开test.txt文件，如果文件存在则读取文件内容，文件不存在则提示用户即可。
	读取内容要求：尝试循环读取内容，读取过程中如果检测到用户意外终止程序，则`except`捕获异常并提示用户。

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

在Python中，抛出自定义异常的语法为` raise 异常类对象`。

需求：密码长度不足，则报异常（用户输入密码，如果输入的长度不足3位，则报错，即抛出自定义异常，并捕获该异常）。

.. code-block:: python

	# 自定义异常类，继承Exception
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

- 异常语法

.. code-block:: python

	try:
	  	可能发生异常的代码
	except:
	  	如果出现异常执行的代码
	else:
	  	没有异常执行的代码
	finally:
	  	无论是否异常都要执行的代码

- 捕获异常

.. code-block:: python

	except 异常类型:
	  	代码

	except 异常类型 as xx:
			代码

- 自定义异常

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

| - 了解模块
| - 导入模块
| - 制作模块
| - `__all__`
| - 包的使用方法

模块
==================================================================================

Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。

模块能定义函数，类和变量，模块里也能包含可执行的代码。

* 导入模块

1 导入模块的方式

::

	- import 模块名
	- from 模块名 import 功能名
	- from 模块名 import *
	- import 模块名 as 别名
	- from 模块名 import 功能名 as 别名

2 导入方式详解

2.1 import

- 语法

.. code-block:: python

	# 1. 导入模块
	import 模块名
	import 模块名1, 模块名2...

	# 2. 调用功能
	模块名.功能名()

- 体验

.. code-block:: python

	import math
	print(math.sqrt(9))  # 3.0

2.2 from..import..

- 语法

.. code-block:: python

from 模块名 import 功能1, 功能2, 功能3...

- 体验

.. code-block:: python

	from math import sqrt
	print(sqrt(9))

2.3 from .. import *

- 语法

.. code-block:: python

	from 模块名 import *

- 体验

.. code-block:: python

	from math import *
	print(sqrt(9))

2.4 as定义别名

- 语法

.. code-block:: python

	# 模块定义别名
	import 模块名 as 别名

	# 功能定义别名
	from 模块名 import 功能 as 别名

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

在Python中，每个Python文件都可以作为一个模块，模块的名字就是文件的名字。**也就是说自定义模块名必须要符合标识符命名规则。**

1 定义模块

新建一个Python文件，命名为`my_module1.py`，并定义`testA`函数。

.. code-block:: python

	def testA(a, b):
	    print(a + b)

2 测试模块

在实际开中，当一个开发人员编写完一个模块后，为了让模块能够在项目中达到想要的效果，这个开发人员会自行在py文件中添加一些测试信息.，例如，在`my_module1.py`文件中添加测试代码。

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

如果使用`from .. import ..`或`from .. import *`导入多个模块的时候，且模块内有同名功能。当调用这个同名功能的时候，调用到的是后面导入的模块的功能。

- 体验

.. code-block:: python

	# 模块1代码
	def my_test(a, b):
	    print(a + b)

	# 模块2代码
	def my_test(a, b):
	    print(a - b)
	   
	# 导入模块和调用功能代码
	from my_module1 import my_test
	from my_module2 import my_test

	# my_test函数是模块2中的函数
	my_test(1, 1)

*  模块定位顺序

当导入一个模块，Python解析器对模块位置的搜索顺序是：

::

	1. 当前目录
	2. 如果不在当前目录，Python则搜索在shell变量PYTHONPATH下的每个目录。
	3. 如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/

模块搜索路径存储在system模块的sys.path变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录。

.. tip::

	| - 注意
	|   - 自己的文件名不要和已有模块名重复，否则导致模块功能无法使用
	|   - `使用from 模块名 import 功能`的时候，如果功能名字重复，调用到的是最后定义或导入的功能。

* `__all__`

如果一个模块文件中有`__all__`变量，当使用`from xxx import *`导入时，只能导入这个列表中的元素。

- my_module1模块代码

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

包将有联系的模块组织在一起，即放到同一个文件夹下，并且在这个文件夹创建一个名字为`__init__.py` 文件，那么这个文件夹就称之为包。

* 制作包

[New] — [Python Package] — 输入包名 — [OK] — 新建功能模块(有联系的模块)。

注意：新建包后，包内部会自动创建`__init__.py`文件，这个文件控制着包的导入行为。

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

















































































