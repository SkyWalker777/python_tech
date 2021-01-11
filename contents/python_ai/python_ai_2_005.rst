JavaScript
##################################################################################

JavaScript 的介绍
**********************************************************************************

* JavaScript 的定义

JavaScript是运行在浏览器端的脚步语言, 是由浏览器解释执行的, 简称js, 它能够让网页和用户有交互功能, 增加良好的用户体验效果。

前端开发三大块 1、HTML：负责网页结构 2、CSS：负责网页样式 3、JavaScript：负责网页行为， 比如:网页与用户的交互效果

* 小结

JavaScript 是运行在浏览器端的脚步语言，它的作用就是负责网页和用户的交互效果。

JavaScript 的使用方式
**********************************************************************************

* 行内式（主要用于事件）

.. code-block:: html
	:linenos:

	<input type="button" name="" onclick="alert('ok！');">

* 内嵌式

.. code-block:: html
	:linenos:

	<script type="text/javascript">        
	    alert('ok！');
	</script>

* 外链式

.. code-block:: html
	:linenos:

	<script type="text/javascript" src="js/index.js"></script>

* 小结

::

	JavaScript的使用方式有三种，分别是:
		行内式
		内嵌式
		外链式

变量和数据类型
**********************************************************************************

* 定义变量

JavaScript 是一种弱类型语言，也就是说不需要指定变量的类型，JavaScript的变量类型由它的值来决定， 定义变量需要用关键字 'var', 一条JavaScript语句应该以“;”结尾

定义变量的语法格式: var 变量名 = 值;

.. code-block:: javascript
	:linenos:

	var iNum = 123;
	var sTr = 'asd';

	//同时定义多个变量可以用","隔开，公用一个‘var’关键字

	var iNum = 45,sTr='qwe',sCount='68';

* JavaScript 注释

JavaScript 的注释分为单行注释(//注释内容)和多行注释(/多行注释/)

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">    

	// 单行注释
	var iNum = 123;
	/*  
	    多行注释
	    1、...
	    2、...
	*/
	var sTr = 'abc123';
	</script>

* 数据类型

js中有六种数据类型，包括五种基本数据类型和一种复杂数据类型(object)。

5种基本数据类型：

::

	1、number 数字类型
	2、string 字符串类型
	3、boolean 布尔类型 true 或 false
	4、undefined undefined类型，变量声明未初始化，它的值就是undefined
	5、null null类型，表示空对象，如果定义的变量将来准备保存对象，可以将变量初始化为null,在页面上获取不到对象，返回的值就是null

1种复合类型： 1、object 后面学习的数组、函数和JavaScript对象都属于复合类型

.. code-block:: javascript
	:linenos:

	//1.1 数字 number
	var iOne = 10.1;

	//1.2 字符串 string
	var sStr = '1234';

	//1.3 布尔 boolean; 
	var bIsTrue = false;

	//1.4 未定义 undefined
	var unData;

	//1.5 null 表示空对象
	var nullData = null;

	//1.6 object 表示对象类型
	var oObj = {
	   name:"隔壁老王",
	   age:88
	}
	// 获取变量的类型
	var type = typeof(oObj);
	alert(type);
	// 获取对象的name属性
	alert(oObj.name);

* 变量命名规范

::

	1、区分大小写
	2、第一个字符必须是字母、下划线（_）或者美元符号（$）
	3、其他字符可以是字母、下划线、美元符或数字

* 匈牙利命名风格

::

	对象o Object 比如：oDiv
	数组a Array 比如：aItems
	字符串s String 比如：sUserName
	整数i Integer 比如：iItemCount
	布尔值b Boolean 比如：bIsComplete
	浮点数f Float 比如：fPrice
	函数fn Function 比如：fnHandler

* 小结

::

	js中有六种数据类型，分别是:
	    number
	    string
	    boolean
	    undefined
	    null
	    objec

函数定义和调用
**********************************************************************************

* 函数定义

函数就是可以重复使用的代码块, 使用关键字 function 定义函数。

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    // 函数定义
	    function fnAlert(){
	        alert('hello!');
	    }
	</script>

* 函数调用

函数调用就是函数名加小括号，比如:函数名(参数[参数可选])

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    // 函数定义
	    function fnAlert(){
	        alert('hello!');
	    }
	    // 函数调用
	    fnAlert();
	</script>

* 定义有参数有返回值的函数

定义函数时，函数如果有参数，参数放到小括号里面，函数如果有返回值，返回值通过 return 关键字来返回

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	function fnAdd(iNum01,iNum02){
	    var iRs = iNum01 + iNum02;
	    return iRs;
	    alert('here!');
	}

	var iCount = fnAdd(3,4);
	alert(iCount);  //弹出7
	</script>

函数中'return'关键字的作用:

::

	1、返回函数中的值
	2、执行完return函数执行结束

* 小结

::

	函数的定义

	  function 函数名(参数[参数可选]){  
	      // 函数的代码实现  
	      ...  
	  }

	函数的调用

	  函数名(参数[参数可选])

变量作用域
**********************************************************************************

* 变量作用域的介绍

变量作用域就是变量的使用范围，变量分为:

::

	局部变量
	全局变量

* 局部变量

局部变量就是在函数内使用的变量，只能在函数内部使用。

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    function myalert()
	    {
	        // 定义局部变量
	        var b = 23;
	        alert(b);
	    }
	    myalert(); // 弹出23
	    alert(b);  // 函数外使用出错
	</script>

* 全局变量

全局变量就是在函数外定义的变量，可以在不同函数内使用。

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    // 定义全局变量
	    var a = 12;
	    function myalert()
	    {
	        // 修改全局变量
	        a++;
	    }
	    myalert();
	    alert(a);  // 弹出13    
	</script>

* 小结

::

	局部变量只能在函数内部使用
	全局变量可以在不同函数内使用

条件语句
**********************************************************************************

* 条件语句的介绍

条件语句就是通过条件来控制程序的走向

* 条件语句语法

::

	if 语句 - 只有当指定条件为 true 时，使用该语句来执行代码
	if...else 语句 - 当条件为 true 时执行代码，当条件为 false 时执行其他代码
	if...else if....else 语句 - 使用该语句来判断多条件，执行条件成立的语句

* 比较运算符

假如 x = 5, 查看比较后的结果:

::

	比较运算符 	描述 	例子
	== 	等于 	x == 8 为 false
	=== 	全等(值和类型) 	x === 5 为 true; x === "5" 为 false
	!= 	不等于 	x != 8 为 true
	> 	大于 	x > 8 为 false
	< 	小于 	x < 8 为 true
	>= 	大于或等于 	x >= 8 为 false
	<= 	小于或等于 	x <= 8 为 true

比较运算符示例代码:

.. code-block:: javascript
	:linenos:

	var iNum01 = 12;
	var sNum01 = '12';

	if(iNum01==12){
	    alert('相等！');
	}
	else{
	    alert('不相等！')
	}

	// "==" 符号默认会将符号两边的变量转换成数字再进行对比，这个叫做隐式转换
	if(sNum01==12){
	    alert('相等！');
	}
	else{
	    alert('不相等！')
	}

	// "===" 符号不会转换符号两边的数据类型
	if(sNum01===12){
	    alert('相等！');
	}
	else{
	    alert('不相等！')
	}

	// 多条件判断
	var sFruit = "苹果";
	if (sFruit == "苹果") {
	    alert("您选择的水果是苹果");
	} else if (sFruit == "鸭梨") {
	    alert("您选择的水果是鸭梨");
	} else {
	    alert("对不起，您选择的水果不存在!")
	}

* 逻辑运算符

假如 x=6, y=3, 查看比较后的结果:

::

	比较运算符 	描述 	例子
	&& 	and 	(x < 10 && y > 1) 为 true
	|| 	or 	(x==5 || y==5) 为 false
	! 	not 	!(x==y) 为 true

逻辑运算符示例代码:

.. code-block:: javascript
	:linenos:

	var x = 6;
	var y = 3;

	if(x < 10 && y > 1){
	    alert('都大于');
	}
	else{
	    alert('至少有一个不大于');
	}

	if(x > 5 || y > 7 ){
	    alert('至少有一个大于');
	}
	else{
	    alert('都不大于');
	}

	if(!(x == y)){
	    alert('等于')
	}
	else{
	    alert('不等于')
	}

* 小结

::

	条件语句三种写法
	    if 语句 适用于单条件判断
	    if else 语句 适用于两种条件的判断(成立和不成立条件判断)。
	    if else if else 语句 适用于多条件判断

获取标签元素
**********************************************************************************

* 获取标签元素

可以使用内置对象 document 上的 getElementById 方法来获取页面上设置了id属性的标签元素，获取到的是一个html对象，然后将它赋值给一个变量，比如：

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    var oDiv = document.getElementById('div1');
	    alert(oDiv);
	</script>
	<div id="div1">这是一个div元素</div>

说明: 上面的代码，如果把javascript写在元素的上面，就会出错，因为页面上从上往下加载执行的，javascript去页面上获取元素div1的时候，元素div1还没有加载。

解决方法有两种:

第一种方法：将javascript放到页面最下边

.. code-block:: javascript
	:linenos:

	<div id="div1">这是一个div元素</div>

	<script type="text/javascript">
	    var oDiv = document.getElementById('div1');
	    alert(oDiv);
	</script>

第二种方法：设置页面加载完成执行的函数，在执行函数里面获取标签元素。

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    window.onload = function(){
	        var oDiv = document.getElementById('div1');
	    }
	</script>

说明: onload是页面所有元素加载完成的事件，给onload设置函数时，当事件触发就会执行设置的函数。

* 小结

获取标签元素需要等待页面加载完成，使用document.getElementById('标签id');

操作标签元素属性
**********************************************************************************

* 属性的操作

首先获取的页面标签元素，然后就可以对页面标签元素的属性进行操作，属性的操作包括:

::

	属性的读取
	属性的设置

属性名在js中的写法 

1. html的属性和js里面属性大多数写法一样，但是“class” 属性写成 “className”
2. “style” 属性里面的属性，有横杠的改成驼峰式，比如：“font-size”，改成”style.fontSize”

.. code-block:: javascript
	:linenos:

	<style>
	    .sty01{
	        font-size:20px;
	        color:red;
	    }
	    .sty02{
	        font-size:30px;
	        color:pink;
	        text-decoration:none;
	    }

	</style>

	<script type="text/javascript">

	    window.onload = function(){
	        var oInput = document.getElementById('input1');
	        var oA = document.getElementById('link1');
	        // 读取属性值
	        var sValue = oInput.value;
	        var sType = oInput.type;
	        var sName = oInput.name;
	        var sLinks = oA.href;

	        // 操作class属性,需要写成“className”
	        oA.className = 'sty02';

	        // 写(设置)属性
	        oA.style.color = 'red';
	        oA.style.fontSize = sValue;
	    }

	</script>

	<input type="text" name="setsize" id="input1" value="20px">
	<a href="#" id="link01" class="sty01">这是一个链接</a>

* innerHTML

innerHTML可以读取或者设置标签包裹的内容

.. code-block:: javascript
	:linenos:

	<script type="text/javascript">
	    window.onload = function(){
	        var oDiv = document.getElementById('div1');
	        //读取
	        var sTxt = oDiv.innerHTML;
	        alert(sTxt);
	        //写入
	        oDiv.innerHTML = '<a href="http://www.itcast.cn">传智播客<a/>';
	    }
	</script>

	<div id="div1">这是一个div元素</div>

* 小结

标签属性的获取和设置:

::

	var 标签对象 = document.getElementById('id名称'); -> 获取标签对象
	var 变量名 = 标签对象.属性名 -> 读取属性
	标签对象.属性名 = 新属性值 -> 设置属性

数组及操作方法
**********************************************************************************

* 数组的介绍

数组就是一组数据的集合，javascript 中，数组里面的数据可以是不同类型的数据，好比 python 里面的列表。

* 数组的定义

.. code-block:: javascript
	:linenos:

	// 实例化对象方式创建
	var aList = new Array(1,2,3);

	// 字面量方式创建，推荐使用
	var aList2 = [1,2,3,'asd'];

* 多维数组

多维数组指的是数组的成员也是数组，把这样的数组叫做多维数组。

.. code-block:: javascript
	:linenos:

	var aList = [[1,2,3],['a','b','c']];

* 数组的操作

::

	1、 获取数组的长度

	var aList = [1,2,3,4];
	alert(aList.length); // 弹出4

	2、 根据下标取值

	var aList = [1,2,3,4];
	alert(aList[0]); // 弹出1

	3、 从数组最后添加和删除数据

	var aList = [1,2,3,4];
	aList.push(5);
	alert(aList); //弹出1,2,3,4,5
	aList.pop();
	alert(aList); // 弹出1,2,3,4
	4、根据下标添加和删除元素

	arr.splice(start,num,element1,.....,elementN)

	参数解析：

	    start：必需，开始删除的索引。
	    num：可选，删除数组元素的个数。
	    elementN：可选，在start索引位置要插入的新元素。

	此方法会删除从start索引开始的num个元素，并将elementN参数插入到start索引位置。

	var colors = ["red", "green", "blue"];
	colors.splice(0,1);  //删除第一项
	alert(colors);  //green,blue

	colors.splice(1, 0, "yellow", "orange");  //从第一个索引位置插入两项数据
	alert(colors);  //green,yellow,organge,blue

	colors.splice(1, 1, "red", "purple");  //删除一项，插入两项数据
	alert(colors);  //green,red,purple,orange,blue

* 小结

::

	数组的定义使用一对中括号
	获取数组的长度使用length属性
	从数组最后添加元素使用push方法
	从数组最后删除元素使用pop方法
	根据下标添加和删除元素使用splice方法

循环语句
**********************************************************************************

* 循环语句的介绍

循环语句就是让一部分代码重复执行，javascript中常用的循环语句有:

::

	for
	while
	do-while

* for 循环

.. code-block:: javascript
	:linenos:

	var array = [1, 4, 5];

	for(var index = 0; index < array.length; index++){
	    result = array[index];
	    alert(result);
	}

* while 循环

.. code-block:: javascript
	:linenos:

	var array = [1, 4, 5];        
	var index = 0;

	while (index < array.length) {
	    result = array[index];
	    alert(result);
	    index++;
	}

说明: 当条件成立的时候, while语句会循环执行

* do-while循环

.. code-block:: javascript
	:linenos:

	var array = [1, 4, 5];
	var index = 0;

	do {
	    result = array[index];
	    alert(result);
	    index++;
	} while (index < array.length);

说明: 当条件不成立的时候do语句也会执行一次

字符串拼接
**********************************************************************************

* 字符串拼接

字符串拼接使用: "+" 运算符

.. code-block:: javascript
	:linenos:

	var iNum1 = 10;
	var fNum2 = 11.1;
	var sStr = 'abc';

	result = iNum1 + fNum2;
	alert(result); // 弹出21.1

	result = fNum2 + sStr;
	alert(result); // 弹出11.1abc

说明: 数字和字符串拼接会自动进行类型转换(隐士类型转换)，把数字类型转成字符串类型进行拼接

* 小结

"+" 运算符能够实现字符串的拼接操作

定时器
**********************************************************************************

* 定时器的介绍

定时器就是在一段特定的时间后执行某段程序代码。

* 定时器的使用

js 定时器有两种创建方式：

::

	setTimeout(func[, delay, param1, param2, ...]) ：以指定的时间间隔（以毫秒计）调用一次函数的定时器
	setInterval(func[, delay, param1, param2, ...]) ：以指定的时间间隔（以毫秒计）重复调用一个函数的定时器

setTimeout函数的参数说明:

::

	第一个参数 func , 表示定时器要执行的函数名
	第二个参数 delay, 表示时间间隔，默认是0，单位是毫秒
	第三个参数 param1, 表示定时器执行函数的第一个参数，一次类推传入多个执行函数对应的参数。

.. code-block:: javascript
	:linenos:

	<script> 
	    function hello(){ 
	        alert('hello'); 
	    } 

	    // 执行一次函数的定时器
	    setTimeout(hello, 500);
	</script>

setInterval函数的参数说明:

::

	第一个参数 func , 表示定时器要执行的函数名
	第二个参数 delay, 表示时间间隔，默认是0，单位是毫秒
	第三个参数 param1, 表示定时器执行函数的第一个参数，一次类推传入多个执行函数对应的参数。

.. code-block:: javascript
	:linenos:

	<script> 
	    function hello(){ 
	        alert('hello'); 
	    } 
	    // 重复执行函数的定时器
	    setInterval(hello, 1000);
	</script>

* 清除定时器

js 清除定时器分别是:

::

	clearTimeout(timeoutID) 清除只执行一次的定时器(setTimeout函数)
	clearInterval(timeoutID) 清除反复执行的定时器(setInterval函数)

clearTimeout函数的参数说明:

timeoutID 为调用 setTimeout 函数时所获得的返回值，使用该返回标识符作为参数，可以取消该 setTimeout 所设定的定时执行操作。

.. code-block:: javascript
	:linenos:

	<script>
	    function hello(){
	        alert('hello');
	        // 清除只执行一次的定时器
	        clearTimeout(t1)
	    }
	    // 执行一次函数的定时器
	    t1 = setTimeout(hello, 500);
	</script>

clearInterval函数的参数说明:

timeoutID 为调用 setInterval 函数时所获得的返回值，使用该返回标识符作为参数，可以取消该 setInterval 所设定的定时执行操作。

.. code-block:: javascript
	:linenos:

	<script> 
	    function hello(){ 
	        alert('hello'); 
	    } 
	    // 重复执行函数的定时器
	    var t1 = setInterval(hello, 1000);

	    function stop(){
	        // 清除反复执行的定时器
	        clearInterval(t1); 
	    }  

	</script> 

	<input type="button" value="停止" onclick="stop();">

* 小结

::

	定时器的创建
	    只执行一次函数的定时器, 对应的代码是setTimeout函数
	    反复执行函数的定时器, 对应的代码是setInterval函数
	清除定时器
	    清除只执行一次函数的定时器, 对应的代码是clearTimeout函数
	    清除清除反复执行的定时器, 对应的代码是clearInterval函数

jQuery
##################################################################################

jQuery 的介绍
**********************************************************************************

* jQuery的定义

jQuery是对JavaScript的封装，它是免费、开源的JavaScript函数库，jQuery 极大地简化了 JavaScript 编程。

* jQuery的作用

jQuery和JavaScript它们的作用一样，都是负责网页行为操作，增加网页和用户的交互效果的，只不过jQuery简化了JavaScript编程，jQuery实现交互效果更简单。

* jQuery的优点

| jQuery兼容了现在主流的浏览器，增加了程序员的开发效率。
| jQuery简化了 JavaScript 编程，代码编写更加简单。

* 小结

::

	jQuery是一个免费、开源的JavaScript函数库
	jQuery的作用和JavaScript一样，都是负责网页和用户的交互效果。
	jQuery的优点就是兼容主流浏览器，代码编写更加简单。

jQuery 的用法
**********************************************************************************

* jQuery 的引入

.. code-block:: javascript
	:linenos:

	<script src="js/jquery-1.12.4.min.js"></script>

* jQuery 的入口函数

我们知道使用js获取标签元素，需要页面加载完成以后再获取，我们通过给onload事件属性设置了一个函数来获取标签元素，而jquery提供了ready函数来解决这个问题，保证获取标签元素没有问题，它的速度比原生的 window.onload 更快。

入口函数示例代码:

.. code-block:: javascript
	:linenos:

	<script src="js/jquery-1.12.4.min.js"></script>
	<script>
	    window.onload = function(){
	        var oDiv = document.getElementById('div01');
	        alert('原生就是获取的div：' + oDiv);
	    };
	    $(document).ready(function(){
	        var $div = $('#div01');
	        alert('jquery获取的div：' + $div);
	    });
	</script>

	<div id="div01">这是一个div</div>

入口函数的简写示例代码:

.. code-block:: javascript
	:linenos:

	<script src="js/jquery-1.12.4.min.js"></script>
	<script>
	    window.onload = function(){
	        var oDiv = document.getElementById('div01');
	        alert('原生就是获取的div：' + oDiv);
	    };

	    /*
	    $(document).ready(function(){
	        var $div = $('#div01');
	        alert('jquery获取的div：' + $div);
	    });
	    */

	    // 上面ready的写法可以简写成下面的形式：
	    $(function(){
	        var $div = $('#div01');
	        alert('jquery获取的div：' + $div);
	    }); 
	</script>

	<div id="div01">这是一个div</div>

* 小结

::

    引入jQuery
    获取标签元素需要在入口函数来完成，它的速度比原生的 window.onload 更快

    jQuery入口函数有两种写法:

	// 完整写法
	$(document).ready(function(){
	   ...
	});

	// 简化写法
	$(function(){
	   ...
	});

jQuery选择器
**********************************************************************************

* jQuery选择器的介绍

jquery选择器就是快速选择标签元素，获取标签的，选择规则和css样式一样。

* jQuery选择器的种类

::

	标签选择器
	类选择器
	id选择器
	层级选择器
	属性选择器

示例代码:

.. code-block:: javascript
	:linenos:

	$('#myId') //选择id为myId的标签
	$('.myClass') // 选择class为myClass的标签
	$('li') //选择所有的li标签
	$('#ul1 li span') //选择id为ul1标签下的所有li标签下的span标签
	$('input[name=first]') // 选择name属性等于first的input标签

说明: 可以使用length属性来判断标签是否选择成功, 如果length大于0表示选择成功，否则选择失败。

.. code-block:: javascript
	:linenos:

	$(function(){
	    result = $("div").length;
	    alert(result);
	});

* 小结

::

	jQuery选择器就是选择标签的
	标签选择器是根据标签名来选择标签
	类选择器是根据类名来选择标签
	id选择器是根据id来选择标签
	层级选择器是根据层级关系来选择标签
	属性选择器是根据属性名来选择标签

选择集过滤
**********************************************************************************

* 选择集过滤的介绍

选择集过滤就是在选择标签的集合里面过滤自己需要的标签

* 选择集过滤的操作

::

	has(选择器名称)方法，表示选取包含指定选择器的标签
	eq(索引)方法，表示选取指定索引的标签

has方法的示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){
	        //  has方法的使用
	        var $div = $("div").has("#mytext");
	        //  设置样式
	        $div.css({"background":"red"});
	    });
	</script>

	<div>
	    这是第一个div
	    <input type="text" id="mytext">
	</div>

	<div>
	    这是第二个div
	    <input type="text">
	    <input type="button">
	</div>

eq方法的示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){
	        //  has方法的使用
	        var $div = $("div").has("#mytext");
	        //  设置样式
	        $div.css({"background":"red"});

	        //  eq方法的使用
	        var $div = $("div").eq(1);
	        //  设置样式
	        $div.css({"background":"yellow"});
	    });
	</script>

	<div>
	    这是第一个div
	    <input type="text" id="mytext">
	</div>

	<div>
	    这是第二个div
	    <input type="text">
	    <input type="button">
	</div>

* 小结

::

	选择集过滤可以使用has方法和eq方法来完成
	jquery给标签设置样式使用css方法

选择集转移
**********************************************************************************

* 选择集转移介绍

选择集转移就是以选择的标签为参照，然后获取转移后的标签

* 选择集转移操作

::

	$('#box').prev(); 表示选择id是box元素的上一个的同级元素
	$('#box').prevAll(); 表示选择id是box元素的上面所有的同级元素
	$('#box').next(); 表示选择id是box元素的下一个的同级元素
	$('#box').nextAll(); 表示选择id是box元素的下面所有的同级元素
	$('#box').parent(); 表示选择id是box元素的父元素
	$('#box').children(); 表示选择id是box元素的所有子元素
	$('#box').siblings(); 表示选择id是box元素的其它同级元素
	$('#box').find('.myClass'); 表示选择id是box元素的class等于myClass的元素

选择集转移的示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){
	        var $div = $('#div01');

	        $div.prev().css({'color':'red'});
	        $div.prevAll().css({'text-indent':50});
	        $div.next().css({'color':'blue'});
	        $div.nextAll().css({'text-indent':80});
	        $div.siblings().css({'text-decoration':'underline'})
	        $div.parent().css({'background':'gray'});
	        $div.children().css({'color':'red'});
	        $div.find('.sp02').css({'font-size':30});
	    });  
	</script>

	<div>
	    <h2>这是第一个h2标签</h2>
	    <p>这是第一个段落</p>
	    <div id="div01">这是一个<span>div</span><span class="sp02">第二个span</span></div>
	    <h2>这是第二个h2标签</h2>
	    <p>这是第二个段落</p>
	</div>

* 小结

::

	prev() 表示获取上一个同级元素
	prevAll() 表示获取上面所有同级元素
	next() 表示获取下一个同级元素
	nextAll() 表示获取下面所有同级元素
	parent() 表示获取父元素
	children() 表示获取所有的子元素
	siblings() 表示获取其它同级元素
	find("选择器名称") 表示获取指定选择器的元素

获取和设置元素内容
**********************************************************************************

* html方法的使用

jquery中的html方法可以获取和设置标签的html内容

示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){

	        var $div = $("#div1");
	        //  获取标签的html内容
	        var result = $div.html();
	        alert(result);
	        //  设置标签的html内容，之前的内容会清除
	        $div.html("<span style='color:red'>你好</span>");
	        //  追加html内容
	        $div.append("<span style='color:red'>你好</span>");

	    });
	</script>

	<div id="div1">
	    <p>hello</p>
	</div>

说明: 给指定标签追加html内容使用append方法

* 小结

::

	获取和设置元素的内容使用: html方法
	给指定元素追加html内容使用: append方法

获取和设置元素属性
**********************************************************************************

* prop方法的使用

之前使用css方法可以给标签设置样式属性，那么设置标签的其它属性可以使用prop方法了。

示例代码:

.. code-block:: javascript
	:linenos:

	<style>
	    .a01{
	        color:red;
	    }
	</style>

	<script>
	    $(function(){
	        var $a = $("#link01");
	        var $input = $('#input01')

	        // 获取元素属性
	        var sId = $a.prop("id");
	        alert(sId);

	        // 设置元素属性
	        $a.prop({"href":"http://www.baidu.com","title":'这是去到百度的链接',"class":"a01"});

	        //  获取value属性
	        // var sValue = $input.prop("value");
	        // alert(sValue);

	        // 获取value属性使用val()方法的简写方式
	        var sValue = $input.val();
	        alert(sValue);
	        // 设置value值
	        $input.val("222222");
	    })
	</script>

	<a id="link01">这是一个链接</a>
	<input type="text" id="input01" value="111111">

说明: 获取value属性和设置value属性还可以通过val方法来完成。

* 小结

::

	获取和设置元素属性的操作可以通过prop方法来完成
	获取和设置元素的value属性可以通过val方法来完成，更加简单和方便

jQuery 事件
**********************************************************************************

* 常用事件

::

	click() 鼠标单击
	blur() 元素失去焦点
	focus() 元素获得焦点
	mouseover() 鼠标进入（进入子元素也触发）
	mouseout() 鼠标离开（离开子元素也触发）
	ready() DOM加载完成

示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){
	        var $li = $('.list li');
	        var $button = $('#button1')
	        var $text = $("#text1");
	        var $div = $("#div1")

	        //  鼠标点击
	        $li.click(function(){             
	            // this指的是当前发生事件的对象，但是它是一个原生js对象
	            // this.style.background = 'red';

	            // $(this) 指的是当前发生事件的jquery对象
	            $(this).css({'background':'gold'});
	            // 获取jquery对象的索引值,通过index() 方法
	            alert($(this).index());
	        });

	        //  一般和按钮配合使用
	        $button.click(function(){
	            alert($text.val());
	        });

	        //  获取焦点
	        $text.focus(function(){
	            $(this).css({'background':'red'});

	        });

	        //  失去焦点
	        $text.blur(function(){
	            $(this).css({'background':'white'});

	        });

	        //  鼠标进入
	        $div.mouseover(function(){
	            $(this).css({'background':'gold'});

	        });

	        //  鼠标离开
	        $div.mouseout(function() {
	            $(this).css({'background':'white'});
	        });
	    });
	</script>

	<div id="div1">
	    <ul class="list">
	        <li>列表文字</li>
	        <li>列表文字</li>
	        <li>列表文字</li>
	    </ul>

	    <input type="text" id="text1">
	    <input type="button" id="button1" value="点击">
	</div>

说明:

::

	this指的是当前发生事件的对象，但是它是一个原生js对象
	$(this) 指的是当前发生事件的jquery对象

* 小结

jQuery常用事件:

::

	click() 鼠标单击
	blur() 元素失去焦点
	focus() 元素获得焦点
	mouseover() 鼠标进入（进入子元素也触发）
	mouseout() 鼠标离开（离开子元素也触发）
	ready() DOM加载完成

事件代理
**********************************************************************************

* 事件代理介绍

事件代理就是利用事件冒泡的原理(事件冒泡就是事件会向它的父级一级一级传递),把事件加到父级上，通过判断事件来源，执行相应的子元素的操作，事件代理首先可以极大减少事件绑定次数，提高性能；其次可以让新加入的子元素也可以拥有相同的操作。

事件冒泡代码:

.. code-block:: javascript
	:linenos:

	 <script>
	    $(function(){

	        var $div1 = $('#div1');
	        var $div2 = $('#div2');

	        $div1.click(function(){
	            alert($(this).html());
	        }); 

	        $div2.click(function(){
	            alert($(this).html());
	        }); 
	    });
	</script>

	 <div id="div1" style="width:200px; height:200px; background: red;">
	    <div id="div2" style="width:100px; height:100px;background: yellow;">
	        哈哈
	    </div>
	</div>

说明: 当点击子元素div，它的点击事件会向它父元素传递，也会触发了父元素的点击事件，这就是事件冒泡。

* 事件代理的使用

一般绑定事件的写法:

.. code-block:: javascript
	:linenos:

	$(function(){
	    $ali = $('#list li');
	    $ali.click(function() {
	        $(this).css({background:'red'});
	    });
	})

	<ul id="list">
	    <li>1</li>
	    <li>2</li>
	    <li>3</li>
	    <li>4</li>
	    <li>5</li>
	</ul>

事件代理的写法

.. code-block:: javascript
	:linenos:

	$(function(){
	    $list = $('#list');
	    // 父元素ul 来代理 子元素li的点击事件
	    $list.delegate('li', 'click', function() {
	        // $(this)表示当前点击的子元素对象
	        $(this).css({background:'red'});
	    });
	})

	<ul id="list">
	    <li>1</li>
	    <li>2</li>
	    <li>3</li>
	    <li>4</li>
	    <li>5</li>
	</ul>

delegate方法参数说明: delegate(childSelector,event,function)

::

	childSelector: 子元素的选择器
	event: 事件名称，比如: 'click'
	function: 当事件触发执行的函数

* 小结

::

	事件代理就是使用父元素来代理子元素的事件，好处是减少事件的绑定次数，提高性能。
	使用场景当多个相同的子元素绑定同一个事件，可以使用事件代理。
	事件代理使用是使用delegate方法来完成

JavaScript 对象
**********************************************************************************

* JavaScript对象的介绍

JavaScript 中的所有事物都是对象：字符串、数值、数组、函数等都可以认为是对象，此外，JavaScript 允许自定义对象，对象可以拥有属性和方法。

* JavaScript创建对象操作

创建自定义javascript对象有两种方式:

::

	通过顶级Object类型来实例化一个对象
	通过对象字面量创建一个对象

Object类创建对象的示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    var person = new Object();

	    // 添加属性：
	    person.name = 'tom';
	    person.age = '25';

	    // 添加方法：
	    person.sayName = function(){
	        alert(this.name);
	    }

	    // 调用属性和方法：
	    alert(person.age);
	    person.sayName();
	</script>

对象字面量创建对象的示例代码: 

.. code-block:: javascript
	:linenos:

	<script>
	    var person2 = {
	        name:'Rose',
	        age: 18,
	        sayName:function(){
	            alert('My name is' + this.name);
	        }
	    }

	    // 调用属性和方法：
	    alert(person2.age);
	    person2.sayName();
	</script>

说明: 调用属性和方法的操作都是通过点语法的方式来完成，对象的创建推荐使用字面量方式，因为更加简单。

* 小结

创建自定义javascript对象有两种方式:

::

	Object
	字面量

json
**********************************************************************************

* json的介绍

json是 JavaScript Object Notation 的首字母缩写，翻译过来就是javascript对象表示法，这里说的json就是类似于javascript对象的字符串，它同时是一种数据格式，目前这种数据格式比较流行，逐渐替换掉了传统的xml数据格式。

* json的格式

json有两种格式：

::

	对象格式
	数组格式

对象格式: 对象格式的json数据，使用一对大括号({})，大括号里面放入key:value形式的键值对，多个键值对使用逗号分隔。

对象格式的json数据:

.. code-block:: javascript
	:linenos:

	{
	    "name":"tom",
	    "age":18
	}

| 格式说明: json中的(key)属性名称和字符串值需要用双引号引起来，用单引号或者不用引号会导致读取数据错误。
| 数组格式: 数组格式的json数据，使用一对中括号([])，中括号里面的数据使用逗号分隔。

数组格式的json数据: ``["tom",18,"programmer"]``

实际开发的json格式比较复杂,例如:

.. code-block:: javascript
	:linenos:

	{
	    "name":"jack",
	    "age":29,
	    "hobby":["reading","travel","photography"]
	    "school":{
	        "name":"Merrimack College",
	        "location":"North Andover, MA"
	    }
	}

json数据转换成JavaScript对象

.. code-block:: javascript
	:linenos:

	var sJson = '{"name":"tom","age":18}';
	var oPerson = JSON.parse(sJson);

	// 操作属性
	alert(oPerson.name);
	alert(oPerson.age);

* 小结

::

	json就是一个javascript对象表示法，json本质上是一个字符串。
	json有两种格式：1. 对象格式, 2. 数组格式

ajax
**********************************************************************************

* ajax的介绍

ajax 是 Asynchronous JavaScript and XML的简写，ajax一个前后台配合的技术，它可以让 javascript 发送异步的 http 请求，与后台通信进行数据的获取，ajax 最大的优点是实现局部刷新，ajax可以发送http请求，当获取到后台数据的时候更新页面显示数据实现局部刷新，在这里大家只需要记住，当前端页面想和后台服务器进行数据交互就可以使用ajax了。

这里提示一下大家, 在html页面使用ajax需要在web服务器环境下运行, 一般向自己的web服务器发送ajax请求。

* ajax的使用

jquery将它封装成了一个方法$.ajax()，我们可以直接用这个方法来执行ajax请求。

示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $.ajax({
	    // 1.url 请求地址
	    url:'http://t.weather.sojson.com/api/weather/city/101010100',
	    // 2.type 请求方式，默认是'GET'，常用的还有'POST'
	    type:'GET',
	    // 3.dataType 设置返回的数据格式，常用的是'json'格式
	    dataType:'JSON',
	    // 4.data 设置发送给服务器的数据, 没有参数不需要设置
	​
	    // 5.success 设置请求成功后的回调函数
	    success:function (response) {
	        console.log(response);    
	    },
	    // 6.error 设置请求失败后的回调函数
	    error:function () {
	        alert("请求失败,请稍后再试!");
	    },
	    // 7.async 设置是否异步，默认值是'true'，表示异步，一般不用写
	    async:true
	});
	</script>

ajax方法的参数说明:

::

	url 请求地址
	type 请求方式，默认是'GET'，常用的还有'POST'
	dataType 设置返回的数据格式，常用的是'json'格式
	data 设置发送给服务器的数据，没有参数不需要设置
	success 设置请求成功后的回调函数
	error 设置请求失败后的回调函数
	async 设置是否异步，默认值是'true'，表示异步，一般不用写
	同步和异步说明
	    同步是一个ajax请求完成另外一个才可以请求，需要等待上一个ajax请求完成，好比线程同步。
	    异步是多个ajax同时请求，不需要等待其它ajax请求完成， 好比线程异步。

ajax的简写方式: $.ajax按照请求方式可以简写成$.get或者$.post方式

ajax简写方式的示例代码:

.. code-block:: javascript
	:linenos:

	<script>
	    $(function(){
	        /*
	         1. url 请求地址
	         2. data 设置发送给服务器的数据, 没有参数不需要设置
	         3. success 设置请求成功后的回调函数
	         4. dataType 设置返回的数据格式，常用的是'json'格式, 默认智能判断数据格式
	        */ 
	        $.get("http://t.weather.sojson.com/api/weather/city/101010100", function(dat,status){
	            console.log(dat);
	            console.log(status);
	            alert(dat);
	        }).error(function(){
	            alert("网络异常");
	        });

	        /*
	         1. url 请求地址
	         2. data 设置发送给服务器的数据, 没有参数不需要设置
	         3. success 设置请求成功后的回调函数
	         4. dataType 设置返回的数据格式，常用的是'json'格式, 默认智能判断数据格式
	        */ 
	        $.post("test.php", {"func": "getNameAndTime"}, function(data){
	            alert(data.name); 
	            console.log(data.time); 
	        }, "json").error(function(){
	            alert("网络异常");
	        }); 
	    });
	</script>

$.get和$.post方法的参数说明:

::

	$.get(url,data,success(data, status, xhr),dataType).error(func)
	$.post(url,data,success(data, status, xhr),dataType).error(func)

	url 请求地址
	data 设置发送给服务器的数据，没有参数不需要设置
	success 设置请求成功后的回调函数
	    data 请求的结果数据
	    status 请求的状态信息, 比如: "success"
	    xhr 底层发送http请求XMLHttpRequest对象
	dataType 设置返回的数据格式
	    "xml"
	    "html"
	    "text"
	    "json"
	error 表示错误异常处理
	    func 错误异常回调函数

* 小结

::

	ajax 是发送http请求获取后台服务器数据的技术
	ajax的简写方式可以使用$.get和$.post方法来完成



































































