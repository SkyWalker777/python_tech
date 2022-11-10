Python 爬虫 1
##################################################################################

.. note::

	| 参考地址: https://b23.tv/QdvJwM
	| 文档地址1: https://book.apeland.cn/details/64/

爬虫基础简介
**********************************************************************************

爬虫相关知识
==================================================================================

| 1.你是否在夜深人静的时候，想看一些会让你更睡不着的图片却苦于没有资源...
| 2.你是否在节假日出行高峰的时候，想快速抢购火车票成功...
| 3.你是否在网上购物的时候，想快速且精准的定位到口碑质量最好的商品...

* 什么是爬虫

通过编写程序，模拟浏览器上网，然后让其去互联网上抓取数据的过程。

* 爬虫的价值

| 实际应用
| 就业

* 爬虫究竟是合法还是违法的？

::

	- 在法律中是不被禁止
	- 具有违法风险
	- 善意爬虫  恶意爬虫

* 爬虫带来的风险

::

	- 爬虫干扰了被访问网站的正常运营
	- 爬虫抓取了收到法律保护的特定类型的数据或信息

* 如何在使用编写爬虫的过程中避免进入局子的厄运呢？

::

	- 时常的优化自己的程序，避免干扰被访问网站的正常运行
	- 在使用，传播爬取到的数据时，审查抓取到的内容，如果发现了涉及到用户隐私
	商业机密等敏感内容需要及时停止爬取或传播


* 爬虫在使用场景中的分类

::

	- 通用爬虫：
	    抓取系统重要组成部分。抓取的是一整张页面数据。
	- 聚焦爬虫：
	    是建立在通用爬虫的基础之上。抓取的是页面中特定的局部内容。
	- 增量式爬虫：
	    检测网站中数据更新的情况。只会抓取网站中最新更新出来的数据。

爬虫的矛与盾
==================================================================================

* 反爬机制

门户网站，可以通过制定相应的策略或者技术手段，防止爬虫程序进行网站数据的爬取。

* 反反爬策略

爬虫程序可以通过制定相关的策略或者技术手段，破解门户网站中具备的反爬机制，从而可以获取门户网站中相关的数据。

* robots.txt协议：

君子协议。规定了网站中哪些数据可以被爬虫爬取哪些数据不可以被爬取。

* http协议

::

	- 概念：就是服务器和客户端进行数据交互的一种形式。

* 常用请求头信息

::

	- User-Agent：请求载体的身份标识
	- Connection：请求完毕后，是断开连接还是保持连接

* 常用响应头信息

::

	- Content-Type：服务器响应回客户端的数据类型

* https协议：

::

	- 安全的超文本传输协议

* 加密方式

::

	- 对称秘钥加密
	- 非对称秘钥加密
	- 证书秘钥加密

requests 模块基础
**********************************************************************************

requests 介绍
==================================================================================

* requests模块

::

	- urllib模块
	- requests模块

| requests模块：python中原生的一款基于网络请求的模块，功能非常强大，简单便捷，效率极高。
| 作用：模拟浏览器发请求。

* 如何使用：（requests模块的编码流程）

::

	- 指定url
	    - UA伪装
	    - 请求参数的处理
	- 发起请求
	- 获取响应数据
	- 持久化存储

* 环境安装： pip install requests

* 实战编码：

::

	- 需求：爬取搜狗首页的页面数据

* 实战巩固

::

	- 需求：爬取搜狗指定词条对应的搜索结果页面（简易网页采集器）
	    - UA检测
	    - UA伪装
	- 需求：破解百度翻译
	    - post请求（携带了参数）
	    - 响应数据是一组json数据
	- 需求：爬取豆瓣电影分类排行榜 https://movie.douban.com/中的电影详情数据

	- 作业：爬取肯德基餐厅查询http://www.kfc.com.cn/kfccda/index.aspx中指定地点的餐厅数据

	- 需求：爬取国家药品监督管理总局中基于中华人民共和国化妆品生产许可证相关数据
	            http://125.35.6.84:81/xk/
	    - 动态加载数据
	    - 首页中对应的企业信息数据是通过ajax动态请求到的。

	    http://125.35.6.84:81/xk/itownet/portal/dzpz.jsp?id=e6c1aa332b274282b04659a6ea30430a
	    http://125.35.6.84:81/xk/itownet/portal/dzpz.jsp?id=f63f61fe04684c46a016a45eac8754fe
	    - 通过对详情页url的观察发现：
	        - url的域名都是一样的，只有携带的参数（id）不一样
	        - id值可以从首页对应的ajax请求到的json串中获取
	        - 域名和id值拼接处一个完整的企业对应的详情页的url
	    - 详情页的企业详情数据也是动态加载出来的
	        - http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById
	        - http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById
	        - 观察后发现：
	            - 所有的post请求的url都是一样的，只有参数id值是不同。
	            - 如果我们可以批量获取多家企业的id后，就可以将id和url形成一个完整的详情页对应详情数据的ajax请求的url

* 数据解析

::

	聚焦爬虫
	正则
	bs4
	xpath

爬取搜狗首页的页面数据
==================================================================================

.. code-block:: python

	#- 需求：爬取搜狗首页的页面数据
	import requests
	if __name__ == "__main__":
	    #step_1:指定url
	    url = 'https://www.sogou.com/'
	    #step_2:发起请求
	    #get方法会返回一个响应对象
	    response = requests.get(url=url)
	    #step_3:获取响应数据.text返回的是字符串形式的响应数据
	    page_text = response.text
	    print(page_text)
	    #step_4:持久化存储
	    with open('./sogou.html','w',encoding='utf-8') as fp:
	        fp.write(page_text)
	    print('爬取数据结束！！！')

让爬虫对应的请求载体身份标识伪装成某一款浏览器
==================================================================================

.. code-block:: python

	#!/usr/bin/env python 
	# -*- coding:utf-8 -*-

	#UA：User-Agent（请求载体的身份标识）
	#UA检测：门户网站的服务器会检测对应请求的载体身份标识，如果检测到请求的载体身份标识为某一款浏览器，
	#说明该请求是一个正常的请求。但是，如果检测到请求的载体身份标识不是基于某一款浏览器的，则表示该请求
	#为不正常的请求（爬虫），则服务器端就很有可能拒绝该次请求。

	#UA伪装：让爬虫对应的请求载体身份标识伪装成某一款浏览器
	import requests
	if __name__ == "__main__":
	    #UA伪装：将对应的User-Agent封装到一个字典中
	    headers = {
	        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	    }
	    url = 'https://www.sogou.com/web'
	    #处理url携带的参数：封装到字典中
	    kw = input('enter a word:')
	    param = {
	        'query':kw
	    }
	    #对指定的url发起的请求对应的url是携带参数的，并且请求过程中处理了参数
	    response = requests.get(url=url,params=param,headers=headers)

	    page_text = response.text
	    fileName = kw+'.html'
	    with open(fileName,'w',encoding='utf-8') as fp:
	        fp.write(page_text)
	    print(fileName,'保存成功！！！')

破解百度翻译
==================================================================================

开发者工具中查看 ajax 的请求, 选中 XHR 选项进行筛选。

XHR，全称为XMLHttpRequest，用于与服务器交互数据，是ajax功能实现所依赖的对象，jquery中的ajax就是对 xhr的封装。

.. code-block:: python

	#!/usr/bin/env python 
	# -*- coding:utf-8 -*-
	import requests
	import json
	if __name__ == "__main__":
	    #1.指定url
	    post_url = 'https://fanyi.baidu.com/sug'
	    #2.进行UA伪装
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'

	    }
	    #3.post请求参数处理（同get请求一致）
	    word = input('enter a word:')
	    data = {
	        'kw':word
	    }
	    #4.请求发送
	    response = requests.post(url=post_url,data=data,headers=headers)
	    #5.获取响应数据:json()方法返回的是obj（如果确认响应数据是json类型的，才可以使用json（））
	    dic_obj = response.json()

	    #持久化存储
	    fileName = word+'.json'
	    fp = open(fileName,'w',encoding='utf-8')
	    json.dump(dic_obj,fp=fp,ensure_ascii=False)

	    print('over!!!')

requests 实战之豆瓣电影爬取
==================================================================================

.. code-block:: python

	import requests
	import json
	if __name__ == "__main__":
	    url = 'https://movie.douban.com/j/chart/top_list'
	    param = {
	        'type': '24',
	        'interval_id': '100:90',
	        'action':'',
	        'start': '0',#从库中的第几部电影去取
	        'limit': '20',#一次取出的个数
	    }
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'

	    }
	    response = requests.get(url=url,params=param,headers=headers)

	    list_data = response.json()

	    fp = open('./douban.json','w',encoding='utf-8')
	    json.dump(list_data,fp=fp,ensure_ascii=False)
	    print('over!!!')

requests 实战之药监总局相关数据爬取
==================================================================================

.. code-block:: python

	import  requests
	import json
	if __name__ == "__main__":
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'

	    }
	    id_list = []  # 存储企业的id
	    all_data_list = []  # 存储所有的企业详情数据
	    #批量获取不同企业的id值
	    url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsList'
	    #参数的封装
	    for page in range(1,6):
	        page = str(page)
	        data = {
	            'on': 'true',
	            'page': page,
	            'pageSize': '15',
	            'productName':'',
	            'conditionType': '1',
	            'applyname':'',
	            'applysn':'',
	        }
	        json_ids = requests.post(url=url,headers=headers,data=data).json()
	        for dic in json_ids['list']:
	            id_list.append(dic['ID'])

	    #获取企业详情数据
	    post_url = 'http://125.35.6.84:81/xk/itownet/portalAction.do?method=getXkzsById'
	    for id in id_list:
	        data = {
	            'id':id
	        }
	        detail_json = requests.post(url=post_url,headers=headers,data=data).json()
	        # print(detail_json,'-------------ending-----------')
	        all_data_list.append(detail_json)

	    #持久化存储all_data_list
	    fp = open('./allData.json','w',encoding='utf-8')
	    json.dump(all_data_list,fp=fp,ensure_ascii=False)
	    print('over!!!')

数据解析
**********************************************************************************

聚焦爬虫:爬取页面中指定的页面内容。

::

	- 编码流程：
	    - 指定url
	    - 发起请求
	    - 获取响应数据
	    - 数据解析
	    - 持久化存储

数据解析分类：

::

	- 正则
	- bs4
	- xpath（***）

数据解析原理概述：

::

	- 解析的局部的文本内容都会在标签之间或者标签对应的属性中进行存储
	- 1.进行指定标签的定位
	- 2.标签或者标签对应的属性中存储的数据值进行提取（解析）

正则解析：

::

	<div class="thumb">

	<a href="/article/121721100" target="_blank">
	<img src="//pic.qiushibaike.com/system/pictures/12172/121721100/medium/DNXDX9TZ8SDU6OK2.jpg" alt="指引我有前进的方向">
	</a>

	</div>

	ex = '<div class="thumb">.*?<img src="(.*?)" alt.*?</div>'


bs4进行数据解析

::

	- 数据解析的原理：
	    - 1.标签定位
	    - 2.提取标签、标签属性中存储的数据值
	- bs4数据解析的原理：
	    - 1.实例化一个BeautifulSoup对象，并且将页面源码数据加载到该对象中
	    - 2.通过调用BeautifulSoup对象中相关的属性或者方法进行标签定位和数据提取
	- 环境安装：
	    - pip install bs4
	    - pip install lxml
	- 如何实例化BeautifulSoup对象：
	    - from bs4 import BeautifulSoup
	    - 对象的实例化：
	        - 1.将本地的html文档中的数据加载到该对象中
	                fp = open('./test.html','r',encoding='utf-8')
	                soup = BeautifulSoup(fp,'lxml')
	        - 2.将互联网上获取的页面源码加载到该对象中
	                page_text = response.text
	                soup = BeatifulSoup(page_text,'lxml')
	    - 提供的用于数据解析的方法和属性：
	        - soup.tagName:返回的是文档中第一次出现的tagName对应的标签
	        - soup.find():
	            - find('tagName'):等同于soup.div
	            - 属性定位：
	                -soup.find('div',class_/id/attr='song')
	        - soup.find_all('tagName'):返回符合要求的所有标签（列表）
	    - select：
	        - select('某种选择器（id，class，标签...选择器）'),返回的是一个列表。
	        - 层级选择器：
	            - soup.select('.tang > ul > li > a')：>表示的是一个层级
	            - oup.select('.tang > ul a')：空格表示的多个层级
	    - 获取标签之间的文本数据：
	        - soup.a.text/string/get_text()
	        - text/get_text():可以获取某一个标签中所有的文本内容
	        - string：只可以获取该标签下面直系的文本内容
	    - 获取标签中属性值：
	        - soup.a['href']

xpath解析：最常用且最便捷高效的一种解析方式。通用性。

::

	- xpath解析原理：
	    - 1.实例化一个etree的对象，且需要将被解析的页面源码数据加载到该对象中。
	    - 2.调用etree对象中的xpath方法结合着xpath表达式实现标签的定位和内容的捕获。
	- 环境的安装：
	    - pip install lxml
	- 如何实例化一个etree对象:from lxml import etree
	    - 1.将本地的html文档中的源码数据加载到etree对象中：
	        etree.parse(filePath)
	    - 2.可以将从互联网上获取的源码数据加载到该对象中
	        etree.HTML('page_text')
	    - xpath('xpath表达式')
	- xpath表达式:
	    - /:表示的是从根节点开始定位。表示的是一个层级。
	    - //:表示的是多个层级。可以表示从任意位置开始定位。
	    - 属性定位：//div[@class='song'] tag[@attrName="attrValue"]
	    - 索引定位：//div[@class="song"]/p[3] 索引是从1开始的。
	    - 取文本：
	        - /text() 获取的是标签中直系的文本内容
	        - //text() 标签中非直系的文本内容（所有的文本内容）
	    - 取属性：
	        /@attrName     ==>img/src

作业：爬取站长素材中免费简历模板

爬取图片
==================================================================================

.. code-block:: python

	import requests
	if __name__ == "__main__":
	    #如何爬取图片数据
	    url = 'https://pic.qiushibaike.com/system/pictures/12172/121721055/medium/9OSVY4ZSU4NN6T7V.jpg'
	    #content返回的是二进制形式的图片数据
	    # text（字符串） content（二进制）json() (对象)
	    img_data = requests.get(url=url).content

	    with open('./qiutu.jpg','wb') as fp:
	        fp.write(img_data)

正则解析
==================================================================================

.. code-block:: python

	import requests
	import re
	import os
	#需求：爬取糗事百科中糗图板块下所有的糗图图片
	if __name__ == "__main__":
	    #创建一个文件夹，保存所有的图片
	    if not os.path.exists('./qiutuLibs'):
	        os.mkdir('./qiutuLibs')

	    url = 'https://www.qiushibaike.com/pic/'
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'

	    }
	    #使用通用爬虫对url对应的一整张页面进行爬取
	    page_text = requests.get(url=url,headers=headers).text

	    #使用聚焦爬虫将页面中所有的糗图进行解析/提取
	    ex = '<div class="thumb">.*?<img src="(.*?)" alt.*?</div>'
	    img_src_list = re.findall(ex,page_text,re.S)
	    # print(img_src_list)
	    for src in img_src_list:
	        #拼接出一个完整的图片url
	        src = 'https:'+src
	        #请求到了图片的二进制数据
	        img_data = requests.get(url=src,headers=headers).content
	        #生成图片名称
	        img_name = src.split('/')[-1]
	        #图片存储的路径
	        imgPath = './qiutuLibs/'+img_name
	        with open(imgPath,'wb') as fp:
	            fp.write(img_data)
	            print(img_name,'下载成功！！！')

正则解析-分页爬取
==================================================================================

.. code-block:: python

	import requests
	import re
	import os
	#需求：爬取糗事百科中糗图板块下所有的糗图图片
	if __name__ == "__main__":
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'

	    }
	    #创建一个文件夹，保存所有的图片
	    if not os.path.exists('./qiutuLibs'):
	        os.mkdir('./qiutuLibs')
	    #设置一个通用的url模板
	    url = 'https://www.qiushibaike.com/pic/page/%d/?s=5184961'
	    # pageNum = 2

	    for pageNum in range(1,3):
	        #对应页码的url
	        new_url = format(url%pageNum)


	        #使用通用爬虫对url对应的一整张页面进行爬取
	        page_text = requests.get(url=new_url,headers=headers).text

	        #使用聚焦爬虫将页面中所有的糗图进行解析/提取
	        ex = '<div class="thumb">.*?<img src="(.*?)" alt.*?</div>'
	        img_src_list = re.findall(ex,page_text,re.S)
	        # print(img_src_list)
	        for src in img_src_list:
	            #拼接出一个完整的图片url
	            src = 'https:'+src
	            #请求到了图片的二进制数据
	            img_data = requests.get(url=src,headers=headers).content
	            #生成图片名称
	            img_name = src.split('/')[-1]
	            #图片存储的路径
	            imgPath = './qiutuLibs/'+img_name
	            with open(imgPath,'wb') as fp:
	                fp.write(img_data)
	                print(img_name,'下载成功！！！')

bs4解析基础
==================================================================================

.. code-block:: python

	from bs4 import BeautifulSoup
	if __name__ == "__main__":
	    #将本地的html文档中的数据加载到该对象中
	    fp = open('./test.html','r',encoding='utf-8')
	    soup = BeautifulSoup(fp,'lxml')
	    # print(soup)
	    # print(soup.a) #soup.tagName 返回的是html中第一次出现的tagName标签
	    # print(soup.div)
	    #find('tagName'):等同于soup.div
	    # print(soup.find('div'))  #print(soup.div)
	    # print(soup.find('div',class_='song').string)
	    # print(soup.find_all('a'))
	    # print(soup.select('.tang'))
	    print(soup.select('.tang > ul a')[0]['href'])

bs4案例
==================================================================================

.. code-block:: python

	import requests
	from bs4 import BeautifulSoup
	#需求：爬取三国演义小说所有的章节标题和章节内容http://www.shicimingju.com/book/sanguoyanyi.html
	if __name__ == "__main__":
	    #对首页的页面数据进行爬取
	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	    }
	    url = 'http://www.shicimingju.com/book/sanguoyanyi.html'
	    page_text = requests.get(url=url,headers=headers).text

	    #在首页中解析出章节的标题和详情页的url
	    #1.实例化BeautifulSoup对象，需要将页面源码数据加载到该对象中
	    soup = BeautifulSoup(page_text,'lxml')
	    #解析章节标题和详情页的url
	    li_list = soup.select('.book-mulu > ul > li')
	    fp = open('./sanguo.txt','w',encoding='utf-8')
	    for li in li_list:
	        title = li.a.string
	        detail_url = 'http://www.shicimingju.com'+li.a['href']
	        #对详情页发起请求，解析出章节内容
	        detail_page_text = requests.get(url=detail_url,headers=headers).text
	        #解析出详情页中相关的章节内容
	        detail_soup = BeautifulSoup(detail_page_text,'lxml')
	        div_tag = detail_soup.find('div',class_='chapter_content')
	        #解析到了章节的内容
	        content = div_tag.text
	        fp.write(title+':'+content+'\n')
	        print(title,'爬取成功！！！')

xpath解析基础
==================================================================================

.. code-block:: python

	from lxml import etree
	if __name__ == "__main__":
	    #实例化好了一个etree对象，且将被解析的源码加载到了该对象中
	    tree = etree.parse('test.html')
	    # r = tree.xpath('/html/body/div')
	    # r = tree.xpath('/html//div')
	    # r = tree.xpath('//div')
	    # r = tree.xpath('//div[@class="song"]')
	    # r = tree.xpath('//div[@class="tang"]//li[5]/a/text()')[0]
	    # r = tree.xpath('//li[7]//text()')
	    # r = tree.xpath('//div[@class="tang"]//text()')
	    r = tree.xpath('//div[@class="song"]/img/@src')

	    print(r)

xpath解析案例-58二手房
==================================================================================

.. code-block:: python

	import requests
	from lxml import etree
	#需求：爬取58二手房中的房源信息
	if __name__ == "__main__":
	    headers = {
	        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36'
	    }
	    #爬取到页面源码数据
	    url = 'https://bj.58.com/ershoufang/'
	    page_text = requests.get(url=url,headers=headers).text

	    #数据解析
	    tree = etree.HTML(page_text)
	    #存储的就是li标签对象
	    li_list = tree.xpath('//ul[@class="house-list-wrap"]/li')
	    fp = open('58.txt','w',encoding='utf-8')
	    for li in li_list:
	        #局部解析
	        title = li.xpath('./div[2]/h2/a/text()')[0]
	        print(title)
	        fp.write(title+'\n')

xpath解析案例-4k图片解析爬取
==================================================================================

.. code-block:: python

	# -*- coding:utf-8 -*-
	#需求：解析下载图片数据 http://pic.netbian.com/4kmeinv/
	import requests
	from lxml import etree
	import os
	if __name__ == "__main__":
	    url = 'http://pic.netbian.com/4kmeinv/'
	    headers = {
	        'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36'
	    }
	    response = requests.get(url=url,headers=headers)
	    #手动设定响应数据的编码格式
	    # response.encoding = 'utf-8'
	    page_text = response.text

	    #数据解析：src的属性值  alt属性
	    tree = etree.HTML(page_text)
	    li_list = tree.xpath('//div[@class="slist"]/ul/li')


	    #创建一个文件夹
	    if not os.path.exists('./picLibs'):
	        os.mkdir('./picLibs')

	    for li in li_list:
	        img_src = 'http://pic.netbian.com'+li.xpath('./a/img/@src')[0]
	        img_name = li.xpath('./a/img/@alt')[0]+'.jpg'
	        #通用处理中文乱码的解决方案
	        img_name = img_name.encode('iso-8859-1').decode('gbk')

	        # print(img_name,img_src)
	        #请求图片进行持久化存储
	        img_data = requests.get(url=img_src,headers=headers).content
	        img_path = 'picLibs/'+img_name
	        with open(img_path,'wb') as fp:
	            fp.write(img_data)
	            print(img_name,'下载成功！！！')

xpath解析案例-全国城市名称爬取
==================================================================================

.. code-block:: python

	import requests
	from lxml import etree
	#项目需求：解析出所有城市名称https://www.aqistudy.cn/historydata/
	if __name__ == "__main__":
	    # headers = {
	    #     'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36'
	    # }
	    # url = 'https://www.aqistudy.cn/historydata/'
	    # page_text = requests.get(url=url,headers=headers).text
	    #
	    # tree = etree.HTML(page_text)
	    # host_li_list = tree.xpath('//div[@class="bottom"]/ul/li')
	    # all_city_names = []
	    # #解析到了热门城市的城市名称
	    # for li in host_li_list:
	    #     hot_city_name = li.xpath('./a/text()')[0]
	    #     all_city_names.append(hot_city_name)
	    #
	    # #解析的是全部城市的名称
	    # city_names_list = tree.xpath('//div[@class="bottom"]/ul/div[2]/li')
	    # for li in city_names_list:
	    #     city_name = li.xpath('./a/text()')[0]
	    #     all_city_names.append(city_name)
	    #
	    # print(all_city_names,len(all_city_names))

	    headers = {
	        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36'
	    }
	    url = 'https://www.aqistudy.cn/historydata/'
	    page_text = requests.get(url=url, headers=headers).text

	    tree = etree.HTML(page_text)
	    #解析到热门城市和所有城市对应的a标签
	    # //div[@class="bottom"]/ul/li/          热门城市a标签的层级关系
	    # //div[@class="bottom"]/ul/div[2]/li/a  全部城市a标签的层级关系
	    a_list = tree.xpath('//div[@class="bottom"]/ul/li/a | //div[@class="bottom"]/ul/div[2]/li/a')
	    all_city_names = []
	    for a in a_list:
	        city_name = a.xpath('./text()')[0]
	        all_city_names.append(city_name)
	    print(all_city_names,len(all_city_names))

验证码
**********************************************************************************

验证码识别

| 验证码和爬虫之间的爱恨情仇？
| 反爬机制：验证码.识别验证码图片中的数据，用于模拟登陆操作。

识别验证码的操作：

::

	- 人工肉眼识别。（不推荐）
	- 第三方自动识别（推荐）
		- 云打码：http://www.yundama.com/demo.html

云打码的使用流程：

::

	- 注册：普通和开发者用户
	- 登录：
	    - 普通用户的登录：查询该用户是否还有剩余的题分
	    - 开发者用户的登录：
	        - 创建一个软件：我的软件-》添加新软件-》录入软件名称-》提交（软件id和秘钥）
	        - 下载示例代码：开发文档-》点此下载：云打码接口DLL-》PythonHTTP示例下载

| 实战：识别古诗文网登录页面中的验证码。
| 使用打码平台识别验证码的编码流程：

::

	- 将验证码图片进行本地下载
	- 调用平台提供的示例代码进行图片数据识别

古诗文网验证码识别
==================================================================================

.. code-block:: python

	CodeClass ====>
	class YDMHttp:
	    apiurl = 'http://api.yundama.com/api.php'
	    username = ''
	    password = ''
	    appid = ''
	    appkey = ''

	    def __init__(self, username, password, appid, appkey):
	        self.username = username
	        self.password = password
	        self.appid = str(appid)
	        self.appkey = appkey

	    def request(self, fields, files=[]):
	        response = self.post_url(self.apiurl, fields, files)
	        response = json.loads(response)
	        return response

	    def balance(self):
	        data = {'method': 'balance', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey}
	        response = self.request(data)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['balance']
	        else:
	            return -9001

	    def login(self):
	        data = {'method': 'login', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey}
	        response = self.request(data)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['uid']
	        else:
	            return -9001

	    def upload(self, filename, codetype, timeout):
	        data = {'method': 'upload', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'codetype': str(codetype), 'timeout': str(timeout)}
	        file = {'file': filename}
	        response = self.request(data, file)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['cid']
	        else:
	            return -9001

	    def result(self, cid):
	        data = {'method': 'result', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'cid': str(cid)}
	        response = self.request(data)
	        return response and response['text'] or ''

	    def decode(self, filename, codetype, timeout):
	        cid = self.upload(filename, codetype, timeout)
	        if (cid > 0):
	            for i in range(0, timeout):
	                result = self.result(cid)
	                if (result != ''):
	                    return cid, result
	                else:
	                    time.sleep(1)
	            return -3003, ''
	        else:
	            return cid, ''

	    def report(self, cid):
	        data = {'method': 'report', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'cid': str(cid), 'flag': '0'}
	        response = self.request(data)
	        if (response):
	            return response['ret']
	        else:
	            return -9001

	    def post_url(self, url, fields, files=[]):
	        for key in files:
	            files[key] = open(files[key], 'rb');
	        res = requests.post(url, files=files, data=fields)
	        return res.text

	import requests
	from lxml import etree
	from CodeClass import YDMHttp

	#封装识别验证码图片的函数
	def getCodeText(imgPath,codeType):
	    # 普通用户用户名
	    username = 'bobo328410948'

	    # 普通用户密码
	    password = 'bobo328410948'

	    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appid = 6003

	    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appkey = '1f4b564483ae5c907a1d34f8e2f2776c'

	    # 图片文件：即将被识别的验证码图片的路径
	    filename = imgPath

	    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
	    codetype = codeType

	    # 超时时间，秒
	    timeout = 20
	    result = None
	    # 检查
	    if (username == 'username'):
	        print('请设置好相关参数再测试')
	    else:
	        # 初始化
	        yundama = YDMHttp(username, password, appid, appkey)

	        # 登陆云打码
	        uid = yundama.login();
	        print('uid: %s' % uid)

	        # 查询余额
	        balance = yundama.balance();
	        print('balance: %s' % balance)

	        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
	        cid, result = yundama.decode(filename, codetype, timeout);
	        print('cid: %s, result: %s' % (cid, result))
	    return result


	#将验证码图片下载到本地
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}
	url = 'https://so.gushiwen.org/user/login.aspx?from=http://so.gushiwen.org/user/collect.aspx'
	page_text = requests.get(url=url,headers=headers).text
	#解析验证码图片img中src属性值
	tree = etree.HTML(page_text)
	code_img_src = 'https://so.gushiwen.org'+tree.xpath('//*[@id="imgCode"]/@src')[0]
	img_data = requests.get(url=code_img_src,headers=headers).content
	#将验证码图片保存到了本地
	with open('./code.jpg','wb') as fp:
	    fp.write(img_data)


	#调用打码平台的示例程序进行验证码图片数据识别
	code_text = getCodeText('code.jpg',1004)

	print('识别结果为：',code_text)

requests 模块高级
**********************************************************************************

模拟登录：

::

	- 爬取基于某些用户的用户信息。

需求：对人人网进行模拟登录。

::

	- 点击登录按钮之后会发起一个post请求
	- post请求中会携带登录之前录入的相关的登录信息（用户名，密码，验证码......）
	- 验证码：每次请求都会变化

需求：爬取当前用户的相关的用户信息（个人主页中显示的用户信息）

| http/https协议特性：无状态。
| 没有请求到对应页面数据的原因：发起的第二次基于个人主页页面请求的时候，服务器端并不知道该此请求是基于登录状态下的请求。

cookie：用来让服务器端记录客户端的相关状态。

::

	- 手动处理：通过抓包工具获取cookie值，将该值封装到headers中。（不建议）
	- 自动处理：
	    - cookie值的来源是哪里？
	        - 模拟登录post请求后，由服务器端创建。
	    session会话对象：
	        - 作用：
	            1.可以进行请求的发送。
	            2.如果请求过程中产生了cookie，则该cookie会被自动存储/携带在该session对象中。
	    - 创建一个session对象：session = requests.Session()
	    - 使用session对象进行模拟登录post请求的发送（cookie就会被存储在session中）
	    - session对象对个人主页对应的get请求进行发送（携带了cookie）

| 代理：破解封IP这种反爬机制。
| 什么是代理：- 代理服务器。

代理的作用：

::

	- 突破自身IP访问的限制。
	- 隐藏自身真实IP

代理相关的网站：

::

	- 快代理
	- 西祠代理
	- www.goubanjia.com

代理ip的类型：

::

	- http：应用到http协议对应的url中
	- https：应用到https协议对应的url中

代理ip的匿名度：

::

	- 透明：服务器知道该次请求使用了代理，也知道请求对应的真实ip
	- 匿名：知道使用了代理，不知道真实ip
	- 高匿：不知道使用了代理，更不知道真实的ip

工具类
==================================================================================

.. code-block:: python

	import http.client, mimetypes, urllib, json, time, requests

	######################################################################

	class YDMHttp:
	    apiurl = 'http://api.yundama.com/api.php'
	    username = ''
	    password = ''
	    appid = ''
	    appkey = ''

	    def __init__(self, username, password, appid, appkey):
	        self.username = username
	        self.password = password
	        self.appid = str(appid)
	        self.appkey = appkey

	    def request(self, fields, files=[]):
	        response = self.post_url(self.apiurl, fields, files)
	        response = json.loads(response)
	        return response

	    def balance(self):
	        data = {'method': 'balance', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey}
	        response = self.request(data)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['balance']
	        else:
	            return -9001

	    def login(self):
	        data = {'method': 'login', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey}
	        response = self.request(data)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['uid']
	        else:
	            return -9001

	    def upload(self, filename, codetype, timeout):
	        data = {'method': 'upload', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'codetype': str(codetype), 'timeout': str(timeout)}
	        file = {'file': filename}
	        response = self.request(data, file)
	        if (response):
	            if (response['ret'] and response['ret'] < 0):
	                return response['ret']
	            else:
	                return response['cid']
	        else:
	            return -9001

	    def result(self, cid):
	        data = {'method': 'result', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'cid': str(cid)}
	        response = self.request(data)
	        return response and response['text'] or ''

	    def decode(self, filename, codetype, timeout):
	        cid = self.upload(filename, codetype, timeout)
	        if (cid > 0):
	            for i in range(0, timeout):
	                result = self.result(cid)
	                if (result != ''):
	                    return cid, result
	                else:
	                    time.sleep(1)
	            return -3003, ''
	        else:
	            return cid, ''

	    def report(self, cid):
	        data = {'method': 'report', 'username': self.username, 'password': self.password, 'appid': self.appid,
	                'appkey': self.appkey, 'cid': str(cid), 'flag': '0'}
	        response = self.request(data)
	        if (response):
	            return response['ret']
	        else:
	            return -9001

	    def post_url(self, url, fields, files=[]):
	        for key in files:
	            files[key] = open(files[key], 'rb');
	        res = requests.post(url, files=files, data=fields)
	        return res.text

模拟登录人人网
==================================================================================

.. code-block:: python

	#编码流程：
	#1.验证码的识别，获取验证码图片的文字数据
	#2.对post请求进行发送（处理请求参数）
	#3.对响应数据进行持久化存储

	from CodeClass import YDMHttp
	import requests
	from lxml import etree
	#封装识别验证码图片的函数
	def getCodeText(imgPath,codeType):
	    # 普通用户用户名
	    username = 'bobo328410948'

	    # 普通用户密码
	    password = 'bobo328410948'

	    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appid = 6003

	    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appkey = '1f4b564483ae5c907a1d34f8e2f2776c'

	    # 图片文件：即将被识别的验证码图片的路径
	    filename = imgPath

	    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
	    codetype = codeType

	    # 超时时间，秒
	    timeout = 20
	    result = None
	    # 检查
	    if (username == 'username'):
	        print('请设置好相关参数再测试')
	    else:
	        # 初始化
	        yundama = YDMHttp(username, password, appid, appkey)

	        # 登陆云打码
	        uid = yundama.login();
	        print('uid: %s' % uid)

	        # 查询余额
	        balance = yundama.balance();
	        print('balance: %s' % balance)

	        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
	        cid, result = yundama.decode(filename, codetype, timeout);
	        print('cid: %s, result: %s' % (cid, result))
	    return result


	#1.对验证码图片进行捕获和识别
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}
	url = 'http://www.renren.com/SysHome.do'
	page_text = requests.get(url=url,headers=headers).text
	tree = etree.HTML(page_text)
	code_img_src = tree.xpath('//*[@id="verifyPic_login"]/@src')[0]
	code_img_data = requests.get(url=code_img_src,headers=headers).content
	with open('./code.jpg','wb') as fp:
	    fp.write(code_img_data)

	#使用云打码提供的示例代码对验证码图片进行识别
	result = getCodeText('code.jpg',1000)
	print(result)
	#post请求的发送（模拟登录）
	login_url = 'http://www.renren.com/ajaxLogin/login?1=1&uniqueTimestamp=2019431046983'
	data = {
	    'email': 'www.zhangbowudi@qq.com',
	    'icode': result,
	    'origURL': 'http://www.renren.com/home',
	    'domain': 'renren.com',
	    'key_id': '1',
	    'captcha_type': 'web_login',
	    'password': '06768edabba49f5f6b762240b311ae5bfa4bcce70627231dd1f08b9c7c6f4375',
	    'rkey': '1028219f2897941c98abdc0839a729df',
	    'f':'https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3Dgds6TUs9Q1ojOatGda5mVsLKC34AYwc5XiN8OuImHRK%26wd%3D%26eqid%3D8e38ba9300429d7d000000035cedf53a',
	}
	response = requests.post(url=login_url,headers=headers,data=data)
	print(response.text)
	print(response.status_code)

	# login_page_text = response.text
	#
	#
	# with open('renren.html','w',encoding='utf-8') as fp:
	#     fp.write(login_page_text)

爬取人人网当前用户的个人详情页数据
==================================================================================

.. code-block:: python

	#编码流程：
	#1.验证码的识别，获取验证码图片的文字数据
	#2.对post请求进行发送（处理请求参数）
	#3.对响应数据进行持久化存储

	from CodeClass import YDMHttp
	import requests
	from lxml import etree
	#封装识别验证码图片的函数
	def getCodeText(imgPath,codeType):
	    # 普通用户用户名
	    username = 'bobo328410948'

	    # 普通用户密码
	    password = 'bobo328410948'

	    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appid = 6003

	    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appkey = '1f4b564483ae5c907a1d34f8e2f2776c'

	    # 图片文件：即将被识别的验证码图片的路径
	    filename = imgPath

	    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
	    codetype = codeType

	    # 超时时间，秒
	    timeout = 20
	    result = None
	    # 检查
	    if (username == 'username'):
	        print('请设置好相关参数再测试')
	    else:
	        # 初始化
	        yundama = YDMHttp(username, password, appid, appkey)

	        # 登陆云打码
	        uid = yundama.login();
	        print('uid: %s' % uid)

	        # 查询余额
	        balance = yundama.balance();
	        print('balance: %s' % balance)

	        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
	        cid, result = yundama.decode(filename, codetype, timeout);
	        print('cid: %s, result: %s' % (cid, result))
	    return result

	#创建一个session对象
	session = requests.Session()

	#1.对验证码图片进行捕获和识别
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}
	url = 'http://www.renren.com/SysHome.do'
	page_text = requests.get(url=url,headers=headers).text
	tree = etree.HTML(page_text)
	code_img_src = tree.xpath('//*[@id="verifyPic_login"]/@src')[0]
	code_img_data = requests.get(url=code_img_src,headers=headers).content
	with open('./code.jpg','wb') as fp:
	    fp.write(code_img_data)

	#使用云打码提供的示例代码对验证码图片进行识别
	result = getCodeText('code.jpg',1000)

	#post请求的发送（模拟登录）
	login_url = 'http://www.renren.com/ajaxLogin/login?1=1&uniqueTimestamp=2019431046983'
	data = {
	    'email': 'www.zhangbowudi@qq.com',
	    'icode': result,
	    'origURL': 'http://www.renren.com/home',
	    'domain': 'renren.com',
	    'key_id': '1',
	    'captcha_type': 'web_login',
	    'password': '06768edabba49f5f6b762240b311ae5bfa4bcce70627231dd1f08b9c7c6f4375',
	    'rkey': '3d1f9abdaae1f018a49d38069fe743c8',
	    'f':'',
	}
	#使用session进行post请求的发送
	response = session.post(url=login_url,headers=headers,data=data)
	print(response.status_code)

	#爬取当前用户的个人主页对应的页面数据
	detail_url = 'http://www.renren.com/289676607/profile'
	#手动cookie处理
	# headers = {
	#     'Cookie':'xxxx'
	# }
	#使用携带cookie的session进行get请求的发送
	detail_page_text = session.get(url=detail_url,headers=headers).text
	with open('bobo.html','w',encoding='utf-8') as fp:
	    fp.write(detail_page_text)

代理操作
==================================================================================

.. code-block:: python

	#需求：
	import requests
	url = 'https://www.baidu.com/s?wd=ip'
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}

	page_text = requests.get(url=url,headers=headers,proxies={"https":'222.110.147.50:3128'}).text

	with open('ip.html','w',encoding='utf-8') as fp:
	    fp.write(page_text)

	#反爬机制：  封ip
	#反反爬策略：使用代理进行请求发送

综合案例-古诗文网模拟登录
==================================================================================

.. code-block:: python

	from CodeClass import YDMHttp
	import requests
	from lxml import etree
	#封装识别验证码图片的函数
	def getCodeText(imgPath,codeType):
	    # 普通用户用户名
	    username = 'bobo328410948'

	    # 普通用户密码
	    password = 'bobo328410948'

	    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appid = 6003

	    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
	    appkey = '1f4b564483ae5c907a1d34f8e2f2776c'

	    # 图片文件：即将被识别的验证码图片的路径
	    filename = imgPath

	    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
	    codetype = codeType

	    # 超时时间，秒
	    timeout = 20
	    result = None
	    # 检查
	    if (username == 'username'):
	        print('请设置好相关参数再测试')
	    else:
	        # 初始化
	        yundama = YDMHttp(username, password, appid, appkey)

	        # 登陆云打码
	        uid = yundama.login();
	        print('uid: %s' % uid)

	        # 查询余额
	        balance = yundama.balance();
	        print('balance: %s' % balance)

	        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
	        cid, result = yundama.decode(filename, codetype, timeout);
	        print('cid: %s, result: %s' % (cid, result))
	    return result


	#1.对验证码图片进行捕获和识别
	headers = {
	    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36'
	}

	#https://so.gushiwen.org/user/login.aspx?from=http://so.gushiwen.org/user/collect.aspx
	#模拟登录：验证码的识别，动态参数的处理，cookie相关的操作处理
	s = requests.Session()
	#1.验证码图片爬取到本地进行存储
	url = 'https://so.gushiwen.org/user/login.aspx?from=http://so.gushiwen.org/user/collect.aspx'
	page_text = s.get(url=url,headers=headers).text
	tree = etree.HTML(page_text)
	img_src = 'https://so.gushiwen.org'+tree.xpath('//*[@id="imgCode"]/@src')[0]
	img_data = s.get(url=img_src,headers=headers).content
	with open('./gushiwen.jpg','wb') as fp:
	    fp.write(img_data)

	#解析动态参数
	__VIEWSTATE = tree.xpath('//input[@id="__VIEWSTATE"]/@value')[0]
	__VIEWSTATEGENERATOR = tree.xpath('//input[@id="__VIEWSTATEGENERATOR"]/@value')[0]

	#将验证码图片提交给打码平台进行识别
	code_text = getCodeText('./gushiwen.jpg',1004)
	print(code_text)
	#模拟登录
	post_url = 'https://so.gushiwen.org/user/login.aspx?from=http%3a%2f%2fso.gushiwen.org%2fuser%2fcollect.aspx'
	data = {
	    #前两个参数是动态参数
	    '__VIEWSTATE': __VIEWSTATE,
	    '__VIEWSTATEGENERATOR': __VIEWSTATEGENERATOR,
	    'from': 'http://so.gushiwen.org/user/collect.aspx',
	    'email': 'www.zhangbowudi@qq.com',
	    'pwd': 'bobo328410948',
	    'code': code_text,
	    'denglu': '登录',
	}
	#s表示的session中已经存储了相关的cookie
	response = s.post(url=post_url,data=data,headers=headers)
	page_text = response.text
	print(response.status_code)
	with open('./gushiwenwang.html','w',encoding='utf-8') as fp:
	    fp.write(page_text)



















































































