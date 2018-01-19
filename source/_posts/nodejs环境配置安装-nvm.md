---
title: nodejs环境配置安装(nvm)
date: 2016-8-20 20:18:39
tags: 
	- nodejs
	- javascript
---
{% asset_img nodeanzhuang.jpg This is an example image %}
在我们前端开发工程中,很多繁琐机械的操作都是会慢慢的被抽离出来的,当我们为dom操作和浏览器兼容性感到厌烦时,jQuery出现了,当我们不想再去理会dom的添加删除等的时候,angularJS来解救我们,那么,随着时间的发展,我们前端开发的王牌语言JavaScript,现在被应用到了服务器中,对的,这是一个里程碑式的创新,今后,你不再需要去使用php/.net等后端语言,仅仅使用JavaScript就可以前端后端通吃,这就是web全栈工程师吧!神奇的node.js为我们JavaScript提供了在后端运行
<!--more-->
的环境,而它的包管理器npm(node package manage)现在也成为了全球最大的开源库生态系统.我们有很多很多的工具都是依赖于node环境进行使用的,所以我们使用node的第一步,就是配置node.js环境.

那么配置node.js环境,当然,我们可以直接从官网下载,然后安装到电脑上就行了,当然,这是正确的,但是如果有一天我们需要使用新版本的node.js,或者使用旧版本的node.js时,我们还是需要去官网下载,然后安装,因为node.js的官方对于node.js的版本更新较慢,以至于一些大神等得不耐烦了,便开始自己更新node.js(当然node.js是开源的),你这一更新,导致官方团队坐不住了,那最后的结果就是官方的是稳定版本,当有新功能会在这个版本较高的发布,所以这就是经常会看到

{% asset_img 1.jpg%}

那么我们的解决方法就是安装一个nvm(node version manage)node版本管理控制器,通过 nvm我们可以随意的下载任意版本的node,可以随意切换使用各个版本的node,使用nvm之前,我们先来看一下,我们使用终端打开应用程序的实例:我们使用终端开启一个软件是,必须要cd到这个程序当前所在的文件目录下,
{%asset_img 2.jpg%}

我们可以看到,当前的FlashFXP的链接存在于桌面的上,当我们在桌面中的node.js文件夹中去试图打开它是打不开的,因为我们终端在当前文件夹中找不到FlashFXP的启动程序,所以会报错,那么我们有没有办法去改变这一现象,可以在任何地方,任何文件中启动真个程序呢?当然是可以的,我们只需要配置一下环境变量就可以了,右键我的电脑->选择属性->高级系统设置->环境变量,到了环境变量这,就找对地方了
{%asset_img 3.jpg%}
　　

环境变量是系统中一个特定的对象,当要求系统运行一个程序而且没有告诉系统这个程序的完整路径的时候,系统除了在当前的文件夹下边寻找之外,还会去环境变量中path中指定的路径去寻找,也就是说,如果你将一个文件夹的指定路径保存到了path中,那么每次在终端启动程序时,你根本就不必进去这个程序所在的文件夹也能启动它了,这里我们会看到有系统变量和用户变量,顾名思义,用户变量就是当前登录用户下配置的各种变量,当我们切换用户的时候,这部分的变量是可以变化的,那么这时候我们就需要将想要添加的文件夹添加到用户变量中就行了,

```
FLASH= "C:\Program Files (x86)\//将当前地址复制到一个变量中,
path = %FLASH%;//将这个变量赋值到path中,这时候在任何地方我们都可以打开这个程序了
```

好了有了上边的铺垫之后,我们就可以来安装nvm了,我们可以使用git直接下载,当然也可以直接去github下载,这里提供一个下载地址,使用手动下载:
```
https://github.com/coreybutler/nvm-windows/releases
```
{%asset_img 4.jpg%}

下载图标中的nvm就行,下载完成后解压,然后安装nvm,安装目录地址注意不要有中文,不然指不定会出现什么错误,我这里直接将它安装在了D:\dev下边,安装完成后一般会去自动给我们配置好系统变量,如果没有,我们需要手动去配置系统变量,原理及方法和我们一开始所讲的一样:
```
NVM_HOME ：指向nvm安装目录
NVM_SYMLINK：nodejs安装目录
Path ：变量值中增加 NVM_HOME 和 NVM_SYMLINK两个环境变量
```
这时我们可以在终端中测试一下:
```
$ nvm -v

Running version 1.1.0.

Usage:

nvm arch : Show if node is running in 32 or 64 bit mode.
nvm install <version> [arch] : The version can be a node.js version or "latest" for the latest stable version.
Optionally specify whether to install the 32 or 64 bit version (defaults to system arch).
Set [arch] to "all" to install 32 AND 64 bit versions.
nvm list [available] : List the node.js installations. Type "available" at the end to see what can be installed. Aliased as ls.
nvm on : Enable node.js version management.
nvm off : Disable node.js version management.
nvm proxy [url] : Set a proxy to use for downloads. Leave [url] blank to see the current proxy.
Set [url] to "none" to remove the proxy.
nvm uninstall <version> : The version must be a specific version.
nvm use [version] [arch] : Switch to use the specified version. Optionally specify 32/64bit architecture.
nvm use <arch> will continue using the selected version, but switch to 32/64 bit mode.
nvm root [path] : Set the directory where nvm should store different versions of node.js.
If <path> is not set, the current root will be displayed.
nvm version : Displays the current running version of nvm for Windows. Aliased as v.

 //这时就表示nvm安装成功了
```
这时候我们就可以安装node了,在终端输入命令：nvm ls-remote,用来查看远端的node版本,当然，你可能看不到结果，此时，你就需要输入另一个命令：export NVM_NODEJS_ORG_MIRROR=http:nodejs.org/dist,然后再输入nvm ls -remote,如果还不行,那就去下边这个地址去查看:
```
http

s://github.com/coreybutler/nodedistro/blob/master/nodeversions.json
```
这时候使用命令nvm install [node版本号]
---

等到进度条走完,输入命令 nvm list 来查看当前安装的node 
{%asset_img 5.jpg%}


可以看到当前安装的有4.3.1版本,那么我么使用 nvm use 4.3.1就可以指定当前使用的node版本为4.3.1,4.3.2后边也会出现(当前使用的标志),当然,我们可以再去下载一个node版本,例如:
{%asset_img 6.jpg%}


等到进度条走完,这是我们就有了5.7.0和4.3.1两个版本了:这时我们可定是想要去在两个版本之间自如的去切换的,这是我们还需要配置一个文件,就是nvm 中的setting文件:

{%asset_img 7.jpg%}

　　打开文件进行配置

     

　　如上,根据自己的文件目录进行配置,root表示的是当前nvm中内容的地址,path则是需要写入环境变量中的地址path中,会在当天dev/ 文件下生成一个nodejs快捷方式,来作为指向当前使用的node版本的快捷方式,如图所示,

　　当我们配置好了之后,就可以进行node版本的切换了:

　　

　　这是我们会发现dev文件中生成了一个nodejs的快捷方式,点击这个快捷方式,就可以调转到当前使用版本的nodejs中,当然,安装好了nodejs之后,node还有一个npm(node package manage),这个工具,在新版本的node中已经集成了进来,所以我们可以直接使用了,通过npm我们不仅可以下载node的各种包,我们还可以下载各种在npm生态网上的包,例如jQuery,angularJS,bootstrap,等等很多包,还包括gulp,bower,grunt等等工具,这个我们下节再去讨论,本文 如有错误,还请指正,谢谢!