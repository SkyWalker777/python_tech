Vue 框架
##################################################################################

Vue.js是前端三大新框架：Angular.js、React.js、Vue.js之一，Vue.js目前的使用和关注程度在三大框架中稍微胜出，并且它的热度还在递增。

| Vue.js读音 /vjuː/, 类似于 view
| Vue.js是一个轻巧、高性能、可组件化的MVVM库，同时拥有非常容易上手的API
| Vue.js是一个构建数据驱动的Web界面的库
| Vue.js是一套构建用户界面的 渐进式框架

通俗的说:

::

	Vue.js是一个构建数据驱动的 web 界面的渐进式框架
	Vue.js 的目标是通过尽可能简单的 API 实现响应的数据绑定和组合的视图组件
	核心是一个响应的数据绑定系统

* vue 的作者

尤雨溪是Vue.js框架的作者，他认为，未来App的趋势是轻量化和细化，能解决问题的应用就是好应用。而在移动互联网时代大的背景下，个人开发者的机遇在门槛低，成本低，跨设备和多平台四个方面。

* Vue.js使用文档及下载Vue.js

| Vue.js使用文档已经写的很完备和详细了，通过以下地址可以查看：``https://cn.vuejs.org/v2/guide/``
| vue.js如果当成一个库来使用，可以通过下面地址下载：``https://cn.vuejs.org/v2/guide/installation.html``
| 可视化学习网站: ``https://scrimba.com/playlist/pXKqta``

第一个 Vue 应用
==================================================================================

| 文档地址: ``https://cn.vuejs.org/v2/guide/#%E8%B5%B7%E6%AD%A5``
| 官方提供了两个包

开发环境版本/生产环境版本

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>

	</body>
	</html>

* 采用模板语法来声明式地将数据渲染进 DOM 的系统

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  {{ message }}
	</div>
	</body>
	</html>

* 创建VUE实例

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  {{ message }}
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    message: 'Hello Vue!'
	  }
	})
	</script>
	</html>

.. tip::

	使用浏览器开发者模式动态修改 message 的值: app.$data.message='lilizhao'

data(数据)
==================================================================================

* 基本语法

数据绑定最常见的形式就是使用“Mustache(胡子)”语法 (双大括号) 的文本插值

``<span>{{ message}}</span>``

Mustache 标签将会被替代为对应数据对象上message属性的值。无论何时，绑定的数据对象上message属性发生了改变，插值处的内容都会更新

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  <span>{{ message }}</span> <br>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    message: 'Hello Vue!'
	  }
	})
	</script>
	</html>

.. tip::

	通过chrome浏览器的快捷键ctrl+shift+i或者F12来展示调试工具

|image0|

也可以通过改变Vue的分隔符来修改 Mustache 标签

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
		<span>[[ message ]]</span>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  delimiters:['[[',']]'],
	  data: {
	    message: 'Hello Vue!'
	  }
	})
	</script>
	</html>

* v-bind 绑定元素属性

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  <span>{{ message }}</span> <br>
	   <span v-bind:title="ads">鼠标悬停几秒钟查看此处动态绑定的提示信息！</span> <br>
	   <a v-bind:href="home" target="_blank">戳我有惊喜</a>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    message: 'Hello Vue!',
	    ads:'页面加载于 ' + new Date().toLocaleString(),
	    home:'http://www.itcast.cn/'
	  }
	})
	</script>
	</html>

.. tip::

	v-bind: 简写 :

if 条件渲染
==================================================================================

通过条件指令可以控制元素的创建(显示)或者销毁(隐藏)

::

	v-if
	v-else-if
	v-else
	v-show

* v-if

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  <span>{{ message }}</span> <br>
	   <span :title="ads">鼠标悬停几秒钟查看此处动态绑定的提示信息！</span> <br>
	   <a :href="home" target="_blank" title="惊喜">戳我有惊喜</a> <br>  
	   <p v-if="seen">现在你看到我了</p>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    message: 'Hello Vue!',
	    ads:'页面加载于 ' + new Date().toLocaleString(),
	    home:'http://www.itcast.cn/',
	    seen:true,
	  }
	})
	</script>
	</html>

效果

|image1|

* v-if和v-else

.. code-block:: html
	:linenos:

	<a href="#" v-if="islogin">个人中心</a>
	<a href="#" v-else>登录</a>

.. tip::

	v-else要紧跟 v-if后边

* v-if,v-else-if和v-else

.. code-block:: html
	:linenos:

	<img src="" alt="皇帝" v-if="level === 1">
	<img src="" alt="皇亲" v-else-if="level === 2">
	<img src="" alt="国戚" v-else-if="level === 3">
	<img src="" alt="大臣" v-else>

* v-show

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	   <p v-if="seen" id="if">现在你看到我了</p>
	   <p v-show="seen" id="show">现在你看到我了</p>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    seen:false
	  }
	})
	</script>
	</html>

效果

|image2|

.. tip::

	| v-show用法和v-if大致一样，但是它不支持v-else,它和v-if的区别是，它制作元素样式的显示和隐藏，元素一直是存在的
	| 注意在vue中使用v-show, 原来的css代码不能设置display属性, 会导致冲突

for 列表渲染
==================================================================================

| v-for 指令可以绑定数组的数据来渲染一个项目列表
| v-for 指令需要使用 item in items 形式的特殊语法，items 是源数据数组并且 item 是数组元素迭代的别名。

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  <ol>
	    <li v-for="todo in todos">
	      {{ todo.text }}
	    </li>
	  </ol>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	   todos: [
	      { text: '学习 JavaScript' },
	      { text: '学习 Vue' },
	      { text: '整个牛X项目' }
	    ]
	  }
	})
	</script>
	</html>

效果

::

	1. 学习 JavaScript
	2. 学习 Vue
	3. 整个牛X项目

* index

.. code-block:: html
	:linenos:

	<ol>
	  <li v-for="(todo,index) in todos">
	    {{ todo.text }}-{{index}}
	  </li>
	</ol>

效果:

::

	1. 学习 JavaScript-0
	2. 学习 Vue-1
	3. 整个牛X项目-2

* 对象

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	  <ul>
	    <li v-for="value in object">
	      {{ value }}
	    </li>
	  </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    object: {
	      title: 'How to do lists in Vue',
	      author: 'Jane Doe',
	      publishedAt: '2016-04-10'
	    }
	  }
	})
	</script>
	</html>

效果:

::

	How to do lists in Vue
	Jane Doe
	2016-04-10

* 对象列表

.. code-block:: html
	:linenos:

	<!DOCTYPE html>
	<html>
	<head>
	    <meta charset="utf-8">
	    <title></title>
	    <!-- 开发环境版本 -->
	    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
	</head>
	<body>
	<div id="app">
	   <ul>
	    <li v-for="item in items">
	      {{ item.title }}~~~{{item.author}}~~~{{item.publishedAt}}
	    </li>
	  </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    items: [
	       {
	        title: 'Vue',
	        author: 'Jane Doe',
	        publishedAt: '2016-04-10'
	      },
	      {
	        title: 'python',
	        author: 'Ricky',
	        publishedAt: '2019-04-10'
	      },
	      {
	        title: 'itcast',
	        author: 'itcast',
	        publishedAt: '2006-05-08'
	      }
	    ]
	  }
	})
	</script>
	</html>

效果:

::

	Vue~~~Jane Doe~~~2016-04-10
	python~~~Ricky~~~2019-04-10
	itcast~~~itcast~~~2006-05-08

methods 事件
==================================================================================




.. |image0| image:: /_static/python_ai/data-bind.webp
.. |image1| image:: /_static/python_ai/v-if.webp
.. |image2| image:: /_static/python_ai/show-if.webp





























































































