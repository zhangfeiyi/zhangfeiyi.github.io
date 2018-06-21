---
layout: post
title:  "JavaScript 语言精粹笔记1-语法、对象、函数"
categories: JavaScript
tags:  对象 函数 this 闭包 面向对象 作用域
---


设置cookie
每个cookie都是一个名/值对，可以把下面这样一个字符串赋值给document.cookie：

document.cookie="userId=828";

 

如果要一次存储多个名/值对，可以使用分号加空格(;)隔开，例如：

document.cookie="userId=828;userName=hulk";

 

在cookie的名或值中不能使用分号(;)、逗号(,)、等号(=)以及空格。在cookie的名中做到这点很容易，但要保存的值是不确定的。如何来存储这些值呢?方法是用escape()函数进行编码，它能将一些特殊符号使用十六进制表示，例如空格将会编码为“20%”，从而可以存储于cookie值中，而且使用此种方案还可以避免中文乱码的出现。例如：

document.cookie="str="+escape("Ilove ajax");

 

相当于：

document.cookie="str=I%20love%20ajax";

 

当使用escape()编码后，在取出值以后需要使用unescape()进行解码才能得到原来的cookie值，这在前面已经介绍过。

 

尽管document.cookie看上去就像一个属性，可以赋不同的值。但它和一般的属性不一样，改变它的赋值并不意味着丢失原来的值，例如连续执行下面两条语句：

document.cookie="userId=828";

 

document.cookie="userName=hulk";

 

这时浏览器将维护两个cookie，分别是userId和userName，因此给document.cookie赋值更像执行类似这样的语句：

document.addcookie("userId=828");

 

document.addcookie("userName=hulk");

 

事实上，浏览器就是按照这样的方式来设置cookie的，如果要改变一个cookie的值，只需重新赋值，例如：

document.cookie="userId=929";

 

这样就将名为userId的cookie值设置为了929。

 

获取cookie的值

 

下面介绍如何获取cookie的值。cookie的值可以由document.cookie直接获得：

varstrcookie=document.cookie;

 

这将获得以分号隔开的多个名/值对所组成的字符串，这些名/值对包括了该域名下的所有cookie。例如：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

document.cookie="userId=828";

document.cookie="userName=hulk";

varstrcookie=document.cookie;

alert(strcookie);

//-->

</script>

 

图7.1显示了输出的cookie值。由此可见，只能够一次获取所有的cookie值，而不能指定cookie名称来获得指定的值，这正是处理cookie值最麻烦的一部分。用户必须自己分析这个字符串，来获取指定的cookie值，例如，要获取userId的值，可以这样实现：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

//设置两个cookie

document.cookie="userId=828";

document.cookie="userName=hulk";

//获取cookie字符串

varstrcookie=document.cookie;

//将多cookie切割为多个名/值对

vararrcookie=strcookie.split("; ");

varuserId;

//遍历cookie数组，处理每个cookie对

for(vari=0;i<arrcookie.length;i++){

vararr=arrcookie[i].split("=");

//找到名称为userId的cookie，并返回它的值

if("userId"==arr[0]){

userId=arr[1];

break;

}

}

alert(userId);

//-->

</script>

 

这样就得到了单个cookie的值

 

用类似的方法，可以获取一个或多个cookie的值，其主要的技巧仍然是字符串和数组的相关操作。

 

给cookie设置终止日期

 

到现在为止，所有的cookie都是单会话cookie，即浏览器关闭后这些cookie将会丢失，事实上这些cookie仅仅是存储在内存中，而没有建立相应的硬盘文件。

 

在实际开发中，cookie常常需要长期保存，例如保存用户登录的状态。这可以用下面的选项来实现：

 

document.cookie="userId=828;expires=GMT_String";

 

其中GMT_String是以GMT格式表示的时间字符串，这条语句就是将userId这个cookie设置为GMT_String表示的过期时间，超过这个时间，cookie将消失，不可访问。例如：如果要将cookie设置为10天后过期，可以这样实现：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

//获取当前时间

vardate=new Date();

varexpireDays=10;

//将date设置为10天以后的时间

date.setTime(date.getTime()+expireDays*24*3600*1000);

//将userId和userName两个cookie设置为10天后过期

document.cookie="userId=828;userName=hulk; expire="+date.toGMTString();

//-->

</script>

 

删除cookie

 

为了删除一个cookie，可以将其过期时间设定为一个过去的时间，例如：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

//获取当前时间

vardate=new Date();

//将date设置为过去的时间

date.setTime(date.getTime()-10000);

//将userId这个cookie删除

document.cookie="userId=828;expire="+date.toGMTString();

//-->

</script>

 

指定可访问cookie的路径

 

默认情况下，如果在某个页面创建了一个cookie，那么该页面所在目录中的其他页面也可以访问该cookie。如果这个目录下还有子目录，则在子目录中也可以访问。例如在www.xxxx.com/html/a.html中所创建的cookie，可以被www.xxxx.com/html /b.html或www.xxx.com/ html/ some/c.html所访问，但不能被www.xxxx.com/d.html访问。

 

为了控制cookie可以访问的目录，需要使用path参数设置cookie，语法如下：

document.cookie="name=value;path=cookieDir";

 

其中cookieDir表示可访问cookie的目录。例如:

document.cookie="userId=320;path=/shop";

 

就表示当前cookie仅能在shop目录下使用。

 

如果要使cookie在整个网站下可用，可以将cookie_dir指定为根目录，例如：

 

document.cookie="userId=320;path=/";

 

指定可访问cookie的主机名

 

和路径类似，主机名是指同一个域下的不同主机，例如：www.google.com和gmail.google.com就是两个不同的主机名。默认情况下，一个主机中创建的cookie在另一个主机下是不能被访问的，但可以通过domain参数来实现对其的控制，其语法格式为：

 

document.cookie="name=value;domain=cookieDomain";

 

以google为例，要实现跨主机访问，可以写为：

 

document.cookie="name=value;domain=.google.com";

 

这样，所有google.com下的主机都可以访问该cookie。

 

综合示例：构造通用的cookie处理函数

 

cookie的处理过程比较复杂，并具有一定的相似性。因此可以定义几个函数来完成cookie的通用操作，从而实现代码的复用。下面列出了常用的cookie操作及其函数实现。

 

1.添加一个cookie：addcookie(name,value,expireHours)

 

该函数接收3个参数：cookie名称，cookie值，以及在多少小时后过期。这里约定expireHours为0时不设定过期时间，即当浏览器关闭时cookie自动消失。该函数实现如下：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

functionaddcookie(name,value,expireHours){

varcookieString=name+"="+escape(value);

//判断是否设置过期时间

if(expireHours>0){

vardate=new Date();

date.setTime(date.getTime+expireHours*3600*1000);

cookieString=cookieString+";expire="+date.toGMTString();

}

document.cookie=cookieString;

}

//-->

</script>

 

2.获取指定名称的cookie值：getcookie(name)

 

该函数返回名称为name的cookie值，如果不存在则返回空，其实现如下：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

functiongetcookie(name){

varstrcookie=document.cookie;

vararrcookie=strcookie.split("; ");

for(vari=0;i<arrcookie.length;i++){

vararr=arrcookie[i].split("=");

if(arr[0]==name)returnarr[1];

}

return"";

}

//-->

</script>

3.删除指定名称的cookie：deletecookie(name)

 

该函数可以删除指定名称的cookie，其实现如下：

<scriptlanguage="JavaScript" type="text/JavaScript">

<!--

functiongetcookie(name){

varstrcookie=document.cookie;

vararrcookie=strcookie.split("; ");

for(vari=0;i<arrcookie.length;i++){

vararr=arrcookie[i].split("=");

if(arr[0]==name)returnarr[1];

}

return"";

}

//-->

</script>

也可以用另一种网上流传的:

<scriptlanguage="JavaScript" type="text/JavaScript">

 

functionSetCookie(name,value)//两个参数，一个是cookie的名子，一个是值

{

varDays = 30; //此 cookie 将被保存 30 天

varexp = new Date(); //new Date("December 31, 9998");

exp.setTime(exp.getTime()+ Days*24*60*60*1000);

document.cookie= name + "="+ escape (value) + ";expires=" +exp.toGMTString();

}

functiongetCookie(name)//取cookies函数

{

vararr = document.cookie.match(new RegExp("(^|)"+name+"=([^;]*)(;|$)"));

if(arr!= null) return unescape(arr[2]); return null;

 

}

functiondelCookie(name)//删除cookie

{

varexp = new Date();

exp.setTime(exp.getTime()- 1);

varcval=getCookie(name);

if(cval!=null)document.cookie= name +"="+cval+";expires="+exp.toGMTString();

}

 

SetCookie("xiaoqi", "3")

alert(getCookie('xiaoqi'));

</script>
