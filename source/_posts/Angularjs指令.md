---
title: Angularjs指令
date: 2016-6-10 20:50:30
tags:
---
ng通过指令这个新属性来扩展html,angular通过内置的指令来为新应用添加功能,允许你自定义指令,ng-app初始化一个ng应用程序,ng-model将元素值绑定到应用程序.
<!--more-->
ng-repeat指令会重复一个HTML元素,例子:
```
<div ng-app="" ng-init="names=['Jani','Hege','Kai']">
 		 <p>使用 ng-repeat 来循环数组</p>
 		 <ul>
  			  <li ng-repeat="x in names track by $index">
   			   {{ x }}
   			 </li>
 		 </ul>
//track by $index加上这个就是可以提高性能,并且还可以使用重复的数组中的值输
出
</div>
```
ng-class是可以接受一些表达式的,经过封装和增强:和原本的设置class的情况有了一定的区别,
使用实例:
```
<div ng-class='{error:isError,warning:isWarning}'>{{messsage}}</div>
```
这里呢我们在控制器中可以改变isError和isWarning的值是为false还是为true,哪一个为true就去赋值为哪一个样式.ng-show还有ng-hide:ng-Animate:是用来是实现动画的额.
自定义指令:
我们使用能促进重用的技术是将数据绑定表达式放在一个指令中,要创建一个指令,首先要定位指令要被放入的目标模块,并调用它的directive()函数,和controller一样,指令时注册在module上,不同在于指令通过module.directiveAPI来注册,module.directive接受的是一个规范化的名字和工厂函数,这个工厂函数返回一个包含不同配置的对象,这个对象用来告诉$compile服务如何进行下一步处理,工厂函数仅在编译器第一次匹配到指令的时候调用一次,通常在工厂函数中执行初始化的工作,该函数使用$injector.invoke调用,所以它可以像controller一样进行依赖注入,优先返回一个定义好的对象,而不是返回一个函数, 
可以使用.directive函数来添加自定义指令.要调用自定义指令，HTML 元素上需要添加自定义指令名。使用驼峰法来命名一个指令， runoobDirective, 但在使用它时需以 - 割, runoob-directive:下边的例子中定义一个指令:这个directive()函数接受指令的名字和一个函数,
```
<script>
var app = angular.module("myApp", []);
app.directive("runoobDirective", function() {
    	  return {
restrict:"A",
              template : "<h1>自定义指令!</h1>"
                 };
          });
       </script>
```
调用指令的时候,我们可以使用元素名进行调用,<runoob-directive>
</runoob-directive>进行调用,还可以通过属性的方式进行调用,还可以通过类名
进行调用,还可以通过注释的方式进行调用,当前,你也可以限定调用的方式为特定的,这是通
过restrict属性来进行特定的调用设置,实例如上一个程序中添加的那样,它的不同的值代表
着不同的调用方式,E(element)作为元素名使用,A(attribute)作为属性使用,C(class)作为类名使用,M(comments)作为注释使用,
restrict的默认值为EA,可以通过属性名和元素名来调用指令,一般推荐这样使用
angularJS:是基于模块的框架,因此上来要创建一个自己的模块,
```
var myAppModule = angular.module("myApp",[]);
```
然后再模块上创建指令directive:
```
myApp.directive("xio",funciton(){
return {
restrict:'asdc',
template:'<div>hello my directive</div>',
replace:true;
}
}
```
函数return了一个键值对组合,其中定义了标签的使用方法,属性等等内容,

>* restrict:定义了标签的使用方法,一共四种,AECM	如果想要注释起作用,需要将replace属性设置为true;注释中指令要留有空格template:定义了标签的模板,里边是用于替换自定义标签的字符串,
>* replace: 是否支持替换,默认为false,即为不支持当前的被替换掉
>* transclude:是否支持内嵌.当前的元素节点是否支持template中的内容进行内嵌
>* templateUrl:'hello.html';
>* templateCache;用来缓存模板
>* scope:{}用于创建一个子scope或孤立的scope,默认为false,true时会从父作用域继承并
创建一个自己的作用域,而ng-controller的作用也是从父作用域继承并创建一个新
作用域,
>* link:用于dom操作任务的函数

link函数接受三个参数:

>* scope:它代表指令被使用的作用域,在上面的例子中它等同与控制器的作用域
指的是template中的作用域
>* elem:它代表绑定指令的元素的jQlite包裹元素,该元素已经被jQuery包裹,
所以我们没有必要将它包含在$()中来进行DOM操作,
>*attars:它代表绑定指令的元素上的属性:
>* priority:优先级,在同一个元素上声明了多个指令时,根据优先级决定哪个被调用,如果	
pritoty相同,则按照声明顺序调用,另外,no-repeat是所有内置指令中优先级最高的,

隔离指令的作用域:
上边我们定义的xio指令已经非常好了,但是它有个致命的缺陷,我们在给定的作用域中
仅能使用一次,现在的实现是,我们每次重用该指令的时候都要为它新创一个控制器,
我们想要做的是能够把指令的作用域与外部的作用域隔离开来,然后映射到外部的作
用域,可以通过创建isolateScope来完成这个目的,这样我们使用指令的scope配置


指令的内嵌使用:
因为标签内部可以嵌套其他的标签,因此想要在自定义标签中嵌套其他标签,则需要:
1,使用transclude属性,并设置为true
2,并使用ng-transclude属性,定义内部嵌套的位置
```
myAppModule.directive("test",function(){
                return{
                    restrict:'AECM',
                    transclude:true,
                    template:"<div>haha! <div ng-transclude></div> wuwu!</div>"
                }
            });
```