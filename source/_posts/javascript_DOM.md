title: javascript html DOM
date: 2015-12-14 12:30:35
tags:
 - javascript
categories: [web编程]

---

# 说明
ECMAscript :解释器
DOM：document object model ，用来操作HTML文件的，例如：document.getElementById（document）
BOM：brower object model ，用来操作浏览器的，例如：关闭窗口，复制内容到剪贴板（window）

通过 HTML DOM，可访问 JavaScript HTML 文档的所有元素。
当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。
<!--more -->

# 查找html元素
通过 id 查找 HTML 元素
`var x=document.getElementById("intro");`

通过标签名查找 HTML 元素
本例查找 id="main" 的元素，然后查找 id="main" 元素中的所有 <p> 元素：
```
var x=document.getElementById("main");
var y=x.getElementsByTagName("p");
```
 
通过类名找到 HTML 元素
`var x=document.getElementsByClassName("intro");`

# 改变 HTML
改变 HTML输出流
在 JavaScript 中，document.write() 可用于直接向 HTML 输出流写内容。

改变 HTML 内容
`document.getElementById("p1").innerHTML="New text!";`

改变 HTML 属性
`document.getElementById("image").src="landscape.jpg";`

# 改变CSS
改变 HTML样式
document.getElementById(id).style.property=new style

改变了 id="id1" 的 HTML 元素的样式，当用户点击按钮时：
```
<!DOCTYPE html>
<html>
<body>

<h1 id="id1">My Heading 1</h1>
<button type="button" 
onclick="document.getElementById('id1').style.color='red'">
Click Me!</button>

</body>
</html>
```
# DOM 事件
我们可以在事件发生时执行 JavaScript.
HTML 事件的例子：
当用户点击鼠标时
当网页已加载时
当图像已加载时
当鼠标移动到元素上时
当输入字段被改变时
当提交 HTML 表单时
当用户触发按键时

当用户在 <h1> 元素上点击时，会改变其内容：
```
<!DOCTYPE html>
<html>
<head>
<script>
function changetext(id)
{
id.innerHTML="Ooops!";
}
</script>
</head>
<body>
<h1 onclick="changetext(this)">Click on this text!</h1>
</body>
</html>
```
其他常事件：
onload 和 onunload 事件会在用户进入或离开页面时被触发。
onload 事件可用于检测访问者的浏览器类型和浏览器版本，并基于这些信息来加载网页的正确版本。
onload 和 onunload 事件可用于处理 cookie。

onchange 事件常结合对输入字段的验证来使用。
`<input type="text" id="fname" onchange="upperCase()">`

onmouseover 和 onmouseout 事件可用于在用户的鼠标移至 HTML 元素上方或移出元素时触发函数。

onmousedown, onmouseup 以及 onclick 构成了鼠标点击事件的所有部分。首先当点击鼠标按钮时，会触发 onmousedown 事件，当释放鼠标按钮时，会触发 onmouseup 事件，最后，当完成鼠标点击时，会触发 onclick 事件。

# DOM EventListener
例如：
document.getElementById("myBtn").addEventListener("click", displayDate);

addEventListener() 方法用于向指定元素添加事件句柄。
addEventListener() 方法添加的事件句柄不会覆盖已存在的事件句柄。

语法
element.addEventListener(event, function, useCapture);
第一个参数是事件的类型 (如 "click" 或 "mousedown").
第二个参数是事件触发后调用的函数。
第三个参数是个布尔值用于描述事件是冒泡还是捕获。该参数是可选的。默认值为 false, 即冒泡传递，当值为 true 时, 事件使用捕获传递。

```
element.addEventListener("click", myFunction);

function myFunction() {
    alert ("Hello World!");
}
```

事件传递有两种方式：冒泡与捕获。
事件传递定义了元素事件触发的顺序。 如果你将 <p> 元素插入到 <div> 元素中，用户点击 <p> 元素, 哪个元素的 "click" 事件先被触发呢？
在 冒泡 中，内部元素的事件会先被触发，然后再触发外部元素，即： <p> 元素的点击事件先触发，然后会触发 <div> 元素的点击事件。
在 捕获 中，外部元素的事件会先被触发，然后才会触发内部元素的事件，即： <div> 元素的点击事件先触发 ，然后再触发 <p> 元素的点击事件。


# HTML DOM 元素(节点)
创建节点
```
<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>

<script>
//创建新的<p> 元素：
var para=document.createElement("p"); 
//如需向 <p> 元素添加文本，您必须首先创建文本节点。这段代码创建了一个文本节点：
var node=document.createTextNode("This is new.");
//必须向 <p> 元素追加这个文本节点：
para.appendChild(node);

//找到一个已有的元素：
var element=document.getElementById("div1");
//在已存在的元素后添加新元素：
element.appendChild(para);
</script>

```

 