---
title: ajax详解
date: 2016-5-10 19:27:10
tags:
	- javascript 
	- ajax
---

## ajax详解
---
ajax(Asynchronous Javascript AndXML)即为异步的JavaScript和XML,顾名思义,这个技术是和我们当前页面刷新无关的,因为它是异步的,在没有ajax的时候,我们如果去请求数据库中的数据就要将当前页面进行刷新,最常用的你可以想到我们的表单验证部分,以前都是填完了所有的表单去一次性验证,像这样的注册界面,你填错了就要刷新页面从头再来,所以,现在有了ajax你可以填一个表格就进行一次验证,而且页面并不会刷新.
<!--more-->
<% %>

对于ajax,他的核心技术是对象,它的整个作用过程其实是当前页面继续工作,它会自己开一个时空隧道和当前界面一起工作,就行两个平行空间一样,它的整个过程可以总结为先创建XMLHttpRequest对象,然后连接服务器,发送请求,最后接受服务器发送过来的数据,好了,XMLHttpRequest是一个可以让我们前端和后端连接起来的十分神奇的对象,我们首先来创建一个XMLHttpRequest对象

　　

复制代码
```
 var xhr =null;
        if(window.XMLHttpRequest){
            xhr = new XMLHttpRequest();
        }else{
            xhr = new ActiveXObject('Microsoft.XMLHTTP');

        }
//这里进行的是针对IE浏览器的兼容性处理,在IE中,我们的xmlhttprequest对象就变成了activeobject,而且里边的参数是不能少的,IE就是这样倔强
复制代码
```
当我们拥有了一个xhr实例后,我们就可以进行向数据库中发送请求,这里我们先来看一下我们需要用的方法,open()方法是建立前端到服务器的请求,而send方法是向服务器发送请求,也就是说,当我们在叫外卖的时候,我们用的美团就像是open()方法,建立我们和店家的联系,而快递小哥就是我们的send()方法,他将我们的美味给我们送过来,这里我们先用get方法做一个示例,在下一块我们再区看一下我们的两种请求方法get和se的区别:
```
var url ="index.php?id=1";
xhr.open('get',url,'true');//open中有三个参数,第一个参数用来指定使用get还是post方式提交,第二参数是指定要发送的url地址,第三个参数指定是否使用异步,第三个参数默认是true;
xhr.send();
```
这是我们已经使用get方式向index.php发送了一个请求,我们穿过去的参数为"id=1";那么我们先来看一下和get和post区别,get和post都是用来进行发送数据的方式,从字面意思来看,get(得到)是从数据库得到东西的请求方式,而post(发送)是要给服务器传送数据的,而且post方式传送数据是经过加密的,post传送的数据放在请求体里边,而get方式只有请求头,没有请求体,所以get也可以传给数据库少量数据,发送的方式是在url地址后边用?做表示,如果有多个参数用&隔开,我们先来看一下post的用法:

```
var url = "index.php";
var data = "id=1";
xhr.open('post',url,true);
xhr.setRequestHeader('Contenttype','application/x-www-form-urlencoded')//post请求需要设置请求头信息
xhr.send(data);//在这里将数据发送过去,
```

那么现在我们已经简单的向服务器端发送了请求,我们正常的一个流程是,服务器会根据我们的参数或者是它自身运算的结果来给我们返回数据,这里我们并不是一定要发送给数据库参数的,举个例子,服务器里有一个数组["0":"苹果","1":"橘子","2":"香蕉"],现在如果我们不传参,服务器会将这一个数组都return给我们,如果我们穿一个id=1,这时返回的数据就是橘子,我们要理解的就是,我们传的参数和数据对于我们获得数据只是一个辅助作用,真正起作用的还是服务器内部的结构,那么我们现在发送了请求后,我们面临一个问题,我们怎么判断请求是否发送成功以及服务器时候处理了我们的请求,还有如果服务器响应了,它返给我们的数据我们该怎么去获取呢?

好的,让我们来看一下,xhr实例上有一个readystate属性,这个属性的代表着当前xmlHttpRequest的状态:
<code>
　0:请求没有发出(在调用open之前)
　1:请求已经建立但还没有发出
  2:请求已经发出
  3:请求正在处理当中
  4:请求已经被服务器处理完毕,相应准备就绪
</code>
　　　

　　

每当readystate状态改变的时候,就会调用onreadystatechange()这个函数,所以我们可以在onreadystatechange进行相应状态的判断以及相应返回数据的获取,当readystate为4时,代表着我们的请求被服务器成功的执行,但我们还需要判断,我们的需要的数据是否成功被返回了呢?我们还有一个status属性,而这个status也有很多的状态码代表着不同的响应状态,其中200代表着响应成功,并且将响应的数据返回到了前端中:

　　

```
xhr.onreadystatechange = function(){
     if(xhr.readystate == 4){//请求已经成功被处理
         if(xhr.status == 200){//成功的从服务器得到了响应
             //这里进行处理返回的数据
        }
    }
}
```
　　得到了响应之后,responseText表示字符串形式的相应数据,responseXML表示获取XML形式的响应数据,getAllResponseHeader():获取所有的响应报头,这里我们来完整的写一个ajax请求:

```
 var xhr =null;
    if(window.XMLHttpRequest){
        xhr = new XMLHttpRequest();
    }else{
        xhr = new ActiveXObject('Microsoft.XMLHTTP');

    }
    var url ="index.php?id=1";
    xhr.open('get',url,'true');//open中有三个参数,第一个参数用来指定使用get还是post方式提交,第二参数是指定要发送的url地址,第三个参数指定是否使用异步,第三个参数默认是true;
    xhr.send();

    xhr.onreadystatechange = function(){
        if(xhr.readystate == 4){//请求已经成功被处理
            if(xhr.status == 200){//成功的从服务器得到了响应
                   alert(responseText);
                }
        }
    }相应的后端会在后边用php实现一个小的demo
```
二,XML和json

　　当我们完成了发送请求并且从后端获取了数据后,我们应该再进一步的去思考,那么我可以指定从后端传过来的数据吗?那么接下来就出现了我们的json和XML数据格式,这两种都是我们进行前端和后端进行传送数据的格式,那么我们先来看一下XML,XML数据其实你也可以看做我们的html标签,只不过它是我们可以自定义的标签,你可以给它的标签名取的很有意义,那样就会很方便你去使用:

　　

```
<china>
    <province name='河南'>
     <city>郑州</city>
    </province>
    
</china>
//这就是一个简单的XML格式的数据,我们对于这样的数据进行操作的时候可以使用js操作DOM对象的方法,
//xml数据必须有一个根节点
```
另外一种经常使用的数据格式就是json了,json是一种独立于语言的数据格式,就是说它是可以在很多种语言中使用的,不限定于某一个特定的语言,而且它相较于xml来说代码量较小,而且易于解析,xml就有些数据量庞大解析不便了,但是碍于json出现的较晚,所以现在大部分人还是使用的xml,无奈与很多后端数据都是用xml存储,json的格式有些类似于我们的JavaScript中的对象字面量:

　　
```
{"name":"james",
  "hobby":"basketball",
    "son" : {"littleson":"er","bigson":"san"}
}//这种就是一个简单的json格式数据,json数据代码量较小,但是可读性来说
还是xml看着比较顺眼,但是人看着不顺眼的代码块恰恰是机器最喜欢的
```
　　得到了数据,我们还需要去解析一下才能够使用,相较于xml有些dom一样的解析方法,xml的解析在js中最长用的解析方法就是json.parse()了,而将js对象转化为json对象我们使用json.stringify(),当然了,json和xml的区别还有很多,这里我献上一个前辈的总结,很详细的一个总结: http://www.cnblogs.com/SanMaoSpace/p/3139186.htm

三,ajax在jQuery中

那么我们之前已经看了ajax在js中的应用,jQuery号称是js的最强悍的一个封装库,怎能没有ajax的封装呢?我们先来简略的看一下jQuery源码中对于ajax的封装:

```
//它的大概位置在七千行左右
jQuery.extend({

    ajax: function( url, options ){}
});
//从这个大概形式中,我们可以看得出来,ajax时封装在jQuery的工具方法中,是在jQuery上直接封装的,所以调用的时候直接使用jQuery这个函数就行,所以我们对于它的调用就是$.ajax();
```
　　那么我们还是写一个简单的post请求方式在jQuery中的应用:

```
 $.ajax({
                    type:"post",  //请求方式
                    url:"a.php", //服务器的链接地址
                    dataType:"json", //传送和接受数据的格式
                    data:{
                        username:"james",
                        password:"123456"
                    },
                    success:function(data){//接受数据成功时调用的函数
                       console.log(data);//data为服务器返回的数据
                    },
                    error:function(request){//请求数据失败时调用的函数
                        alert("发生错误:"+request.status);
                    }
});
```
有了post请求方式的例子,想必get方式的写法大家也不在话下,看着jQuery的例子,是不是感觉很简单呢,很多兼容性的处理jQuery都已经帮我们做好了,就是这个feel.

四,跨域请求

在上边的例子中,我们使用ajax请求的都是在本地和我们同源的文件,因为JavaScript在设计时出于安全方面的考虑,不允许跨域请求,那么什么情况才算是跨域呢?



上边的就是我们的不同源的情况,遇到这种情况,再去使用上边所讲的方式就不行了,那么我们该怎样去解决跨域请求的问题呢?JSONP(JSON with Padding)是 JSON 的一种“使用模式”，可用于解决主流浏览器的数据访问问题,因为html的<script>元素标签可以从其他来源动态的加载数据,所以我们可以利用<script>标签来实现跨域请求,这种方式就成为jsonp,当然了,jsonp和json可不是一回事,json是数据的一种传输格式,而jsonp是一种跨域请求方式,简单的理解就是:

```
<html>
<body>
    <script src='test.js'></script>
</body>
</html>
//script中的src没有同源限制,它可以加载其他任意文件.而jsonp利用的就是这一个功能展开的
```
 对于jsonp这种跨域请求方式,我们首先先来写一个例子,然后再根据这个例子去慢慢的介绍:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>jsonp</title>
</head>
<body>
<script src="http://cdn.weather.hao.360.cn/api_weather_info.php?app=hao360&_jsonp=weather&code=131111"></script>
//这是360天气的一个天气预报的一个api接口,

</body>
</html>
```
当我们运行上边例子后,在chrome浏览器中打开开发者模式->点击network->查看请求包->点击response,这时我们可以查看到:



 

是的这时候远方的数据库中向我们返回了一大串数据,仔细观察的话,不难发现,这一串数据的格式就是weather( 数据  );咦,这不是一个weather函数调用吗?是的,没错,我们的数据库向我们返回的就是一个函数调用,在看一下,我们script中的url地址后边的参数_jsonp=weather,我们将它改为tianqi发现返回的是一个tianqi().哦,原来_jsonp参数是指定服务器返还给我们的函数名,那么函数中的参数就是服务器返还给我们的数据,那么我们也发现此时浏览器已经报错了:



那么也就是说我们的代码中没有weather这个函数,加上上边的讲解,我们这时应该去直接的说一下它的运行机制了,执行完script中的跨域调用后,会直接调用weather函数,所以这时候我们要再代码中写一个为weather函数让script来执行,那么weather函数中的参数就是我们要的数据,我们这时就可以在weather函数中直接使用我们的数据了,这里还要说明一点,jsonp只有get请求方式,也就是传参都要再url后边,而且其中的数据传输格式都为json,

　　

```
<script>
    function weather(data){//回调函数
        console.log(data);
    }
</script>
<script src="http://cdn.weather.hao.360.cn/api_weather_info.php?app=hao360&_jsonp=weather&code=111111"></script>

```
这时我们就可以看到结果:



在这里我们使用jsonp调用数据时,我们需要先去了解一下后台数据的格式,因为我们要去传一些参数进行获取数据,所以前端后端是不分家的,那么我们当天也可以使用动态加载script标签的方法来进行点击按钮获取数据

```
 function createScript(){
        var script = document.createElement('script');
        var url    = "http://cdn.weather.hao.360.cn/api_weather_info.php?app=hao360&_jsonp=weather&code=";
        script.src = url+params;
        document.body.appendChild(script);
    }//使用这个函数就可以动态的加载数据了
```
　　那么下边我们来看一下jQuery中jsonp的使用:

```
   $.ajax({
                type:'get',
                url:天气预报接口,
                async:'true', // 是否为异步调用
                dataType:'jsonp', 指定数据传输方式
                jsonp:'jsoncallback',     //回调函数名的key值 可省略
                jsonpCallback:'XBox',       //回调函数的函数名 可省略
                success:function(data){
                   //成功后执行的函数,data就是我们要获取的函数值
                },
                error  :function(){
                     //失败时执行的函数
               }
```
获得数据我们可以显示在HTML标签中,有了这种异步调用方式,我们就可以去做一些很好玩的东西了,什么天气预报,快递查询,音乐播放器了,都不在话下,当然,跨域调用还有很多种,但我就对jsonp使用的多一点,其他中方法就不提啦,文章中可能会有一些知识点被漏掉,毕竟是自己的一个小总结,希望能对大家有点小帮助.