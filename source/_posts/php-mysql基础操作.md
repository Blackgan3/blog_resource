---
title: php+mysql基础操作
date: 2016-6-30 20:58:00
tags: 
	- php
	- mysql
---
　　PHP可以说是当下很火的一门后端语言了,它小巧玲珑,和html等前端语言配合的可以说是天衣无缝,加之xampp,wampp等工具的出现,更是前端开发者的福音,作为一名前端ER,如果不熟悉一门后端语言的话,那简直都不好意思说自己要加薪,那么我们对于php这门语言学习时,如果我们对js或者其他任何一门语言学的好的话,再去学习一门新的语言那是很好上手的,<!--more-->无非就是先从变量,常量,执行语句,循环条件判断,数组,函数,面向对象,加上这门语言的一些高级函数,什么date,画图,浏览器信息存储函数啦,逐个的去看去对比,真的很顺手有没有,那么我们今天就来看一下我们前端经常用到的一些php+mysql操作,这需要我们有一定的mysql基础了.

　　当我们建好了一个表的时候,我们很自然的就会去想到,我们要怎么去查找信息,增加信息,修改信息,删除信息,那么这就是我们最基础的操纵了,在操作这个数据表之前,我们需要先找到它 代码1:
```
 1 //这里我们使用mysql_connect来进行数据库的链接,它一共有三个参数,分别是,数据库地址,用户名,用户密码,对于mysql的配置,我们可以使用shell命令行或者myphpAdmin进行界面配置
 2 $con = mysql_connect('localhost','root','123');
 3 if($con){
 4     echo "连接数据库成功";
 5 }
 6 else{
 7     echo mysql_error();
 8     echo "连接数据库失败";
 9 }
10 //当我们连接到了整个数据库后,我们需要去找到库中我们的小仓库,这里使用函数mysql_select_db('仓库名');
11 if(mysql_select_db('mysql')){
12     echo mysql_error();
13 }
14 
15 //设置转码字符
16 if(mysql_query('set names utf8')){
17 
18 }else{
19     echo mysql_error();
20 }
```
　　需要说明的是,在php5之后,对于MySQL数据库操作的函数开头变为了mysqli,这两者的区别也是有的,mysqli的功能更强大,增加了面向对象功能,但它也是向下兼容的,在用法上二者有细微的差别,这里我们的实例都是使用的mysql开头的函数,当我们成功连接到数据库后,我们先来进行插入数据 代码2:

　　

首先我们先建立一个表单,指定提交方式为post,当提交时执行a.php文件
```
<?php 
    //此处执行代码1进行数据库连接功能,连接到数据库后
    $username = $_POST['username'];//从超级全局变量中取到表单提交过来的值
    $password  = $_POST['password'];
    //这里就是我们的数据插入语句,其中uers是代码1中小仓库mysql总的一个表,表名为users;
    $sql = "inset into users (username,password)values('$username','password')";
    mysql_query($sql);//使用mysql_query()函数来执行sql语句来达到操作数据库,它就像是一个终端,可以执行我们输入的mysql操作语句,
     
    
?>
```
　　对于代码2,首先是建立一个提交表单,每一输入框中设定它的name属性,每一个name属性就是超级全局变量数组$_POST中的key值,所以在php中我们可以获得提交的值,在插入语句的书写时,因为我们在php中的书写方式是以字符串的形式来书写的,所以不会有编译器的提示功能,就算写错 编译器也不会立马提醒你,所以在这块一定要多注意,特别是双引号,单引号嵌套的问题,当我们插入了一条语句后,那你说姓名数错了,我想要改一些,那么php当然可以改,那么在修改的时候我们就要注意了,修改不像插入一样,直接插入到了表尾,这是计算机就会问了,我要修改哪一个呢 代码3:
```
 1 //还是原来的表单,和原来不一样的配方,
 2 //在数据库中,我们给每一行记录都加一个ID,这个字段称为主键,我们将它设置为自增的.
 3 //我们在点击修改按钮时将id通过get方法传送过来,这是就可以通过id获得数据条了,
 4 //获得传过来的id值
 5 $id = $_GET['id'];
 6 //执行mysql语句,返回选择中的资源
 7 $query = mysql_query("select * from article where id=$id");
 8 //通过mysql_fetch_assoc函数来将数据转换为一个关联数组,还有几个作用类似的函数,我们在代码5中介绍,
 9 $data = mysql_fetch_assoc($query);
10 <form action='b.php' method='post'>
11     <input type = 'text' name='username' value="<?php echo $data['username']?>"//将要修改的信息输出到修改框中
12     <input type = 'text' name='password' vlaue="<?php echo $data['password'?>"
13     <input type = 'submit' value='提交修改' >
14 </form>
```
　　好了,上边的代码,只是我们修改数据的一个准备工作,我们在修改前,需要将要修改的数据输出到修改界面中,这样用户可以直观的看到他修改了数据,不能将修改数据搞的像输入信息一样,那样体验性从何而来,代码4:
```
//这里就和我们插入信息时情况差不多了,
<?php
//首先执行代码1连接数据库
$id = $_POST['id'];
$username=$_POST['username'];
$password =$_POST['password'];//获取表单修改后的值
//执行修改语句update
$updatesql = "update article set 
username='$username',password='$password'where id=$id";
if(mysql_query($updatesql)){
    echo "<script>alert('修改文章成功');</script>";//修改成功
}else{
    echo "<script>alert('修改文章失败');</script>";
}
?>
```

　　修改成功了我们的信息后,我们就来看一下php中常用的那些用来获取数据记录的函数:比如mysql_fetch_array,mqsql_fetch_assoc,mysql_fetch_row等等;代码5:

```                             
   //$res是获取到的资源
    $row1= mysql_fetch_row($res);//返回了查询到的数据的第一条
    mysql_fetch_row每执行一次,都从资源也就是结果集中依次取一条数据,以数组形式返回出来,如果当前已经取到最后一条数据时,返回空结果
   $row2 = mysql_fetch_assoc($res);作用是返回一个关联数组,数组的下标就是我们在数据表中的字段名,这种方式我们可以随意的对每个字段进行操作.
   $row3 = mysql_fetch_object($res);//返回一个对象.
   $row4 = mysql_fetch_array($res);//mysql_fetch_array的第二个参数是一个关联数组: 也就是说返回的数据中有一个索引数组有一个关联数组,可以在其中传入第二个参数指定输出哪一个数组,MYSQL_ASSOC只输出关联数组
MYSQL_NUM:只输出索引数组,输出结果和mysql_fetch_row一样,但是它默认是输出两个数组.
```
　　看完了我们的插入和修改信息,相信对于删除信息也是很简单的了,道理都是一样的,获取到数据条的id,然后依据id去进行删除:代码6:
```
$id = $_GET['id'];
$deletesql = "delete from article where id=$id";
if(mysql_query($deletesql)){
    echo "<script>alert('删除文章成功');</script>";

}else{
    echo "<script>alert('删除文章失败');</script>";

}
```
　　那么我们在实际应用中最多的另外一种情况就是查找信息了,我们可以用在数据库中信息查重,也可以用在用户登录信息的判断,数据库中的信息统计等:
```
//执行代码1进行数据库的连接
//接收到表单提交过来的信息,
$username = $_POST['username'];
$password = $_POST['password'];
//在这里,我们使用select语句去选择数据表中和username和password都相等的数据条,如果有,就证明我们输入的信息正确,能够登陆,否则,输入的信息不正确,
$sql = "SELECT * FROM users WHERE username='$username' and password='$password'";
$res = mysqli_query($conn,$sql);
$row = mysqli_num_rows($res);
$self= mysqli_fetch_assoc($res);
if($row){
    echo "<script> alert('恭喜你,登录成功!');  
    </script>";
    header('location:../main.php?id='.$self['id']);

}else{
    echo "<script> alert('用户名或密码输入错误,请重新登录'); window.location.href='../login.html';</script>";
}
```
　　当然,文中的代码对于所获取到的值都没有进行是否为空的判断,和格式是否正确,这都是要加上去的,代码的完善性还不高,到这里,php对于mysql的基本操作算是介绍了个大概,一起加油!

 