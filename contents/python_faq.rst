Python 问题篇
##################################################################################

current limit exceeds maximum limit
**********************************************************************************

in init_resources resource.setrlimit(resource.RLIMIT_NOFILE, (soft, hard)) ValueError: current limit exceeds maximum limit

.. code-block:: python

	$ ulimit -n -H
	unlimited
	$ sysctl kern.maxfilesperproc
	kern.maxfilesperproc: 10240

	$ sudo sysctl -w kern.maxfilesperproc=20000
	kern.maxfilesperproc: 10240 -> 20000

python 正则不能正确识别
**********************************************************************************

.. code-block:: python

	reg = re.compile(r"^[^()]+$|(?<=\()[^)]+")
	print(reg.findall("没有括号匹配全部内容"))
	print(reg.findall("aaaaaaa(有括号匹配括号里内容)"))

	print(reg.match("没有括号匹配全部内容"))
	print(reg.match("aaaaaaa(有括号匹配括号里内容)"))

.. attention::
	
	| finall会试图找出所有能匹配的内容。
	| match 默认头开 查找，如果不匹配，应该是不回头了的。

如何在 pycharm 中设置环境变量
**********************************************************************************

今天运行tensorflow的时候，发现在pycharm下，程序无法找到CUDA的libcupti.so文件。而在添加完环境变量：

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH/usr/local/cuda/extras/CUPTI/lib64:
后，在命令行可以运行程序。然而，在Pycharm中运行程序，仍无法找到CUDA库文件。

经过下午的折腾，终于找到解决方案：

::

	在菜单Run->Edit configurations 中，手动设置Environment variables，添加LD_LIBRARY_PATH的内容，即可解决问题。

ValueError: too many values to unpack (expected 3)
**********************************************************************************

该bug是由于你所调用的方法返回值个数和你接收参数的个数不一致造成的。比如：

.. code-block:: python

	def num_op(x, y):
		return x+y, x-y, x*y, x/y
	a, b, c = num_op(1,2)

num_op函数一共有四个返回值，但是调用的时候只接收三个返回值，所以就报错了。

解决方案：修改调用参数的代码，保持接收参数个数与函数返回参数个数一致。 a, b, c, d = num_op(1,2), 这样就可以正确返回了。

.. raw:: html

	<script
	   type="text/javascript"
	   src="https://utteranc.es/client.js"
	   async="async"
	   repo="lilizhaolilizhao/python_learn"
	   issue-term="pathname"
	   theme="github-light"
	   label="💬 comment"
	   crossorigin="anonymous"
	/>

jupter notebook 格式转化
**********************************************************************************

运行nbconvert脚本的命令行语法是：

	| $ jupyter nbconvert --to FORMAT notebook.ipynb


将 jupyter 笔记本文件 notebook.ipynb 转换成 FORMAT 字符串给出的输出格式。

当前支持的输出格式：

	| HTML
	| LaTeX
	| PDF
	| Reveal.js HTML 
	| slideshow
	| Markdown
	| Ascii
	| reStructruredText
	| executable 
	| script
	| notebook

实战案例:

	| jupyter nbconvert --to rst  21.\ Pandas怎样快捷方便的处理日期数据.ipynb





