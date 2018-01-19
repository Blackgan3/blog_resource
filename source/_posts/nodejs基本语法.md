---
title: nodejs基本语法
date: 2016-9-02 14:36:41
tags:
	- nodejs
	- javascript
---
nodejs是JavaScript的一个在后端的运行环境,关于nodejs的认识,我们可以看上一篇文章<<初识nodejs>>,我们要使用nodejs,首先要安装nodejs,安装的东西你可理解为java中的运行环境一样,至于怎么安装,我们最好安装nvm(nodejs version manage),安装的过程也写在了另一篇博客<<nodejs安装>>(那时懵懂期,回头再修改的详细一点)
<!--more-->

---
那么nodejs作为一个后台的平台,他可以为我们提供的很多,我们可以用JavaScript语言进行文件的获取,删除等等文件操作,我们可以用JavaScript开一个本地服务器,我们可以和mongoDB配合来搭建后台,他给我们前端带来了太多的惊喜,让我们深深的迷恋上了它,但正所谓学一个新知识时,我们都需要从简,从基础学起,那么在nodejs环境下,我们的基本语法又是怎样的呢?
## 模块
---
nodejs是支持ES6的,所以在这里你可以尽情的去写,不用考虑浏览器不兼容了(这里是后端,哈哈),安装好了nodejs后,我们在终端输入node,就会进入到nodejs环境,这里我们可以输入:
```
console.log("hello world!");
```
可以看到我们控制台输出了hello world,我第一次写的时候还以为这是chrome的开发者模式呢,这或许就是chrom v8引擎的作用吧,我们这些简单的指令可以直接在终端中输出,那么如果是运行一大段代码呢?我们可以写个js文件里边,
```
在hello.js中
var hello = function(){
    console.log("hello function");
    }
hello();
```
然后node hello.js,这样我们文件中写的程序就能运行出来了,nodejsshi是基于commonJS的,所以当我们的程序变大的时候,就可以使用模块了,nodejs是模块化的,什么是模块,每个模块就是nodejs的每个功能,我们有http模块来提供服务器,有fs模块来提供对文件操作的各种功能,我们写的代码可以分成一个一个的模块,这样可以提高我们代码的复用性,以及减少代码之间的耦合性,可以避免函数名和变量名的冲突.

---
java和python使用**import**来导入其他函数库,而php和ruby是通过require,上边也说过,nodejs是基于CommonJS的,在node中我们可以使用**require**这个关键字来导入模块.例如:
```
var http = require('http');
var fs   = require('fs');
```
首先,我们可以使用原生的和外部的模块,这些模块都是别人写好的或者nodejs自带的,他们都给我们提供各种各样的功能,我们只需要直接require它们就能使用了,
其次,我们也可以自己编写模块,比如你讲一个输出hello的函数单独封装成一个模块,
```
//这里是hello模块
(function(){
    var hello = function(){
        console.log("hello");
    }
    export.hello = hello;
})()
```
这里我们使用exports导出hello函数,然后再另一文件中通过require导入另一个模块,这时再另一个模块中,
```
var hello = require('hello.js');//然后运行hello
hello.hello();//这时输出hello
```
我们还有另一种导出方法,在hello.js中
```
module.exports=hello;//通过这种导出方法,我们在使用它的时候的用法为:
---
var hello = require('hello.js');
hello();//这时输出hello
```
这时我们就出现了不同的地方
### exports和module.exports的区别
---
在讲他们的区别之前,我们先来看一个例子,这个例子以及他们区别的理解我是在[node中文社区][1]学习的.
```
var a ={name:"小明"};
var b =a;
console.log(a.name);
console.log(b.name);
//输出 小明 小明
b.name="小李";
console.log(a.name);
console.log(b.name);
//输出 小李 小李
```
这里,由于a直接赋给b,这是b相当于a的引用,a和b指向同一块存储区域,这是改变了b的name,相应的a的name也发生了改变.那么这就引出了我们要讲的:
>* module.exports原本就是一个空对象
>* exprots是这个对象的一个引用
>* 平时我们require进来的就是module.exports对象

上边这**三大法则**就是module.exports和exports的主要区别,我们可以这样来看:
```
    exports = module.exports;
    所以现在exports.hello = hello;
    就相当于module.exports.hello = hello;
    当另一文件导入var hello = require('hello.js');时
    因为是导入了module.exports这个对象,所以使用hello的时候,是:
        hello.hello();
    
```
所以我们导出模块的时候,直接使用module.exports=hello;就行了.

---
据说nodejs可以使用c/c++编写二进制模块,但因为前端出身的我虽然学过c/c++但研究不深,我还是好好的用JavaScript吧!

---
当我们需要使用一些线上的模块时,只需要去nodejs.org里边去搜索,然后使用npm install就可以了,npm是和nodejs捆绑在一起的,
## 回调函数
---
回调函数是什么呢?我们对于JavaScript中的函数应该不陌生了吧,回到函数就是JavaScript中的函数.当在nodejs中,回调函数是来实现异步调用的,回调函数被称为要去完成的任务,nodejs中很频繁的使用回调函数,nodejs中的所有的API模块都是由回调函数所实现的.由回调函数来实现异步调用,其实就是给你造成一种假象,让你像使用ajax一样让你感觉很顺畅,不堵不堵不堵(嘿嘿嘿)!
我们先提前看一下nodejs在读取文件的时候的一个操作
```
//这里有一个input.txt文件,里边的内容为:"这是一个测试文件"
var fs = require('fs');
var data = fs.readFileSync('input.txt');//同步操作
console.log(data.toString());
console.log("程序结束");
```
运行这个文件后显示
```
这是一个测试文件
程序结束
//这是我们很容易理解的程序流程,程序从上往下一次执行,
```
那我们再来看一个
```
//input.txt文件中的内容不变,
var fs = require('fs');
fs.readFile('input.txt',function(err,data){
    if(err){return console.eror(err)}
    console.log(data.toString());
});
console.log("程序结束");
```
这时我们再运行这个程序,就会发现输出的内容为:
```
程序结束
这是一个测试文件
//这是我们会发现先输出的是"程序结束"
```
我们先不用去纠结上边那个函数是什么意思,我们应该能看得出来,第二次的函数是一个回调函数,这就是我们的阻塞和非阻塞IO的区别,当程序执行到异步的读文件时,会先将这个相对耗时多的步骤存入一个事件循环(looping)中,转而去执行下边的程序,等到下边的程序执行完了之后再回去执行回调函数这个它存起来的任务,而在程序执行的时候就给我造成一个感觉,好快!这就是我们异步调用,至少目前我感觉,这个异步调用时nodejs的利器.
## 总结
nodejs的基本语句还是JavaScript,只不过它是模块化开发,更有利我们去管理代码,他的异步调用时他的一大利器,这不同于其他多线程的语言,我们只是通过回调函数来实现这一功能,所以它对服务器的压力也不大!
  [1]: https://cnodejs.org/topic/5231a630101e574521e45ef8