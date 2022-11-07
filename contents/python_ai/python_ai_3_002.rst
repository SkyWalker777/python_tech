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
**********************************************************************************

| 文档地址: ``https://cn.vuejs.org/v2/guide/#%E8%B5%B7%E6%AD%A5``
| 官方提供了两个包

开发环境版本/生产环境版本

.. code-block:: html

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
**********************************************************************************

* 基本语法

数据绑定最常见的形式就是使用“Mustache(胡子)”语法 (双大括号) 的文本插值

``<span>{{ message}}</span>``

Mustache 标签将会被替代为对应数据对象上message属性的值。无论何时，绑定的数据对象上message属性发生了改变，插值处的内容都会更新

.. code-block:: html

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
**********************************************************************************

通过条件指令可以控制元素的创建(显示)或者销毁(隐藏)

::

	v-if
	v-else-if
	v-else
	v-show

* v-if

.. code-block:: html

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

	<a href="#" v-if="islogin">个人中心</a>
	<a href="#" v-else>登录</a>

.. tip::

	v-else要紧跟 v-if后边

* v-if,v-else-if和v-else

.. code-block:: html

	<img src="" alt="皇帝" v-if="level === 1">
	<img src="" alt="皇亲" v-else-if="level === 2">
	<img src="" alt="国戚" v-else-if="level === 3">
	<img src="" alt="大臣" v-else>

* v-show

.. code-block:: html

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
**********************************************************************************

| v-for 指令可以绑定数组的数据来渲染一个项目列表
| v-for 指令需要使用 item in items 形式的特殊语法，items 是源数据数组并且 item 是数组元素迭代的别名。

.. code-block:: html

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
**********************************************************************************

可以用v-on指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。

.. code-block:: html

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
	  <button v-on:click="counter += 1">Add 1</button>
	  <p>The button above has been clicked {{ counter }} times.</p>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    counter:0
	  }
	})
	</script>
	</html>

|image3|

* 事件处理方法

然而许多事件处理逻辑会更为复杂，所以直接把 JavaScript 代码写在v-on指令中是不可行的。因此v-on还可以接收一个需要调用的方法名称

.. code-block:: html

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
	  <button v-on:click="add">Add 1</button>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    counter:0
	  },
	  methods:{
	    add:function(){
	      counter+=1
	      alert(this.counter)
	    }
	  }
	})
	</script>
	</html>

* 事件处理方法传递参数

.. code-block:: html

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
	  <button v-on:click="addnum(counter)">Add {{counter}}</button> 
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	    counter:1
	  },
	  methods:{
	    addnum:function(num){
	      this.counter = num+this.counter
	      alert(this.counter)
	    }
	  }
	})
	</script>
	</html>

.. tip::

	v-on: 简写 @

model 表单输入绑定(双向绑定数据)
**********************************************************************************

::

	单行文本框
	多行文本框
	单选框
	多选框
	下拉框

	参考地址: https://cn.vuejs.org/v2/guide/forms.html

可以用v-model指令在表单<input>、<textarea>及<select>元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

.. note::

	v-model会忽略所有表单元素的value、checked、selected特性的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的data选项中声明初始值

.. code-block:: html

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
	    <table border="1">
	      <tr><td>用 户 名</td><td><input type="text" v-model="username" @blur="checkusername"> </td></tr>
	      <tr><td>密码</td><td><input type="password" v-model="password1"> </td></tr>
	      <tr><td>确认密码</td><td><input type="password" v-model="password2"></td></tr>
	      <tr><td>性别</td>
	        <td>
	          男<input type="radio" name="sex"  value="boy" v-model="sex"> 
	          女 <input type="radio" name="sex" value="girl" v-model="sex"></td>
	        </tr>
	      <tr><td>爱好</td>
	        <td>
	          足球 <input type="checkbox" name="like" value="足球" v-model="like"> 
	          篮球 <input type="checkbox" name="like" value="篮球" v-model="like">
	          兵乓球<input type="checkbox" name="like" value="兵乓球" v-model="like"> 
	        </td>
	      </tr>
	      <tr><td>所在城市</td>
	        <td>
	          <select name="city" v-model="city">
	            <option value="北京">北京</option>
	            <option value="上海">上海</option>
	            <option value="广州">广州</option>
	            <option value="深圳">深圳</option>
	          </select> 
	        </td>
	      </tr>
	      <tr><td>个人简介</td><td><textarea name="desc" v-model="desc"></textarea> </td></tr>
	    </table>
	    <button @click="register">注册</button>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	      username:'',
	      password1:'',
	      password2:'',
	      sex:'',
	      like:[],
	      city:'',
	      desc:''

	  },
	  methods:{
	    register:function(){

	        alert(this.username+this.password1+this.password2+this.sex+this.like+this.city+this.desc)
	    },
	    checkusername:function(){
	      alert(this.username)
	    }
	  }
	})
	</script>
	</html>

效果

|image4|

Todolist 案例
**********************************************************************************

|image5|

准备工作

.. code-block:: html

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
	   <input type="text"> <button>添加</button>
	   <hr/>
	   <ul>
	     <li>1</li>
	     <li>2</li>
	     <li>3</li>
	   </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	      items:['学习Vue','学习Django基础','学习Django前台'],
	  },
	  methods:{

	  }
	})
	</script>
	</html>

* 列表数据渲染

.. code-block:: html

	<ul>
	 <li v-for="item in items">{{item}}</li>
	</ul>

* 绑定数据能够添加到列表中

.. code-block:: html

	<body>
	<div id="app">
	   <input type="text" v-model="newitem"> <button @click="addNetItem">添加</button>
	   <hr/>
	   <ul>
	     <li v-for="item in items">{{item}}</li>
	   </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	      items:['学习Vue','学习Django基础','学习Django前台'],
	      newitem:'',
	  },
	  methods:{
	      addNetItem:function(){
	        this.items.push(this.newitem);
	        this.newitem='';
	      }
	  }
	})
	</script>
	</html>

* 实现删除功能

.. code-block:: html

	<div id="app">
	   <input type="text" v-model="newitem"> <button @click="addNetItem">添加</button>
	   <hr/>
	   <ul>
	     <li v-for="(item,index) in items">
	      <span>{{item}}</span> 
	      <a href="javascript:;" @click="deleteItem(index)">删除</a>
	     </li>
	   </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	      items:['学习Vue','学习Django基础','学习Django前台'],
	      newitem:'',
	  },
	  methods:{
	      addNetItem:function(){
	        this.items.push(this.newitem);
	        this.newitem='';
	      },
	      deleteItem:function(index){
	        this.items.splice(index,1)
	      }
	  }
	})
	</script>
	</html>

* 添加移动按钮,实现移动功能

.. code-block:: html

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
	   <input type="text" v-model="newitem"> <button @click="addNetItem">添加</button>
	   <hr/>
	   <ul>
	     <li v-for="(item,index) in items">
	     <a href="javascript:;" @click="upItem(index)">↑</a>
	      <span>{{item}}</span> 
	      <a href="javascript:;" @click="downItem(index)">↓</a>
	      <a href="javascript:;" @click="deleteItem(index)">删除</a>
	     </li>
	   </ul>
	</div>
	</body>
	<script type="text/javascript">
	var app = new Vue({
	  el: '#app',
	  data: {
	      items:['学习Vue','学习Django基础','学习Django前台'],
	      newitem:'',
	  },
	  methods:{
	      addNetItem:function(){
	        this.items.push(this.newitem);
	        this.newitem='';
	      },
	      deleteItem:function(index){
	        this.items.splice(index,1);
	      },
	      upItem:function(index){
	          current=this.items[index];
	          this.items.splice(index,1);
	          this.items.splice(index-1,0,current);
	      },
	      downItem:function(index){
	          current=this.items[index];
	          this.items.splice(index,1);
	          this.items.splice(index+1,0,current);
	      }
	  }
	})
	</script>
	</html>

ES6 语法
**********************************************************************************

.. tip::

	ES6标准入门: http://caibaojian.com/es6/

* ES6 语法介绍

ES6是JavaScript语言的新版本，它也可以叫做ES2015，之前学习的JavaScript属于ES5，ES6在它的基础上增加了一些语法，ES6是未来JavaScript的趋势，而且vue组件开发中会使用很多的ES6的语法，所以掌握这些常用的ES6语法是必须的。

* 变量声明

var:它是用来声明变量的。如果在方法中声明，则为局部变量；如果在全局中声明，则为全局变量。``var num=10``

|image6|

let:ES6新增了let命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

.. code-block:: javascript

	{
	  let a = 10;
	  var b = 1;
	}

|image7|

上面代码在代码块之中，分别用let和var声明了两个变量。然后在代码块之外调用这两个变量，结果let声明的变量报错，var声明的变量返回了正确的值。这表明，let声明的变量只在它所在的代码块有效。

::

	for循环的计数器，就很合适使用let命令。

	for (let i = 0; i < 10; i++) {}
	计数器i只在for循环体内有效，在循环体外引用就会报错。

const:const声明一个只读的常量。一旦声明，常量的值就不能改变。``const PI = 3.1415;``

|image8|

* Javascript对象的写法

ES5的写法

.. code-block:: javascript

	var person = { 
	    name:'itcast',
	    age:13,
	    say:function(){
	        alert('hello')
	    }
	}

	person.say()

|image9|

还可以写

.. code-block:: javascript

	var person = {};
	person.name='itheima';
	person.age=13;
	person.say = function (){alert('hello')}
	person.say();

ES6的写法

需要注意的是, 实现简写,有一个前提,必须变量名属性名一致

.. code-block:: javascript

	//定义变量
	var name='itcast';
	var age=13;
	//创建对象
	var person = {
	    name,
	    age,
	    say:function(){
	        alert('hello');
	    }
	};
	//调用
	person.say()

|image10|

* ES6 的箭头函数

作用:

::

	定义函数新的方式
	改变this的指向

定义函数新的方式

.. code-block:: javascript

	//无参数,无返回值
	var say = ()=> {
	    alert('我是无参数无返回值函数');
	}
	//有参数,无返回值
	var eat = food => {
	    alert('我喜欢吃'+food);
	}
	//有参数,有返回值
	var total = (num1,num2) => {
	    return num1+num2;
	}

改变this的指向

如果层级比较深的时候, this的指向就变成了window, 这时候就可以通过箭头函数解决这个指向的问题

.. code-block:: javascript

	var person = {
	    name:'itcast',
	    age:13,
	    say:function(){
	        alert('my name is ' + this.name);
	    }
	}
	//调用
	person.say()

实例生命周期
**********************************************************************************

|image11|

各个生命周期函数的作用

::

	beforeCreate
		vm对象实例化之前
	created
		vm对象实例化之后
	beforeMount
		vm将作用标签之前
	mounted(重要时机初始化数据使用)
		vm将作用标签之后
	beforeUpdate
		数据或者属性更新之前
	updated
		数据或者属性更新之后

axios 发送 ajax 请求
**********************************************************************************

axios github : https://github.com/axios/axios

.. |image0| image:: /_static/python_ai/data-bind.webp
.. |image1| image:: /_static/python_ai/v-if.webp
.. |image2| image:: /_static/python_ai/show-if.webp
.. |image3| image:: /_static/python_ai/v-on.png
.. |image4| image:: /_static/python_ai/input-model.webp
.. |image5| image:: /_static/python_ai/todo_list.png
.. |image6| image:: /_static/python_ai/var_define.png
.. |image7| image:: /_static/python_ai/let.png
.. |image8| image:: /_static/python_ai/const_define.png
.. |image9| image:: /_static/python_ai/js_object.png
.. |image10| image:: /_static/python_ai/js_object_6.png
.. |image11| image:: /_static/python_ai/vue-life.webp




















































































