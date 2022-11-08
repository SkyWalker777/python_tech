数据序列
##################################################################################

字符串
**********************************************************************************

认识字符串
==================================================================================

.. code-block:: python

	a = 'hello world'
	b = "abcdefg"
	print(type(a))
	print(type(b))

.. tip::

	控制台显示结果为`<class 'str'>`， 即数据类型为str(字符串)。

* 字符串特征

.. code-block:: python

	# 一对引号字符串

	name1 = 'Tom'
	name2 = "Rose"

	# 三引号字符串

	name3 = ''' Tom '''
	name4 = """ Rose """
	a = ''' i am Tom,
	        nice to meet you! '''

	b = """ i am Rose,
	        nice to meet you! """

.. tip::

	三引号形式的字符串支持换行。

* 字符串输出

.. code-block:: python

	print('hello world')

	name = 'Tom'
	print('我的名字是%s' % name)
	print(f'我的名字是{name}')

* 字符串输入

.. code-block:: python

	name = input('请输入您的名字：')
	print(f'您输入的名字是{name}')
	print(type(name))

	password = input('请输入您的密码：')
	print(f'您输入的密码是{password}')
	print(type(password))

下标
==================================================================================

`“下标”`又叫`“索引”`，就是编号。比如火车座位号，座位号的作用：按照编号快速找到对应的座位。同理，下标的作用即是通过下标快速找到对应的数据。

需求：字符串`name = "abcdef"`，取到不同下标对应的数据。

.. code-block:: python

	name = "abcdef"

	print(name[1])
	print(name[0])
	print(name[2])

.. tip::

	注意：下标从==0==开始。

切片
==================================================================================

切片是指对操作的对象截取其中一部分的操作。**字符串、列表、元组**都支持切片操作。

* 语法

序列[开始位置下标:结束位置下标:步长]

.. tip::

	| 1. 不包含结束位置下标对应的数据， 正负整数均可；
	| 2. 步长是选取间隔，正负整数均可，默认步长为1。

.. code-block:: python

	name = "abcdefg"

	print(name[2:5:1])  # cde
	print(name[2:5])  # cde
	print(name[:5])  # abcde
	print(name[1:])  # bcdefg
	print(name[:])  # abcdefg
	print(name[::2])  # aceg
	print(name[:-1])  # abcdef, 负1表示倒数第一个数据
	print(name[-4:-1])  # def
	print(name[::-1])  # gfedcba

常用操作方法
==================================================================================

字符串的常用操作方法有查找、修改和判断三大类。

* 查找

所谓字符串查找方法即是查找子串在字符串中的位置或出现的次数。

- find()：检测某个子串是否包含在这个字符串中，如果在返回这个子串开始的位置下标，否则则返回-1。

.. code-block:: python

	# 语法: 字符串序列.find(子串, 开始位置下标, 结束位置下标)
	# 注意: 开始和结束位置下标可以省略，表示在整个字符串序列中查找

	mystr = "hello world and itcast and itheima and Python"

	print(mystr.find('and'))  # 12
	print(mystr.find('and', 15, 30))  # 23
	print(mystr.find('ands'))  # -1

- index()：检测某个子串是否包含在这个字符串中，如果在返回这个子串开始的位置下标，否则则报异常。

.. code-block:: python

	# 字符串序列.index(子串, 开始位置下标, 结束位置下标)
	# 注意：开始和结束位置下标可以省略，表示在整个字符串序列中查找。

	mystr = "hello world and itcast and itheima and Python"

	print(mystr.index('and'))  # 12
	print(mystr.index('and', 15, 30))  # 23
	try:
	    print(mystr.index('ands'))  # 报错
	except:
	    pass

- rfind()： 和 find() 功能相同，但查找方向为==右侧==开始。
- rindex()：和 index() 功能相同，但查找方向为==右侧==开始。
- count()：返回某个子串在字符串中出现的次数

.. code-block:: python

	# 字符串序列.count(子串, 开始位置下标, 结束位置下标)
	# 注意：开始和结束位置下标可以省略，表示在整个字符串序列中查找。
	mystr = "hello world and itcast and itheima and Python"

	print(mystr.count('and'))  # 3
	print(mystr.count('ands'))  # 0
	print(mystr.count('and', 0, 20))  # 1

* 修改

所谓修改字符串，指的就是通过函数的形式修改字符串中的数据。

- replace()：替换

.. code-block:: python

	字符串序列.replace(旧子串, 新子串, 替换次数)
	注意：替换次数如果查出子串出现次数，则替换次数为该子串出现次数。

	mystr = "hello world and itcast and itheima and Python"
	# 结果：hello world he itcast he itheima he Python
	print(mystr.replace('and', 'he'))
	# 结果：hello world he itcast he itheima he Python
	print(mystr.replace('and', 'he', 10))
	# 结果：hello world and itcast and itheima and Python
	print(mystr)

	注意：数据按照是否能直接修改分为==可变类型==和==不可变类型==两种。字符串类型的数据修改的时候不能改变原有字符串，属于不能直接修改数据的类型即是不可变类型。

- split()：按照指定字符分割字符串。

.. code-block:: python

	字符串序列.split(分割字符, num)
	注意：num表示的是分割字符出现的次数，即将来返回数据个数为num+1个。

	mystr = "hello world and itcast and itheima and Python"

	# 结果：['hello world ', ' itcast ', ' itheima ', ' Python']
	print(mystr.split('and'))
	# 结果：['hello world ', ' itcast ', ' itheima and Python']
	print(mystr.split('and', 2))
	# 结果：['hello', 'world', 'and', 'itcast', 'and', 'itheima', 'and', 'Python']
	print(mystr.split(' '))
	# 结果：['hello', 'world', 'and itcast and itheima and Python']
	print(mystr.split(' ', 2))

	注意：如果分割字符是原有字符串中的子串，分割后则丢失该子串。

- join()：用一个字符或子串合并字符串，即是将多个字符串合并为一个新的字符串。

.. code-block:: python

	字符或子串.join(多字符串组成的序列)

	list1 = ['chuan', 'zhi', 'bo', 'ke']
	t1 = ('aa', 'b', 'cc', 'ddd')
	# 结果：chuan_zhi_bo_ke
	print('_'.join(list1))
	# 结果：aa...b...cc...ddd
	print('...'.join(t1))

- capitalize()：将字符串第一个字符转换成大写。

.. code-block:: python

	mystr = "hello world and itcast and itheima and Python"

	# 结果：Hello world and itcast and itheima and python
	print(mystr.capitalize())
	注意：capitalize()函数转换后，只字符串第一个字符大写，其他的字符全都小写。

- title()：将字符串每个单词首字母转换成大写。

.. code-block:: python

	mystr = "hello world and itcast and itheima and Python"

	# 结果：Hello World And Itcast And Itheima And Python
	print(mystr.title())

- lower()：将字符串中大写转小写。

.. code-block:: python

	mystr = "hello world and itcast and itheima and Python"

	# 结果：hello world and itcast and itheima and python
	print(mystr.lower())

- upper()：将字符串中小写转大写。

.. code-block:: python

	mystr = "hello world and itcast and itheima and Python"

	# 结果：HELLO WORLD AND ITCAST AND ITHEIMA AND PYTHON
	print(mystr.upper())

| - lstrip()：删除字符串左侧空白字符。
| - rstrip()：删除字符串右侧空白字符。
| - strip()：删除字符串两侧空白字符。

| - ljust()：返回一个原字符串左对齐,并使用指定字符(默认空格)填充至对应长度 的新字符串。
| - rjust()：返回一个原字符串右对齐,并使用指定字符(默认空格)填充至对应长度 的新字符串，语法和ljust()相同。
| - center()：返回一个原字符串居中对齐,并使用指定字符(默认空格)填充至对应长度 的新字符串，语法和ljust()相同。

* 判断

所谓判断即是判断真假，返回的结果是布尔型数据类型：True 或 False。

- startswith()：检查字符串是否是以指定子串开头，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。

.. code-block:: python

	字符串序列.startswith(子串, 开始位置下标, 结束位置下标)

	mystr = "hello world and itcast and itheima and Python   "
	# 结果：True
	print(mystr.startswith('hello'))
	# 结果False
	print(mystr.startswith('hello', 5, 20))

- endswith()：：检查字符串是否是以指定子串结尾，是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查。

.. code-block:: python

	字符串序列.endswith(子串, 开始位置下标, 结束位置下标)

	mystr = "hello world and itcast and itheima and Python"
	# 结果：True
	print(mystr.endswith('Python'))
	# 结果：False
	print(mystr.endswith('python'))
	# 结果：False
	print(mystr.endswith('Python', 2, 20))

- isalpha()：如果字符串至少有一个字符并且所有字符都是字母则返回 True, 否则返回 False。

.. code-block:: python

	mystr1 = 'hello'
	mystr2 = 'hello12345'

	# 结果：True
	print(mystr1.isalpha())

	# 结果：False
	print(mystr2.isalpha())

- isdigit()：如果字符串只包含数字则返回 True 否则返回 False。

.. code-block:: python

	mystr1 = 'aaa12345'
	mystr2 = '12345'

	# 结果： False
	print(mystr1.isdigit())

	# 结果：False
	print(mystr2.isdigit())

- isalnum()：如果字符串至少有一个字符并且所有字符都是字母或数字则返 回 True,否则返回 False。

.. code-block:: python

	mystr1 = 'aaa12345'
	mystr2 = '12345-'

	# 结果：True
	print(mystr1.isalnum())

	# 结果：False
	print(mystr2.isalnum())

- isspace()：如果字符串中只包含空白，则返回 True，否则返回 False。

.. code-block:: python

	mystr1 = '1 2 3 4 5'
	mystr2 = '     '

	# 结果：False
	print(mystr1.isspace())

	# 结果：True
	print(mystr2.isspace())

列表
**********************************************************************************

列表的应用场景
==================================================================================

| 思考：有一个人的姓名(TOM)怎么书写存储程序？答：变量。
| 思考：如果一个班级100位学生，每个人的姓名都要存储，应该如何书写程序？声明100个变量吗？ 答：列表即可， 列表一次性可以存储多个数据。

列表的格式
==================================================================================

[数据1, 数据2, 数据3, 数据4......]

.. tip::

	列表可以一次性存储多个数据，且可以为不同数据类型。

列表的常用操作
==================================================================================

列表的作用是一次性存储多个数据，程序员可以对这些数据进行的操作有：增、删、改、查。

* 查找

下标

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']

	print(name_list[0])  # Tom
	print(name_list[1])  # Lily
	print(name_list[2])  # Rose


函数

- index()：返回指定数据所在位置的下标 。

.. code-block:: python

	列表序列.index(数据, 开始位置下标, 结束位置下标)

	name_list = ['Tom', 'Lily', 'Rose']
	print(name_list.index('Lily', 0, 2))  # 1
	注意：如果查找的数据不存在则报错。

- count()：统计指定数据在当前列表中出现的次数。

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	print(name_list.count('Lily'))  # 1

- len()：访问列表长度，即列表中数据的个数。

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	print(len(name_list))  # 3

判断是否存在

- in：判断指定数据在某个列表序列，如果在返回True，否则返回False

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']

	# 结果：True
	print('Lily' in name_list)

	# 结果：False
	print('Lilys' in name_list)

- not in：判断指定数据不在某个列表序列，如果不在返回True，否则返回False

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']

	# 结果：False
	print('Lily' not in name_list)

	# 结果：True
	print('Lilys' not in name_list)

需求：查找用户输入的名字是否已经存在。

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']

	name = input('请输入您要搜索的名字：')

	if name in name_list:
	    print(f'您输入的名字是{name}, 名字已经存在')
	else:
	    print(f'您输入的名字是{name}, 名字不存在')

* 增加

作用：增加指定数据到列表中。

- append()：列表结尾追加数据。

.. code-block:: python

	列表序列.append(数据)

	name_list = ['Tom', 'Lily', 'Rose']
	name_list.append('xiaoming')
	# 结果：['Tom', 'Lily', 'Rose', 'xiaoming']
	print(name_list)

..tip ::
	
	列表追加数据的时候，直接在原列表里面追加了指定数据，即修改了原列表，故列表为可变类型数据。

如果append()追加的数据是一个序列，则追加整个序列到列表

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	name_list.append(['xiaoming', 'xiaohong'])
	# 结果：['Tom', 'Lily', 'Rose', ['xiaoming', 'xiaohong']]
	print(name_list)

- extend()：列表结尾追加数据，如果数据是一个序列，则将这个序列的数据逐一添加到列表。

.. code-block:: python

	列表序列.extend(数据)
	name_list = ['Tom', 'Lily', 'Rose']
	name_list.extend('xiaoming')
	# 结果：['Tom', 'Lily', 'Rose', 'x', 'i', 'a', 'o', 'm', 'i', 'n', 'g']
	print(name_list)

	name_list = ['Tom', 'Lily', 'Rose']
	name_list.extend(['xiaoming', 'xiaohong'])
	# 结果：['Tom', 'Lily', 'Rose', 'xiaoming', 'xiaohong']
	print(name_list)

- insert()：指定位置新增数据。

.. code-block:: python

	列表序列.insert(位置下标, 数据)
	name_list = ['Tom', 'Lily', 'Rose']
	name_list.insert(1, 'xiaoming')
	# 结果：['Tom', 'xiaoming', 'Lily', 'Rose']
	print(name_list)

* 删除

- del

.. code-block:: python

	语法: del 目标
	删除列表
	name_list = ['Tom', 'Lily', 'Rose']
	# 结果：报错提示：name 'name_list' is not defined
	del name_list
	print(name_list)

	删除指定数据
	name_list = ['Tom', 'Lily', 'Rose']
	del name_list[0]
	# 结果：['Lily', 'Rose']
	print(name_list)

- pop()：删除指定下标的数据(默认为最后一个)，并返回该数据。

.. code-block:: python

	语法: 列表序列.pop(下标)
	name_list = ['Tom', 'Lily', 'Rose']
	del_name = name_list.pop(1)
	# 结果：Lily
	print(del_name)
	# 结果：['Tom', 'Rose']
	print(name_list)

- remove()：移除列表中某个数据的第一个匹配项。

.. code-block:: python

	列表序列.remove(数据)
	name_list = ['Tom', 'Lily', 'Rose']
	name_list.remove('Rose')
	# 结果：['Tom', 'Lily']
	print(name_list)

- clear()：清空列表

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']

	name_list.clear()
	print(name_list) # 结果： []

* 修改

- 修改指定下标数据

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	name_list[0] = 'aaa'
	# 结果：['aaa', 'Lily', 'Rose']
	print(name_list)

- 逆置：reverse()

.. code-block:: python

	num_list = [1, 5, 2, 3, 6, 8]
	num_list.reverse()
	# 结果：[8, 6, 3, 2, 5, 1]
	print(num_list)

- 排序：sort()

.. code-block:: python

	语法: 列表序列.sort( key=None, reverse=False)
	注意：reverse表示排序规则，**reverse = True** 降序， **reverse = False** 升序（默认）
	num_list = [1, 5, 2, 3, 6, 8]
	num_list.sort()
	# 结果：[1, 2, 3, 5, 6, 8]
	print(num_list)

* 复制

.. code-block:: python

	函数：copy()
	name_list = ['Tom', 'Lily', 'Rose']
	name_li2 = name_list.copy()
	# 结果：['Tom', 'Lily', 'Rose']
	print(name_li2)

列表的循环遍历
==================================================================================

需求：依次打印列表中的各个数据。

* while

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	i = 0
	while i < len(name_list):
	    print(name_list[i])
	    i += 1

* for

.. code-block:: python

	name_list = ['Tom', 'Lily', 'Rose']
	for i in name_list:
	    print(i)

列表嵌套
==================================================================================

| 所谓列表嵌套指的就是一个列表里面包含了其他的子列表。
| 应用场景：要存储班级一、二、三三个班级学生姓名，且每个班级的学生姓名在一个列表。

.. code-block:: python

	name_list = [['小明', '小红', '小绿'], ['Tom', 'Lily', 'Rose'], ['张三', '李四', '王五']]

	思考： 如何查找到数据"李四"？
	# 第一步：按下标查找到李四所在的列表
	print(name_list[2])

	# 第二步：从李四所在的列表里面，再按下标找到数据李四
	print(name_list[2][1])

元组
**********************************************************************************

元组的应用场景
==================================================================================

| 思考：如果想要存储多个数据，但是这些数据是不能修改的数据，怎么做？
| 答：列表？列表可以一次性存储多个数据，但是列表中的数据允许更改。

.. code-block:: python

	num_list = [10, 20, 30]
	num_list[0] = 100

==一个元组可以存储多个数据，元组内的数据是不能修改的。==

定义元组
==================================================================================

元组特点：定义元组使用==小括号==，且==逗号==隔开各个数据，数据可以是不同的数据类型。

.. code-block:: python

	# 多个数据元组
	t1 = (10, 20, 30)

	# 单个数据元组
	t2 = (10,)

	注意：如果定义的元组只有一个数据，那么这个数据后面也好添加逗号，否则数据类型为唯一的这个数据的数据类型

	t2 = (10,)
	print(type(t2))  # tuple

	t3 = (20)
	print(type(t3))  # int

	t4 = ('hello')
	print(type(t4))  # str

元组的常见操作
==================================================================================

元组数据不支持修改，只支持查找，具体如下：

- 按下标查找数据

.. code-block:: python

	tuple1 = ('aa', 'bb', 'cc', 'bb')
	print(tuple1[0])  # aa

- index()：查找某个数据，如果数据存在返回对应的下标，否则报错，语法和列表、字符串的index方法相同。

.. code-block:: python

	tuple1 = ('aa', 'bb', 'cc', 'bb')
	print(tuple1.index('aa'))  # 0

- count()：统计某个数据在当前元组出现的次数。

.. code-block:: python

	tuple1 = ('aa', 'bb', 'cc', 'bb')
	print(tuple1.count('bb'))  # 2

- len()：统计元组中数据的个数。

.. code-block:: python

	tuple1 = ('aa', 'bb', 'cc', 'bb')
	print(len(tuple1))  # 4

	注意：元组内的直接数据如果修改则立即报错
	tuple1 = ('aa', 'bb', 'cc', 'bb')
	tuple1[0] = 'aaa'

	但是如果元组里面有列表，修改列表里面的数据则是支持的，故自觉很重要。
	tuple2 = (10, 20, ['aa', 'bb', 'cc'], 50, 30)
	print(tuple2[2])  # 访问到列表

	# 结果：(10, 20, ['aaaaa', 'bb', 'cc'], 50, 30)
	tuple2[2][0] = 'aaaaa'
	print(tuple2)

字典
**********************************************************************************

字典的应用场景
==================================================================================

思考1： 如果有多个数据，例如：'Tom', '男', 20，如何快速存储？

.. code-block:: python

	list1 = ['Tom', '男', 20]

思考2：如何查找到数据'Tom'？

答：查找到下标为0的数据即可。

.. code-block:: python

	list1[0]

思考3：如果将来数据顺序发生变化，如下所示，还能用`list1[0]`访问到数据'Tom'吗？。

.. code-block:: python

	list1 = ['男', 20, 'Tom']


答：不能，数据'Tom'此时下标为2。

思考4：数据顺序发生变化，每个数据的下标也会随之变化，如何保证数据顺序变化前后能使用同一的标准查找数据呢？

答：字典，字典里面的数据是以==键值对==形式出现，字典数据和数据顺序没有关系，即字典不支持下标，后期无论数据如何变化，只需要按照对应的键的名字查找数据即可。

创建字典的语法
==================================================================================

字典特点：

::

	- 符号为==大括号==
	- 数据为==键值对==形式出现
	- 各个键值对之间用==逗号==隔开

.. code-block:: python

	# 有数据字典
	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

	# 空字典
	dict2 = {}

	dict3 = dict()

.. tip::

	一般称冒号前面的为键(key)，简称k；冒号后面的为值(value)，简称v。

字典常见操作
==================================================================================

* 增

| 写法：==字典序列[key] = 值==
| 注意：如果key存在则修改这个key对应的值；如果key不存在则新增此键值对。

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

	dict1['name'] = 'Rose'
	# 结果：{'name': 'Rose', 'age': 20, 'gender': '男'}
	print(dict1)

	dict1['id'] = 110

	# {'name': 'Rose', 'age': 20, 'gender': '男', 'id': 110}
	print(dict1)

.. tip::

	注意：字典为可变类型。

* 删

- del() / del：删除字典或删除字典中指定键值对。

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

	del dict1['gender']
	# 结果：{'name': 'Tom', 'age': 20}
	print(dict1)

- clear()：清空字典

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}

	dict1.clear()
	print(dict1)  # {}

* 改

| 写法：==字典序列[key] = 值==
| 注意：如果key存在则修改这个key对应的值 ；如果key不存在则新增此键值对。

* 查

key值查找

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	print(dict1['name'])  # Tom
	print(dict1['id'])  # 报错

如果当前查找的key存在，则返回对应的值；否则则报错。

get()

.. code-block:: python

	字典序列.get(key, 默认值)
	注意：如果当前查找的key不存在则返回第二个参数(默认值)，如果省略第二个参数，则返回None。

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	print(dict1.get('name'))  # Tom
	print(dict1.get('id', 110))  # 110
	print(dict1.get('id'))  # None

keys()

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	print(dict1.keys())  # dict_keys(['name', 'age', 'gender'])

values()

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	print(dict1.values())  # dict_values(['Tom', 20, '男'])

items()

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	print(dict1.items())  # dict_items([('name', 'Tom'), ('age', 20), ('gender', '男')])

字典的循环遍历
==================================================================================

* 遍历字典的key

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	for key in dict1.keys():
	    print(key)

* 遍历字典的value

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	for value in dict1.values():
	    print(value)

* 遍历字典的元素

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	for item in dict1.items():
	    print(item)

* 遍历字典的键值对

.. code-block:: python

	dict1 = {'name': 'Tom', 'age': 20, 'gender': '男'}
	for key, value in dict1.items():
	    print(f'{key} = {value}')

集合
**********************************************************************************

创建集合
==================================================================================

创建集合使用`{}`或`set()`， 但是如果要创建空集合只能使用`set()`，因为`{}`用来创建空字典。

.. code-block:: python

	s1 = {10, 20, 30, 40, 50}
	print(s1)

	s2 = {10, 30, 20, 10, 30, 40, 30, 50}
	print(s2)

	s3 = set('abcdefg')
	print(s3)

	s4 = set()
	print(type(s4))  # set

	s5 = {}
	print(type(s5))  # dict

.. tip::

	| 1. 集合可以去掉重复数据；
	| 2. 集合数据是无序的，故不支持下标

集合常见操作方法
==================================================================================

* 增加数据

- add()

.. code-block:: python

	s1 = {10, 20}
	s1.add(100)
	s1.add(10)
	print(s1)  # {100, 10, 20}

因为集合有去重功能，所以，当向集合内追加的数据是当前集合已有数据的话，则不进行任何操作。

- update(), 追加的数据是序列。

.. code-block:: python

	s1 = {10, 20}
	# s1.update(100)  # 报错
	s1.update([100, 200])
	s1.update('abc')
	print(s1)

* 删除数据

- remove()，删除集合中的指定数据，如果数据不存在则报错。

.. code-block:: python

	s1 = {10, 20}

	s1.remove(10)
	print(s1)

	s1.remove(10)  # 报错
	print(s1)

- discard()，删除集合中的指定数据，如果数据不存在也不会报错。

.. code-block:: python

	s1 = {10, 20}

	s1.discard(10)
	print(s1)

	s1.discard(10)
	print(s1)

- pop()，随机删除集合中的某个数据，并返回这个数据。

.. code-block:: python

	s1 = {10, 20, 30, 40, 50}

	del_num = s1.pop()
	print(del_num)
	print(s1)

* 查找数据

- in：判断数据在集合序列
- not in：判断数据不在集合序列

.. code-block:: python

	s1 = {10, 20, 30, 40, 50}

	print(10 in s1)
	print(10 not in s1)

公共操作
**********************************************************************************

运算符
==================================================================================

::

	| 运算符 |      描述      |      支持的容器类型      |
	| :----: | :------------: | :----------------------: |
	|   +    |      合并      |    字符串、列表、元组    |
	|   *    |      复制      |    字符串、列表、元组    |
	|   in   |  元素是否存在  | 字符串、列表、元组、字典 |
	| not in | 元素是否不存在 | 字符串、列表、元组、字典 |

* +

.. code-block:: python

	# 1. 字符串 
	str1 = 'aa'
	str2 = 'bb'
	str3 = str1 + str2
	print(str3)  # aabb


	# 2. 列表 
	list1 = [1, 2]
	list2 = [10, 20]
	list3 = list1 + list2
	print(list3)  # [1, 2, 10, 20]

	# 3. 元组 
	t1 = (1, 2)
	t2 = (10, 20)
	t3 = t1 + t2
	print(t3)  # (10, 20, 100, 200)

* *

.. code-block:: python

	# 1. 字符串
	print('-' * 10)  # ----------

	# 2. 列表
	list1 = ['hello']
	print(list1 * 4)  # ['hello', 'hello', 'hello', 'hello']

	# 3. 元组
	t1 = ('world',)
	print(t1 * 4)  # ('world', 'world', 'world', 'world')

* in 或 not in

.. code-block:: python

	# 1. 字符串
	print('a' in 'abcd')  # True
	print('a' not in 'abcd')  # False

	# 2. 列表
	list1 = ['a', 'b', 'c', 'd']
	print('a' in list1)  # True
	print('a' not in list1)  # False

	# 3. 元组
	t1 = ('a', 'b', 'c', 'd')
	print('aa' in t1)  # False
	print('aa' not in t1)  # True

公共方法
==================================================================================

::

	| 函数                    | 描述                                                         |
	| ----------------------- | ------------------------------------------------------------ |
	| len()                   | 计算容器中元素个数                                           |
	| del 或 del()            | 删除                                                         |
	| max()                   | 返回容器中元素最大值                                         |
	| min()                   | 返回容器中元素最小值                                         |
	| range(start, end, step) | 生成从start到end的数字，步长为 step，供for循环使用           |
	| enumerate()             | 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。 |

* len()

.. code-block:: python

	# 1. 字符串
	str1 = 'abcdefg'
	print(len(str1))  # 7

	# 2. 列表
	list1 = [10, 20, 30, 40]
	print(len(list1))  # 4

	# 3. 元组
	t1 = (10, 20, 30, 40, 50)
	print(len(t1))  # 5

	# 4. 集合
	s1 = {10, 20, 30}
	print(len(s1))  # 3

	# 5. 字典
	dict1 = {'name': 'Rose', 'age': 18}
	print(len(dict1))  # 2

* del()

.. code-block:: python

	# 1. 字符串
	str1 = 'abcdefg'
	del str1
	print(str1)

	# 2. 列表
	list1 = [10, 20, 30, 40]
	del(list1[0])
	print(list1)  # [20, 30, 40]

* max()

.. code-block:: python

	# 1. 字符串
	str1 = 'abcdefg'
	print(max(str1))  # g

	# 2. 列表
	list1 = [10, 20, 30, 40]
	print(max(list1))  # 40

* min()

.. code-block:: python

	# 1. 字符串
	str1 = 'abcdefg'
	print(min(str1))  # a

	# 2. 列表
	list1 = [10, 20, 30, 40]
	print(min(list1))  # 10

* range()

.. code-block:: python

	# 1 2 3 4 5 6 7 8 9
	for i in range(1, 10, 1):
	    print(i)

	# 1 3 5 7 9
	for i in range(1, 10, 2):
	    print(i)

	# 0 1 2 3 4 5 6 7 8 9
	for i in range(10):
	    print(i)

注意：range()生成的序列不包含end数字。

* enumerate()

.. code-block:: python

	enumerate(可遍历对象, start=0)
	注意：start参数用来设置遍历数据的下标的起始值，默认为0。

	list1 = ['a', 'b', 'c', 'd', 'e']
	for i in enumerate(list1):
	    print(i)
	for index, char in enumerate(list1, start=1):
	    print(f'下标是{index}, 对应的字符是{char}')

容器类型转换
==================================================================================

* tuple()

.. code-block:: python

	作用：将某个序列转换成元组
	list1 = [10, 20, 30, 40, 50, 20]
	s1 = {100, 200, 300, 400, 500}

	print(tuple(list1))
	print(tuple(s1))

* list()

.. code-block:: python

	作用：将某个序列转换成列表
	t1 = ('a', 'b', 'c', 'd', 'e')
	s1 = {100, 200, 300, 400, 500}

	print(list(t1))
	print(list(s1))

* set()

.. code-block:: python

	作用：将某个序列转换成集合
	list1 = [10, 20, 30, 40, 50, 20]
	t1 = ('a', 'b', 'c', 'd', 'e')

	print(set(list1))
	print(set(t1))

.. note::

	| 集合可以快速完成列表去重
	| 集合不支持下标

推导式
**********************************************************************************

列表推导式
==================================================================================

| 作用：用一个表达式创建一个有规律的列表或控制一个有规律列表。
| 列表推导式又叫列表生成式。

* 快速体验

需求：创建一个0-10的列表。

- while循环实现

.. code-block:: python

	# 1. 准备一个空列表
	list1 = []

	# 2. 书写循环，依次追加数字到空列表list1中
	i = 0
	while i < 10:
	    list1.append(i)
	    i += 1

	print(list1)

- for循环实现

.. code-block:: python

	list1 = []
	for i in range(10):
	    list1.append(i)

	print(list1)

- 列表推导式实现

.. code-block:: python

	list1 = [i for i in range(10)]
	print(list1)

* 带if的列表推导式

方法一：range()步长实现

.. code-block:: python

	list1 = [i for i in range(0, 10, 2)]
	print(list1)

- 方法二：if实现

.. code-block:: python

	list1 = [i for i in range(10) if i % 2 == 0]
	print(list1)

* 多个for循环实现列表推导式

需求：创建列表如下：

.. code-block:: python

	[(1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]

	list1 = [(i, j) for i in range(1, 3) for j in range(3)]
	print(list1)

字典推导式
==================================================================================

思考：如果有如下两个列表：

.. code-block:: python

	list1 = ['name', 'age', 'gender']
	list2 = ['Tom', 20, 'man']

| 如何快速合并为一个字典？答：字典推导式
| 字典推导式作用：快速合并列表为字典或提取字典中目标数据。

* 快速体验

1. 创建一个字典：字典key是1-5数字，value是这个数字的2次方。

.. code-block:: python

	dict1 = {i: i**2 for i in range(1, 5)}
	print(dict1)  # {1: 1, 2: 4, 3: 9, 4: 16}

2. 将两个列表合并为一个字典

.. code-block:: python

	list1 = ['name', 'age', 'gender']
	list2 = ['Tom', 20, 'man']

	dict1 = {list1[i]: list2[i] for i in range(len(list1))}
	print(dict1)

3. 提取字典中目标数据

.. code-block:: python

	counts = {'MBP': 268, 'HP': 125, 'DELL': 201, 'Lenovo': 199, 'acer': 99}

	# 需求：提取上述电脑数量大于等于200的字典数据
	count1 = {key: value for key, value in counts.items() if value >= 200}
	print(count1)  # {'MBP': 268, 'DELL': 201}

集合推导式
==================================================================================

需求：创建一个集合，数据为下方列表的2次方。

.. code-block:: python

	list1 = [1, 1, 2]

	代码实现如下
	list1 = [1, 1, 2]
	set1 = {i ** 2 for i in list1}
	print(set1)  # {1, 4}

.. note::

	注意：集合有数据去重功能。









































































































































































