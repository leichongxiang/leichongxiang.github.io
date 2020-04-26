title: javascript html BOM
date: 2015-12-14 12:30:35
tags:
 - javascript
categories: [web编程]

---
浏览器对象模型（Browser Object Model (BOM)）
所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。
相同的
`window.document.getElementById("header"); ===>document.getElementById("header");`

<!--more -->

# Window 尺寸
```
var w=window.innerWidth
|| document.documentElement.clientWidth
|| document.body.clientWidth;

var h=window.innerHeight
|| document.documentElement.clientHeight
|| document.body.clientHeight;

```

# Window Screen
screen.availWidth - 可用的屏幕宽度
screen.availHeight - 可用的屏幕高度

# Window Location
location.hostname 返回 web 主机的域名
location.pathname 返回当前页面的路径和文件名
location.port 	  返回 web 主机的端口 （80 或 443）
location.protocol 返回所使用的 web 协议（http:// 或 https://）

location.href 属性返回当前页面的 URL。
location.assign() 方法加载新的文档

# Window History
history.back() - 与在浏览器点击后退按钮相同
history.forward() - 与在浏览器中点击按钮向前相同

# Window Navigator
window.navigator 对象在编写时可不使用 window 这个前缀。
```
<div id="example"></div>

<script>

txt = "<p>Browser CodeName: " + navigator.appCodeName + "</p>";
txt+= "<p>Browser Name: " + navigator.appName + "</p>";
txt+= "<p>Browser Version: " + navigator.appVersion + "</p>";
txt+= "<p>Cookies Enabled: " + navigator.cookieEnabled + "</p>";
txt+= "<p>Platform: " + navigator.platform + "</p>";
txt+= "<p>User-agent header: " + navigator.userAgent + "</p>";
txt+= "<p>User-agent language: " + navigator.systemLanguage + "</p>";

document.getElementById("example").innerHTML=txt;

</script>
```

# JavaScript 弹窗
可以在 JavaScript 中创建三种消息框：警告框、确认框、提示框

警告框
window.alert("sometext");
window.alert() 方法可以不带上window对象，直接使用alert()方法。

确认框
确认框通常用于验证是否接受用户操作。
当确认卡弹出时，用户可以点击 "确认" 或者 "取消" 来确定用户操作。
当你点击 "确认", 确认框返回 true， 如果点击 "取消", 确认框返回 false。
语法
window.confirm("sometext");
window.confirm() 方法可以不带上window对象，直接使用confirm()方法。

提示框

window.prompt("sometext","defaultvalue");
window.prompt() 方法可以不带上window对象，直接使用prompt()方法。

换行
弹窗使用 反斜杠 + "n"(\n) 来设置换行。
alert("Hellon\nHow are you?");

# JavaScript 计时事件
setInterval() - 间隔指定的毫秒数不停地执行指定的代码。
setTimeout()  - 暂停指定的毫秒数后执行指定的代码
clearInterval() 方法用于停止 setInterval() 方法执行的函数代码。
clearTimeout() 方法用于停止执行setTimeout()方法的函数代码。

setInterval(function(){alert("Hello")},3000);

# Cookies
Cookies 是一些数据, 存储于你电脑上的文本文件中。
当 web 服务器向浏览器发送 web 页面时，在连接关闭后，服务端不会记录用户的信息。
Cookies 的作用就是用于解决 "如何记录客户端的用户信息":
当用户访问 web 页面时，他的名字可以记录在 cookie 中。
在用户下一次访问该页面时，可以在 cookie 中读取用户访问记录。

# JavaScript 库
JavaScript 库 - jQuery、Prototype、MooTools。其实这就是3中框架。

引用jQuery
```
<script src="http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js">
</script>
```

实例

JavaScript 方式：
```
function myFunction()
{
var obj=document.getElementById("h01");
obj.innerHTML="Hello jQuery";
}
onload=myFunction;
```

jQuery 方式：
```
<!DOCTYPE html>
<html>
<head>
<script src="http://apps.bdimg.com/libs/jquery/2.1.1/jquery.min.js">
</script>
<script>
function myFunction()
{
$("#h01").html("Hello jQuery")
}
$(document).ready(myFunction);
</script>
</head>
<body>
<h1 id="h01"></h1>
</body>
</html>
```
说明：
主要的 jQuery 函数是 $() 函数（jQuery 函数）。如果您向该函数传递 DOM 对象，它会返回 jQuery 对象，带有向其添加的 jQuery 功能。
jQuery 允许您通过 CSS 选择器来选取元素。

HTML DOM 文档对象被传递到 jQuery ：$(document)。
当您向 jQuery 传递 DOM 对象时，jQuery 会返回以 HTML DOM 对象包装的 jQuery 对象。
jQuery 函数会返回新的 jQuery 对象，其中的 ready() 是一个方法。
由于在 JavaScript 中函数就是变量，因此可以把 myFunction 作为变量传递给 jQuery 的 ready 方法。

接下来将需要学习jQuery框架了。