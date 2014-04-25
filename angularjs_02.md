Angularjs-基础教程（2）
===

>针对于这个其实我不太清楚应该针对于哪些人或者说不知道从哪开始写，所以这里我就按照一种简单的思路开始写


+ **1.angular.element**
+ **2.angular.Bootstrap**

>我们非常清楚ng-app应用到节点，angular自动帮你初始化，初始化的过程分为如下几个步骤

+ 1.angular会在document load的时候自动初始化，首先会找到ng-app这个指令指定的节点。
+ 2.加载与module相关的指令
+ 3.创建与应用相关的injector(依赖管理器)
+ 4.以制定的ng-app为根节点，开始对Dom进行compile

>现在我们自己初始化一下，做一个等同于ng-app这个指令的东西。angular.element这个就是包装,包装原始DOM元素或HTML字符串作为jQuery元素。angular.Bootstrap可以手动初始化脚本，我们用这两个来初始化这个脚本



	<!DOCTYPE HTML>
	<html lang="zh-cn">
	<head>
	    <meta charset="UTF-8">
	    <title>Bootstrap-manual</title>
	    <style type="text/css">
	        .ng-cloak {
	            display: none;
	        }
	    </style>
	</head>
	<body>
	    这里是ng-app外面的~~{{1+2}}
	    <div id="widuu" >这里是ng-app里面~~~ {{1+2}}</div>
	    <script src="angular.min.js" type="text/javascript"></script>
	    <script type="text/javascript">
	        angular.element(document).ready(function() {
	            angular.bootstrap(angular.element(document.getElementById("widuu")));
	        });
	    </script>
	</body>
	</html>

###2.compiler

>我们清楚的看到angularjs的官方文档上边都是驼峰式命名法，譬如ngApp、ngModule、ngBind等等这些都是相关的指令，其中html compiler就是允许我们自己定义元素 属性和标签，Angular将这些附加行为称为directives。

---


>官方文档对compiler的解释是这样的

	Compiler
	Compiler is an Angular service which traverses the DOM looking for attributes. The compilation process happens in two phases.
	
	Compile: traverse the DOM and collect all of the directives. The result is a linking function.
	
	Link: combine the directives with a scope and produce a live view. Any changes in the scope model are reflected in the view, and any user interactions with the view are reflected in the scope model. This makes the scope model the single source of truth.
	
	Some directives such as ng-repeat clone DOM elements once for each item in a collection. Having a compile and link phase improves performance since the cloned template only needs to be compiled once, and then linked once for each clone instance.

>compiler是angular的一个(service),负责遍历dom节点和查找属性，编译分为两个阶段：

>1.编译：遍历节点和收集所有的directives，返回一个链接函数linking function

>2.链接：将directives绑定到一个作用域（scope）中，创建一个实况视图（live view）。在scope中的任何改变，将会在视图中得到体现（更新视图）；任何用户对模版的活动（改变），将会体现在scope model中（双向绑定）。这使得scope model能够反映正确的值。

>一些directives，诸如ng-repeat，会为每一个在集合（collection）中的元素复制一次特定的元素（组合）。编译和链接两个阶段，使性能得以提升。因为克隆出来的模版(template)只需要编译一次，然后为每一个集合中的元素进行一次链接（类似模版缓存）。

###3.一步一步创建自己的directive

+ 1.了解directive

>首先了解，directive是按照驼峰式命名法，如ngModule,当编译的时候匹配是这样的，举例如下：

	<input ng-model="foo">

	<input data-ng:model="foo">

>directive可以使用x-或者data-作为前缀，可以使用:, -, 或者 _等分隔符来转换驼峰式命名方法，如下所示：

	<span ng-bind="name"></span> <br/>
  	<span ng:bind="name"></span> <br/>
    <span ng_bind="name"></span> <br/>
    <span data-ng-bind="name"></span> <br/>
    <span x-ng-bind="name"></span> <br/>

>一般我们使用ng-bind对应ngBind，这种格式

>$compile可以匹配directive基于元素名称,属性,类名以及注释

	<my-dir></my-dir>
	<span my-dir="exp"></span>
	<!-- directive: my-dir exp -->
	<span class="my-dir: exp;"></span>

>　在编译过程中，compiler通过$interpolate服务匹配文本与属性中的嵌入表达式（如{{something}}）。这些表达式将会注册为watches，并且作为digest cycle的一部分，一同更新。下面是一个简单的interpolation：

	<img src="img/{{username}}.jpg"/>Hello {{username}}!

+ 2.编译的步骤

>HTML“编译”的三个步骤：

>　　1. 首先，通过浏览器的标准API，将HTML转换为DOM对象。这是很重要的一步。因为模版必须是可解析（符合规范）的HTML。这里可以跟大多数的模版系统做对比，它们一般是基于字符串的，而不是基于DOM元素的。

>　　2. 对DOM的编译（compilation）是通过调用$comple()方法完成的。这个方法遍历DOM，对directive进行匹配。如果匹配成功，那么它将与对应的DOM一起，加入到directive列表中。只要所有与指定DOM关联的directive被识别出来，他们将按照优先级排序，并按照这个顺序执行他们的compile() 函数。directive的编译函数（compile function），拥有一个修改DOM结构的机会，并负责产生link() function的解析。$compile()方法返回一个组合的linking function，是所有directive自身的compile function返回的linking function的集合。

>　　3. 通过上一步返回的linking function，将模版与scope连接起来。这反过来会调用directive自身的linking function，允许它们在元素上注册一些监听器（listener），以及与scope一起建立一些watches。这样得出的结果，是在scope与DOM之间的一个双向、即时的绑定。scope发生改变时，DOM会得到对应的响应。

	var $compile = ...; // injected into your code

    var scope = ...;

    var html = '<div ng-bind='exp'></div>';

    // Step 1: parse HTML into DOM element
    var template = angular.element(html);

    // Step 2: compile the template
    var linkFn = $compile(template);

    // Step 3: link the compiled template with the scope.
    linkFn(scope);

#####ngAttr属性绑定

	<svg>
	  <circle ng-attr-cx="{{cx}}"></circle>
	</svg>

今天就写到这里，明天开始写创建directive ~~~控制篇幅不要太长，这章主要概念的多~~~
 

