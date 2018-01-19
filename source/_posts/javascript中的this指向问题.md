---
title: javascript中的this指向问题
date: 2016-3-29 22:21:25
tags:
	-javascript
---
在深入学习JavaScript之后,我们越来越多的会遇到函数或者在对象内部中,对于this的指向问题的疑惑,其实基本上每一个编程语言中都有一个this,这个this的指向都是大同小异,你也可以汉化它的意思,this的意思是这个,就是本身的意思,那么在JavaScript中,我们的this代替的那个本身到底会随着代码情况的不同而有什么变化呢?今天,我们就来一体探讨一下JavaScript中关于this的指向问题:
<!--more-->
首先呢,this可以指向window对象,和其他调用它的对象,当然,在严格模式下,它指向undefined,那我们就先来看一下最简单的形式,代码1:
```
1 function People (name,age){
2      this.name = name;
3      this.age    = age;
4 }
5 var xiaoMing=new People('xiaoming',13);
6 alert(xiaoMing.name);//这时会弹出xiaoming
```
上边呢,是我们对于this的理解最简单的一种形式,xiaoMing是我们通过构造函数People新创建的一个对象,那么函数中的this指向的就是我们的对象xiaoMing,所有当我们调用xiaoMing.name的时候,会弹出它的名字,注意,在这里我们美创建一个对象,这个对象就会有它自己本身的this来指向它本身,好了,这是我们应用中最简单的一种方式,相信大家对于这种方式都不会有疑惑.我们接着往下看,代码2:
```
function People (name,age,shoes){
this.name = name;
this.age = age;
this.shoes =shoes
this.run = function (){
console.log(this.name+"穿着"+this.shoes+"在跑步");
  }
}
var xiaoMing=new People('xiaoming',13,'nike');
xiaoMing.run();//xiaoming穿着nike在跑步
```
 
 

那么这里呢,我们就可以看到,在类中的方法,调用到了类中的属性,是可以调用的,这个就像你要去跑步,而鞋子就是你自己的,那么你当然可以穿着你的nike鞋子去跑步了,好了,现在到了这里,我们应该都可以理解,这就是我们最基本的用法.我们接着往下看,代码3:
```
 1 var name = "小红";
 2 var shoes= "adidas"
 3 function People (name,age,shoes){
 4      this.name  = name;
 5      this.age   = age;
 6      this.shoes =shoes
 7      this.run   = (function (){
 8          return function(){
 9              console.log(this.name+"穿着"+this.shoes+"在跑步");
10          }
11      
12      })();
13 }
14 var xiaoMing=new People('xiaoming',13,'nike');
15     xiaoMing.run();//1.html:241 xiaoming穿着nike在跑步
16 var other = xiaoMing.run;
17          other();//1.html:241 小红穿着adidas在跑步
```
好了从上边的代码中,我们可以明显的看到this在对象中的指向问题,要记住一点,this指向的是对象,是一个对象实体,是一个有意义的,它并不指向函数,例如代码3的第15行,run函数自调用返回的是一个闭包,有人可能会觉得第15行会返回小红,但因为第15行的调用者还是xiaoMing,所以它返回的是xiaoMing的方法,这就与第17行形成一个对比.
```
 1 function People (name,age,shoes){
 2      this.name  = name;
 3      this.age   = age;
 4      this.shoes =shoes
 5      this.run   = (function (){
 6         var timer= setInterval(function(){
 7         console.log(this.name+"穿着"+this.shoes+"在跑步");
 8      },30)
 9              
10          
11      
12      })();
13 }
14 var xiaoMing=new People('xiaoming',13,'nike');
15 //这里会疯狂的输出小红.
```
这里就出来了我们要说明的第一个问题,当this在定时器当中时,this的指向为变为window,所以我们也可以这样理解,this的指向并不是定义的时候就确定的,而是在调用时确定的,这也就给我们带来了这么多的不确定性,对于this来说,我们最常用的方式,也是最容易出现指向错误的地方,应该就是匿名函数中:

 
```
 1 var name = "小红";
 2 var shoes= "adidas"
 3 function People (name,age,shoes){
 4      this.name  = name;
 5      this.age   = age;
 6      this.shoes =shoes
 7      this.run   = (function (){
 8         console.log(this.name+"穿着"+this.shoes+"在跑步");
 9 
10      })();
11              
12          
13     
14 }
15 var xiaoMing=new People('xiaoming',13,'nike');
16 //输出小红
```
对于匿名函数,我么可以这样去理解,每一个函数在执行的i时候都会去寻找它的this调用者和arguments参数,而匿名函数的this是window,所以匿名函数的执行具有全局性,所以,我们在使用匿名函数时,一定要注意它的this指向问题,我们我们可以使用that捕获,call apply和bind等方式去改正this的指向问题,
```
 1 var name = "小红";
 2 var shoes= "adidas"
 3 function People (name,age,shoes){
 4      this.name  = name;
 5      this.age   = age;
 6      this.shoes =shoes
 7      this.run   = (function (){
 8         console.log(this.name+"穿着"+this.shoes+"在跑步");
 9         
10      })();
11      return {};
12              
13          
14     
15 }
16 var xiaoMing=new People('xiaoming',13,'nike');
17 console.log(xiaoMing.name);//undefined
```
这里我们需要注意另外一个问题,就是当我们构造函数中有一个return返回值时,且这个返回值时一个对象的时候,那么this会指向这个返回的对象,而一般我们的构造函数都是没有返回值,这是我们另外要注意的一点.

好了,讲到这里,希望大家对于this的理解能够多一点点,在应用中我们多用下this,会加深我们对this的理解.有不足的地方还希望指正,谢谢!

 