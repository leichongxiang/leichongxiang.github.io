title: javascript的基础知识
date: 2015-12-10 13:53:35
tags:
 - javascript 
categories: [web编程]

---
# JavaScript 简介
说到网页制作，谈到JavaScript，就不得不说HTML和CSS。三者之间有什么关系呢，下图很好的诠释了三者在网页中的功能和关系。HTML是网页内容的主体，承载着所有要显示的内容；而CSS主要是控制内容的展示方式和风格；至于JavaScript就是网页的行为和交互.即就是：用户交互和数据处理。

# 语法结构

## 简单说明
HTML 中的脚本必须位于 <script> 与 </script> 标签之间。
脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。
外部的javascript,`<script src="myScript.js"></script>`
html中多个空格只会当做一个空格
JavaScript 注释// ;多行注释以 /* 开始，以 */ 结尾。

## 字面量
数字（Number）字面量 可以是整数或者是小数，或者是科学计数(e)。
```
3.14
1001
123e5
```
字符串（String）字面量 可以使用单引号或双引号
```
"John Doe"
'John Doe'
```
表达式字面量 用于计算：
数组（Array）字面量 定义一个数组：
`[40, 100, 1, 5, 25, 10]`
对象（Object）字面量 定义一个对象：
`{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}`
函数（Function）字面量 定义一个函数：
```
function myFunction(a, b) {
   	return a * b;                                // 返回 a 乘于 b 的结果
}
```

## 变量
Javascript程序采用Unicode字符集，区分大小写
Javascript的标识符必须以$、字母或_开头
语句用分号来结束；
```
var a = "hello";
document.write(a);
```
`注意Javascript中数值不区分整数和浮点数`

## JavaScript 数据类型
* 字符串（String）
* 数字(Number)
* 布尔(Boolean)
* 数组(Array)
* 对象(Object)
* 空（Null）
* 未定义(Undefined)

注意：
Undefined 和 Null
Undefined 这个值表示变量不含有值。
可以通过将变量的值设置为 null 来清空变量。

对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (name : value) 来定义。属性由逗号分隔：
`
var person={
firstname : "John",
lastname  : "Doe",
id        :  5566
};
`
可以使用以下语法创建对象方法：
methodName : function() { code lines }
访问对象方法：
objectName.methodName()
访问属性：
objectName.firstname

# 输出
## 操作html元素
```
document.getElementById("demo") 是使用 id 属性来查找 HTML 元素的 JavaScript 代码 。
innerHTML = "Paragraph changed." 是用于修改元素的 HTML 内容(innerHTML)的 JavaScript 代码。
```

## 写到 HTML 文档
使用 document.write() 仅仅向文档输出写内容。
```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
    <script>
		document.write("Hello World！");
	</script>
</head>
<body>

</body>
</html>

```
## 控制台
使用 console.log() 方法在浏览器中显示 JavaScript 值
```
<script>
a = 5;
b = 6;
c = a + b;
console.log(c);
</script>
```

# javascript事件
HTML 事件是发生在 HTML 元素上的事情。
当在 HTML 页面中使用 JavaScript 时， JavaScript 可以触发这些事件。

举例html事件：
- HTML 页面完成加载
- HTML input 字段改变时
- HTML 按钮被点击
在事件触发时 JavaScript 可以执行一些代码

例如：
```
<button onclick='getElementById("demo").innerHTML=Date()'>The time is?</button>
```
常见的HTML事件
onchange	HTML 元素改变
onclick		用户点击 HTML 元素
onmouseover	用户在一个HTML元素上移动鼠标
onmouseout	用户从一个HTML元素上移开鼠标
onkeydown	用户按下键盘按键
onload		浏览器已完成页面的加载


## 字符串
+运算符用于把文本值或字符串变量加起来（连接起来）。
```
x=5+5;
y="5"+5;
z="Hello"+5;

结果
10
55
Hello5

```
## 比较运算符
其他的>,<,!=,==,<=,>=都是一样的

注意：
===	绝对等于（值和类型均相等）
!==	 绝对不等于（值或类型不相等）


## 流程控制
### 条件语句
* if 语句 						- 只有当指定条件为 true 时，使用该语句来执行代码
* if...else 语句 				- 当条件为 true 时执行代码，当条件为 false 时执行其他代码
* if...else if....else 语句		- 使用该语句来选择多个代码块之一来执行

### switch 语句
* switch 语句 					- 使用该语句来选择多个代码块之一来执行
```
switch(n)
{
case 1:
  执行代码块 1
break;
case 2:
  执行代码块 2
break;
default:
 n 与 case 1 和 case 2 不同时执行的代码
}
```
### for 语句
JavaScript 支持不同类型的循环：
for - 循环代码块一定的次数
for/in - 循环遍历对象的属性

```
for (语句 1; 语句 2; 语句 3)
  {
  被执行的代码块
  }
```
for /in的实例：
```
var person={fname:"John",lname:"Doe",age:25}; 

for (x in person)
  {
  txt=txt + person[x];
  }
```
### while 语句
```
//格式一：
while (条件)
  {
  需要执行的代码
  }
//格式二：  
do
  {
  需要执行的代码
  }
while (条件);  
```
### 中断循环
break 语句跳出循环后，会继续执行该循环之后的代码（如果有的话）：
continue 语句中断循环中的迭代，如果出现了指定的条件，然后继续循环中的下一个迭代。
JavaScript 标签：
```
label:
statements
```

break labelname; 
continue labelname;

continue 语句（带有或不带标签引用）只能用在循环中。
break 语句（不带标签引用），只能用在循环或 switch 中。
通过标签引用，break 语句可用于跳出任何 JavaScript 代码块：
