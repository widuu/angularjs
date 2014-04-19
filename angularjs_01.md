Angularjs-基础教程（1）
===

>很久没有写过东西了，感觉写东西都不知道从哪里开始了，现在还是先写点技术性的吧，angularjs--我兄弟把它叫成“俺哥啦js”

####1.下载

	官方网址：https://angularjs.org/

	CDN：https://ajax.googleapis.com/ajax/libs/angularjs/1.3.0-beta.5/angular.min.js

####2.简单介绍使用

+ **1.ng-app**

>决定了angularjs的作用域范围，你可以如下使用

	<html ng-app> 
	…  
	</html> 

>来让angularjs渲染整个页面，也可以使用

	<div ng-app='myapp'>
	……
	</div>

>来渲染其中的一部分。

+ **2.ng-model**

>ng-model,当你的数据模型被改变的时候，譬如ng-model='test',其中这个test的数值被改变的时候，{{test}}的数值也将跟随改变，也就是连接到ng-model中的test也跟随改变,如下

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app>
		<input ng-model='test' >{{test}}
		</body>
	</html>

![效果图](http://widuu.u.qiniudn.com/angularjs1.gif)

+ **3.angular.module**

>这个主要是做模块的注册，创建和索引，譬如我们ng-app想把这个注册成为一个服务就要用，当我们引用索引一个模块的时候也要使用

	angular.module(name, [requires], [configFn]);
	#name 		类型string创建的检索模块的名称
	#requires	简单理解就是你需要包含的使用模块，譬如ngRoute路由模块
	#configFn	可以选配的功能模块，功能和module.config类似


+ **4.controller**

>controller是angular.Module中的方法controller(name, constructor);其中name是controller的名字，constructor是控制器构造函数，我们利用一段代码来说明

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[]);
			app.controller('mytest',function($scope){
				$scope.test="hello word";
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</body>
	</html>

![效果图](http://widuu.u.qiniudn.com/angularjs/2.gif)

+ **5.value**

>value也是angular.Module中的方法value(name, object);其中name是service的名称，object是服务器实例对象,这个时候我们就可以把上边的代码修改正成这样

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[])
			.value('testvalue','word');
			app.controller('mytest',function($scope,testvalue){
				$scope.test="hello "+ testvalue;
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</body>
	</html>

+ **5.factory**


>factory也是angular.Module中的方法factory(name, providerFunction);;其中name是service的名称，providerFunction是函数用于创建新的服务器对象,这个时候我们就可以把上边的代码修改正成这样

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[])
				.value('testvalue','widuu')
				.factory('testfactory',function(testvalue){
					return{
						lable:function(){
							return "this can output : hello "+ testvalue;
						}
					}
				});
			app.controller('mytest',function($scope,testvalue,testfactory){
				$scope.test = "hello "+ testvalue;
				$scope.output = testfactory.lable();
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</p>
			{{output}}
		</body>
	</html>

![效果图](http://widuu.u.qiniudn.com/angularjs/3.png)

+ **6.provider**


>provider也是angular.Module中的方法provider(name, providerType);其中name是service的名称，providerFunction是函数用于创建新的服务器对象,这个跟factory差不多，我们现在用provider重写

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[])
				.value('testvalue','widuu')
				.provider('testprovider',
					function(){
					  this.lable = "this will output : hello widuu";
					  this.$get = function () {
	  				       return this;
				       }
					}
				);
			app.controller('mytest',function($scope,testvalue,testprovider){
				$scope.test = "hello "+ testvalue;
				$scope.output = testprovider.lable;
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</p>
			{{output}}
		</body>
	</html>

+ **7.service**


>service也是angular.Module中的方法service(name, constructor);其中name是service的名称，constructor一个将被实例化的构造函数,这个跟factory差不多，我们现在用service重写

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[])
				.value('testvalue','widuu')
				.service('testservice',
					function(testvalue){
					 	this.lable = function(){
					 		return "this will output:hello "+testvalue;
					 	}
					}
				);
			app.controller('mytest',function($scope,testvalue,testservice){
				$scope.test = "hello "+ testvalue;
				$scope.output = testservice.lable();
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</p>
			{{output}}
		</body>
	</html>

+ **8.constant**

>constant也是angular.Module中的方法constant(name, object);其中name是常量的名称，而object是常量的值，我们可以这样写的

	<!doctype html>
	<html>
		<head>
			<script src="angular.min.js" type="text/javascript"></script>
			<script type="text/javascript">
			var app = angular.module('myapp',[])
				.value('testvalue','widuu')
				.constant('count',23)
				.service('testservice',
					function(testvalue,count){
					 	this.lable = function(){
					 		return "this will output:hello "+testvalue+",age is "+count;
					 	}
					}
				);
			app.controller('mytest',function($scope,testvalue,testservice){
				$scope.test = "hello "+ testvalue;
				$scope.output = testservice.lable();
			});
			</script>
			<title>learing argularjs--widuu</title>
		</head>
		<body ng-app='myapp' ng-controller='mytest' >
		<input ng-model='test'>{{test}}
		</p>
			{{output}}
		</body>
	</html>	

![效果图](http://widuu.u.qiniudn.com/angularjs/4.png)

>今天就写到这里，然后以后继续,转载请注明，来自[微度网络-网络技术支持中心](http://www.widuu.com)
