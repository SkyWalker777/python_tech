Selenium 篇
##################################################################################

原理与安装
**********************************************************************************

原理
==================================================================================

Selenium 是一个 Web 应用的自动化框架。通过它，我们可以写出自动化程序，像人一样在浏览器里操作web界面。 比如点击界面按钮，在文本框中输入文字 等操作。

而且还能从web界面获取信息。 比如获取12306票务信息，招聘网站职位信息，财经网站股票价格信息 等等，然后用程序进行分析处理。Selenium 的自动化原理是这样的.

|image0|

从上图可以看出：

我们写的自动化程序 需要使用 客户端库。程序的自动化请求都是通过这个库里面的编程接口发送给浏览器。

比如，我们要模拟用户点击界面按钮， 自动化程序里面就应该 调用客户端库相应的函数， 就会发送 点击元素 的请求给 下方的 浏览器驱动。 然后，浏览器驱动再转发这个请求给浏览器。

这个自动化程序发送给浏览器驱动的请求 是HTTP请求。客户端库从哪里来的？ 是Selenium组织提供的。Selenium组织提供了多种 编程语言的Selenium客户端库， 包括 java，python，js， ruby等，方便不同编程语言的开发者使用。我们只需要安装好客户端库，调用这些库，就可以发出自动化请求给浏览器咯。

浏览器驱动 也是一个独立的程序，是由浏览器厂商提供的， 不同的浏览器需要不同的浏览器驱动。 比如 Chrome浏览器和 火狐浏览器有 各自不同的驱动程序。浏览器驱动接收到我们的自动化程序发送的界面操作请求后，会转发请求给浏览器， 让浏览器去执行对应的自动化操作。

浏览器执行完操作后，会将自动化的结果返回给浏览器驱动， 浏览器驱动再通过HTTP响应的消息返回给我们的自动化程序的客户端库。自动化程序的客户端库 接收到响应后，将结果转化为 数据对象 返回给 我们的代码。

我们的程序就可以知道这次自动化操作的结果如何了。自动化程序的客户端库 接收到响应后，将结果转化为 数据对象 返回给 我们的代码。我们的程序就可以知道这次自动化操作的结果如何了。

总结一下，selenium 自动化流程如下：

::

	自动化程序调用Selenium 客户端库函数（比如点击按钮元素）
	客户端库会发送Selenium 命令 给浏览器的驱动程序
	浏览器驱动程序接收到命令后 ,驱动浏览器去执行命令
	浏览器执行命令
	浏览器驱动程序获取命令执行的结果，返回给我们自动化程序
	自动化程序对返回结果进行处理

安装
==================================================================================

Selenium环境的安装主要就是安装两样东西： 客户端库 和 浏览器 驱动 。

安装客户端库
----------------------------------------------------------------------------------

不同的编程语言选择不同的Selenium客户端库。对应 Python 语言来说，Selenium客户端库的安装非常简单，用 pip 命令即可。打开 命令行程序，运行如下命令

::

	pip install selenium

安装浏览器驱动
----------------------------------------------------------------------------------

浏览器驱动 是和 浏览器对应的。 不同的浏览器 需要选择不同的浏览器驱动。目前主流的浏览器中，谷歌 Chrome 浏览器对 Selenium自动化的支持更加成熟一些。强烈推荐大家使用 Chrome浏览器。

浏览器下载地址: ``https://www.google.cn/chrome/``

确保Chrome浏览器安装好以后，请大家打开下面的连接，访问Chrome 浏览器的驱动下载页面 ``https://chromedriver.storage.googleapis.com/index.html``

注意浏览器驱动 必须要和浏览器版本匹配，下图红圈里面的版本号 就是和浏览器版本号对应的

|image1|

| 比如：当前Chrome浏览器版本是72, 通常就需要下载72开头的目录里面的驱动程序 。
| 注意：驱动和浏览器的版本号越接近越好，但是略有差别（比如72和73），通常也没有什么问题。

打开目录，里面有3个zip包，分别对应Linux、Mac、Windows平台。如果我们是Windows平台的电脑，就下载 chromedriver_win32.zip. 这是个zip包，下载下来之后，解压里面的程序文件 chromedriver.exe 到某个目录下面，注意这个目录的路径最好是没有中文名和空格的。

比如，解压到 d:\webdrivers 目录下面。也就是保证我们的Chrome浏览器驱动路径为 d:\webdrivers\chromedriver.exe

简单示例
==================================================================================

下面的代码, 可以自动化的 打开Chrome浏览器，并且自动化打开百度网站，可以大家可以运行一下看看。

.. code-block:: python

	from selenium import webdriver

	# 创建 WebDriver 对象，指明使用chrome浏览器驱动
	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	# 调用WebDriver 对象的get方法 可以让浏览器打开指定网址
	wd.get('https://www.baidu.com')

其中，下面这行代码，就会运行浏览器驱动，并且运行Chrome浏览器

.. code-block:: python

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

注意，等号右边 返回的是 WebDriver 类型的对象，我们可以通过这个对象来操控浏览器，比如 打开网址、选择界面元素等。而下面这行代码，就是使用 WebDriver 的 get 方法 打开网址 百度

.. code-block:: python

	wd.get('https://www.baidu.com')

执行上面这行代码时，自动化程序就发起了 打开百度网址的 请求消息 ，通过浏览器驱动， 给 Chrome浏览器。Chome浏览器接收到该请求后，就会打开百度网址，通过浏览器驱动， 告诉自动化程序 打开成功。

.. tip::

	执行完自动化代码，如果想关闭浏览器窗口可以调用WebDriver对象的 quit 方法，像这样 wd.quit()

浏览器驱动目录加入环境变量 Path
==================================================================================

.. code-block:: python

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

如果，我们把浏览器驱动 所在目录 加入环境变量 Path， 再写代码时，就可以无需指定浏览器驱动路径了，像下面这样

.. code-block:: python

	wd = webdriver.Chrome()

因为，Selenium会自动在环境变量 Path 指定的那些目录里查找名为chromedriver.exe 的文件。

一定要注意的是， 加入环境变量 Path 的，不是浏览器驱动全路径，比如 d:\webdrivers\chromedriver.exe. 而是 浏览器驱动所在目录，比如 d:\webdrivers

那么，selenium又是如何 自动化地 在网页上 点击、输入、获取信息，将在接下来的章节要学习。

pycharm 中如何识别导入的库
==================================================================================

在 setting 中进行设置, Python Inteepeter 安装新库;

|image2|

扩展知识
==================================================================================

浏览器和驱动之间的接口是各浏览器厂商私有的，通常我们无需关心。喜欢刨根问底的朋友，可以参考 ``https://chromedevtools.github.io/devtools-protocol/``

选择元素的基本方法
**********************************************************************************

对于百度搜索页面，如果我们想自动化输入 白月黑羽 ，怎么做呢？这就是在网页中，操控界面元素。

web界面自动化，要操控元素，首先需要 选择 界面元素 ，或者说 定位 界面元素就是 先告诉浏览器，你要操作 哪个 界面元素， 让它找到你要操作的界面元素。我们必须要让浏览器 先找到元素，然后，才能操作元素。

选择元素的方法
==================================================================================

对应web自动化来说， 就是要告诉浏览器，你要操作的界面元素是什么。

那么，怎么告诉浏览器 呢？方法就是：告诉浏览器，你要操作的这个 web 元素的 特征。 就是告诉浏览器，这个元素它有什么与众不同的地方，可以让浏览器一下子找到它。

元素的特征怎么查看？可以使用浏览器的 开发者工具栏 帮我们查看、选择 web 元素。请大家用chrome浏览器访问百度，按F12后，点击下图箭头处的Elements标签，即可查看页面对应的HTML 元素

|image3|

然后，再点击 最左边的图标，如下所示

|image4|

之后，鼠标在界面上点击哪个元素，就可以查看 该元素对应的html标签了。比如，前面的图的高亮处，就是百度搜索输入框 对应的 input元素。

根据 元素的id 属性选择元素
==================================================================================

大家仔细看上面的 input元素 内容，会发现它有一个属性叫id。

|image5|

我们可以把 id 想象成元素的编号， 是用来在html中标记该元素的。 根据规范， 如果元素有id属性 ，这个id 必须是当前html中唯一的。所以如果元素有id， 根据id选择元素是最简单高效的方式。

这里，百度搜索框 元素的 id值为 kw, 下面的代码，可以自动化在浏览器中 访问百度，并且在输入框中搜索 白月黑羽。大家可以运行一下看看。

.. code-block:: python

	from selenium import webdriver

	# 创建 WebDriver 对象，指明使用chrome浏览器驱动
	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	# 调用WebDriver 对象的get方法 可以让浏览器打开指定网址
	wd.get('https://www.baidu.com')

	# 根据id选择元素，返回的就是该元素对应的WebElement对象
	element = wd.find_element_by_id('kw')

	# 通过该 WebElement对象，就可以对页面元素进行操作了
	# 比如输入字符串到 这个 输入框里
	element.send_keys('白月黑羽\n')

其中

.. code-block:: python

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

前面讲过，driver赋值的是 WebDriver 类型的对象，我们可以通过这个对象来操控浏览器，比如 打开网址、选择界面元素等。下面的代码

.. code-block:: python

	wd.find_element_by_id('kw')

使用了 WebDriver 对象 的方法 find_element_by_id，这行代码运行是，就会发起一个请求通过 浏览器驱动 转发给浏览器，告诉它，需要选择一个id为 kw 的元素。

浏览器，找到id为kw的元素后，将结果通过 浏览器驱动 返回给 自动化程序， 所以 find_element_by_id 方法会 返回一个 WebElement 类型的对象。 这个WebElement 对象可以看成是对应 页面元素 的遥控器。

我们通过这个WebElement对象，就可以 操控 对应的界面元素。比如: 调用这个对象的 send_keys 方法就可以在对应的元素中 输入字符串, 调用这个对象的 click 方法就可以 点击 该元素。

根据 class属性、tag名 选择元素
==================================================================================

根据 class属性 选择元素
----------------------------------------------------------------------------------

web自动化的难点和重点之一，就是如何 选择 我们想要操作的web页面元素。除了根据元素的id ，我们还可以根据元素的 class 属性选择元素。

就像一个 学生张三 可以定义类型为 中国人 或者 学生一样， 中国人 和 学生 都是 张三 的 类型。元素也有类型， class 属性就用来标志着元素 类型.

请大家 点击打开这个网址 ``http://cdn1.python3.vip/files/selenium/sample1.html``. 这个网址对应的html内容 有如下的部分

.. code-block:: html

	<body>
	    
	    <div class="plant"><span>土豆</span></div>
	    <div class="plant"><span>洋葱</span></div>
	    <div class="plant"><span>白菜</span></div>

	    <div class="animal"><span>狮子</span></div>
	    <div class="animal"><span>老虎</span></div>
	    <div class="animal"><span>山羊</span></div>

	</body>

所有的植物元素都有个class属性 值为 plant。所有的动物元素都有个class属性 值为 animal。

如果我们要选择 所有的 动物， 就可以使用方法 find_elements_by_class_name 。注意element后面多了个s.

.. code-block:: python

	wd.find_elements_by_class_name('animal')

.. tip::

	| find_elements_by_class_name 方法返回的是找到的符合条件的 所有 元素 (这里有3个元素)， 放在一个 列表 中返回。
	| 而如果我们使用 find_element_by_class_name (注意少了一个s) 方法， 就只会返回 第一个 元素。

大家可以运行如下代码看看。

.. code-block:: python

	from selenium import webdriver

	# 创建 WebDriver 实例对象，指明使用chrome浏览器驱动
	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	# WebDriver 实例对象的get方法 可以让浏览器打开指定网址
	wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

	# 根据 class name 选择元素，返回的是 一个列表
	# 里面 都是class 属性值为 animal的元素对应的 WebElement对象
	elements = wd.find_elements_by_class_name('animal')

	# 取出列表中的每个 WebElement对象，打印出其text属性的值
	# text属性就是该 WebElement对象对应的元素在网页中的文本内容
	for element in elements:
	    print(element.text)

首先，大家要注意： 通过 WebElement 对象的 text属性 可以获取该元素 在网页中的文本内容。所以 下面的代码，可以打印出 element 对应 网页元素的 文本

.. code-block:: python

	print(element.text)

如果我们把 ``elements = wd.find_elements_by_class_name('animal')``, 去掉一个s ，改为

.. code-block:: python

	element = wd.find_element_by_class_name('animal')
	print(element.text)

那么返回的就是第一个class 属性为 animal的元素， 也就是这个元素

.. code-block:: html

	<div class="animal"><span>狮子</span></div>

就像一个 学生张三 可以定义有 多个 类型： 中国人 和 学生 ， 中国人 和 学生 都是 张三 的 类型。元素也可以有 多个class类型 ，多个class类型的值之间用 空格 隔开，比如

.. code-block:: html

	<span class="chinese student">张三</span>

注意，这里 span元素 有两个class属性，分别 是 chinese 和 student， 而不是一个 名为 chinese student 的属性。

我们要用代码选择这个元素，可以指定任意一个class 属性值，都可以选择到这个元素，如下

.. code-block:: python

	element = wd.find_elements_by_class_name('chinese')

或者

.. code-block:: python

	element = wd.find_elements_by_class_name('student')

而不能这样写

.. code-block:: python

	element = wd.find_elements_by_class_name('chinese student')

根据 tag 名 选择元素
----------------------------------------------------------------------------------

类似的，我们可以通过方法 find_elements_by_tag_name ，选择所有的tag名为 div的元素，如下所示

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

	# 根据 tag name 选择元素，返回的是 一个列表
	# 里面 都是 tag 名为 div 的元素对应的 WebElement对象
	elements = wd.find_elements_by_tag_name('div')

	# 取出列表中的每个 WebElement对象，打印出其text属性的值
	# text属性就是该 WebElement对象对应的元素在网页中的文本内容
	for element in elements:
	    print(element.text)

find_element 和 find_elements 的区别
----------------------------------------------------------------------------------

| 使用 find_elements 选择的是符合条件的 所有 元素， 如果没有符合条件的元素， 返回空列表;
| 使用 find_element 选择的是符合条件的 第一个 元素， 如果没有符合条件的元素， 抛出 NoSuchElementException 异常

通过 WebElement 对象选择元素
==================================================================================

| 不仅 WebDriver对象有 选择元素 的方法， WebElement对象 也有选择元素的方法。WebElement对象 也可以调用 find_elements_by_xxx， find_element_by_xxx 之类的方法
| WebDriver 对象 选择元素的范围是 整个 web页面， 而 WebElement 对象 选择元素的范围是 该元素的内部。

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

	element = wd.find_element_by_id('container')

	# 限制 选择元素的范围是 id 为 container 元素的内部。
	spans = element.find_elements_by_tag_name('span')
	for span in spans:
	    print(span.text)

输出结果就只有

::

	内层11
	内层12
	内层21

等待界面元素出现
==================================================================================

| 在我们进行网页操作的时候， 有的元素内容不是可以立即出现的， 可能会等待一段时间。
| 比如 百度搜索一个词语， 我们点击搜索后， 浏览器需要把这个搜索请求发送给百度服务器， 百度服务器进行处理后，把搜索结果返回给我们。
| 所以，从点击搜索到得到结果，需要一定的时间，只是通常百度服务器的处理比较快，我们感觉好像是立即出现了搜索结果。百度搜索的每个结果 对应的界面元素 其ID 分别是数字 1,2,3,4...

如下

|image6|

那么我们可以试试用如下代码 来将 第一个搜索结果里面的文本内容 打印出来

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('https://www.baidu.com')

	element = wd.find_element_by_id('kw')

	element.send_keys('白月黑羽\n')

	# id 为 1 的元素 就是第一个搜索结果
	element = wd.find_element_by_id('1')

	# 打印出 第一个搜索结果的文本字符串
	print (element.text)

如果大家去运行一下，就会发现有如下异常抛出

::

	selenium.common.exceptions.NoSuchElementException: Message: no such element: Unable to locate element: {"method":"css selector","selector":"[id="1"]"}
	  (Session info: chrome=86.0.4240.111)

| NoSuchElementException 的意思就是在当前的网页上 找不到该元素， 就是找不到 id 为 1 的元素。
| 为什么呢？因为我们的代码执行的速度比 百度服务器响应的速度 快。百度还没有来得及 返回搜索结果，我们就执行了如下代码

.. code-block:: python

	element = wd.find_element_by_id('1')

| 在那短暂的瞬间， 网页上是没有用 id为1的元素的 （因为还没有搜索结果呢）。自然就会报告错误 id为1 的元素不存在了。
| 那么怎么解决这个问题呢？很多聪明的读者可以想到， 点击搜索后， 用sleep 来 等待几秒钟， 等百度服务器返回结果后，再去选择 id 为1 的元素， 就像下面这样

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('https://www.baidu.com')

	element = wd.find_element_by_id('kw')

	element.send_keys('黑羽魔巫宗\n')

	# 等待 2 秒
	from time import sleep
	sleep(2)

	# 2 秒 过后，再去搜索
	element = wd.find_element_by_id('1')

	# 打印出 第一个搜索结果的文本字符串
	print (element.text)

大家可以运行一下，基本是可以的，不会再报错了。但是这样的方法 有个很大的问题，就是：设置等待多长时间合适呢？这次百度网站反应可能比较快，我们等了一秒钟就可以了。

但是谁知道下次他的反应是不是还这么快呢？百度也曾经出现过服务器瘫痪的事情。可能有的读者说，我干脆sleep比较长的时间， 等待 20 秒， 总归可以了吧？

这样也有很大问题，假如一个自动化程序里面需要10次等待， 就要花费 200秒。 而可能大部分时间， 服务器反映都是很快的，根本不需要等20秒， 这样就造成了大量的时间浪费了。

Selenium提供了一个更合理的解决方案，是这样的：

| 当发现元素没有找到的时候， 并不 立即返回 找不到元素的错误。而是周期性（每隔半秒钟）重新寻找该元素，直到该元素找到，或者超出指定最大等待时长，这时才 抛出异常（如果是 find_elements 之类的方法， 则是返回空列表）。
| Selenium 的 Webdriver 对象 有个方法叫 implicitly_wait 该方法接受一个参数， 用来指定 最大等待时长。

如果我们 加入如下代码

.. code-block:: python

	wd.implicitly_wait(10)

那么后续所有的 find_element 或者 find_elements 之类的方法调用 都会采用上面的策略：如果找不到元素， 每隔 半秒钟 再去界面上查看一次， 直到找到该元素， 或者 过了10秒 最大时长。

这样，我们的百度搜索的例子的最终代码如下

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome()

	# 设置最大等待时长为 10秒
	wd.implicitly_wait(10)

	wd.get('https://www.baidu.com')

	element = wd.find_element_by_id('kw')

	element.send_keys('黑羽魔巫宗\n')

	element = wd.find_element_by_id('1')

	print (element.text)

大家再运行一下，可以发现不会有错误了。那么是不是有了implicitwait， 可以彻底不用sleep了呢？不是的，有的时候我们等待元素出现，仍然需要sleep。

操控元素的基本方法
**********************************************************************************

选择到元素之后，我们的代码会返回元素对应的 WebElement对象，通过这个对象，我们就可以 操控 元素了。

操控元素通常包括:

::

	点击元素
	在元素中输入字符串，通常是对输入框这样的元素
	获取元素包含的信息，比如文本内容，元素的属性

点击元素
==================================================================================

点击元素 非常简单，就是调用元素WebElement对象的 click方法。前面我们已经学过。这里我们要补充讲解一点。当我们调用 WebElement 对象的 click 方法去点击 元素的时候， 浏览器接收到自动化命令，点击的是该元素的 中心点 位置 。

例如，对于下面的这样一个元素:

|image7|

我们要点击 添加客户 这个按钮，既可以点击 右边对应的 绿色框子总的button 元素 ，也可以点击红色框子中的span元素 。因为这两个元素的中心点都是 button 内部，都是有效点击区域

输入框
==================================================================================

输入字符串 也非常简单，就是调用元素WebElement对象的send_keys方法。前面我们也已经学过。如果我们要 把输入框中已经有的内容清除掉，可以使用WebElement对象的clear方法

请大家点击 这个网址 http://cdn1.python3.vip/files/selenium/test3.html, 并且按F12，观察HTML的内容. 我们要写一个自动化程序：要求在输入框中填入姓名：白月黑羽。而且要做到输入框中已经有的提示字符，需要先 清除掉

代码应该如下:

.. code-block:: python

	element = wd.find_element_by_id("input1")

	element.clear() # 清除输入框已有的字符串
	element.send_keys('白月黑羽') # 输入新字符串

获取元素信息
==================================================================================

获取元素的文本内容
----------------------------------------------------------------------------------

通过WebElement对象的 text 属性，可以获取元素 展示在界面上的 文本内容。

.. code-block:: python

	element = wd.find_element_by_id('animal')
	print(element.text)

获取元素属性
----------------------------------------------------------------------------------

通过WebElement对象的 get_attribute 方法来获取元素的属性值，比如要获取元素属性class的值，就可以使用 element.get_attribute('class')

.. code-block:: python

	element = wd.find_element_by_id('input_name')
	print(element.get_attribute('class'))

执行完自动化代码，如果想关闭浏览器窗口可以调用WebDriver对象的 quit 方法，像这样 wd.quit()

获取整个元素对应的HTML
----------------------------------------------------------------------------------

| 要获取整个元素对应的HTML文本内容，可以使用 element.get_attribute('outerHTML');
| 如果，只是想获取某个元素 内部 的HTML文本内容，可以使用 element.get_attribute('innerHTML')

获取输入框里面的文字
----------------------------------------------------------------------------------

对于input输入框的元素，要获取里面的输入文本，用text属性是不行的，这时可以使用 element.get_attribute('value')

.. code-block:: python

	element = wd.find_element_by_id("input1")
	print(element.get_attribute('value')) # 获取输入框中的文本

获取元素文本内容2
----------------------------------------------------------------------------------

| 通过WebElement对象的 text 属性，可以获取元素 展示在界面上的 文本内容。
| 但是，有时候，元素的文本内容没有展示在界面上，或者没有完全完全展示在界面上。 这时，用WebElement对象的text属性，获取文本内容，就会有问题。
| 出现这种情况，可以尝试使用 element.get_attribute('innerText') ，或者 element.get_attribute('textContent')

css表达式-上篇
**********************************************************************************

前面我们看到了根据 id、class属性、tag名 选择元素。如果我们要选择的 元素 没有id、class 属性，或者有些我们不想选择的元素 也有相同的 id、class属性值，怎么办呢？

这时候我们通常可以通过 CSS selector 语法选择元素。

CSS Selector 语法选择元素原理
==================================================================================

| HTML中经常要 为 某些元素 指定 显示效果，比如 前景文字颜色是红色， 背景颜色是黑色， 字体是微软雅黑等。
| 那么CSS必须告诉浏览器：要 选择哪些元素 ， 来使用这样的显示风格。比如 ，下图中，为什么狮子老虎山羊会显示为红色呢？

|image8|

| 因为蓝色框里面用css 样式，指定了class 值为animal的元素，要显示为红色。其中 蓝色框里面的 .animal 就是 CSS Selector ，或者说 CSS 选择器。
| CSS Selector 语法就是用来选择元素的。既然 CSS Selector 语法 天生就是浏览器用来选择元素的，selenium自然就可以使用它用在自动化中，去选择要操作的元素了。
| 只要 CSS Selector 的语法是正确的， Selenium 就可以选择到该元素。CSS Selector 非常强大，学习Selenium Web自动化一定要学习CSS Selector

通过 CSS Selector 选择单个元素的方法是

.. code-block:: python

	find_element_by_css_selector(CSS Selector参数)

选择所有元素的方法是

.. code-block:: python

	find_elements_by_css_selector(CSS Selector参数)

CSS Selector 选择元素非常灵活强大， 大家可以从下面的例子看出来。

根据 tag名、id、class 选择元素
==================================================================================

CSS Selector 同样可以根据tag名、id 属性和 class属性 来 选择元素，根据 tag名 选择元素的 CSS Selector 语法非常简单，直接写上tag名即可，

比如 要选择 所有的tag名为div的元素，就可以是这样

.. code-block:: python

	elements = wd.find_elements_by_css_selector('div')
	等价于
	elements = wd.find_elements_by_tag_name('div')

根据id属性 选择元素的语法是在id号前面加上一个井号： #id值; 比如 http://cdn1.python3.vip/files/selenium/sample1.html;

有下面这样的元素：

.. code-block:: python

	<input  type="text" id='searchtext' />

就可以使用 #searchtext 这样的 CSS Selector 来选择它。比如，我们想在 id 为 searchtext 的输入框中输入文本 你好 ，完整的Python代码如下

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

	element = wd.find_element_by_css_selector('#searchtext')
	element.send_keys('你好')

根据class属性 选择元素的语法是在 class 值 前面加上一个点： .class值; 比如 这个网址 http://cdn1.python3.vip/files/selenium/sample1.html

要选择所有 class 属性值为 animal的元素 动物 除了这样写

.. code-block:: python

	elements = wd.find_elements_by_class_name('animal')

还可以这样写

.. code-block:: python

	elements = wd.find_elements_by_css_selector('.animal')

因为是选择 所有符合条件的 ，所以用 find_elements 而不是 find_element

选择 子元素 和 后代元素
==================================================================================

HTML中， 元素 内部可以 包含其他元素， 比如 下面的 HTML片段

.. code-block:: html

	<div id='container'>  
	    <div id='layer1'>
	        <div id='inner11'>
	            <span>内层11</span>
	        </div>
	        <div id='inner12'>
	            <span>内层12</span>
	        </div>
	    </div>

	    <div id='layer2'>
	        <div id='inner21'>
	            <span>内层21</span>
	        </div>
	    </div>
	</div>

下面的一段话有些绕口， 请 大家细心 阅读：

::

	id 为 container 的div元素 包含了 id 为 layer1 和 layer2 的两个div元素。
	这种包含是直接包含， 中间没有其他的层次的元素了。 所以 id 为 layer1 和 layer2 的两个div元素 是 id 为 container 的div元素 的 直接子元素
	而 id 为 layer1 的div元素 又包含了 id 为 inner11 和 inner12 的两个div元素。 中间没有其他层次的元素，所以这种包含关系也是 直接子元素 关系
	id 为 layer2 的div元素 又包含了 id 为 inner21 这个div元素。 这种包含关系也是 直接子元素 关系

	而对于 id 为 container 的div元素来说， id 为 inner11 、inner12 、inner22 的元素 和 两个 span类型的元素 都不是 它的直接子元素， 因为中间隔了 几层。
	虽然不是直接子元素， 但是 它们还是在 container 的内部， 可以称之为它 的 后代元素
	后代元素也包括了直接子元素， 比如 id 为 layer1 和 layer2 的两个div元素 也可以说 是 id 为 container 的div元素 的 直接子元素，同时也是后代子元素

如果 元素2 是 元素1 的 直接子元素， CSS Selector 选择子元素的语法是这样的

::

	元素1 > 元素2

中间用一个大于号 （我们可以理解为箭头号）; 注意，最终选择的元素是 元素2， 并且要求这个 元素2 是 元素1 的直接子元素

也支持更多层级的选择， 比如 

::

	元素1 > 元素2 > 元素3 > 元素4

就是选择 元素1 里面的子元素 元素2 里面的子元素 元素3 里面的子元素 元素4 ， 最终选择的元素是 元素4

如果 元素2 是 元素1 的 后代元素， CSS Selector 选择后代元素的语法是这样的

::

	元素1   元素2

中间是一个或者多个空格隔开, 最终选择的元素是 元素2 ， 并且要求这个 元素2 是 元素1 的后代元素。

也支持更多层级的选择， 比如 

::

	元素1   元素2   元素3  元素4

最终选择的元素是 元素4

根据属性选择
==================================================================================

id、class 都是web元素的 ```属性``` ，因为它们是很常用的属性，所以css选择器专门提供了根据 id、class 选择的语法。
那么其他的属性呢？

::

	<a href="http://www.miitbeian.gov.cn">苏ICP备88885574号</a>

| 里面根据 href选择，可以用css 选择器吗？当然可以！css 选择器支持通过任何属性来选择元素，语法是用一个方括号 [] 。
| 比如要选择上面的a元素，就可以使用 [href="http://www.miitbeian.gov.cn"]。这个表达式的意思是，选择 属性href值为 http://www.miitbeian.gov.cn 的元素。

完整代码如下

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'e:\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample1.html')

	# 根据属性选择元素
	element = wd.find_element_by_css_selector('[href="http://www.miitbeian.gov.cn"]')

	# 打印出元素对应的html
	print(element.get_attribute('outerHTML'))

| 当然，前面可以加上标签名的限制，比如 div[class='SKnet'] 表示 选择所有 标签名为div，且class属性值为SKnet的元素。属性值用单引号，双引号都可以。
| 根据属性选择，还可以不指定属性值，比如 [href], 表示选择 所有 具有 属性名 为href 的元素，不管它们的值是什么。
| CSS 还可以选择 属性值 包含 某个字符串 的元素. 比如， 要选择a节点，里面的href属性包含了 miitbeian 字符串，就可以这样写 ``a[href*="miitbeian"]``

| 还可以 选择 属性值 以某个字符串 开头 的元素. 比如， 要选择a节点，里面的href属性以 http 开头 ，就可以这样写 ``a[href^="http"]``
| 还可以 选择 属性值 以某个字符串 结尾 的元素. 比如， 要选择a节点，里面的href属性以 gov.cn 结尾 ，就可以这样写 ``a[href$="gov.cn"]``

| 如果一个元素具有多个属性 ``<div class="misc" ctype="gun">沙漠之鹰</div>``
| CSS 选择器 可以指定 选择的元素要 同时具有多个属性的限制，像这样 div[class=misc][ctype=gun]

验证 CSS Selector
==================================================================================

| 那么我们怎么验证 CSS Selector 的语法是否正确选择了我们要选择的元素呢？
| 当然可以像下面这样，写出Python代码，运行看看，能否操作成功

.. code-block:: python

	element = wd.find_element_by_css_selector('#searchtext')
	element.input('输入的文本')

| 但是这样做的问题就是：太麻烦了。当我们进行自动化开发的时候，有大量选择元素的语句，都要这样一个个的验证，就非常耗时间。

| 由于 CSS Selector 是浏览器直接支持的，可以在浏览器 开发者工具栏 中验证。比如我们使用Chrome浏览器打开 http://cdn1.python3.vip/files/selenium/sample1.html
| 按 F12 打开 开发者工具栏, 如果我们要验证 下面的表达式 ``#bottom > .footer2  a``

| 能否选中 这个元素 ``<a href="http://www.miitbeian.gov.cn">苏ICP备88885574号</a>``
| 可以这样做: 点击 Elements 标签后， 同时按 Ctrl 键 和 F 键， 就会出现下图箭头处的 搜索框

|image9|

| 我们可以在里面输入任何 CSS Selector 表达式 ，如果能选择到元素， 右边的的红色方框里面就会显示出类似 2 of 3 这样的内容。
| of 后面的数字表示这样的表达式 总共选择到几个元素; of 前面的数字表示当前黄色高亮显示的是 其中第几个元素
| 上图中的 1 of 1 就是指 ： CSS 选择语法 #bottom > .footer2 a; 在当前网页上共选择到 1 个元素， 目前高亮显示的是第1个。如果我们输入 .plant 就会发现，可以选择到3个元素

|image10|

css表达式-下篇
**********************************************************************************

选择语法联合使用
==================================================================================

| CSS selector的另一个强大之处在于： 选择语法 可以 联合使用: http://cdn1.python3.vip/files/selenium/sample1.html
| 比如， 我们要选择 网页 html 中的元素 <span class='copyright'>版权</span>

.. code-block:: html

	<div id='bottom'>
	    <div class='footer1'>
	        <span class='copyright'>版权</span>
	        <span class='date'>发布日期：2018-03-03</span>
	    </div>
	    <div class='footer2'>
	        <span>备案号
	            <a href="http://www.miitbeian.gov.cn">苏ICP备88885574号</a>
	        </span>
	    </div>        
	</div>   

CSS selector 表达式 可以这样写：

::

	div.footer1 > span.copyright

就是 选择 一个class 属性值为 copyright 的 span 节点， 并且要求其 必须是 class 属性值为 footer1 的 div节点 的子节点, 也可以更简单：

::

	.footer1 > .copyright

就是 选择 一个class 属性值为copyright 的节点（不限类型）， 并且要求其 必须是 class 属性值为 footer1 的节点的 子节点, 当然 这样也是可以的：

::

	.footer1  .copyright

因为子元素同时也是后代元素

组选择
==================================================================================

如果我们要 同时选择所有class 为 plant 和 class 为 animal 的元素。怎么办？这种情况，css选择器可以 使用 逗号 ，称之为 组选择 ，像这样

::

	.plant , .animal

再比如，我们要同时选择所有tag名为div的元素 和 id为BYHY的元素，就可以像这样写

::

	div,#BYHY

对应的selenium代码如下

.. code-block:: python

	elements = wd.find_elements_by_css_selector('div,#BYHY')
	for element in elements:
	    print(element.text)

再看一个例子: 打开这个网址 http://cdn1.python3.vip/files/selenium/sample1a.html, 我们要选择所有 唐诗里面的作者和诗名， 也就是选择所有 id 为 t1 里面的 span 和 p 元素

我们是不是应该这样写呢？ ``#t1 > span,p`` , 不行哦，这样写的意思是 选择所有 id 为 t1 里面的 span 和 所有的 p 元素

只能这样写 ``#t1 > span , #t1 > p``

按次序选择子节点
==================================================================================

示例地址: http://cdn1.python3.vip/files/selenium/sample1b.html, 对应的html如下，关键信息如下

.. code-block:: html

	<body>  
	   <div id='t1'>
	       <h3> 唐诗 </h3>
	       <span>李白</span>
	       <p>静夜思</p>
	       <span>杜甫</span>
	       <p>春夜喜雨</p>              
	   </div>      
	    
	   <div id='t2'>
	       <h3> 宋词 </h3>
	       <span>苏轼</span>
	       <p>赤壁怀古</p>
	       <p>明月几时有</p>
	       <p>江城子·乙卯正月二十日夜记梦</p>
	       <p>蝶恋花·春景</p>
	       <span>辛弃疾</span>
	       <p>京口北固亭怀古</p>
	       <p>青玉案·元夕</p>
	       <p>西江月·夜行黄沙道中</p>
	   </div>             
	</body>

父元素的第n个子节点
----------------------------------------------------------------------------------

| 可以指定选择的元素 是父元素的第几个子节点, 使用 nth-child
| 比如，我们要选择 唐诗 和宋词 的第一个 作者，也就是说 选择的是 第2个子元素，并且是span类型, 所以这样可以这样写 span:nth-child(2), 如果你不加节点类型限制，直接这样写 :nth-child(2), 就是选择所有位置为第2个的所有元素，不管是什么类型

父元素的倒数第n个子节点
----------------------------------------------------------------------------------

也可以反过来， 选择的是父元素的 倒数第几个子节点 ，使用 nth-last-child, 比如： ``p:nth-last-child(1)``, 就是选择第倒数第1个子元素，并且是p元素

父元素的第几个某类型的子节点
----------------------------------------------------------------------------------

| 我们可以指定选择的元素 是父元素的第几个 某类型的 子节点
| 使用 nth-of-type, 比如，我们要选择 唐诗 和宋词 的第一个 作者，可以像上面那样思考：选择的是 第2个子元素，并且是span类型, 所以这样可以这样写 span:nth-child(2) ，
| 还可以这样思考，选择的是 第1个span类型 的子元素, 所以也可以这样写 span:nth-of-type(1)

父元素的倒数第几个某类型的子节点
----------------------------------------------------------------------------------

当然也可以反过来， 选择父元素的 倒数第几个某类型 的子节点, 使用 nth-last-of-type, 像这样: ``p:nth-last-of-type(2)``

奇数节点和偶数节点
----------------------------------------------------------------------------------

| 如果要选择的是父元素的 某类型偶数节点，使用 nth-of-type(even)
| 如果要选择的是父元素的 某类型奇数节点，使用 nth-of-type(odd)

兄弟节点选择
==================================================================================

相邻兄弟节点选择
----------------------------------------------------------------------------------

| 上面的例子里面，我们要选择 唐诗 和宋词 的第一个 作者, 还有一种思考方法，就是选择 h3 后面紧跟着的兄弟节点 span。
| 这就是一种 相邻兄弟 关系，可以这样写 h3 + span, 表示元素 紧跟关系的 是 加号

后续所有兄弟节点选择
----------------------------------------------------------------------------------

| 如果要选择是 选择 h3 后面所有的兄弟节点 span，可以这样写 h3 ~ span
| 更多CSS选择器的介绍，可以参考CSS 选择器参考手册: http://www.w3school.com.cn/cssref/css_selectors.asp

frame切换/窗口切换
**********************************************************************************

切换到frame
==================================================================================

demo 地址: http://cdn1.python3.vip/files/selenium/sample2.html

如果我们要 选择 下图方框中 所有的 蔬菜，使用css选择，怎么写表达式？当然，要先查看到它们的html元素特征

|image11|

大家可能会照旧写出如下代码：

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')

	# 根据 class name 选择元素，返回的是 一个列表
	elements = wd.find_elements_by_class_name('plant')

	for element in elements:
	    print(element.text)

| 运行一下，你就会发现，运行结果打印内容为空白，说明没有选择到 class 属性值为 plant 的元素。
| 为什么呢？因为仔细看，你可以发现， 这些元素是在一个叫 iframe的 元素中的。

|image12|

| 这个 iframe 元素非常的特殊， 在html语法中，frame 元素 或者iframe元素的内部 会包含一个 被嵌入的 另一份html文档。
| 在我们使用selenium打开一个网页是， 我们的操作范围 缺省是当前的 html ， 并不包含被嵌入的html文档里面的内容。
| 如果我们要 操作 被嵌入的 html 文档 中的元素， 就必须 切换操作范围 到 被嵌入的文档中。怎么切换呢？使用 WebDriver 对象的 switch_to 属性，像这样

::

	wd.switch_to.frame(frame_reference)

| 其中， frame_reference 可以是 frame 元素的属性 name 或者 ID 。如这里，就可以填写 iframe元素的id ‘frame1’ 或者 name属性值 ‘innerFrame’。
| 像这样

::

	wd.switch_to.frame('frame1')

或者

::

	wd.switch_to.frame('innerFrame')

| 也可以填写frame 所对应的 WebElement 对象。我们可以根据frame的元素位置或者属性特性，使用find系列的方法，选择到该元素，得到对应的WebElement对象
| 比如，这里就可以写

::

	wd.switch_to.frame(wd.find_element_by_tag_name("iframe"))

然后，就可以进行后续操作frame里面的元素了。上面的例子的正确代码如下

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')


	# 先根据name属性值 'innerFrame'，切换到iframe中
	wd.switch_to.frame('innerFrame')

	# 根据 class name 选择元素，返回的是 一个列表
	elements = wd.find_elements_by_class_name('plant')

	for element in elements:
	    print(element.text)

| 如果我们已经切换到某个iframe里面进行操作了，那么后续选择和操作界面元素 就都是在这个frame里面进行的。
| 这时候，如果我们又需要操作 主html（我们把最外部的html称之为主html） 里面的元素了呢？怎么切换回原来的主html呢？

| 很简单，写如下代码即可

::

	wd.switch_to.default_content()

| 例如，在上面 代码 操作完 frame里面的元素后， 需要 点击 主html 里面的按钮，就可以这样写

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')

	wd.get('http://cdn1.python3.vip/files/selenium/sample2.html')


	# 先根据name属性值 'innerFrame'，切换到iframe中
	wd.switch_to.frame('innerFrame')

	# 根据 class name 选择元素，返回的是 一个列表
	elements = wd.find_elements_by_class_name('plant')

	for element in elements:
	    print(element.text)

	# 切换回 最外部的 HTML 中
	wd.switch_to.default_content()

	# 然后再 选择操作 外部的 HTML 中 的元素
	wd.find_element_by_id('outerbutton').click()

	wd.quit()

切换到新的窗口
==================================================================================

| 在网页上操作的时候，我们经常遇到，点击一个链接 或者 按钮，就会打开一个 新窗口 。demo 地址: http://cdn1.python3.vip/files/selenium/sample3.html
| 在打开的网页中，点击 链接 “访问bing网站” ， 就会弹出一个新窗口，访问bing网址。如果我们用Selenium写自动化程序 在新窗口里面 打开一个新网址， 并且去自动化操作新窗口里面的元素，会有什么问题呢？
| 问题就在于，即使新窗口打开了， 这时候，我们的 WebDriver对象对应的 还是老窗口，自动化操作也还是在老窗口进行，我们可以运行如下代码验证一下

.. code-block:: python

	from selenium import webdriver

	wd = webdriver.Chrome(r'd:\webdrivers\chromedriver.exe')
	wd.implicitly_wait(10)

	wd.get('http://cdn1.python3.vip/files/selenium/sample3.html')

	# 点击打开新窗口的链接
	link = wd.find_element_by_tag_name("a")
	link.click()

	# wd.title属性是当前窗口的标题栏 文本
	print(wd.title)

| 运行完程序后，最后一行 打印当前窗口的标题栏 文本， 输出内容是 ``白月黑羽测试网页3``
| 说明， 我们的 WebDriver对象指向的还是老窗口，否则的话，运行结果就应该新窗口的标题栏 “微软Bing搜索”.
| 如果我们要到新的窗口里面操作，该怎么做呢？可以使用Webdriver对象的switch_to属性的 window方法，如下所示：

::

	wd.switch_to.window(handle)

| 其中，参数handle需要传入什么呢？WebDriver对象有window_handles 属性，这是一个列表对象， 里面包括了当前浏览器里面所有的窗口句柄。
| 所谓句柄，大家可以想象成对应网页窗口的一个ID，那么我们就可以通过 类似下面的代码，

.. code-block:: python

	for handle in wd.window_handles:
	    # 先切换到该窗口
	    wd.switch_to.window(handle)
	    # 得到该窗口的标题栏字符串，判断是不是我们要操作的那个窗口
	    if 'Bing' in wd.title:
	        # 如果是，那么这时候WebDriver对象就是对应的该该窗口，正好，跳出循环，
	        break

| 上面代码的用意就是：我们依次获取 wd.window_handles 里面的所有 句柄 对象， 并且调用 wd.switch_to.window(handle) 方法，切入到每个窗口，
| 然后检查里面该窗口对象的属性（可以是标题栏，地址栏），判断是不是我们要操作的那个窗口，如果是，就跳出循环。
| 同样的，如果我们在新窗口 操作结束后， 还要回到原来的窗口，该怎么办？我们可以仍然使用上面的方法，依次切入窗口，然后根据 标题栏 之类的属性值判断。
| 还有更省事的方法。因为我们一开始就在 原来的窗口里面，我们知道 进入新窗口操作完后，还要回来，可以事先 保存该老窗口的 句柄，使用如下方法

.. code-block:: python

	# mainWindow变量保存当前窗口的句柄
	mainWindow = wd.current_window_handle

切换到新窗口操作完后，就可以直接像下面这样，将driver对应的对象返回到原来的窗口

.. code-block:: python

	#通过前面保存的老窗口的句柄，自己切换到老窗口
	wd.switch_to.window(mainWindow)

选择框
**********************************************************************************

demo 地址: http://cdn1.python3.vip/files/selenium/test2.html

并且按F12，观察HTML的内容, 常见的选择框包括： radio框、checkbox框、select框

radio 框
==================================================================================

radio框选择选项，直接用WebElement的click方法，模拟用户点击就可以了。比如, 我们要在下面的html中：

* 先打印当前选中的老师名字
* 再选择 小雷老师

.. code-block:: html

	<div id="s_radio">
	  <input type="radio" name="teacher" value="小江老师">小江老师<br>
	  <input type="radio" name="teacher" value="小雷老师">小雷老师<br>
	  <input type="radio" name="teacher" value="小凯老师" checked="checked">小凯老师
	</div>

对应的代码如下

.. code-block:: python

	# 获取当前选中的元素
	element = wd.find_element_by_css_selector(
	  '#s_radio input[checked=checked]')
	print('当前选中的是: ' + element.get_attribute('value'))

	# 点选 小雷老师
	wd.find_element_by_css_selector(
	  '#s_radio input[value="小雷老师"]').click()

checkbox 框
==================================================================================

对checkbox进行选择，也是直接用 WebElement 的 click 方法，模拟用户点击选择。

需要注意的是，要选中checkbox的一个选项，必须 先获取当前该复选框的状态 ，如果该选项已经勾选了，就不能再点击。否则反而会取消选择。

比如, 我们要在下面的html中：选中 小雷老师

.. code-block:: html

	<div id="s_checkbox">
	  <input type="checkbox" name="teacher" value="小江老师">小江老师<br>
	  <input type="checkbox" name="teacher" value="小雷老师">小雷老师<br>
	  <input type="checkbox" name="teacher" value="小凯老师" checked="checked">小凯老师
	</div>

我们的思路可以是这样：

::

	先把 已经选中的选项全部点击一下，确保都是未选状态
	再点击 小雷老师

示例代码

.. code-block:: python

	# 先把 已经选中的选项全部点击一下
	elements = wd.find_elements_by_css_selector(
	  '#s_checkbox input[checked="checked"]')

	for element in elements:
	    element.click()

	# 再点击 小雷老师
	wd.find_element_by_css_selector(
	  "#s_checkbox input[value='小雷老师']").click()

select 框
==================================================================================

| radio框及checkbox框都是input元素，只是里面的type不同而已。select框 则是一个新的select标签，大家可以对照浏览器网页内容查看一下对于Select 选择框， Selenium 专门提供了一个 Select类 进行操作。
| Select类 提供了如下的方法: ``select_by_value``

| 根据选项的 value属性值 ，选择元素。比如，下面的HTML，``<option value="foo">Bar</option>``
| 就可以根据 foo 这个值选择该选项，``s.select_by_value('foo')``  ``select_by_index``

| 根据选项的 次序 （从0开始），选择元素 ``select_by_visible_text`` 
| 根据选项的 可见文本 ，选择元素。比如，下面的HTML，``<option value="foo">Bar</option>``

| 就可以根据 Bar 这个内容，选择该选项 ``s.select_by_visible_text('Bar')`` ``deselect_by_value``

| 根据选项的value属性值， 去除 选中元素 ``deselect_by_index``
| 根据选项的次序，去除 选中元素 ``deselect_by_visible_text``
| 根据选项的可见文本，去除 选中元素 ``deselect_all`` 去除 选中所有元素

Select 单选框
----------------------------------------------------------------------------------

| 对于 select 单选框，操作比较简单：不管原来选的是什么，直接用Select方法选择即可。
| 例如，选择示例里面的小雷老师，示例代码如下

.. code-block:: python

	# 导入Select类
	from selenium.webdriver.support.ui import Select

	# 创建Select对象
	select = Select(wd.find_element_by_id("ss_single"))

	# 通过 Select 对象选中小雷老师
	select.select_by_visible_text("小雷老师")

Select 多选框
----------------------------------------------------------------------------------

| 对于select多选框，要选中某几个选项，要注意去掉原来已经选中的选项。例如，我们选择示例多选框中的 小雷老师 和 小凯老师
| 可以用select类 的deselect_all方法，清除所有 已经选中 的选项。然后再通过 select_by_visible_text方法 选择 小雷老师 和 小凯老师。

示例代码如下：

.. code-block:: python

	# 导入Select类
	from selenium.webdriver.support.ui import Select

	# 创建Select对象
	select = Select(wd.find_element_by_id("ss_multi"))

	# 清除所有 已经选中 的选项
	select.deselect_all()

	# 选择小雷老师 和 小凯老师
	select.select_by_visible_text("小雷老师")
	select.select_by_visible_text("小凯老师")

实战技巧
**********************************************************************************

更多动作
==================================================================================

| 之前我们对web元素做的操作主要是：选择元素，然后 点击元素 或者 输入 字符串。还有没有其他的操作了呢？有。
| 比如：比如 鼠标右键点击、双击、移动鼠标到某个元素、鼠标拖拽等。这些操作，可以通过 Selenium 提供的 ActionChains 类来实现。
| ActionChains 类 里面提供了 一些特殊的动作的模拟，我们可以通过 ActionChains 类的代码查看到，如下所示

|image13|

我们以移动鼠标到某个元素为例。百度首页的右上角，有个 更多产品 选项，如下图所示

|image14|

如果我们把鼠标放在上边，就会弹出 下面的 糯米、音乐、图片 等图标。使用 ActionChains 来 模拟鼠标移动 操作的代码如下：

.. code-block:: python

	from selenium import webdriver

	driver = webdriver.Chrome(r'f:\chromedriver.exe')
	driver.implicitly_wait(5)

	driver.get('https://www.baidu.com/')

	from selenium.webdriver.common.action_chains import ActionChains

	ac = ActionChains(driver)

	# 鼠标移动到 元素上
	ac.move_to_element(
	    driver.find_element_by_css_selector('[name="tj_briicon"]')
	).perform()

直接执行 javascript
==================================================================================

我们可以直接让浏览器运行一段javascript代码，并且得到返回值，如下

.. code-block:: python

	# 直接执行 javascript，里面可以直接用return返回我们需要的数据
	nextPageButtonDisabled = driver.execute_script(
	    '''
	    ele = document.querySelector('.soupager > button:last-of-type');
	    return ele.getAttribute('disabled')
	    ''')

	# 返回的数据转化为Python中的数据对象进行后续处理
	if nextPageButtonDisabled == 'disabled': # 是最后一页
	    return True
	else: # 不是最后一页
	    return False

冻结界面
==================================================================================

有些网站上面的元素， 我们鼠标放在上面，会动态弹出一些内容。比如，百度首页的右上角，有个 更多产品 选项，如下图所示

|image14|

| 如果我们把鼠标放在上边，就会弹出 下面的 糯米、音乐、图片 等图标。如果我们要用 selenium 自动化 点击 糯米图标，就需要 F12 查看这个元素的特征。
| 但是 当我们的鼠标 从 糯米图标 移开， 这个 栏目就整个消失了， 就没法 查看 其对应的 HTML。怎么办？可以如下图所示：

|image15|

| 在 开发者工具栏 console 里面执行如下js代码: ``setTimeout(function(){debugger}, 5000)``

| 这句代码什么意思呢？表示在 5000毫秒后，执行 debugger 命令
| 执行该命令会 浏览器会进入debug状态。 debug状态有个特性， 界面被冻住， 不管我们怎么点击界面都不会触发事件。
| 所以，我们可以在输入上面代码并回车 执行后， 立即 鼠标放在界面 右上角 更多产品处。

| 这时候，就会弹出 下面的 糯米、音乐、图片 等图标。
| 然后，我们仔细等待 5秒 到了以后， 界面就会因为执行了 debugger 命令而被冻住。
| 然后，我们就可以点击 开发者工具栏的 查看箭头， 再去 点击 糯米图标 ，查看其属性了。

弹出对话框
==================================================================================

| 有的时候，我们经常会在操作界面的时候，出现一些弹出的对话框。请点击打开这个网址: http://cdn1.python3.vip/files/selenium/test4.html
| 分别点击界面的3个按钮，你可以发现：弹出的对话框有三种类型，分别是 Alert（警告信息）、confirm（确认信息）和prompt（提示输入）

Alert
----------------------------------------------------------------------------------

| Alert 弹出框，目的就是显示通知信息，只需用户看完信息后，点击 OK（确定） 就可以了。那么，自动化的时候，代码怎么模拟用户点击 OK 按钮呢？
| selenium提供如下方法进行操作: ``driver.switch_to.alert.accept()``

| 注意：如果我们不去点击它，页面的其它元素是不能操作的。 {: .notice–info}
| 如果程序要获取弹出对话框中的信息内容， 可以通过 如下代码 ``driver.switch_to.alert.text``

示例代码如下

.. code-block:: python

	from selenium import webdriver
	driver = webdriver.Chrome()
	driver.implicitly_wait(5)
	driver.get('http://cdn1.python3.vip/files/selenium/test4.html')

	# --- alert ---
	driver.find_element_by_id('b1').click()

	# 打印 弹出框 提示信息
	print(driver.switch_to.alert.text) 

	# 点击 OK 按钮
	driver.switch_to.alert.accept()

Confirm
----------------------------------------------------------------------------------

| Confirm弹出框，主要是让用户确认是否要进行某个操作。

| 比如：当管理员在网站上选择删除某个账号时，就可能会弹出 Confirm弹出框， 要求确认是否确定要删除。
| Confirm弹出框 有两个选择供用户选择，分别是 OK 和 Cancel， 分别代表 确定 和 取消 操作。
| 那么，自动化的时候，代码怎么模拟用户点击 OK 或者 Cancel 按钮呢？selenium提供如下方法进行操作

| 如果我们想点击 OK 按钮， 还是用刚才的 accept方法，如下 ``driver.switch_to.alert.accept()``
| 如果我们想点击 Cancel 按钮， 可以用 dismiss方法，如下 ``driver.switch_to.alert.dismiss()``

示例代码如下

.. code-block:: python

	from selenium import webdriver
	driver = webdriver.Chrome()
	driver.implicitly_wait(5)
	driver.get('http://cdn1.python3.vip/files/selenium/test4.html')

	# --- confirm ---
	driver.find_element_by_id('b2').click()

	# 打印 弹出框 提示信息
	print(driver.switch_to.alert.text)

	# 点击 OK 按钮 
	driver.switch_to.alert.accept()

	driver.find_element_by_id('b2').click()

	# 点击 取消 按钮
	driver.switch_to.alert.dismiss()

Prompt
----------------------------------------------------------------------------------

| 出现 Prompt 弹出框 是需要用户输入一些信息，提交上去。
| 比如：当管理员在网站上选择给某个账号延期时，就可能会弹出 Prompt 弹出框， 要求输入延期多长时间。

| 可以调用如下方法 ``driver.switch_to.alert.send_keys()``

示例代码如下

.. code-block:: python

	from selenium import webdriver
	driver = webdriver.Chrome()
	driver.implicitly_wait(5)
	driver.get('http://cdn1.python3.vip/files/selenium/test4.html')

	# --- prompt ---
	driver.find_element_by_id('b3').click()

	# 获取 alert 对象
	alert = driver.switch_to.alert

	# 打印 弹出框 提示信息
	print(alert.text)

	# 输入信息，并且点击 OK 按钮 提交
	alert.send_keys('web自动化 - selenium')
	alert.accept()

	# 点击 Cancel 按钮 取消
	driver.find_element_by_id('b3').click()
	alert = driver.switch_to.alert
	alert.dismiss()

.. tip::

	有些弹窗并非浏览器的alert 窗口，而是html元素，这种对话框，只需要通过之前介绍的选择器选中并进行相应的操作就可以了。 {: .notice–info}

其他技巧
==================================================================================

下面是一些其他的 Selenium 自动化技巧

窗口大小
----------------------------------------------------------------------------------

| 有时间我们需要获取窗口的属性和相应的信息，并对窗口进行控制
| 获取窗口大小: ``driver.get_window_size()``; 改变窗口大小: ``driver.set_window_size(x, y)``

获取当前窗口标题
----------------------------------------------------------------------------------

浏览网页的时候，我们的窗口标题是不断变化的，可以使用WebDriver的title属性来获取当前窗口的标题栏字符串。``driver.title``

获取当前窗口URL地址
----------------------------------------------------------------------------------

``driver.current_url``

例如，访问网易，并获取当前窗口的标题和URL

.. code-block:: python

	from selenium import  webdriver

	driver = webdriver.Chrome()
	driver.implicitly_wait(5)

	# 打开网站
	driver.get('https://www.163.com')

	# 获取网站标题栏文本
	print(driver.title) 

	# 获取网站地址栏文本
	print(driver.current_url) 

截屏
----------------------------------------------------------------------------------

| 有的时候，我们需要把浏览器屏幕内容保存为图片文件。

| 比如，做自动化测试时，一个测试用例检查点发现错误，我们可以截屏为文件，以便测试结束时进行人工核查。
| 可以使用 WebDriver 的 get_screenshot_as_file方法来截屏并保存为图片。

.. code-block:: python

	from selenium import  webdriver

	driver = webdriver.Chrome()
	driver.implicitly_wait(5)

	# 打开网站
	driver.get('https://www.baidu.com/')

	# 截屏保存为图片文件
	driver.get_screenshot_as_file('1.png')

手机模式
----------------------------------------------------------------------------------

我们可以通过 desired_capabilities 参数，指定以手机模式打开chrome浏览器. 参考代码，如下

.. code-block:: python

	from selenium import webdriver

	mobile_emulation = { "deviceName": "Nexus 5" }

	chrome_options = webdriver.ChromeOptions()

	chrome_options.add_experimental_option("mobileEmulation", mobile_emulation)

	driver = webdriver.Chrome( desired_capabilities = chrome_options.to_capabilities())

	driver.get('http://www.baidu.com')

	input()
	driver.quit()

上传文件
----------------------------------------------------------------------------------

| 有时候，网站操作需要上传文件。比如，著名的在线图片压缩网站： https://tinypng.com/
| 通常，网站页面上传文件的功能，是通过 type 属性 为 file 的 HTML input 元素实现的。

| 如下所示： ``<input type="file" multiple="multiple">``
| 使用selenium自动化上传文件，我们只需要定位到该input元素，然后通过 send_keys 方法传入要上传的文件路径即可。

如下所示：

.. code-block:: python

	# 先定位到上传文件的 input 元素
	ele = wd.find_element_by_css_selector('input[type=file]')

	# 再调用 WebElement 对象的 send_keys 方法
	ele.send_keys(r'h:\g02.png')

如果需要上传多个文件，可以多次调用send_keys，如下

.. code-block:: python

	ele = wd.find_element_by_css_selector('input[type=file]')
	ele.send_keys(r'h:\g01.png')
	ele.send_keys(r'h:\g02.png')

自动化 Edge 浏览器
==================================================================================

| 自动化基于Chromium内核的 微软最新Edge浏览器，首先需要查看Edge的版本。
| 点击菜单 帮助和反馈 > 关于Microsoft Edge ，在弹出界面中，查看到版本，比如: 版本 79.0.309.71 (官方内部版本) (64 位)

| 然后 点击这里(https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/#downloads)，打开Edge浏览器驱动下载网页 ，并选择下载对应版本的驱动。
| 在自动化代码中，指定使用Edge Webdriver类，并且指定 Edge 驱动路径，如下所示

.. code-block:: python

	from selenium import webdriver

	driver = webdriver.Edge(r'd:\tools\webdrivers\msedgedriver.exe')

	driver.get('http://www.51job.com')

自动化 Electron 程序
==================================================================================

| Electron程序都是基于基于Chromium技术开发的，所以基本也可以用Chromedriver驱动自动化。要自动化，首先需要得到内置 Chromium的版本号。
| 向开发人员查询打开 Dev Tools 窗口的快捷键（通常是ctrl + Shift + I），打开Dev Tools 窗口后， 在 Console tab中输入 如下语句，查看版本

::

	> navigator.appVersion.match(/.*Chrome\/([0-9\.]+)/)[1]  "79.0.3945.130"

| 然后去 chromedriver下载网址(https://chromedriver.storage.googleapis.com/index.html) ，下载对应版本的驱动。
| 在自动化程序中需要指定打开的可执行程序为Electron程序，而不是 Chrome浏览器。

如下所示

.. code-block:: python

	from selenium import webdriver
	from selenium.webdriver.chrome.options import Options

	ops = Options()

	# 指定Electron程序路径
	ops.binary_location = r"C:\electronAPP.exe"
	driver = webdriver.Chrome(r"e:\chromedriver.exe", options = ops)

Xpath 选择器
**********************************************************************************

Xpath 语法简介
==================================================================================

| 前面我们学习了CSS 选择元素。大家可以发现非常灵活、强大。还有一种 灵活、强大 的选择元素的方式，就是使用 Xpath 表达式。
| XPath (XML Path Language) 是由国际标准化组织W3C指定的，用来在 XML 和 HTML 文档中选择节点的语言。
| 目前主流浏览器 (chrome、firefox，edge，safari) 都支持XPath语法，xpath有 1 和 2 两个版本，目前浏览器支持的是 xpath 1的语法。

| 既然已经有了CSS，为什么还要学习 Xpath呢？ 因为有些场景 用 css 选择web 元素 很麻烦，而xpath 却比较方便。
| 另外 Xpath 还有其他领域会使用到，比如 爬虫框架 Scrapy， 手机App框架 Appium。

| 示例网址: http://cdn1.python3.vip/files/selenium/test1.html

| 按F12打开调试窗口，点击 Elements标签。要验证 Xpath 语法是否能成功选择元素，也可以像 验证 CSS 语法那样，按组合键 Ctrl + F ，就会出现 搜索框
| xpath 语法中，整个HTML文档根节点用’/‘表示，如果我们想选择的是根节点下面的html节点，则可以在搜索框输入

``/html``

| 如果输入下面的表达式

``/html/body/div``

| 这个表达式表示选择html下面的body下面的div元素。注意 / 有点像 CSS中的 > , 表示直接子节点关系。

绝对路径选择
-----------------------------------------------------------------------------------

| 从根节点开始的，到某个节点，每层都依次写下来，每层之间用 / 分隔的表达式，就是某元素的 绝对路径
| 上面的xpath表达式 /html/body/div ，就是一个绝对路径的xpath表达式， 等价于 css表达式 html>body>div
| 自动化程序要使用Xpath来选择web元素，应该调用 WebDriver对象的方法 find_element_by_xpath 或者 find_elements_by_xpath，像这样：

``elements = driver.find_elements_by_xpath("/html/body/div")``

相对路径选择
-----------------------------------------------------------------------------------

| 有的时候，我们需要选择网页中某个元素， 不管它在什么位置。比如，选择示例页面的所有标签名为 div 的元素，如果使用css表达式，直接写一个 div 就行了。
| 那xpath怎么实现同样的功能呢？ xpath需要前面加 // , 表示从当前节点往下寻找所有的后代元素,不管它在什么位置。
| 所以xpath表达式，应该这样写： //div, ‘//’ 符号也可以继续加在后面,比如，要选择 所有的 div 元素里面的 所有的 p 元素 ，不管div 在什么位置，也不管p元素在div下面的什么位置，则可以这样写 //div//p

| 对应的自动化程序如下

``elements = driver.find_elements_by_xpath("//div//p")``

| 如果使用CSS选择器，对应代码如下

``elements = driver.find_elements_by_css_selector("div p")``

| 如果，要选择 所有的 div 元素里面的 直接子节点 p ， xpath，就应该这样写了 //div/p, 如果使用CSS选择器，则为 div > p

通配符
-----------------------------------------------------------------------------------

| 如果要选择所有div节点的所有直接子节点，可以使用表达式 //div/*, * 是一个通配符，对应任意节点名的元素，等价于CSS选择器 div > *
| 代码如下：

.. code-block:: python

	elements = driver.find_elements_by_xpath("//div/*")
	for element in elements:
	    print(element.get_attribute('outerHTML'))

根据属性选择
==================================================================================

Xpath 可以根据属性来选择元素。根据属性来选择元素 是通过 这种格式来的 [@属性名='属性值']

注意：

* 属性名注意前面有个@
* 属性值一定要用引号， 可以是单引号，也可以是双引号

根据id属性选择
-----------------------------------------------------------------------------------

选择 id 为 west 的元素，可以这样 //*[@id='west']

根据 class 属性选择
-----------------------------------------------------------------------------------

选择所有 select 元素中 class为 single_choice 的元素，可以这样 //select[@class='single_choice'], 如果一个元素class 有多个，比如

.. code-block:: python

	<p id="beijing" class='capital huge-city'>
	    北京    
	</p>

如果要选 它， 对应的 xpath 就应该是 //p[@class="capital huge-city"], 不能只写一个属性，像这样 //p[@class="capital"] 则不行

根据其他属性
-----------------------------------------------------------------------------------

同样的道理，我们也可以利用其它的属性选择, 比如选择 具有multiple属性的所有页面元素 ，可以这样 //*[@multiple]

属性值包含字符串
-----------------------------------------------------------------------------------

| 要选择 style属性值 包含 color 字符串的 页面元素 ，可以这样 //*[contains(@style,'color')]
| 要选择 style属性值 以 color 字符串 开头 的 页面元素 ，可以这样 //*[starts-with(@style,'color')]
| 要选择 style属性值 以 某个 字符串 结尾 的 页面元素 ，大家可以推测是 //*[ends-with(@style,'color')]， 但是，很遗憾，这是xpath 2.0 的语法 ，目前浏览器都不支持

按次序选择
==================================================================================

前面学过 css 表达式可以根据元素在父节点中的次序选择， 非常实用。xpath也可以根据次序选择元素。 语法比css更简洁，直接在方括号中使用数字表示次序

某类型 第几个 子元素
-----------------------------------------------------------------------------------

| 比如, 要选择 p类型第2个的子元素，就是 ``//p[2]``
| 注意，选择的是 p类型第2个的子元素 ， 不是 第2个子元素，并且是p类型 。

| 注意体会区别, 再比如，要选取父元素为div 中的 p类型 第2个 子元素 ``//div/p[2]``

第几个子元素
-----------------------------------------------------------------------------------

也可以选择第2个子元素，不管是什么类型，采用通配符, 比如 选择父元素为div的第2个子元素，不管是什么类型. ``//div/*[2]``

某类型 倒数第几个 子元素
-----------------------------------------------------------------------------------

当然也可以选取倒数第几个子元素, 比如：

* 选取p类型倒数第1个子元素, ``//p[last()]``
* 选取p类型倒数第2个子元素, ``//p[last()-1]``
* 选择父元素为div中p类型倒数第3个子元素 ``//div/p[last()-2]``

范围选择
-----------------------------------------------------------------------------------

xpath还可以选择子元素的次序范围。比如，

* 选取option类型第1到2个子元素 ``//option[position()<=2]`` 或者 ``//option[position()<3]``
* 选择class属性为multi_choice的前3个子元素 ``//*[@class='multi_choice']/*[position()<=3]``
* 选择class属性为multi_choice的后3个子元素 ``//*[@class='multi_choice']/*[position()>=last()-2]``

为什么不是 last()-3 呢？ 因为

::

	last() 本身代表最后一个元素
	last()-1 本身代表倒数第2个元素
	last()-2 本身代表倒数第3个元素

组选择、父节点、兄弟节点
==================================================================================

组选择
-----------------------------------------------------------------------------------

| css有组选择，可以同时使用多个表达式，多个表达式选择的结果都是要选择的元素, css 组选择，表达式之间用 逗号 隔开
| xpath也有组选择， 是用 竖线 隔开多个表达式, 比如，要选所有的option元素 和所有的 h4 元素，可以使用

``//option | //h4`` 等同于CSS选择器 option , h4

再比如，要选所有的 class 为 single_choice 和 class 为 multi_choice 的元素，可以使用

//*[@class='single_choice'] | //*[@class='multi_choice'] 等同于CSS选择器 ``.single_choice , .multi_choice``

选择父节点
-----------------------------------------------------------------------------------

| xpath可以选择父节点， 这是css做不到的。某个元素的父节点用 ``/..`` 表示, 比如，要选择 id 为 china 的节点的父节点，可以这样写 ``//*[@id='china']/..`` 。
| 当某个元素没有特征可以直接选择，但是它有子节点有特征， 就可以采用这种方法，先选择子节点，再指定父节点。
| 还可以继续找上层父节点，比如 ``//*[@id='china']/../../..``

兄弟节点选择
-----------------------------------------------------------------------------------

| 前面学过 css选择器，要选择某个节点的后续兄弟节点，用 波浪线, xpath也可以选择 后续 兄弟节点，用这样的语法 following-sibling::
| 比如，要选择 class 为 single_choice 的元素的所有后续兄弟节点 //*[@class='single_choice']/following-sibling::*, 等同于CSS选择器 .single_choice ~ *
| 如果，要选择后续节点中的div节点， 就应该这样写 //*[@class='single_choice']/following-sibling::div

| xpath还可以选择 前面的 兄弟节点，用这样的语法 preceding-sibling::
| 比如，要选择 class 为 single_choice 的元素的所有前面的兄弟节点 //*[@class='single_choice']/preceding-sibling::*
| 而CSS选择器目前还没有方法选择前面的 兄弟节点

| 要了解更多Xpath选择语法，可以点击这里，打开Xpath选择器参考手册: http://www.w3school.com.cn/xpath/index.asp 

selenium 注意点
==================================================================================

我们来看一个例子: 

* 先选择示例网页中，id是china的元素
* 然后通过这个元素的WebElement对象，使用find_elements_by_xpath，选择里面的p元素，

.. code-block:: python

	# 先寻找id是china的元素
	china = wd.find_element_by_id('china')

	# 再选择该元素内部的p元素
	elements = china.find_elements_by_xpath('//p')

	# 打印结果
	for element in elements:
	    print('----------------')
	    print(element.get_attribute('outerHTML'))

运行发现，打印的 不仅仅是 china内部的p元素， 而是所有的p元素。要在某个元素内部使用xpath选择元素， 需要 在xpath表达式最前面加个点 。

::

	elements = china.find_elements_by_xpath('.//p')

Selenium 完整案例
==================================================================================

以下为完成案例, 供以后参考使用 

.. code-block:: python

	from selenium import webdriver
	from selenium.webdriver.support.ui import Select
	import re

	# 获取事件单号
	def get_event_list():
	    # 获取事件单列表
	    for gongdan_element in gongdan_elements:
	        try:
	            id_element = gongdan_element.find_element_by_class_name('id')
	            assign_name_element = gongdan_element.find_element_by_class_name('assignName')
	            # print(id_element.text + '==> ' + assign_name_element.text)

	            if (assign_name_element.text == 'xxx'):
	                # print(id_element.text + '==> ' + assign_name_element.text)
	                event_list.append(id_element.text)
	        except Exception as ex:
	            print(ex)
	            pass

	# 获取订单列表; 返回 时间单-订单 元组信息
	def get_event_order_list():
	    global element
	    # 循环事件单号, 获得相应的订单号
	    for event_orderid in event_list:
	        # 临时打开事件单页面, 查找到订单号后关闭
	        wd.get('http://web.itsv.xxx.com/incident/detail?id=' + event_orderid)

	        element = wd.find_element_by_css_selector('#title')
	        # print(event_orderid + '-----> ' + element.get_attribute('value'))
	        match_obj = re.findall(r'[0-9]{10,12}', element.get_attribute('value'))

	        # 获得客服 erp 信息
	        wd.find_element_by_xpath('/html/body/div[1]/ul/li[2]').click()
	        customerName = wd.find_element_by_xpath('/html/body/div[1]/div/div[2]/div/div[1]/div[1]/div/input[2]').get_attribute('value')
	        customerErp = wd.find_element_by_xpath('/html/body/div[1]/div/div[2]/div/div[1]/div[3]/div/input').get_attribute('value')

	        # 正则提取到的 订单号
	        orderid_extract = None
	        if match_obj:
	            # print("==>  订单号: ", match_obj[0])
	            orderid_extract = match_obj[0]
	        else:
	            element = wd.find_element_by_css_selector('#desc')
	            # print('=======> ' + element.get_attribute('value'))
	            match_obj = re.findall(r'[0-9]{10,12}', element.get_attribute('value'), 0)
	            if match_obj:
	                # print("==>  订单号: ", match_obj[0])
	                orderid_extract = match_obj[0]

	        # 如果正常提取到 orderid_extract 就进行记录
	        if orderid_extract:
	            detail_text = wd.find_element_by_id('solution').get_attribute('value')
	            # 拼购返现扣款查询接口调用异常
	            try:
	                if '拼购返现扣款查询接口调用异常' in detail_text:
	                    result_dict[(event_orderid, orderid_extract, customerName, customerErp)] = ' =====> 分析结果: xxx 订单, 拆单后, 从子单上取消订单。由 xxx 协助解决。'
	                else:
	                    event_order_list.append((event_orderid, match_obj[0], customerName, customerErp))
	            except:
	                pass

	            # 运费券没退的问题
	            try:
	                desc_text = wd.find_element_by_id('desc').get_attribute('value')
	                title_text = wd.find_element_by_id('title').get_attribute('value')
	                if '运费券' in detail_text or '运费券' in desc_text or '运费券' in title_text:
	                    result_dict[(event_orderid, orderid_extract, customerName, customerErp)] = ' =====> 分析结果: 运费券没退, 找 xxx 解决.'
	            except:
	                pass


	from selenium.webdriver.chrome.options import Options

	options = Options()
	# options.headless = True
	options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")

	# 调试浏览器
	wd = webdriver.Chrome(options=options, executable_path=r'/Users/xxx/技术资料/python/chromedriver')
	# wd = webdriver.Chrome(executable_path=r'/Users/xxx/技术资料/python/chromedriver')
	# 设置最大等待时长为 500 毫秒
	wd.implicitly_wait(0.5)

	# 存放事件单号
	event_list = []  ## 空列表

	# 打开运营服务台，查看分配的工单, 跳转到登录页面, 输入用户名和密码
	wd.get('http://web.itsv.xxx.com/')

	# 获得应用服务台页面句柄
	yyfwtWin = wd.current_window_handle

	try:
	    # 切换 frame
	    wd.switch_to.frame(wd.find_element_by_xpath('//*[@id="admin-body"]/iframe'))
	except:
	    wd.find_element_by_id('username').send_keys('xxxxxx')
	    wd.find_element_by_id('password').send_keys('xxxxxx\n')

	    # 切换 frame
	    wd.switch_to.frame(wd.find_element_by_xpath('//*[@id="admin-body"]/iframe'))
	    pass

	# # 设置分页为 50
	pageSelect = Select(wd.find_element_by_id('listpagesize'))
	pageSelect.select_by_value('50')

	from time import sleep
	sleep(2)

	# 获取工单号
	# element = wd.find_element_by_id('incident-list-table')
	# gongdan_elements = element.find_elements_by_tag_name('tr')
	gongdan_elements = wd.find_elements_by_xpath('/html/body/div[1]/div/div/fieldset/div/div[1]/table/tbody/tr[*]')

	# 获取事件单号
	get_event_list()

	# 打印事件数
	# print(len(event_list))

	# 存放事件单号-订单列表元组
	event_order_list = []  ## 空列表

	# 存储结果信息
	result_dict = {}

	# 获取订单列表
	get_event_order_list()

	# 测试数据
	# event_order_list.clear()
	# event_order_list.append(('xxx', 'xxx3880', 'xx', 'xx'))

	# 如果找到则标记为 True
	success_flag = False

	# 全球购 与 ...完成 诊断
	# 全球购枚举值
	quanqiugou_enums = {'8138', '8137', '8136', '8135', '1904', '1903', '1902', '1901', '1900', '1899', '1898', '1897',
	                    '1896',
	                    '1764', '1763', '1762', '1761', '1760', '1759', '1758', '1757', '1756', '1755', '1754'}

	# 支付方式 实付枚举值[xxxxxx]
	shifu_enums = {'[6]', '[8]', '[10]', '[7]', '[9]', '[30]', '[11]', '[56]', '[86]', '[89]', '[90]', '[91]'}

	# 常见问题诊断
	def qaDiagnosis():
	    # 全球购问题诊断
	    global pay_enum_eles, pay_enum_ele, success_flag
	    wd.find_element_by_id('orderId').send_keys(event_order[1] + '\n')
	    # 支付枚举
	    pay_enum_eles = wd.find_elements_by_xpath('/html/body/div[1]/div/div[3]/div[2]/table/tbody/tr[*]/td[2]')
	    for pay_enum_ele in pay_enum_eles:
	        if pay_enum_ele.text in quanqiugou_enums:
	            success_flag = True
	            result_dict[event_order] = ' =====> 分析结果: 全球购...订单, 拆单后, 从子单上取消订单。由 xx 协助解决。'
	            return

	    # 已支付的信息 分支一: 已支付和已完成...值相同
	    have_pay_info_ele = wd.find_element_by_xpath('/html/body/div[1]/div/div[3]/div[2]/table/tbody/tr[last()]/td/label')
	    # 已...信息
	    sucess_refund_ele = wd.find_element_by_xpath('/html/body/div[1]/div/div[4]/div/table/tbody[2]/tr/td/label[2]')

	    match_obj = re.findall('已支付：([0-9.]{2,20})', have_pay_info_ele.text)
	    if match_obj and sucess_refund_ele.text == match_obj[0]:
	        success_flag = True
	        result_dict[event_order] = ' =====> 分析结果: 已...完成，请核实(已支付和已完成...值相同)。'
	        return

	    # 已支付的信息 分支一: 实付部分和已完成...值相同
	    pay_enum_eles = wd.find_elements_by_xpath('/html/body/div[1]/div/div[3]/div[2]/table/tbody/tr[*]/td[2]')

	    have_pay_info_ele = wd.find_element_by_xpath('/html/body/div[1]/div/div[3]/div[2]/table/tbody/tr[last()]/td/label')
	    # 已...信息
	    sucess_refund_ele = wd.find_element_by_xpath('/html/body/div[1]/div/div[4]/div/table/tbody[2]/tr/td/label[2]')

	    match_obj = re.findall('已支付：([0-9.]{2,20})', have_pay_info_ele.text)
	    if match_obj and sucess_refund_ele.text == match_obj[0]:
	        success_flag = True
	        result_dict[event_order] = ' =====> 分析结果: 已...完成，请核实(已支付和已完成...值相同)。'
	        return

	    # 已支付的信息 分支二: 实付部分已全退
	    # 支付类型枚举
	    customer_pay_info_eles = wd.find_elements_by_xpath(
	        '/html/body/div[1]/div/div[3]/div[2]/table/tbody/tr[position()<last()]')
	    total_shifu_cost = 0.0
	    for customer_pay_info_ele in customer_pay_info_eles:
	        pay_type_enum_ele = customer_pay_info_ele.find_element_by_xpath('td[1]')

	        # 获得实付部分金额
	        match_obj = re.findall('[[0-9]{0,10}]', pay_type_enum_ele.text)
	        if match_obj and match_obj[0] in shifu_enums:
	            shifu_money = float(customer_pay_info_ele.find_element_by_xpath('td[5]').text)
	            if shifu_money > 0:
	                total_shifu_cost += shifu_money

	    if total_shifu_cost <= float(sucess_refund_ele.text):
	        success_flag = True
	        result_dict[event_order] = ' =====> 分析结果: 已...完成，请核实(已完成实付部分...)。'
	        return

	    # 预售订单(未付尾款)不退定金 1. 对账应收 -- 对账实收(0.00 -- 0.00) 2. 订单详细信息 有预售信息
	    try:
	        # 对账信息 0.00 -- 0.00 诊断
	        duizhang_info = wd.find_element_by_xpath('/html/body/div[1]/div/div[2]/div/table/tbody/tr[3]/td[4]').text
	        if duizhang_info == '0.00 -- 0.00':
	            wd.get('http://om.xxx.com/detail?orderId=' + event_order[1])

	            # 预售标题栏
	            yushou_title = wd.find_element_by_xpath('/html/body/div[2]/div[3]/div[12]/table/tbody/tr[1]/td').text
	            if yushou_title == '预售信息':
	                success_flag = True
	                result_dict[event_order] = ' =====> 分析结果:预售订单(未付尾款), 不退定金。 '
	                return
	    except:
	        pass

	    # 赔付金额超过 实付 问题
	    try:
	        wd.get('http://rcweb-fm.xxx.com/rest/demo/getPayout3?orderId=' + event_order[1])
	        match_obj = re.findall('"totalAmount":([0-9.]{0,20})', wd.find_element_by_xpath('/html/body').text)
	        if match_obj and total_shifu_cost < float(match_obj[0]):
	            success_flag = True
	            result_dict[event_order] = '''
	             =====> 分析结果:您好，经查，订单有赔付，且赔付金额超出了客服现金支付的金额，因为如果删单后，系统会自动返还虚拟资产，而产生资损，故系统限制无法线上...；
	            备注后联系咚咚“IT服务”强制删单，退还虚拟资产部分的金额，现金支付的部分可以采取赔付的方式...；把控资金不要多退或者少退
	            '''
	            return
	    except:
	        pass

	for event_order in event_order_list:
	    # print('订单号==> ' + event_order[1])

	    # 冲销诊断, 需要手动改数
	    wd.get(
	        'http://10.178.55.166/index.html?sso_service_ticket=BJ.c2d7c12e57d04fc7a42c05d9b894a30d#/wholeRefundDataQuery')
	    orderid_element = wd.find_element_by_css_selector(
	        '#app > div > div.ivu-layout.ivu-layout-has-sider > div.right-content.ivu-layout-content > div > form > div.ivu-form-item.ivu-form-item-required > div > div > input')
	    orderid_element.send_keys(event_order[1])
	    query_btn = wd.find_element_by_css_selector(
	        '#app > div > div.ivu-layout.ivu-layout-has-sider > div.right-content.ivu-layout-content > div > form > div:nth-child(2) > div > button')
	    query_btn.click()

	    # 冲销诊断, 需要手动改数
	    try:
	        # ...状态
	        refund_status_ele = wd.find_element_by_xpath(
	            '/html/body/div/div/div[2]/div[2]/div/div/div[4]/div[2]/div/div/div/div[1]/div[2]/table/tbody/tr/td[9]/div/span')
	        if '待冲销' in refund_status_ele.text:
	            success_flag = True
	            result_dict[event_order] = ' =====> 分析结果: 待冲销, 需手动修改后台数据。'
	    except:
	        pass

	    # 流程未到...侧
	    try:
	        nodata_info = wd.find_element_by_xpath('/html/body/div[1]/div/div[2]/div[2]/div/div/div[1]/div[2]/div/div/div/div[1]/div[3]/table/tbody/tr/td/span').text
	        if nodata_info == '暂无数据':
	            # success_flag = True
	            # 后续的诊断可覆盖此项 非最终诊断结果
	            result_dict[event_order] = ' =====> 分析结果: ...控制无数据,流程未到...侧。'
	    except:
	        pass

	    # 赔付项诊断
	    if not success_flag:
	        try:
	            backup_info = wd.find_element_by_xpath(
	                '/html/body/div/div/div[2]/div[2]/div/div/div[7]/div[2]/div/div/div/div[1]/div[2]/table/tbody/tr/td[8]').text
	            cost_info = wd.find_element_by_xpath(
	                '/html/body/div/div/div[2]/div[2]/div/div/div[7]/div[2]/div/div/div/div[1]/div[2]/table/tbody/tr/td[7]').text
	            success_flag = True
	            result_dict[event_order] = ' =====> ' + backup_info + ' ' + cost_info + ' 元。'
	        except:
	            pass

	    # 0元问题诊断
	    if not success_flag:
	        try:
	            wd.get('http://rcweb-fm.xxx.com/rest/sys/queryAfterSaleDBTable')
	            wd.find_element_by_xpath('/html/body/form/table/tbody/tr/td[4]/input').send_keys(event_order[1])
	            wd.find_element_by_xpath('/html/body/form/div[2]/div[2]/div[12]/div[1]').click()
	            # 业务类型 + ...金额
	            buss_type = wd.find_element_by_xpath('/html/body/form/div[2]/div[2]/div[12]/div[2]/table/tbody[1]/tr/td[29]').text
	            refund_mount = wd.find_element_by_xpath('/html/body/form/div[2]/div[2]/div[12]/div[2]/table/tbody[1]/tr/td[16]').text
	            if buss_type == '54' and refund_mount == '0':
	                success_flag = True
	                result_dict[event_order] = ' =====> 0 元问题。 由 ...... 负责解决.'
	        except:
	            pass

	    # 常见问题诊断
	    if not success_flag:
	        try:
	            wd.get('http://orderbank.360buy.com/')
	            try:
	                wd.switch_to.frame('mainFrame')

	                # 常见问题诊断
	                qaDiagnosis()
	            except Exception as ex:
	                # print(ex)
	                # 需要登录
	                wd.switch_to.frame('mainFrame')
	                wd.find_element_by_id('username').send_keys('Llz')
	                wd.find_element_by_id('password').send_keys('xxxxxx\n')

	                # 常见问题诊断
	                qaDiagnosis()
	                pass
	        except:
	            pass

	    # 诊断完一个订单后, 重置标志位
	    success_flag = False

	for result_key in result_dict.keys():
	    print('事件单号: ' + result_key[0] + ', 订单号: ' + result_key[1] + ', 客服: ' + result_key[2] + ', ' + result_key[3] + result_dict[result_key])

	# wd.quit()

	pass

Selenium FAQ
**********************************************************************************

python/爬虫：用 selenium 控制已打开的浏览器
==================================================================================

加参数启动chrome， ``./Google\ Chrome --remote-debugging-port=9222``

``chrome.exe [--headless] --remote-debugging-port=9222 --user-data-dir="C:\selenum\AutomationProfile"``

::

	--remote-debugging-port: 可以指定任何打开的端口，selenium启动时要用这个端口。
	--user-data-dir: 指定创建新chrome配置文件的目录。它确保在单独的配置文件中启动chrome，不会污染你的默认配置文件。
	--headless: 无头模式

启动 selenium 程序

.. code-block:: python

	from selenium import webdriver
	from selenium.webdriver.chrome.options import Options

	chrome_options = Options()
	chrome_options.add_experimental_option("debuggerAddress", "127.0.0.1:9222")
	# driver就是当前浏览器窗口
	driver = webdriver.Chrome(chrome_options=chrome_options)

	# 获取当前打开的网页html内容
	html = driver.page_source

.. tip::
	
	mac 环境上启动 /Applications/Google Chrome.app/Contents/MacOS/Google Chrome --remote-debugging-port=9222


.. |image0| image:: /_static/python/tut_20200504110845_36.png
.. |image1| image:: /_static/python/094633_aa63fe7e_2066991.png
.. |image2| image:: /_static/python/WX20201024-101256@2x.webp
.. |image3| image:: /_static/python/36257654_38093280-0935e954-339d-11e8-882a-8fb3dd534d81.png
.. |image4| image:: /_static/python/36257654_38093410-73d3966c-339d-11e8-9d6d-ade673f51d76.png
.. |image5| image:: /_static/python/36257654_38093785-8c917100-339e-11e8-8cd6-3ccebbe20407.png
.. |image6| image:: /_static/python/336257654_38163256-b8ca567a-3523-11e8-9ec7-ce4a667c76ad.png
.. |image7| image:: /_static/python/36257654_64305834-39fd8880-cfc3-11e9-9299-d1ec45405e1a.png
.. |image8| image:: /_static/python/36257654_62668791-c36b6c00-b9bf-11e9-8196-8df5c8ffd890.png
.. |image9| image:: /_static/python/36257654_38160687-1fe71db4-34f4-11e8-81e7-b65b5edd5e69.png
.. |image10| image:: /_static/python/36257654_38160817-d286d148-34f5-11e8-8488-db5bf83bc7f3.png
.. |image11| image:: /_static/python/36257654_44899250-d4cde200-ad33-11e8-9abf-e1f24be6fbe3.png
.. |image12| image:: /_static/36257654_44899479-76edca00-ad34-11e8-9a56-cb4be10fceb5.png
.. |image13| image:: /_static/36257654_62782521-ffe8b600-baec-11e9-9ca8-fde0af030da5.png
.. |image14| image:: /_static/36257654_44762007-09953a00-ab78-11e8-979e-da7933a78b54.png
.. |image15| image:: /_static/36257654_44762324-3e55c100-ab79-11e8-89af-4a744d775c45.png

























































































































