html+css 基础
##################################################################################

html 的介绍
**********************************************************************************

* html 的定义

HTML 的全称为：HyperText Mark-up Language, 指的是超文本标记语言。 标记：就是标签, <标签名称> </标签名称>, 比如: <html></html>、<h1></h1> 等，标签大多数都是成对出现的。

所谓超文本，有两层含义:

::

	因为网页中还可以图片、视频、音频等内容(超越文本限制)
	它还可以在网页中跳转到另一个网页，与世界各地主机的网页链接(超链接文本)

* html 的作用

html是用来开发网页的，它是开发网页的语言。

* 小结

::

	html是开发网页的语言
	html中的标签大多数都是成对出现的, 格式: <标签名></标签名>

html 的基本结构
**********************************************************************************

* 结构代码

.. code-block:: html

	<!DOCTYPE html>
	<html>
	    <head>            
	        <meta charset="UTF-8">
	        <title>网页标题</title>
	    </head>
	    <body>
	          网页显示内容
	    </body>
	</html>

| 第一行<!DOCTYPE html>是文档声明, 用来指定页面所使用的html的版本, 这里声明的是一个html5的文档。
| <html>...</html>标签是开发人员在告诉浏览器，整个网页是从<html>这里开始的，到<html>结束,也就是html文档的开始和结束标签。
| <head>...</head>标签用于定义文档的头部,是负责对网页进行设置标题、编码格式以及引入css和js文件的。
| <body>...</body>标签是编写网页上显示的内容。

* 浏览网页文件

网页文件的后缀是.html或者.htm, 一个html文件就是一个网页，html文件用编辑器打开显示的是文本，可以用文本的方式编辑它，如果用浏览器打开，浏览器会按照标签描述内容将文件渲染成网页。

* 小结

|image0|

初始常用的 html 标签
**********************************************************************************

* 常用的 html 标签

.. code-block:: html

	<!-- 1、成对出现的标签：-->

	<h1>h1标题</h1>
	<div>这是一个div标签</div>
	<p>这个一个段落标签</p>


	<!-- 2、单个出现的标签： -->
	<br>
	<img src="images/pic.jpg" alt="图片">
	<hr>

	<!-- 3、带属性的标签，如src、alt 和 href等都是属性 -->
	<img src="images/pic.jpg" alt="图片">
	<a href="http://www.baidu.com">百度网</a>

	<!-- 4、标签的嵌套 -->
	<div>
	    <img src="images/pic.jpg" alt="图片">
	    <a href="http://www.baidu.com">百度网</a>
	</div>

.. tip::


	| 标签不区分大小写，但是推荐使用小写。
	| 根据标签的书写形式，标签分为双标签(闭合标签)和单标签(空标签)
	| 	双标签是指由开始标签和结束标签组成的一对标签，这种标签允许嵌套和承载内容，比如: div标签
	| 	单标签是一个标签组成，没有标签内容， 比如: img标签

* 小结

::

	学习 html 语言就是学习标签的用法，常用的标签有20多个。
	编写 html 标签建议使用小写
	根据书写形式，html 标签分为双标签和单标签
	单标签没有标签内容，双标签可以嵌套其它标签和承载文本内容

资源路径
**********************************************************************************

当我们使用img标签显示图片的时候，需要指定图片的资源路径，比如:

::

	<img src="images/logo.png">

这里的src属性就是设置图片的资源路径的，资源路径可以分为相对路径和绝对路径。

* 相对路径

从当前操作 html 的文档所在目录算起的路径叫做相对路径

.. code-block:: html

	<!-- 相对路径方式1 -->
	<img src="./images/logo.png">
	<!-- 相对路径方式2 -->
	<img src="images/logo.png">

* 绝对路径

从根目录算起的路径叫做绝对路径，Windows 的根目录是指定的盘符，mac OS 和Linux 是/

.. code-block:: html

	<!-- 绝对路径 -->
	<img src="/Users/apple/Desktop/demo/hello/images/logo.png">
	<img src="C:\demo\images\001.jpg">

.. note::

	一般都会使用相对路径，绝对路径的操作在其它电脑上打开会有可能出现资源文件找不到的问题

* 小结

::

	相对路径和绝对路径是 html 标签使用资源文件的两种方式，一般使用相对路径。
	相对路径是从当前操作的 html 文档所在目录算起的路径
	绝对 路径是从根目录算起的路径

列表标签
**********************************************************************************

* 列表标签的种类

::

	无序列表标签(ul标签)
	有序列表标签(ol标签)

* 无序列表

.. code-block:: html

	<!-- ul标签定义无序列表 -->
	<ul>
	    <!-- li标签定义列表项目 -->
	    <li>列表标题一</li>
	    <li>列表标题二</li>
	    <li>列表标题三</li>
	</ul>

* 有序列表

.. code-block:: html

	<!-- ol标签定义有序列表 -->
	<ol>
	    <!-- li标签定义列表项目 -->
	    <li><a href="#">列表标题一</a></li>
	    <li><a href="#">列表标题二</a></li>
	    <li><a href="#">列表标题三</a></li>
	</ol>

* 小结

::

	列表标签有无序列表标签(ul标签)和有序列表标签(ol标签)
	列表项目对顺序有要求的时候使用ol标签
	列表项目对顺序无要求的时候使用ul标签

表格标签
**********************************************************************************

* 表格的结构

表格是由行和列组成，好比一个excel文件

* 表格标签

.. code-block:: html

	<table>标签：表示一个表格

	    <tr>标签：表示表格中的一行
	        <td>标签：表示表格中的列
	        <th>标签：表示表格中的表头

	示例代码:
	<table>
	    <tr>
	        <th>姓名</th>
	        <th>年龄</th>
	    </tr>
	    <tr>
	        <td>张三</td>
	        <td>18</td> 
	    </tr>
	</table>

表格边线合并: border-collapse 设置表格的边线合并，如：border-collapse:collapse;

表单标签
**********************************************************************************

* 表单的介绍

表单用于搜集不同类型的用户输入(用户输入的数据)，然后可以把用户数据提交到web服务器 。

* 表单相关标签的使用

.. code-block:: html

	<form>标签 表示表单标签，定义整体的表单区域

	<label>标签 表示表单元素的文字标注标签，定义文字标注

	<input>标签 表示表单元素的用户输入标签，定义不同类型的用户输入数据方式
	    type属性
	        type="text" 定义单行文本输入框
	        type="password" 定义密码输入框
	        type="radio" 定义单选框
	        type="checkbox" 定义复选框
	        type="file" 定义上传文件
	        type="submit" 定义提交按钮
	        type="reset" 定义重置按钮
	        type="button" 定义一个普通按钮

	<textarea>标签 表示表单元素的多行文本输入框标签 定义多行文本输入框

	<select>标签 表示表单元素的下拉列表标签 定义下拉列表
	    <option>标签 与<select>标签配合，定义下拉列表中的选项

	示例代码:

	<form>
	    <p>
	        <label>姓名：</label><input type="text">
	    </p>
	    <p>
	        <label>密码：</label><input type="password">
	    </p>
	    <p>
	        <label>性别：</label>
	        <input type="radio"> 男
	        <input type="radio"> 女
	    </p>
	    <p>
	        <label>爱好：</label>
	        <input type="checkbox"> 唱歌
	        <input type="checkbox"> 跑步
	        <input type="checkbox"> 游泳
	    </p>
	    <p>
	        <label>照片：</label>
	        <input type="file">
	    </p>
	    <p>
	        <label>个人描述：</label>
	        <textarea></textarea>
	    </p>
	    <p>
	        <label>籍贯：</label>
	        <select>
	            <option>北京</option>
	            <option>上海</option>
	            <option>广州</option>
	            <option>深圳</option>
	        </select>
	    </p>
	    <p>
	        <input type="submit" value="提交">
	        <input type="reset" value="重置">
	    </p>
	</form>

* 小结

::

	表单标签是<form>标签
	常用的表单元素标签有: <label>、<input>、 <textarea>、<select> 等标签

表单提交
**********************************************************************************

* 表单属性设置

<form>标签 表示表单标签，定义整体的表单区域

::

	action属性 设置表单数据提交地址
	method属性 设置表单提交的方式，一般有“GET”方式和“POST”方式, 不区分大小写

* 表单元素属性设置

::

	name属性 设置表单元素的名称，该名称是提交数据时的参数名
	value属性 设置表单元素的值，该值是提交数据时参数名所对应的值

* 示例代码

.. code-block:: html

	<form action="https://www.baidu.com" method="GET">
	    <p>
	        <label>姓名：</label><input type="text" name="username" value="11" />
	    </p>
	    <p>
	        <label>密码：</label><input type="password" name="password" />
	    </p>
	    <p>
	        <label>性别：</label>
	        <input type="radio" name="gender" value="0" /> 男
	        <input type="radio" name="gender" value="1" /> 女
	    </p>
	    <p>
	        <label>爱好：</label>
	        <input type="checkbox" name="like" value="sing" /> 唱歌
	        <input type="checkbox" name="like" value="run" /> 跑步
	        <input type="checkbox" name="like" value="swiming" /> 游泳
	    </p>
	    <p>
	        <label>照片：</label>
	        <input type="file" name="person_pic">
	    </p>
	    <p>
	        <label>个人描述：</label>
	        <textarea name="about"></textarea>
	    </p>
	    <p>
	        <label>籍贯：</label>
	        <select name="site">
	            <option value="0">北京</option>
	            <option value="1">上海</option>
	            <option value="2">广州</option>
	            <option value="3">深圳</option>
	        </select>
	    </p>
	    <p>
	        <input type="submit" name="" value="提交">
	        <input type="reset" name="" value="重置">
	    </p>
	</form>

* 小结

::

	表单标签的作用就是可以把用户输入数据一起提交到web服务器。
	表单属性设置
	    action: 是设置表单数据提交地址
	    method: 是表单提交方式，提交方式有GET和POST
	表单元素属性设置
	    name: 表单元素的名称，用于作为提交表单数据时的参数名
	    value: 表单元素的值，用于作为提交表单数据时参数名所对应的值

css 的介绍
**********************************************************************************

* css 的定义

css(Cascading Style Sheet)层叠样式表，它是用来美化页面的一种语言。

没有使用css的效果图

|image1|

使用css的效果图

|image2|

* css 的作用

::

	美化界面, 比如: 设置标签文字大小、颜色、字体加粗等样式。
	控制页面布局, 比如: 设置浮动、定位等样式。

* css 的基本语法

选择器{

样式规则

}

样式规则：

属性名1：属性值1;

属性名2：属性值2;

属性名3：属性值3;

...

选择器:是用来选择标签的，选出来以后给标签加样式。

代码示例:

.. code-block:: html

	div{ 
	    width:100px; 
	    height:100px; 
	    background:gold; 
	}

.. note::

	css 是由两个主要的部分构成：选择器和一条或多条样式规则，注意:样式规则需要放到大括号里面。

* 小结

::

	css 是层叠样式表，它是用来美化网页和控制页面布局的。
	定义 css 的语法格式是: 选择器{样式规则}

css 的引入方式
**********************************************************************************

css 的三种引入方式

::

	行内式
	内嵌式（内部样式）
	外链式

* 行内式

直接在标签的 style 属性中添加 css 样式

示例代码: ``<div style="width:100px; height:100px; background:red ">hello</div>``

优点：方便、直观。 缺点：缺乏可重用性。

* 内嵌式（内部样式）

在<head>标签内加入<style>标签，在<style>标签中编写css代码。

示例代码:

.. code-block:: html

	<head>
	   <style type="text/css">
	      h3{
	         color:red;
	      }
	   </style>
	</head>

.. note

优点：在同一个页面内部便于复用和维护。 缺点：在多个页面之间的可重用性不够高。

* 外链式

将css代码写在一个单独的.css文件中，在<head>标签中使用<link>标签直接引入该文件到页面中。

示例代码:

.. code-block:: html

	<link rel="stylesheet" type="text/css" href="css/main.css">

.. note::

	优点：使得css样式与html页面分离，便于整个页面系统的规划和维护，可重用性高。 缺点：css代码由于分离到单独的css文件，容易出现css代码过于集中，若维护不当则极容易造成混乱。

* css引入方式选择

::

	行内式几乎不用
	内嵌式在学习css样式的阶段使用
	外链式在公司开发的阶段使用，可以对 css 样式和 html 页面分别进行开发。

* 小结

::

	css 的引入有三种方式, 分别是行内式、内嵌式、外链式。
	外链式是在公司开发的时候会使用，最能体现 div+css 的标签内容与显示样式分离的思想， 也最易改版维护，代码看起来也是最美观的一种。

css 选择器
**********************************************************************************

* css 选择器的定义

css 选择器是用来选择标签的，选出来以后给标签加样式。

* css 选择器的种类

::

	标签选择器
	类选择器
	层级选择器(后代选择器)
	id选择器
	组选择器
	伪类选择器

* 标签选择器

根据标签来选择标签，以标签开头，此种选择器影响范围大，一般用来做一些通用设置。

.. code-block:: html

	<style type="text/css">
	    p{
	        color: red;
	    }
	</style>

	<div>hello</div>
	<p>hello</p>

* 类选择器

根据类名来选择标签，以 . 开头, 一个类选择器可应用于多个标签上，一个标签上也可以使用多个类选择器，多个类选择器需要使用空格分割，应用灵活，可复用，是css中应用最多的一种选择器。 

.. code-block:: html

	<style type="text/css">
	    .blue{color:blue}
	    .big{font-size:20px}
	    .box{width:100px;height:100px;background:gold} 
	</style>

	<div class="blue">这是一个div</div>
	<h3 class="blue big box">这是一个标题</h3>
	<p class="blue box">这是一个段落</p>

* 层级选择器(后代选择器)

根据层级关系选择后代标签，以选择器1 选择器2开头，主要应用在标签嵌套的结构中，减少命名。

.. code-block:: html

	<style type="text/css">
	    div p{
	        color: red;
	    }
	    .con{width:300px;height:80px;background:green}
	    .con span{color:red}
	    .con .pink{color:pink}
	    .con .gold{color:gold}    
	</style>

	<div>
	    <p>hello</p>
	</div>

	<div class="con">
	    <span>哈哈</span>
	    <a href="#" class="pink">百度</a>
	    <a href="#" class="gold">谷歌</a>
	</div>
	<span>你好</span>
	<a href="#" class="pink">新浪</a>

注意点: 这个层级关系不一定是父子关系，也有可能是祖孙关系，只要有后代关系都适用于这个层级选择器

* id选择器

根据id选择标签，以#开头, 元素的id名称不能重复，所以id选择器只能对应于页面上一个元素，不能复用，id名一般给程序使用，所以不推荐使用id作为选择器。

.. code-block:: html

	<style type="text/css">
	    #box{color:red} 
	</style>

	<p id="box">这是一个段落标签</p>   <!-- 对应以上一条样式，其它元素不允许应用此样式 -->
	<p>这是第二个段落标签</p> <!-- 无法应用以上样式，每个标签只能有唯一的id名 -->
	<p>这是第三个段落标签</p> <!-- 无法应用以上样式，每个标签只能有唯一的id名  -->

注意点: 虽然给其它标签设置id=“box”也可以设置样式，但是不推荐这样做，因为id是唯一的，以后js通过id只能获取一个唯一的标签对象。

* 组选择器

根据组合的选择器选择不同的标签，以 , 分割开, 如果有公共的样式设置，可以使用组选择器。

.. code-block:: html

	<style type="text/css">
	    .box1,.box2,.box3{width:100px;height:100px}
	    .box1{background:red}
	    .box2{background:pink}
	    .box2{background:gold}
	</style>

	<div class="box1">这是第一个div</div>
	<div class="box2">这是第二个div</div>
	<div class="box3">这是第三个div</div>

* 伪类选择器

用于向选择器添加特殊的效果, 以 : 分割开, 当用户和网站交互的时候改变显示效果可以使用伪类选择器

.. code-block:: html

	<style type="text/css">
	    .box1{width:100px;height:100px;background:gold;}
	    .box1:hover{width:300px;}
	</style>

	<div class="box1">这是第一个div</div>

* 小结

::

	css 选择器就是用来选择标签设置样式的
	常用的 css 选择器有六种，分别是:
	    标签选择器
	    类选择器
	    层级选择器(后代选择器)
	    id选择器
	    组选择器
	    伪类选择器

css 属性
**********************************************************************************

* 布局常用样式属性

::

	width 设置元素(标签)的宽度，如：width:100px;
	height 设置元素(标签)的高度，如：height:200px;
	background 设置元素背景色或者背景图片，如：background:gold; 设置元素的背景色, background: url(images/logo.png); 设置元素的背景图片。
	border 设置元素四周的边框，如：border:1px solid black; 设置元素四周边框是1像素宽的黑色实线
	以上也可以拆分成四个边的写法，分别设置四个边的：
	border-top 设置顶边边框，如：border-top:10px solid red;
	border-left 设置左边边框，如：border-left:10px solid blue;
	border-right 设置右边边框，如：border-right:10px solid green;
	border-bottom 设置底边边框，如：border-bottom:10px solid pink;
	padding 设置元素包含的内容和元素边框的距离，也叫内边距，如padding:20px;padding是同时设置4个边的，也可以像border一样拆分成分别设置四个边:padding-top、padding-left、padding-right、padding-bottom。
	margin 设置元素和外界的距离，也叫外边距，如margin:20px;margin是同时设置4个边的，也可以像border一样拆分成分别设置四个边:margin-top、margin-left、margin-right、margin-bottom。
	float 设置元素浮动，浮动可以让块元素排列在一行，浮动分为左浮动：float:left; 右浮动：float:right;

* 文本常用样式属性

::

	color 设置文字的颜色，如： color:red;
	font-size 设置文字的大小，如：font-size:12px;
	font-family 设置文字的字体，如：font-family:'微软雅黑';为了避免中文字不兼容，一般写成：font-family:'Microsoft Yahei';
	font-weight 设置文字是否加粗，如：font-weight:bold; 设置加粗 font-weight:normal 设置不加粗
	line-height 设置文字的行高，如：line-height:24px; 表示文字高度加上文字上下的间距是24px，也就是每一行占有的高度是24px
	text-decoration 设置文字的下划线，如：text-decoration:none; 将文字下划线去掉
	text-align 设置文字水平对齐方式，如text-align:center 设置文字水平居中
	text-indent 设置文字首行缩进，如：text-indent:24px; 设置文字首行缩进24px

* 布局常用样式属性示例代码

.. code-block:: html

	<style>
	    .box1{
	        width: 200px; 
	        height: 200px; 
	        background:yellow; 
	        border: 1px solid black;
	    }

	    .box2{
	        /* 这里是注释内容 */
	        /* 设置宽度 */
	        width: 100px;
	        /* 设置高度 */
	        height: 100px;
	        /* 设置背景色 */
	        background: red;
	        /* 设置四边边框 */
	        /* border: 10px solid black; */
	        border-top: 10px solid black;
	        border-left: 10px solid black;
	        border-right: 10px solid black;
	        border-bottom: 10px solid black;
	        /* 设置内边距， 内容到边框的距离，如果设置四边是上右下左 */
	        /* padding: 10px;   */
	        padding-left: 10px;
	        padding-top: 10px;
	        /* 设置外边距，设置元素边框到外界元素边框的距离 */
	        margin: 10px;
	        /* margin-top: 10px;
	        margin-left: 10px; */
	        float: left;
	    }

	    .box3{
	        width: 48px; 
	        height: 48px; 
	        background:pink; 
	        border: 1px solid black;
	        float: left;
	    }

	</style>

	<div class="box1">
	    <div class="box2">
	        padding 设置元素包含的内容和元素边框的距离
	    </div>
	    <div class="box3">
	    </div>
	</div>

* 文本常用样式属性示例

.. code-block:: html

	<style>
	    p{
	       /* 设置字体大小  浏览器默认是 16px */
	       font-size:20px;
	       /* 设置字体 */
	       font-family: "Microsoft YaHei"; 
	       /* 设置字体加粗 */
	       font-weight: bold;
	       /* 设置字体颜色 */
	       color: red;
	       /* 增加掉下划线 */
	       text-decoration: underline;
	       /* 设置行高  */
	       line-height: 100px;
	       /* 设置背景色 */
	       background: green;
	       /* 设置文字居中 */
	       /* text-align: center; */
	       text-indent: 40px;
	    }

	    a{
	        /* 去掉下划线 */
	        text-decoration: none;
	    }
	</style>

	<a href="#">连接标签</a>
	<p>
	    你好，世界!
	</p>

* 小结

::

	设置不同的样式属性会呈现不同网页的显示效果
	样式属性的表现形式是: 属性名:属性值;

css 元素溢出
**********************************************************************************

* 什么是 css 元素溢出

当子元素(标签)的尺寸超过父元素(标签)的尺寸时，此时需要设置父元素显示溢出的子元素的方式，设置的方法是通过overflow属性来完成。

overflow的设置项：

::

	visible 默认值, 显示子标签溢出部分。
	hidden 隐藏子标签溢出部分。
	auto 如果子标签溢出，则可以滚动查看其余的内容。

* 示例代码

.. code-block:: html

	<style>
	    .box1{
	        width: 100px;
	        height: 200px;
	        background: red;
	        /* 在父级上设置子元素溢出的部分如何显示 */
	        /* overflow: hidden; */
	        overflow: auto;
	    }
	    .box2{
	        width: 50px;
	        height: 300px;
	        background: yellow;
	    }
	</style>

	<div class="box1">
	    <div class="box2">hello</div>
	</div>

* 小结

::

	overflow样式属性是设置子标签溢出的显示方式
	常用使用overflow:hidden;来解决元素溢出

css 显示特性
**********************************************************************************

* display 属性的使用

display 属性是用来设置元素的类型及隐藏的，常用的属性有：

::

	none 元素隐藏且不占位置
	inline 元素以行内元素显示
	block 元素以块元素显示

* 示例代码

.. code-block:: html

	<style>
	    .box{
	        /* 将块元素转化为行内元素 */
	        display:inline;
	    } 

	    .link01{
	        /* 将行内元素转化为块元素 */
	        display:block;
	        background: red;

	    }

	    .con{
	        width:200px;
	        height:200px;
	        background:gold;

	        /* 将元素隐藏 */
	        display:none;
	    }

	</style>

	<div class="con"></div>
	<div class="box">这是第一个div</div>
	<div class="box">这是第二个div</div>
	<a href="#" class="link01">这是第一个链接</a>
	<a href="#" class="link01">这是第二个链接</a>

说明: 行内元素不能设置宽高， 块元素或者行内块元素可以设置宽高。

* 小结

通常隐藏元素使用 display:none

盒子模型
**********************************************************************************

* 盒子模型的介绍

所谓的盒子模型就是把HTML页面的元素看作一个矩形盒子，矩形盒子是由内容(content)、内边距(padding)、边框(border)、外边距(margin)四部分组成。

盒子模型示意图如下：

|image3|

* 盒子模型相关样式属性

::

	盒子的内容宽度(width)，注意：不是盒子的宽度
	盒子的内容高度(height)，注意：不是盒子的高度
	盒子的边框(border)
	盒子内的内容和边框之间的间距(padding)
	盒子与盒子之间的间距(margin)

设置宽高：设置盒子的宽高，此宽高是指盒子内容的宽高，不是盒子整体宽高

::

	width:200px;  /* 设置盒子的宽度，此宽度是指盒子内容的宽度，不是盒子整体宽度(难点) */ 
	height:200px; /* 设置盒子的高度，此高度是指盒子内容的高度，不是盒子整体高度(难点) */

设置边框: 设置一边的边框，比如顶部边框，可以按如下设置：

::

	border-top:10px solid red;

说明: 其中10px表示线框的粗细；solid表示线性；red表示边框的颜色

设置其它三个边的方法和上面一样，把上面的'top'换成'left'就是设置左边，换成'right'就是设置右边，换成'bottom'就是设置底边。

四个边如果设置一样，可以将四个边的设置合并成一句： ``border:10px solid red;``

设置内间距padding

设置盒子四边的内间距，可设置如下：

::

	padding-top：20px;     /* 设置顶部内间距20px */ 
	padding-left:30px;     /* 设置左边内间距30px */ 
	padding-right:40px;    /* 设置右边内间距40px */ 
	padding-bottom:50px;   /* 设置底部内间距50px */

上面的设置可以简写如下：

::

	padding：20px 40px 50px 30px; /* 四个值按照顺时针方向，分别设置的是 上 右 下 左  
	四个方向的内边距值。 */

padding后面还可以跟3个值，2个值和1个值，它们分别设置的项目如下：

::

	padding：20px 40px 50px; /* 设置顶部内边距为20px，左右内边距为40px，底部内边距为50px */ 
	padding：20px 40px; /* 设置上下内边距为20px，左右内边距为40px*/ 
	padding：20px; /* 设置四边内边距为20px */

设置外间距margin: 外边距的设置方法和padding的设置方法相同，将上面设置项中的'padding'换成'margin'就是外边距设置方法。

盒子的真实尺寸

盒子的width和height值固定时，如果盒子增加border和padding，盒子整体的尺寸会变大，所以盒子的真实尺寸为：

::

	盒子宽度 = width + padding左右 + border左右
	盒子高度 = height + padding上下 + border上下

* 小结

::

	盒子模型的5个主要样式属性
	    width：内容的宽度(不是盒子的宽度)
	    height：内容的高度(不是盒子的高度)
	    padding：内边距。
	    border：边框。
	    margin：外边距
	盒子的真实尺寸只会受到宽度、高度、边框、内边距四个属性的影响，不会受到外边距属性的影响。

.. |image0| image:: /_static/python_ai/html_info.png
.. |image1| image:: /_static/python_ai/css1.png
.. |image2| image:: /_static/python_ai/css2.png
.. |image3| image:: /_static/python_ai/hzmx.png





































































































