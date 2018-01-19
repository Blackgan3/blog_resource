---
title: javascript事件委托机制
date: 2016-3-26 13:16:44
tags:
	-javascript
---
## 前言
 这几天看到一个面试题,大概就是,让你给1000个li都添加一个click事件,应该怎么添加?大多数人第一开始的感觉可能就是,每个li上边都添加一个呗,那要是这样的话,估计面试的时候就会GG了,这里就是撤出了我们的事件冒泡和捕获机制,以及事件委托机制,对于上边这些,我们慢慢来看.
<!--more-->
首先说一下事件冒泡和事件捕获机制,事件冒泡是有微软公司提出来的,事件捕获是有网景公司提出来的,当时两家是争论的不可开交,后来w3c也没办法,就采取了折中的方式,事件产生后先捕获后冒泡,

通常,在js中监听事件的方法共有三种,分别是:
　```
　  ele.addEventListener(type,listener,[useCapture]);//IE6~8不支持
　  ele.attachEvent('on'+type,listener);//IE6~10支持,IE11不支持
　  ele.onClick=function(){};//所有浏览器都支持
　```
　　　
w3c规范中定义了三个事件阶段,依次是捕获阶段,目标阶段,冒泡阶段,而w3c指定的dom2级规定中,使用的是addEventListener来监听事件的.所以我们就以addEventListener来讲解,首先事假冒泡就像你从往水中扔一块石子,水中的气泡从下边往上冒一样,意思为触发事件后从子元素王父元素方向触发,而捕获机制则正好相反,捕获机制是从父元素往子元素方向进行事件触发,而addEventListener函数中的第三位参数正是来决定是使用捕获机制还是冒泡机制的,当useCapture为true是为捕获机制,当useCapture为false时是冒泡机制,我们看一下例子:

　```
　　<div class="parent">
　　<div class="child"></div>
   </div>
       <script>
          var parent = document.getElementsByClassName('parent')[0];
          var child  = document.getElementsByClassName('child')[0];
          parent.addEventListener('click',function(){
          console.log("这里是父元素");
          },false);
    child.addEventListener('click',function(){
        console.log("这里是子元素");
    },false);
	</script>
　```


当我们点击子元素是显示上图,当我们将false改为true后就会发现执行顺序会反过来,这就是事件冒泡和捕获的区别,他们两个刚好相反,

![冒泡和捕获图解]("https://www.google.com.hk/url?sa=i&rct=j&q=&esrc=s&source=images&cd=&cad=rja&uact=8&ved=0ahUKEwjU_trj4ffPAhVKsY8KHcXZA5cQjRwIBw&url=%68%74%74%70%3a%2f%2f%7a%6b%72%65%61%64%2e%63%6f%6d%2f%61%72%74%69%63%6c%65%2f%31%30%38%37%32%36%37%2e%68%74%6d%6c&psig=AFQjCNGzGgynaGwhKfEEuEOPDqA4m5dpWA&ust=1477547029456525")
　　
那么使用这种**绑定机制**我们的弊端在于要去给每一个对象绑定事件会是一个特别麻烦的事情,当我们要删除一个事件或者要改变一个事件的时候会特别的繁琐,更重要的是,我们增加了JavaScript和dom节点之间的关联,而且一点出现循环引用,很有可能造成内存泄露,这些都是它的弊端,

那么解决这种弊端的一种方法就是事件代理(event delegation),这个方法可以让你避免去给每一个节点一一的添加事件,它的做法是将这些监听事件去绑定到这些节点的父元素上,在父元素上的这个监听函数自动去判断是哪一个子元素触发的事件,从而可以对触发事件的子元素进行操作,这里我们给出的例子是davidwalsh所给出的一个例子:

 　　现在我们有一个父元素ul和几个li子元素,
 	 ```
 	<ul id="parent-list">
    	<li id="post-1">Item 1</li>
    	<li id="post-2">Item 2</li>
    	<li id="post-3">Item 3</li>
    	<li id="post-4">Item 4</li>
    	<li id="post-5">Item 5</li>
    	<li id="post-6">Item 6</li>
		</ul>
 	 ```
 　　
 现在我们要实现的是,当我们点击每一个li节点的时候,都会输出li节点中的内容,按照上边的写法,你可以选中这些li,让后给他们加上这些方法,然后等到不需要了再将他们移除,如果有100个li,1000个li呢,这将会成为你的噩梦,较好的解决方法就是给父元素添加一个监听事件,之后的问题便是怎么去判断出来时哪一个li被点击了?　我们可以在监听事件中去判断当前event的target来判断是否是我们要找的节点,这里我们有一个简单的例子:
 	```
 	// 找到父元素,绑定一个监听事件
document.getElementById("parent-list").addEventListener("click", function(e) {
    // e.target是点击的元素
    // 如果它是li元素
    if(e.target && e.target.nodeName == "LI") {
        //
        console.log("List item ", e.target.id.replace("post-", ""), " was clicked!");
    }
});
 	```
 　　
 当ul中发生点击事件后,因为addEventListener默认是冒泡事件,所以监听事件会在底层事件冒泡过来时执行,在触发了事件后,去检测是否是我们要寻找的目标元素,如果不是,就会忽略过去,那我们不仅仅可以通过目标元素的标签是不是我们需要的目标元素,我们还可以根据目标元素的属性或者类名来进行检测,利用ele.maeches这个API来进行处理,
 ```
 document.getElementById("myDiv").addEventListener("click",function(e) {
    // e.target 就是当前被点击的元素
  if (e.target && e.target.matches("a.classA")) {
    console.log("Anchor element clicked!");
    }
});
 ```
 　　　
 因此我们可以看得出来,使用事件代理这种方式,能够给我们带来很多的便捷,可以避免很多坑,使用事件代理是一种很强大的方法.
>>如果你无法简洁的表达你的想法，那只说明你还不够了解它。 -- 阿尔伯特·爱因斯坦