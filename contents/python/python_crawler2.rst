Python 爬虫 2
##################################################################################

高性能异步爬虫
**********************************************************************************

| 高性能异步爬虫
| 目的：在爬虫中使用异步实现高性能的数据爬取操作。

异步爬虫的方式：

::

	- 1.多线程，多进程（不建议）：
	    好处：可以为相关阻塞的操作单独开启线程或者进程，阻塞操作就可以异步执行。
	    弊端：无法无限制的开启多线程或者多进程。
	- 2.线程池、进程池（适当的使用）：
	    好处：我们可以降低系统对进程或者线程创建和销毁的一个频率，从而很好的降低系统的开销。
	    弊端：池中线程或进程的数量是有上限。

	- 3.单线程+异步协程（推荐）：
	    event_loop：事件循环，相当于一个无限循环，我们可以把一些函数注册到这个事件循环上，
	    当满足某些条件的时候，函数就会被循环执行。

	    coroutine：协程对象，我们可以将协程对象注册到事件循环中，它会被事件循环调用。
	    我们可以使用 async 关键字来定义一个方法，这个方法在调用时不会立即被执行，而是返回
	    一个协程对象。

	    task：任务，它是对协程对象的进一步封装，包含了任务的各个状态。

	    future：代表将来执行或还没有执行的任务，实际上和 task 没有本质区别。

	    async 定义一个协程.

	    await 用来挂起阻塞方法的执行。

同步爬虫
==================================================================================

.. code-block:: python
	:linenos:

	import requests
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}
	urls = [
	    'http://xmdx.sc.chinaz.net/Files/DownLoad/jianli/201904/jianli10231.rar',
	    'http://zjlt.sc.chinaz.net/Files/DownLoad/jianli/201904/jianli10229.rar',
	    'http://xmdx.sc.chinaz.net/Files/DownLoad/jianli/201904/jianli10231.rar'
	]

	def get_content(url):
	    print('正在爬取：',url)
	    #get方法是一个阻塞的方法
	    response = requests.get(url=url,headers=headers)
	    if response.status_code == 200 :
	        return response.content

	def parse_content(content):
	    print('响应数据的长度为：',len(content))

	for url in urls:
	    content = get_content(url)
	    parse_content(content)

线程池基本使用
==================================================================================

.. code-block:: python
	:linenos:

	# import time
	# #使用单线程串行方式执行
	#
	# def get_page(str):
	#     print("正在下载 ：",str)
	#     time.sleep(2)
	#     print('下载成功：',str)
	#
	# name_list =['xiaozi','aa','bb','cc']
	#
	# start_time = time.time()
	#
	# for i in range(len(name_list)):
	#     get_page(name_list[i])
	#
	# end_time = time.time()
	# print('%d second'% (end_time-start_time))
	import time
	#导入线程池模块对应的类
	from multiprocessing.dummy import Pool
	#使用线程池方式执行
	start_time = time.time()
	def get_page(str):
	    print("正在下载 ：",str)
	    time.sleep(2)
	    print('下载成功：',str)

	name_list =['xiaozi','aa','bb','cc']

	#实例化一个线程池对象
	pool = Pool(4)
	#将列表中每一个列表元素传递给get_page进行处理。
	pool.map(get_page,name_list)
	pool.close()
	pool.join()
	end_time = time.time()
	print(end_time-start_time)

线程池在爬虫案例中的应用
==================================================================================

.. code-block:: python
	:linenos:

	import requests
	from lxml import etree
	import re
	from multiprocessing.dummy import Pool
	#需求：爬取梨视频的视频数据
	headers = {
	    'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/71.0.3578.98 Safari/537.36'
	}
	#原则：线程池处理的是阻塞且较为耗时的操作

	#对下述url发起请求解析出视频详情页的url和视频的名称
	url = 'https://www.pearvideo.com/category_5'
	page_text = requests.get(url=url,headers=headers).text

	tree = etree.HTML(page_text)
	li_list = tree.xpath('//ul[@id="listvideoListUl"]/li')
	urls = [] #存储所有视频的链接and名字
	for li in li_list:
	    detail_url = 'https://www.pearvideo.com/'+li.xpath('./div/a/@href')[0]
	    name = li.xpath('./div/a/div[2]/text()')[0]+'.mp4'
	    #对详情页的url发起请求
	    detail_page_text = requests.get(url=detail_url,headers=headers).text
	    #从详情页中解析出视频的地址（url）
	    ex = 'srcUrl="(.*?)",vdoUrl'
	    video_url = re.findall(ex,detail_page_text)[0]
	    dic = {
	        'name':name,
	        'url':video_url
	    }
	    urls.append(dic)
	#对视频链接发起请求获取视频的二进制数据，然后将视频数据进行返回
	def get_video_data(dic):
	    url = dic['url']
	    print(dic['name'],'正在下载......')
	    data = requests.get(url=url,headers=headers).content
	    #持久化存储操作
	    with open(dic['name'],'wb') as fp:
	        fp.write(data)
	        print(dic['name'],'下载成功！')
	#使用线程池对视频数据进行请求（较为耗时的阻塞操作）
	pool = Pool(4)
	pool.map(get_video_data,urls)

	pool.close()
	pool.join()

协程
==================================================================================

.. code-block:: python
	:linenos:

	import asyncio

	async def request(url):
	    print('正在请求的url是',url)
	    print('请求成功,',url)
	    return url
	#async修饰的函数，调用之后返回的一个协程对象
	c = request('www.baidu.com')

	# #创建一个事件循环对象
	# loop = asyncio.get_event_loop()
	#
	# #将协程对象注册到loop中，然后启动loop
	# loop.run_until_complete(c)

	#task的使用
	# loop = asyncio.get_event_loop()
	# #基于loop创建了一个task对象
	# task = loop.create_task(c)
	# print(task)
	#
	# loop.run_until_complete(task)
	#
	# print(task)

	#future的使用
	# loop = asyncio.get_event_loop()
	# task = asyncio.ensure_future(c)
	# print(task)
	# loop.run_until_complete(task)
	# print(task)

	def callback_func(task):
	    #result返回的就是任务对象中封装的协程对象对应函数的返回值
	    print(task.result())

	#绑定回调
	loop = asyncio.get_event_loop()
	task = asyncio.ensure_future(c)
	#将回调函数绑定到任务对象中
	task.add_done_callback(callback_func)
	loop.run_until_complete(task)


多任务协程01
==================================================================================

.. code-block:: python
	:linenos:

	import asyncio
	import time

	async def request(url):
	    print('正在下载',url)
	    #在异步协程中如果出现了同步模块相关的代码，那么就无法实现异步。
	    # time.sleep(2)
	    #当在asyncio中遇到阻塞操作必须进行手动挂起
	    await asyncio.sleep(2)
	    print('下载完毕',url)

	start = time.time()
	urls = [
	    'www.baidu.com',
	    'www.sogou.com',
	    'www.goubanjia.com'
	]

	#任务列表：存放多个任务对象
	stasks = []
	for url in urls:
	    c = request(url)
	    task = asyncio.ensure_future(c)
	    stasks.append(task)

	loop = asyncio.get_event_loop()
	#需要将任务列表封装到wait中
	loop.run_until_complete(asyncio.wait(stasks))

	print(time.time()-start)

多任务异步协程02
==================================================================================

.. code-block:: python
	:linenos:

	import requests
	import asyncio
	import time

	start = time.time()
	urls = [
	    'http://127.0.0.1:5000/bobo','http://127.0.0.1:5000/jay','http://127.0.0.1:5000/tom'
	]

	async def get_page(url):
	    print('正在下载',url)
	    #requests.get是基于同步，必须使用基于异步的网络请求模块进行指定url的请求发送
	    #aiohttp:基于异步网络请求的模块
	    response = requests.get(url=url)
	    print('下载完毕：',response.text)

	tasks = []

	for url in urls:
	    c = get_page(url)
	    task = asyncio.ensure_future(c)
	    tasks.append(task)

	loop = asyncio.get_event_loop()
	loop.run_until_complete(asyncio.wait(tasks))

	end = time.time()

	print('总耗时:',end-start)

aiohttp 实现多任务异步协程
==================================================================================

.. code-block:: python
	:linenos:

	# 测试任务
	from flask import Flask
	import time

	app = Flask(__name__)

	@app.route('/bobo')
	def index_bobo():
	    time.sleep(2)
	    return 'Hello bobo'

	@app.route('/jay')
	def index_jay():
	    time.sleep(2)
	    return 'Hello jay'

	@app.route('/tom')
	def index_tom():
	    time.sleep(2)
	    return 'Hello tom'

	if __name__ == '__main__':
	    app.run(threaded=True)

	#环境安装：pip install aiohttp
	#使用该模块中的ClientSession
	import requests
	import asyncio
	import time
	import aiohttp

	start = time.time()
	# urls = [
	#     'http://127.0.0.1:5000/bobo','http://127.0.0.1:5000/jay','http://127.0.0.1:5000/tom',
	#     'http://127.0.0.1:5000/bobo', 'http://127.0.0.1:5000/jay', 'http://127.0.0.1:5000/tom',
	#     'http://127.0.0.1:5000/bobo', 'http://127.0.0.1:5000/jay', 'http://127.0.0.1:5000/tom',
	#     'http://127.0.0.1:5000/bobo', 'http://127.0.0.1:5000/jay', 'http://127.0.0.1:5000/tom',
	#
	# ]
	from multiprocessing.dummy import Pool
	pool = Pool(2)

	urls = []
	for i in range(10):
	    urls.append('http://127.0.0.1:5000/bobo')
	print(urls)
	async def get_page(url):
	    async with aiohttp.ClientSession() as session:
	        #get()、post():
	        #headers,params/data,proxy='http://ip:port'
	        async with await session.get(url) as response:
	            #text()返回字符串形式的响应数据
	            #read()返回的二进制形式的响应数据
	            #json()返回的就是json对象
	            #注意：获取响应数据操作之前一定要使用await进行手动挂起
	            page_text = await response.text()
	            print(page_text)

	tasks = []

	for url in urls:
	    c = get_page(url)
	    task = asyncio.ensure_future(c)
	    tasks.append(task)

	loop = asyncio.get_event_loop()

	loop.run_until_complete(asyncio.wait(tasks))

	end = time.time()

	print('总耗时:',end-start)

多任务异步协程实战演练
==================================================================================

.. code-block:: python
	:linenos:

	import requests
	from lxml import etree
	import time
	import os
	start = time.time()
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}
	if not os.path.exists('./libs'):
	    os.mkdir('./libs')
	url = 'http://pic.netbian.com/4kmeinv/index_%d.html'
	a = []
	for page in range(2,50):
	    new_url = format(url%page)
	    page_text = requests.get(url=new_url,headers=headers).text
	    tree = etree.HTML(page_text)
	    li_list = tree.xpath('//div[@class="slist"]/ul/li')
	    for li in li_list:
	        img_src = 'http://pic.netbian.com' + li.xpath('./a/img/@src')[0]
	        name = img_src.split('/')[-1]
	        # data = requests.get(url=img_src).content
	        # path = './libs/'+name
	        # with open(path,'wb') as fp:
	        #     fp.write(data)
	        #     print(name,'下载成功')
	        a.append(name)
	print(len(a))
	print('总耗时：',time.time()-start)

动态加载数据处理
**********************************************************************************

selenium模块的基本使用
==================================================================================

* 问题：selenium模块和爬虫之间具有怎样的关联？

::

	- 便捷的获取网站中动态加载的数据
	- 便捷实现模拟登录

* 什么是selenium模块？

::

	- 基于浏览器自动化的一个模块。

* selenium使用流程：

::

	- 环境安装：pip install selenium
	- 下载一个浏览器的驱动程序（谷歌浏览器）
	    - 下载路径：http://chromedriver.storage.googleapis.com/index.html
	    - 驱动程序和浏览器的映射关系：http://blog.csdn.net/huilan_same/article/details/51896672
	- 实例化一个浏览器对象
	- 编写基于浏览器自动化的操作代码
	    - 发起请求：get(url)
	    - 标签定位：find系列的方法
	    - 标签交互：send_keys('xxx')
	    - 执行js程序：excute_script('jsCode')
	    - 前进，后退：back(),forward()
	    - 关闭浏览器：quit()

	- selenium处理iframe
	    - 如果定位的标签存在于iframe标签之中，则必须使用switch_to.frame(id)
	    - 动作链（拖动）：from selenium.webdriver import ActionChains
	        - 实例化一个动作链对象：action = ActionChains(bro)
	        - click_and_hold（div）：长按且点击操作
	        - move_by_offset(x,y)
	        - perform()让动作链立即执行
	        - action.release()释放动作链对象

* 12306模拟登录

::

	- 超级鹰：http://www.chaojiying.com/about.html
	    - 注册：普通用户
	    - 登录：普通用户
	        - 题分查询：充值
	        - 创建一个软件（id）
	        - 下载示例代码

	- 12306模拟登录编码流程：
	    - 使用selenium打开登录页面
	    - 对当前selenium打开的这张页面进行截图
	    - 对当前图片局部区域（验证码图片）进行裁剪
	        - 好处：将验证码图片和模拟登录进行一一对应。
	    - 使用超级鹰识别验证码图片（坐标）
	    - 使用动作链根据坐标实现点击操作
	    - 录入用户名密码，点击登录按钮实现登录

演示程序
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from time import sleep
	# 后面是你的浏览器驱动位置，记得前面加r'','r'是防止字符转义的
	driver = webdriver.Chrome(r'./chromedriver')
	# 用get打开百度页面
	driver.get("http://www.baidu.com")
	# 查找页面的“设置”选项，并进行点击
	driver.find_elements_by_link_text('设置')[0].click()
	sleep(2)
	# # 打开设置后找到“搜索设置”选项，设置为每页显示50条
	driver.find_elements_by_link_text('搜索设置')[0].click()
	sleep(2)
	# 选中每页显示50条
	m = driver.find_element_by_id('nr')
	sleep(2)
	m.find_element_by_xpath('//*[@id="nr"]/option[3]').click()
	m.find_element_by_xpath('.//option[3]').click()
	sleep(2)
	# 点击保存设置
	driver.find_elements_by_class_name("prefpanelgo")[0].click()
	sleep(2)
	# 处理弹出的警告页面   确定accept() 和 取消dismiss()
	driver.switch_to_alert().accept()
	sleep(2)
	# 找到百度的输入框，并输入 美女
	driver.find_element_by_id('kw').send_keys('美女')
	sleep(2)
	# 点击搜索按钮
	driver.find_element_by_id('su').click()
	sleep(2)
	# 在打开的页面中找到“Selenium - 开源中国社区”，并打开这个页面
	driver.find_elements_by_link_text('美女_百度图片')[0].click()
	sleep(3)
	# 关闭浏览器
	driver.quit()

selenium 基础用法
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from lxml import etree
	from time import sleep
	#实例化一个浏览器对象（传入浏览器的驱动成）
	bro = webdriver.Chrome(executable_path='./chromedriver')
	#让浏览器发起一个指定url对应请求
	bro.get('http://125.35.6.84:81/xk/')

	#page_source获取浏览器当前页面的页面源码数据
	page_text = bro.page_source

	#解析企业名称
	tree = etree.HTML(page_text)
	li_list = tree.xpath('//ul[@id="gzlist"]/li')
	for li in li_list:
	    name = li.xpath('./dl/@title')[0]
	    print(name)
	sleep(5)
	bro.quit()

selenium 其他自动化操作
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from time import sleep
	bro = webdriver.Chrome(executable_path='./chromedriver')

	bro.get('https://www.taobao.com/')

	#标签定位
	search_input = bro.find_element_by_id('q')
	#标签交互
	search_input.send_keys('Iphone')

	#执行一组js程序  翻屏(往下滚动一屏) 滑动按钮
	bro.execute_script('window.scrollTo(0,document.body.scrollHeight)')
	sleep(2)
	#点击搜索按钮
	btn = bro.find_element_by_css_selector('.btn-search')
	btn.click()

	bro.get('https://www.baidu.com')
	sleep(2)
	#回退
	bro.back()
	sleep(2)
	#前进
	bro.forward()

	sleep(5)

	bro.quit()

动作链和 iframe 的处理
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from time import sleep
	#导入动作链对应的类
	from selenium.webdriver import ActionChains
	bro = webdriver.Chrome(executable_path='./chromedriver')

	bro.get('https://www.runoob.com/try/try.php?filename=jqueryui-api-droppable')

	#如果定位的标签是存在于iframe标签之中的则必须通过如下操作在进行标签定位
	bro.switch_to.frame('iframeResult')#切换浏览器标签定位的作用域
	div = bro.find_element_by_id('draggable')

	#动作链
	action = ActionChains(bro)
	#点击长按指定的标签
	action.click_and_hold(div)

	for i in range(5):
	    #perform()立即执行动作链操作
	    #move_by_offset(x,y):x水平方向 y竖直方向
	    action.move_by_offset(17,0).perform()
	    sleep(0.5)

	#释放动作链
	action.release()

	bro.quit()

模拟登录 qq 空间
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from time import sleep

	bro = webdriver.Chrome(executable_path='./chromedriver')

	bro.get('https://qzone.qq.com/')

	bro.switch_to.frame('login_frame')

	a_tag = bro.find_element_by_id("switcher_plogin")
	a_tag.click()

	userName_tag = bro.find_element_by_id('u')
	password_tag = bro.find_element_by_id('p')
	sleep(1)
	userName_tag.send_keys('328410948')
	sleep(1)
	password_tag.send_keys('123456789')
	sleep(1)
	btn = bro.find_element_by_id('login_button')
	btn.click()

	sleep(3)

	bro.quit()

谷歌无头浏览器+反检测
==================================================================================

.. code-block:: python
	:linenos:

	from selenium import webdriver
	from time import sleep
	#实现无可视化界面
	from selenium.webdriver.chrome.options import Options
	#实现规避检测
	from selenium.webdriver import ChromeOptions

	#实现无可视化界面的操作
	chrome_options = Options()
	chrome_options.add_argument('--headless')
	chrome_options.add_argument('--disable-gpu')

	#实现规避检测
	option = ChromeOptions()
	option.add_experimental_option('excludeSwitches', ['enable-automation'])

	#如何实现让selenium规避被检测到的风险
	bro = webdriver.Chrome(executable_path='./chromedriver',chrome_options=chrome_options,options=option)

	#无可视化界面（无头浏览器） phantomJs
	bro.get('https://www.baidu.com')

	print(bro.page_source)
	sleep(2)
	bro.quit()

基于 selenium 实现 12306 模拟登录
==================================================================================

.. code-block:: python
	:linenos:

	#下述代码为超级鹰提供的示例代码
	import requests
	from hashlib import md5

	class Chaojiying_Client(object):

	    def __init__(self, username, password, soft_id):
	        self.username = username
	        password =  password.encode('utf8')
	        self.password = md5(password).hexdigest()
	        self.soft_id = soft_id
	        self.base_params = {
	            'user': self.username,
	            'pass2': self.password,
	            'softid': self.soft_id,
	        }
	        self.headers = {
	            'Connection': 'Keep-Alive',
	            'User-Agent': 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0)',
	        }

	    def PostPic(self, im, codetype):
	        """
	        im: 图片字节
	        codetype: 题目类型 参考 http://www.chaojiying.com/price.html
	        """
	        params = {
	            'codetype': codetype,
	        }
	        params.update(self.base_params)
	        files = {'userfile': ('ccc.jpg', im)}
	        r = requests.post('http://upload.chaojiying.net/Upload/Processing.php', data=params, files=files, headers=self.headers)
	        return r.json()

	    def ReportError(self, im_id):
	        """
	        im_id:报错题目的图片ID
	        """
	        params = {
	            'id': im_id,
	        }
	        params.update(self.base_params)
	        r = requests.post('http://upload.chaojiying.net/Upload/ReportError.php', data=params, headers=self.headers)
	        return r.json()

	# chaojiying = Chaojiying_Client('bobo328410948', 'bobo328410948', '899370')	#用户中心>>软件ID 生成一个替换 96001
	# im = open('12306.jpg', 'rb').read()													#本地图片文件路径 来替换 a.jpg 有时WIN系统须要//
	# print(chaojiying.PostPic(im, 9004)['pic_str'])
	#上述代码为超级鹰提供的示例代码

	#使用selenium打开登录页面
	from selenium import webdriver
	import time
	from PIL import Image
	from selenium.webdriver import ActionChains
	bro = webdriver.Chrome(executable_path='./chromedriver')
	bro.get('https://kyfw.12306.cn/otn/login/init')
	time.sleep(1)

	#save_screenshot就是将当前页面进行截图且保存
	bro.save_screenshot('aa.png')

	#确定验证码图片对应的左上角和右下角的坐标（裁剪的区域就确定）
	code_img_ele = bro.find_element_by_xpath('//*[@id="loginForm"]/div/ul[2]/li[4]/div/div/div[3]/img')
	location = code_img_ele.location  # 验证码图片左上角的坐标 x,y
	print('location:',location)
	size = code_img_ele.size  #验证码标签对应的长和宽
	print('size:',size)
	#左上角和右下角坐标
	rangle = (
	int(location['x']), int(location['y']), int(location['x'] + size['width']), int(location['y'] + size['height']))
	#至此验证码图片区域就确定下来了

	i = Image.open('./aa.png')
	code_img_name = './code.png'
	#crop根据指定区域进行图片裁剪
	frame = i.crop(rangle)
	frame.save(code_img_name)

	#将验证码图片提交给超级鹰进行识别
	chaojiying = Chaojiying_Client('bobo328410948', 'bobo328410948', '899370')	#用户中心>>软件ID 生成一个替换 96001
	im = open('code.png', 'rb').read()													#本地图片文件路径 来替换 a.jpg 有时WIN系统须要//
	print(chaojiying.PostPic(im, 9004)['pic_str'])
	result = chaojiying.PostPic(im, 9004)['pic_str']
	all_list = [] #要存储即将被点击的点的坐标  [[x1,y1],[x2,y2]]
	if '|' in result:
	    list_1 = result.split('|')
	    count_1 = len(list_1)
	    for i in range(count_1):
	        xy_list = []
	        x = int(list_1[i].split(',')[0])
	        y = int(list_1[i].split(',')[1])
	        xy_list.append(x)
	        xy_list.append(y)
	        all_list.append(xy_list)
	else:
	    x = int(result.split(',')[0])
	    y = int(result.split(',')[1])
	    xy_list = []
	    xy_list.append(x)
	    xy_list.append(y)
	    all_list.append(xy_list)
	print(all_list)
	#遍历列表，使用动作链对每一个列表元素对应的x,y指定的位置进行点击操作
	for l in all_list:
	    x = l[0]
	    y = l[1]
	    ActionChains(bro).move_to_element_with_offset(code_img_ele, x, y).click().perform()
	    time.sleep(0.5)

	bro.find_element_by_id('username').send_keys('www.zhangbowudi@qq.com')
	time.sleep(2)
	bro.find_element_by_id('password').send_keys('bobo_15027900535')
	time.sleep(2)
	bro.find_element_by_id('loginSub').click()
	time.sleep(30)
	bro.quit()

scrapy 框架
**********************************************************************************

scrapy框架

- 什么是框架？

::

	- 就是一个集成了很多功能并且具有很强通用性的一个项目模板。

- 如何学习框架？

::

	- 专门学习框架封装的各种功能的详细用法。

- 什么是scrapy？

::

	- 爬虫中封装好的一个明星框架。功能：高性能的持久化存储，异步的数据下载，高性能的数据解析，分布式

- scrapy框架的基本使用

::

	- 环境的安装：
	    - mac or linux：pip install scrapy
	    - windows:
	        - pip install wheel
	        - 下载twisted，下载地址为http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted
	        - 安装twisted：pip install Twisted‑17.1.0‑cp36‑cp36m‑win_amd64.whl
	        - pip install pywin32
	        - pip install scrapy
	        测试：在终端里录入scrapy指令，没有报错即表示安装成功！
	- 创建一个工程：scrapy startproject xxxPro
	- cd xxxPro
	- 在spiders子目录中创建一个爬虫文件
	    - scrapy genspider spiderName www.xxx.com
	- 执行工程：
	    - scrapy crawl spiderName

- scrapy数据解析

- scrapy持久化存储

::

	- 基于终端指令：
	    - 要求：只可以将parse方法的返回值存储到本地的文本文件中
	    - 注意：持久化存储对应的文本文件的类型只可以为：'json', 'jsonlines', 'jl', 'csv', 'xml', 'marshal', 'pickle
	    - 指令：scrapy crawl xxx -o filePath
	    - 好处：简介高效便捷
	    - 缺点：局限性比较强（数据只可以存储到指定后缀的文本文件中）

	- 基于管道：
	    - 编码流程：
	        - 数据解析
	        - 在item类中定义相关的属性
	        - 将解析的数据封装存储到item类型的对象
	        - 将item类型的对象提交给管道进行持久化存储的操作
	        - 在管道类的process_item中要将其接受到的item对象中存储的数据进行持久化存储操作
	        - 在配置文件中开启管道
	    - 好处：
	        - 通用性强。

	- 面试题：将爬取到的数据一份存储到本地一份存储到数据库，如何实现？
	    - 管道文件中一个管道类对应的是将数据存储到一种平台
	    - 爬虫文件提交的item只会给管道文件中第一个被执行的管道类接受
	    - process_item中的return item表示将item传递给下一个即将被执行的管道类

- 基于Spider的全站数据爬取

::

	- 就是将网站中某板块下的全部页码对应的页面数据进行爬取
	- 需求：爬取校花网中的照片的名称
	- 实现方式：
	    - 将所有页面的url添加到start_urls列表（不推荐）
	    - 自行手动进行请求发送（推荐）
	        - 手动请求发送：
	            - yield scrapy.Request(url,callback):callback专门用做于数据解析

- 五大核心组件

::

	引擎(Scrapy)
	    用来处理整个系统的数据流处理, 触发事务(框架核心)
	调度器(Scheduler)
	    用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
	下载器(Downloader)
	    用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)
	爬虫(Spiders)
	    爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面
	项目管道(Pipeline)
	    负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。

- 请求传参

::

	- 使用场景：如果爬取解析的数据不在同一张页面中。（深度爬取）
	- 需求：爬取boss的岗位名称，岗位描述

- 图片数据爬取之ImagesPipeline

::

	- 基于scrapy爬取字符串类型的数据和爬取图片类型的数据区别？
	    - 字符串：只需要基于xpath进行解析且提交管道进行持久化存储
	    - 图片：xpath解析出图片src的属性值。单独的对图片地址发起请求获取图片二进制类型的数据

	- ImagesPipeline：
	    - 只需要将img的src的属性值进行解析，提交到管道，管道就会对图片的src进行请求发送获取图片的二进制类型的数据，且还会帮我们进行持久化存储。
	- 需求：爬取站长素材中的高清图片
	- 使用流程：
	    - 数据解析（图片的地址）
	    - 将存储图片地址的item提交到制定的管道类
	    - 在管道文件中自定制一个基于ImagesPipeLine的一个管道类
	        - get_media_request
	        - file_path
	        - item_completed
	    - 在配置文件中：
	        - 指定图片存储的目录：IMAGES_STORE = './imgs_bobo'
	        - 指定开启的管道：自定制的管道类

- 中间件

::

	- 下载中间件
	    - 位置：引擎和下载器之间
	    - 作用：批量拦截到整个工程中所有的请求和响应
	    - 拦截请求：
	        - UA伪装:process_request
	        - 代理IP:process_exception:return request

	    - 拦截响应：
	        - 篡改响应数据，响应对象
	        - 需求：爬取网易新闻中的新闻数据（标题和内容）
	            - 1.通过网易新闻的首页解析出五大板块对应的详情页的url（没有动态加载）
	            - 2.每一个板块对应的新闻标题都是动态加载出来的（动态加载）
	            - 3.通过解析出每一条新闻详情页的url获取详情页的页面源码，解析出新闻内容

- CrawlSpider:类，Spider的一个子类

::

	- 全站数据爬取的方式
	    - 基于Spider：手动请求
	    - 基于CrawlSpider
	- CrawlSpider的使用：
	    - 创建一个工程
	    - cd XXX
	    - 创建爬虫文件（CrawlSpider）：
	        - scrapy genspider -t crawl xxx www.xxxx.com
	        - 链接提取器：
	            - 作用：根据指定的规则（allow）进行指定链接的提取
	        - 规则解析器：
	            - 作用：将链接提取器提取到的链接进行指定规则（callback）的解析
	    #需求：爬取sun网站中的编号，新闻标题，新闻内容，标号
	        - 分析：爬取的数据没有在同一张页面中。
	        - 1.可以使用链接提取器提取所有的页码链接
	        - 2.让链接提取器提取所有的新闻详情页的链接

- 分布式爬虫

::

	- 概念：我们需要搭建一个分布式的机群，让其对一组资源进行分布联合爬取。
	- 作用：提升爬取数据的效率

	- 如何实现分布式？
	    - 安装一个scrapy-redis的组件
	    - 原生的scarapy是不可以实现分布式爬虫，必须要让scrapy结合着scrapy-redis组件一起实现分布式爬虫。
	    - 为什么原生的scrapy不可以实现分布式？
	        - 调度器不可以被分布式机群共享
	        - 管道不可以被分布式机群共享
	    - scrapy-redis组件作用：
	        - 可以给原生的scrapy框架提供可以被共享的管道和调度器
	    - 实现流程
	        - 创建一个工程
	        - 创建一个基于CrawlSpider的爬虫文件
	        - 修改当前的爬虫文件：
	            - 导包：from scrapy_redis.spiders import RedisCrawlSpider
	            - 将start_urls和allowed_domains进行注释
	            - 添加一个新属性：redis_key = 'sun' 可以被共享的调度器队列的名称
	            - 编写数据解析相关的操作
	            - 将当前爬虫类的父类修改成RedisCrawlSpider
	        - 修改配置文件settings
	            - 指定使用可以被共享的管道：
	                ITEM_PIPELINES = {
	                    'scrapy_redis.pipelines.RedisPipeline': 400
	                }
	            - 指定调度器：
	                # 增加了一个去重容器类的配置, 作用使用Redis的set集合来存储请求的指纹数据, 从而实现请求去重的持久化
	                DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
	                # 使用scrapy-redis组件自己的调度器
	                SCHEDULER = "scrapy_redis.scheduler.Scheduler"
	                # 配置调度器是否要持久化, 也就是当爬虫结束了, 要不要清空Redis中请求队列和去重指纹的set。如果是True, 就表示要持久化存储, 就不清空数据, 否则清空数据
	                SCHEDULER_PERSIST = True
	            - 指定redis服务器：

	        - redis相关操作配置：
	            - 配置redis的配置文件：
	                - linux或者mac：redis.conf
	                - windows:redis.windows.conf
	                - 代开配置文件修改：
	                    - 将bind 127.0.0.1进行删除
	                    - 关闭保护模式：protected-mode yes改为no
	            - 结合着配置文件开启redis服务
	                - redis-server 配置文件
	            - 启动客户端：
	                - redis-cli
	        - 执行工程：
	            - scrapy runspider xxx.py
	        - 向调度器的队列中放入一个起始的url：
	            - 调度器的队列在redis的客户端中
	                - lpush xxx www.xxx.com
	        - 爬取到的数据存储在了redis的proName:items这个数据结构中

初始使用
==================================================================================

.. code-block:: python
	:linenos:

	//settings 配置文件
	BOT_NAME = 'firstBlood'

	......
	......

	# Crawl responsibly by identifying yourself (and your website) on the user-agent
	#USER_AGENT = 'firstBlood (+http://www.yourdomain.com)'
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	LOG_LEVEL = 'ERROR'

	#显示指定类型的日志信息
	LOG_LEVEL = 'ERROR'

	......
	......

	//first 文件
	import scrapy

	class FirstSpider(scrapy.Spider):
	    #爬虫文件的名称：就是爬虫源文件的一个唯一标识
	    name = 'first'
	    #允许的域名：用来限定start_urls列表中哪些url可以进行请求发送
	    # allowed_domains = ['www.baidu.com']

	    #起始的url列表：该列表中存放的url会被scrapy自动进行请求的发送
	    start_urls = ['https://www.baidu.com/','https://www.sogou.com']

	    #用作于数据解析：response参数表示的就是请求成功后对应的响应对象
	    def parse(self, response):
	        print(response)

基于终端指令的持久化存储
==================================================================================

.. code-block:: python
	:linenos:

	//settings 配置文件

	BOT_NAME = 'qiubaiPro'

	# Crawl responsibly by identifying yourself (and your website) on the user-agent
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	LOG_LEVEL = 'ERROR'

	//items.py
	import scrapy

	class QiubaiproItem(scrapy.Item):
	    # define the fields for your item here like:
	    author = scrapy.Field()
	    content = scrapy.Field()
	    # pass

	//qiubai.py
	import scrapy
	from qiubaiPro.items import QiubaiproItem

	class QiubaiSpider(scrapy.Spider):
	    name = 'qiubai'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['https://www.qiushibaike.com/text/']

		def parse(self, response):
		    #解析：作者的名称+段子内容
		    div_list = response.xpath('//div[@id="content-left"]/div')
		    all_data = [] #存储所有解析到的数据
		    for div in div_list:
		        #xpath返回的是列表，但是列表元素一定是Selector类型的对象
		        #extract可以将Selector对象中data参数存储的字符串提取出来
		        # author = div.xpath('./div[1]/a[2]/h2/text()')[0].extract()
		        author = div.xpath('./div[1]/a[2]/h2/text()').extract_first()
		        #列表调用了extract之后，则表示将列表中每一个Selector对象中data对应的字符串提取了出来
		        content = div.xpath('./a[1]/div/span//text()').extract()
		        content = ''.join(content)

		        dic = {
		            'author':author,
		            'content':content
		        }

		        all_data.append(dic)

		    return all_data

基于管道的持久化存储
==================================================================================

.. code-block:: python
	:linenos:

	//items.py
	import scrapy

	class QiubaiproItem(scrapy.Item):
	    # define the fields for your item here like:
	    author = scrapy.Field()
	    content = scrapy.Field()
	    # pass

	//qiubai.py
	import scrapy
	from qiubaiPro.items import QiubaiproItem

	class QiubaiSpider(scrapy.Spider):
	    name = 'qiubai'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['https://www.qiushibaike.com/text/']

	    def parse(self, response):
	        #解析：作者的名称+段子内容
	        div_list = response.xpath('//div[@id="content-left"]/div')
	        all_data = [] #存储所有解析到的数据
	        for div in div_list:
	            #xpath返回的是列表，但是列表元素一定是Selector类型的对象
	            #extract可以将Selector对象中data参数存储的字符串提取出来
	            # author = div.xpath('./div[1]/a[2]/h2/text()')[0].extract()
	            author = div.xpath('./div[1]/a[2]/h2/text() | ./div[1]/span/h2/text()').extract_first()
	            #列表调用了extract之后，则表示将列表中每一个Selector对象中data对应的字符串提取了出来
	            content = div.xpath('./a[1]/div/span//text()').extract()
	            content = ''.join(content)

	            item = QiubaiproItem()
	            item['author'] = author
	            item['content'] = content

	            yield item#将item提交给了管道

	//pipelines.py
	import pymysql

	class QiubaiproPipeline(object):
	    fp = None
	    #重写父类的一个方法：该方法只在开始爬虫的时候被调用一次
	    def open_spider(self,spider):
	        print('开始爬虫......')
	        self.fp = open('./qiubai.txt','w',encoding='utf-8')


	    #专门用来处理item类型对象
	    #该方法可以接收爬虫文件提交过来的item对象
	    #该方法没接收到一个item就会被调用一次
	    def process_item(self, item, spider):
	        author = item['author']
	        content= item['content']

	        self.fp.write(author+':'+content+'\n')

	        return item #就会传递给下一个即将被执行的管道类

	    def close_spider(self,spider):
	        print('结束爬虫！')
	        self.fp.close()

	#管道文件中一个管道类对应将一组数据存储到一个平台或者载体中
	class mysqlPileLine(object):
	    conn = None
	    cursor = None
	    def open_spider(self,spider):
	        self.conn = pymysql.Connect(host='127.0.0.1',port=3306,user='root',password='123456',db='qiubai',charset='utf8')
	    def process_item(self,item,spider):
	        self.cursor = self.conn.cursor()

	        try:
	            self.cursor.execute('insert into qiubai values("%s","%s")'%(item["author"],item["content"]))
	            self.conn.commit()
	        except Exception as e:
	            print(e)
	            self.conn.rollback()

	        return item
	    def close_spider(self,spider):
	        self.cursor.close()
	        self.conn.close()


	#爬虫文件提交的item类型的对象最终会提交给哪一个管道类？
	    #先执行的管道类

	//配置文件(setting)中开启管道
	ITEM_PIPELINES = {
	   'qiubaiPro.pipelines.QiubaiproPipeline': 300,
	    #300表示的是优先级，数值越小优先级越高
	}

全站数据爬取
==================================================================================

.. code-block:: python
	:linenos:

	//settings.py
	BOT_NAME = 'xiaohuaPro'

	SPIDER_MODULES = ['xiaohuaPro.spiders']
	NEWSPIDER_MODULE = 'xiaohuaPro.spiders'

	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	# Crawl responsibly by identifying yourself (and your website) on the user-agent
	#USER_AGENT = 'xiaohuaPro (+http://www.yourdomain.com)'
	LOG_LEVEL = 'ERROR'
	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	//xiaohua.py
	import scrapy

	class XiaohuaSpider(scrapy.Spider):
	    name = 'xiaohua'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['http://www.521609.com/meinvxiaohua/']

	    #生成一个通用的url模板(不可变)
	    url = 'http://www.521609.com/meinvxiaohua/list12%d.html'
	    page_num = 2

	    def parse(self, response):
	        li_list = response.xpath('//*[@id="content"]/div[2]/div[2]/ul/li')
	        for li in li_list:
	            img_name = li.xpath('./a[2]/b/text() | ./a[2]/text()').extract_first()
	            print(img_name)

	        if self.page_num <= 11:
	            new_url = format(self.url%self.page_num)
	            self.page_num += 1
	            #手动请求发送:callback回调函数是专门用作于数据解析
	            yield scrapy.Request(url=new_url,callback=self.parse)

scrapy五大核心组件简介
==================================================================================

scrapy的基本使用我们已经掌握，但是各位心中一定会有些许的疑问，我们在编写scrapy工程的时候，我们只是在定义相关类中的属性或者方法，但是我们并没有手动的对类进行实例化或者手动调用过相关的方法，那么这些操作都是谁做的呢？接下来我们就来看看scrapy的五大核心组件的工作流程，然后大家就会上述的疑问有基本了解了。

|image0|

* 引擎(Scrapy)
	
	* 用来处理整个系统的数据流处理, 触发事务(框架核心)

* 调度器(Scheduler)

	* 用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址

* 下载器(Downloader)

	* 用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)

* 爬虫(Spiders)

	* 爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面

* 项目管道(Pipeline)

	* 负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。

请求传参
==================================================================================

.. code-block:: python
	:linenos:

	//items.py
	import scrapy

	class BossproItem(scrapy.Item):
	    # define the fields for your item here like:
	    job_name = scrapy.Field()
	    job_desc = scrapy.Field()
	    # pass

	//boss.py
	import scrapy
	from bossPro.items import BossproItem

	class BossSpider(scrapy.Spider):
	    name = 'boss'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['https://www.zhipin.com/job_detail/?query=python&city=101010100&industry=&position=']

	    url = 'https://www.zhipin.com/c101010100/?query=python&page=%d'
	    page_num = 2

	   #回调函数接受item
	    def parse_detail(self,response):
	        item = response.meta['item']

	        job_desc = response.xpath('//*[@id="main"]/div[3]/div/div[2]/div[2]/div[1]/div//text()').extract()
	        job_desc = ''.join(job_desc)
	        # print(job_desc)
	        item['job_desc'] = job_desc

	        yield item

	    #解析首页中的岗位名称
	    def parse(self, response):
	        li_list = response.xpath('//*[@id="main"]/div/div[3]/ul/li')
	        for li in li_list:
	            item = BossproItem()

	            job_name = li.xpath('.//div[@class="info-primary"]/h3/a/div[1]/text()').extract_first()
	            item['job_name'] = job_name
	            # print(job_name)
	            detail_url = 'https://www.zhipin.com'+li.xpath('.//div[@class="info-primary"]/h3/a/@href').extract_first()
	            #对详情页发请求获取详情页的页面源码数据
	            #手动请求的发送
	            #请求传参：meta={}，可以将meta字典传递给请求对应的回调函数
	            yield scrapy.Request(detail_url,callback=self.parse_detail,meta={'item':item})

	        #分页操作
	        if self.page_num <= 3:
	            new_url = format(self.url%self.page_num)
	            self.page_num += 1

	            yield scrapy.Request(new_url,callback=self.parse)

图片爬取
==================================================================================

.. code-block:: python
	:linenos:

	//img.py
	# -*- coding: utf-8 -*-
	import scrapy
	from imgsPro.items import ImgsproItem

	class ImgSpider(scrapy.Spider):
	    name = 'img'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['http://sc.chinaz.com/tupian/']

	    def parse(self, response):
	        div_list = response.xpath('//div[@id="container"]/div')
	        for div in div_list:
	            #注意：使用伪属性
	            src = div.xpath('./div/a/img/@src2').extract_first()

	            item = ImgsproItem()
	            item['src'] = src

	            yield item

	//items.py
	class ImgsproItem(scrapy.Item):
	    # define the fields for your item here like:
	    src = scrapy.Field()
	    # pass

	//pipelines
	from scrapy.pipelines.images import ImagesPipeline
	import scrapy
	class imgsPileLine(ImagesPipeline):

	    #就是可以根据图片地址进行图片数据的请求
	    def get_media_requests(self, item, info):

	        yield scrapy.Request(item['src'])

	    #指定图片存储的路径
	    def file_path(self, request, response=None, info=None):
	        imgName = request.url.split('/')[-1]
	        return imgName

	    def item_completed(self, results, item, info):
	        return item #返回给下一个即将被执行的管道类

中间件使用
==================================================================================

.. code-block:: python
	:linenos:

	//settings.py
	BOT_NAME = 'middlePro'

	SPIDER_MODULES = ['middlePro.spiders']
	NEWSPIDER_MODULE = 'middlePro.spiders'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	# Enable or disable downloader middlewares
	DOWNLOADER_MIDDLEWARES = {
	   'middlePro.middlewares.MiddleproDownloaderMiddleware': 543,
	}

	//middlewares.py
	from scrapy import signals

	import random

	class MiddleproDownloaderMiddleware(object):
	    # Not all methods need to be defined. If a method is not defined,
	    # scrapy acts as if the downloader middleware does not modify the
	    # passed objects.
	    user_agent_list = [
	        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 "
	        "(KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1",
	        "Mozilla/5.0 (X11; CrOS i686 2268.111.0) AppleWebKit/536.11 "
	        "(KHTML, like Gecko) Chrome/20.0.1132.57 Safari/536.11",
	        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.6 "
	        "(KHTML, like Gecko) Chrome/20.0.1092.0 Safari/536.6",
	        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.6 "
	        "(KHTML, like Gecko) Chrome/20.0.1090.0 Safari/536.6",
	        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.1 "
	        "(KHTML, like Gecko) Chrome/19.77.34.5 Safari/537.1",
	        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/536.5 "
	        "(KHTML, like Gecko) Chrome/19.0.1084.9 Safari/536.5",
	        "Mozilla/5.0 (Windows NT 6.0) AppleWebKit/536.5 "
	        "(KHTML, like Gecko) Chrome/19.0.1084.36 Safari/536.5",
	        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
	        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_0) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
	        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 "
	        "(KHTML, like Gecko) Chrome/19.0.1061.0 Safari/536.3",
	        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.24 "
	        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24",
	        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/535.24 "
	        "(KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24"
	    ]
	    PROXY_http = [
	        '153.180.102.104:80',
	        '195.208.131.189:56055',
	    ]
	    PROXY_https = [
	        '120.83.49.90:9000',
	        '95.189.112.214:35508',
	    ]
	    #拦截请求
	    def process_request(self, request, spider):
	        #UA伪装
	        request.headers['User-Agent'] = random.choice(self.user_agent_list)

	        #为了验证代理的操作是否生效
	        request.meta['proxy'] = 'http://183.146.213.198:80'
	        return None
	    #拦截所有的响应
	    def process_response(self, request, response, spider):
	        # Called with the response returned from the downloader.

	        # Must either;
	        # - return a Response object
	        # - return a Request object
	        # - or raise IgnoreRequest
	        return response
	    #拦截发生异常的请求
	    def process_exception(self, request, exception, spider):
	        if request.url.split(':')[0] == 'http':
	            #代理
	            request.meta['proxy'] = 'http://'+random.choice(self.PROXY_http)
	        else:
	            request.meta['proxy'] = 'https://' + random.choice(self.PROXY_https)

	        return request  #将修正之后的请求对象进行重新的请求发送

	class MiddleproPipeline(object):
	    def process_item(self, item, spider):
	        return item

	//middle.py
	class MiddleSpider(scrapy.Spider):
	    #爬取百度
	    name = 'middle'
	    # allowed_domains = ['www.xxxx.com']
	    start_urls = ['http://www.baidu.com/s?wd=ip']

	    def parse(self, response):
	        page_text = response.text

	        with open('./ip.html','w',encoding='utf-8') as fp:
	            fp.write(page_text)

* 网易新闻爬取

.. code-block:: python
	:linenos:

	//items.py
	class WangyiproItem(scrapy.Item):
	    # define the fields for your item here like:
	    title = scrapy.Field()
	    content = scrapy.Field()

	//middlewares.py
	from scrapy import signals

	from scrapy.http import HtmlResponse
	from time import sleep
	class WangyiproDownloaderMiddleware(object):
	    # Not all methods need to be defined. If a method is not defined,
	    # scrapy acts as if the downloader middleware does not modify the
	    # passed objects.

	    def process_request(self, request, spider):
	        # Called for each request that goes through the downloader
	        # middleware.

	        # Must either:
	        # - return None: continue processing this request
	        # - or return a Response object
	        # - or return a Request object
	        # - or raise IgnoreRequest: process_exception() methods of
	        #   installed downloader middleware will be called
	        return None
	    #该方法拦截五大板块对应的响应对象，进行篡改
	    def process_response(self, request, response, spider):#spider爬虫对象
	        bro = spider.bro#获取了在爬虫类中定义的浏览器对象

	        #挑选出指定的响应对象进行篡改
	        #通过url指定request
	        #通过request指定response
	        if request.url in spider.models_urls:
	            bro.get(request.url) #五个板块对应的url进行请求
	            sleep(3)
	            page_text = bro.page_source  #包含了动态加载的新闻数据

	            #response #五大板块对应的响应对象
	            #针对定位到的这些response进行篡改
	            #实例化一个新的响应对象（符合需求：包含动态加载出的新闻数据），替代原来旧的响应对象
	            #如何获取动态加载出的新闻数据？
	                #基于selenium便捷的获取动态加载数据
	            new_response = HtmlResponse(url=request.url,body=page_text,encoding='utf-8',request=request)

	            return new_response
	        else:
	            #response #其他请求对应的响应对象
	            return response

	    def process_exception(self, request, exception, spider):
	        # Called when a download handler or a process_request()
	        # (from other downloader middleware) raises an exception.

	        # Must either:
	        # - return None: continue processing this exception
	        # - return a Response object: stops process_exception() chain
	        # - return a Request object: stops process_exception() chain
	        pass

	class WangyiproPipeline(object):
	    def process_item(self, item, spider):
	        print(item)
	        return item

	//settings.py
	#USER_AGENT = 'wangyiPro (+http://www.yourdomain.com)'
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	DOWNLOADER_MIDDLEWARES = {
	   'wangyiPro.middlewares.WangyiproDownloaderMiddleware': 543,
	}

	ITEM_PIPELINES = {
	   'wangyiPro.pipelines.WangyiproPipeline': 300,
	}
	LOG_LEVEL = 'ERROR'

	//wangyi.py
	import scrapy
	from selenium import webdriver
	from wangyiPro.items import WangyiproItem
	class WangyiSpider(scrapy.Spider):
	    name = 'wangyi'
	    # allowed_domains = ['www.cccom']
	    start_urls = ['https://news.163.com/']
	    models_urls = []  #存储五个板块对应详情页的url
	    #解析五大板块对应详情页的url

	    #实例化一个浏览器对象
	    def __init__(self):
	        self.bro = webdriver.Chrome(executable_path='/Users/bobo/Desktop/小猿圈爬虫课程/chromedriver')

	    def parse(self, response):
	        li_list = response.xpath('//*[@id="index2016_wrap"]/div[1]/div[2]/div[2]/div[2]/div[2]/div/ul/li')
	        alist = [3,4,6,7,8]
	        for index in alist:
	            model_url = li_list[index].xpath('./a/@href').extract_first()
	            self.models_urls.append(model_url)

	        #依次对每一个板块对应的页面进行请求
	        for url in self.models_urls:#对每一个板块的url进行请求发送
	            yield scrapy.Request(url,callback=self.parse_model)

	    #每一个板块对应的新闻标题相关的内容都是动态加载
	    def parse_model(self,response): #解析每一个板块页面中对应新闻的标题和新闻详情页的url
	        # response.xpath()
	        div_list = response.xpath('/html/body/div/div[3]/div[4]/div[1]/div/div/ul/li/div/div')
	        for div in div_list:
	            title = div.xpath('./div/div[1]/h3/a/text()').extract_first()
	            new_detail_url = div.xpath('./div/div[1]/h3/a/@href').extract_first()

	            item = WangyiproItem()
	            item['title'] = title

	            #对新闻详情页的url发起请求
	            yield scrapy.Request(url=new_detail_url,callback=self.parse_detail,meta={'item':item})
	    def parse_detail(self,response):#解析新闻内容
	        content = response.xpath('//*[@id="endText"]//text()').extract()
	        content = ''.join(content)
	        item = response.meta['item']
	        item['content'] = content

	        yield item

	    def closed(self,spider):
	        self.bro.quit()

	//pipelines.py
	class WangyiproPipeline(object):
	    def process_item(self, item, spider):
	        print(item)
	        return item

全栈数据爬取
==================================================================================

.. code-block:: python
	:linenos:

	//settings.py
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'
	LOG_LEVEL = 'ERROR'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False

	# Configure item pipelines
	# See https://doc.scrapy.org/en/latest/topics/item-pipeline.html
	ITEM_PIPELINES = {
	   'sunPro.pipelines.SunproPipeline': 300,
	}

	//pipelines.py
	class SunproPipeline(object):
	    def process_item(self, item, spider):
	        #如何判定item的类型
	        #将数据写入数据库时，如何保证数据的一致性
	        if item.__class__.__name__ == 'DetailItem':
	            print(item['new_id'],item['content'])
	            pass
	        else:
	            print(item['new_num'],item['title'])
	        return item

	//items.py
	class SunproItem(scrapy.Item):
	    # define the fields for your item here like:
	    title = scrapy.Field()
	    new_num = scrapy.Field()


	class DetailItem(scrapy.Item):
	    new_id = scrapy.Field()
	    content = scrapy.Field()

	//sun.py
	import scrapy
	from scrapy.linkextractors import LinkExtractor
	from scrapy.spiders import CrawlSpider, Rule
	from sunPro.items import SunproItem,DetailItem

	#需求：爬取sun网站中的编号，新闻标题，新闻内容，标号
	class SunSpider(CrawlSpider):
	    name = 'sun'
	    # allowed_domains = ['www.xxx.com']
	    start_urls = ['http://wz.sun0769.com/index.php/question/questionType?type=4&page=']

	    #链接提取器：根据指定规则（allow="正则"）进行指定链接的提取
	    link = LinkExtractor(allow=r'type=4&page=\d+')
	    link_detail = LinkExtractor(allow=r'question/\d+/\d+\.shtml')
	    rules = (
	        #规则解析器：将链接提取器提取到的链接进行指定规则（callback）的解析操作
	        Rule(link, callback='parse_item', follow=True),
	        #follow=True：可以将链接提取器 继续作用到 连接提取器提取到的链接 所对应的页面中
	        Rule(link_detail,callback='parse_detail')
	    )
	    #http://wz.sun0769.com/html/question/201907/421001.shtml
	    #http://wz.sun0769.com/html/question/201907/420987.shtml

	    #解析新闻编号和新闻的标题
	    #如下两个解析方法中是不可以实现请求传参！
	    #如法将两个解析方法解析的数据存储到同一个item中，可以以此存储到两个item
	    def parse_item(self, response):
	        #注意：xpath表达式中不可以出现tbody标签
	        tr_list = response.xpath('//*[@id="morelist"]/div/table[2]//tr/td/table//tr')
	        for tr in tr_list:
	            new_num = tr.xpath('./td[1]/text()').extract_first()
	            new_title = tr.xpath('./td[2]/a[2]/@title').extract_first()
	            item = SunproItem()
	            item['title'] = new_title
	            item['new_num'] = new_num

	            yield item

	    #解析新闻内容和新闻编号
	    def parse_detail(self,response):
	        new_id = response.xpath('/html/body/div[9]/table[1]//tr/td[2]/span[2]/text()').extract_first()
	        new_content = response.xpath('/html/body/div[9]/table[2]//tr[1]//text()').extract()
	        new_content = ''.join(new_content)

	        # print(new_id,new_content)
	        item = DetailItem()
	        item['content'] = new_content
	        item['new_id'] = new_id

	        yield item

分布式
==================================================================================

.. code-block:: python
	:linenos:

	//pipelines.py
	class FbsproPipeline(object):
	    def process_item(self, item, spider):
	        return item

	//items.py
	class FbsproItem(scrapy.Item):
	    # define the fields for your item here like:
	    title = scrapy.Field()
	    new_num = scrapy.Field()
	    # pass

	//fbs.py
	import scrapy
	from scrapy.linkextractors import LinkExtractor
	from scrapy.spiders import CrawlSpider, Rule
	from fbsPro.items import FbsproItem
	from scrapy_redis.spiders import RedisCrawlSpider
	class FbsSpider(RedisCrawlSpider):
	    name = 'fbs'
	    # allowed_domains = ['www.xxx.com']
	    # start_urls = ['http://www.xxx.com/']
	    redis_key = 'sun'

	    rules = (
	        Rule(LinkExtractor(allow=r'type=4&page=\d+'), callback='parse_item', follow=True),
	    )

	    def parse_item(self, response):
	        tr_list = response.xpath('//*[@id="morelist"]/div/table[2]//tr/td/table//tr')
	        for tr in tr_list:
	            new_num = tr.xpath('./td[1]/text()').extract_first()
	            new_title = tr.xpath('./td[2]/a[2]/@title').extract_first()

	            item = FbsproItem()
	            item['title'] = new_title
	            item['new_num'] = new_num

	            yield item

	//settings.py
	ROBOTSTXT_OBEY = False
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	#指定管道
	ITEM_PIPELINES = {
	    'scrapy_redis.pipelines.RedisPipeline': 400
	}
	#指定调度器
	# 增加了一个去重容器类的配置, 作用使用Redis的set集合来存储请求的指纹数据, 从而实现请求去重的持久化
	DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
	# 使用scrapy-redis组件自己的调度器
	SCHEDULER = "scrapy_redis.scheduler.Scheduler"
	# 配置调度器是否要持久化, 也就是当爬虫结束了, 要不要清空Redis中请求队列和去重指纹的set。如果是True, 就表示要持久化存储, 就不清空数据, 否则清空数据
	SCHEDULER_PERSIST = True

	#指定redis
	REDIS_HOST = '127.0.0.1' #redis远程服务器的ip（修改）
	REDIS_PORT = 6379

增量式爬虫
**********************************************************************************

.. code-block:: python
	:linenos:

	//movie.py
	import scrapy
	from scrapy.linkextractors import LinkExtractor
	from scrapy.spiders import CrawlSpider, Rule

	from redis import Redis
	from moviePro.items import MovieproItem
	class MovieSpider(CrawlSpider):
	    name = 'movie'
	    # allowed_domains = ['www.ccc.com']
	    start_urls = ['https://www.4567tv.tv/frim/index1.html']

	    rules = (
	        Rule(LinkExtractor(allow=r'/frim/index1-\d+\.html'), callback='parse_item', follow=True),
	    )
	    # 创建redis链接对象
	    conn = Redis(host='127.0.0.1', port=6379)

	    #用于解析每一个页码对应页面中的电影详情页的url
	    def parse_item(self, response):
	        li_list = response.xpath('/html/body/div[1]/div/div/div/div[2]/ul/li')
	        for li in li_list:
	            # 获取详情页的url
	            detail_url = 'https://www.4567tv.tv' + li.xpath('./div/a/@href').extract_first()

	            # 将详情页的url存入redis的set中
	            ex = self.conn.sadd('urls', detail_url)
	            if ex == 1:
	                print('该url没有被爬取过，可以进行数据的爬取')
	                yield scrapy.Request(url=detail_url, callback=self.parst_detail)
	            else:
	                print('数据还没有更新，暂无新数据可爬取！')

	    # 解析详情页中的电影名称和类型，进行持久化存储
	    def parst_detail(self, response):
	        item = MovieproItem()
	        item['name'] = response.xpath('/html/body/div[1]/div/div/div/div[2]/h1/text()').extract_first()
	        item['desc'] = response.xpath('/html/body/div[1]/div/div/div/div[2]/p[5]/span[2]//text()').extract()
	        item['desc'] = ''.join(item['desc'])
	        yield item

	//items.py
	class MovieproItem(scrapy.Item):
	    # define the fields for your item here like:
	    name = scrapy.Field()
	    desc = scrapy.Field()
	    # pass

	//pipelines.py
	from redis import Redis
	class MovieproPipeline(object):
	    conn = None
	    def open_spider(self,spider):
	        self.conn = spider.conn
	    def process_item(self, item, spider):
	        dic = {
	            'name':item['name'],
	            'desc':item['desc']
	        }
	        # print(dic)
	        self.conn.lpush('movieData',dic)
	        return item

	//settings.py
	USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36'

	# Obey robots.txt rules
	ROBOTSTXT_OBEY = False
	LOG_LEVEL = 'ERROR'

	//settings.py
	ITEM_PIPELINES = {
	   'moviePro.pipelines.MovieproPipeline': 300,
	}

.. |image0| image:: /_static/python/5_components.png



















































































































