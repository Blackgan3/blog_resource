---
title: jquery中的事件机制
date: 2016-4-10 19:42:22
tags:
	- jquery 
	- 事件
---
　　昨天呢,我们大家一起分享了jQuery中的样式选择器,那么今天我们就来看一下jQuery中的事件机制,其实,jQuery中的事件机制与JavaScript中的事件机制区别是不大的,只是,JavaScript中调用是原生的函数方法,而jQuery中调用的绑定的是jQuery中的对象方法,那么在昨天的第一篇中,我们已经说过了jQuery对象和DOM对象之间的转换,至于其中的转换的原理,我们就需要去分析一下jQuery中的源码了,这个我们在源码分析中再去做讨论,
<!--more-->

　　首先呢,我们先来看一下,jQuery中的ready事件,它的作用有些类似于window中的onload事件,只不过二者的区别在于,onload事件是要等到网页中的dom节点加载完成,并且css进行样式渲染后,JavaScript中进行了预加载后再执行,而ready事件呢,是在网页中的标签被解析为DOM节点是就执行其中的函数,直白点说,ready执行的比onload早,那么我们在用jQuery的时候,需要做一个ready的执行定义:
```
 1   //ready的作用,是为具体加载的实现.
 2         $(document).ready(function(){
 3            alert('1234');
 4         });
 5         //第二种,少了一个document,创建一个空的jQuery对象.
 6         $().ready(function(){
 7             alert('abcd');
 8         });
 9         //第三种,简写版的,
10         $(function (){
11             alert('23333');
12         });
```
那么我们再来看一下onload和ready的第二个区别:
```
 1  //如果有两个window.onload它只会执行最后一个.如果有多个的话,后边的会将前面的覆盖掉.
 2        
 3         window.onload=function(){
 4             alert(1);
 5         }
 6         window.onload = function(){
 7             alert(2);
 8         }
 9         //而window.ready 则可以设置多个函数,都去作用.
10         $(document).ready(function(){alert(1)})
11       $(document).ready(function(){alert(3)})
12 //这里的可以设置多个函数,是指的对于同一个对象设置
```
　　那么我们再来看一下jQuery中的事件绑定,这里我们一onclick事件来举例,当我们在js中进行事件绑定的时候,我们分为dom1级事件,dom2级事件,其中还包括对于IE浏览器的兼容性考虑,那么当我们使用jQuery中的事件机制时,就不用去考虑那么多了,jQuery内部已经帮我们做好了兼容性之类的操作,讲真,这真的是很方便了我们了:

　　


```
 1  //DOM1级事件
 2         var itNode = document.getElementById('id');
 3         itNode.onclick = function(){};
 4          div.onclick = null; DOM1级中解除事件绑定
 5     
 6         //DOM2级事件
 7         itNode.addEventListener('click',function(){});
 8         itNode.removeEventListener();
 9        itNode.attachEvent();//IE 6 8 7 兼容性处理
10         detachEvent(); IE中接触事件绑定
11         //使用jQuery来操作绑定事件
12         //$().事件类型(function(){});
13         //$().事件类型();
14         $(function(){
15             $('div').click(function(){//给div绑定了一个onclick事件
16                 $('div').css('color','green');
17             });
18             $('div').mouseover(function(){
19                 $(this).css('color','yellow');
20             });
21         });
```
　　这里的一个主意点就是当绑定事件时,事件名是不用写on的,其实我们还可以用另一种写法$('div').on('click',funciton(){});上面的那种写法就是这种写法的简化,二者的作用还是一样的.那么当我们需要给当前jQuery对象添加多个事件的时候,我们就可以使用bind方法来处理了:
```
 1   
 2             //$().bind(事件类型,事件处理函数(分为有名函数,和匿名函数))
 3             $(function(){
 4                 //绑定一个鼠标移动上去处理的事件.
 5                 $('div').bind('mouseover',function(){
 6                     $('div').css('background-color','blue');
 7                 });
 8                 $('div').bind('mouseout',function(){
 9                     $('div').css('background-color','yellow');
10                 });
11 
12                 //一个函数绑定多个事件 这多个时间之间只需要用空格隔开.
13                 //这个虽然可以绑定多个事件,但是函数比较单一,不灵活.
14                 $('div').bind('mouseover mouseout',function(){
15                     console.log(1234);
16                 });
17                 //使用对象的形式来绑定多个事件,
18                 $('div').bind({
19                    click: function(){console.log(1);},
20                    mouseover: function(){alert(22);},
21                    mouseout : function(){alert(33);}
22                 });
23               
24                 //绑定有名的函数
25                 function fa(){alert("fa");};
26                 function fb(){alert("fb");};
27                 function fc(){alert("fc");};
28                 $('div').bind('click',fb);
29                 $('div').bind('click',fc);
30 
31                 $('div').unbind();//这样直接调用unbind函数会直接将所有绑定的事件都取消掉.
32                 $('div').unbind('click');//这样子就会将div里边所有的click绑定的事件取消掉了
33                 //直接解除指定的函数的绑定,这种方式也只能解除有名函数的绑定,不能解除指定的匿名函数的绑定
34                 $('div').unbind('click',fa);
35 
36             });
```
　　当然,在事件处理中,事件对象无疑是我们经常要使用的一个对象,在js中,我们获取对象时还要考虑到兼容性问题,但是在jquery中,我们根本就不用考虑,只需要直接使用函数中传递的event参数就行了:
```
 1     function(evt){
 2            var evt = evt || window.event;
 3             evt.preventDefault();//主流浏览器阻止默认事件
 4             evt.returnValue = false;// IE中阻止对象默认事件
 5             evt.stopPropagation();//主流浏览器中阻止浏览器冒泡
 6             evt.cancelBubble();//IE中阻止浏览器冒泡处理
 7            
 8         }
 9  $().bind('click',function(evt){
10                //evt不用做兼容性处理
11                 evt.preventDefault();//阻止浏览器的默认动作.
12                 evt.stopPropagation();//阻止浏览器冒泡,
13         });    
```
　　当然了,我们jQuery中的事件也是由键盘事件,鼠标事件,表单事件,甚至还有自定义的事件等等,和js中的基本一样,总结起来呢,使用jQuery使我们不用去考虑兼容性问题,同时代码量也大大的减少了.