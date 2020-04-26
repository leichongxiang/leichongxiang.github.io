title: javascript小知识
date: 2015-12-11 17:53:35
tags:
 - javascript 
categories: [web编程]

---

# typeof 操作符
你可以使用 typeof 操作符来检测变量的数据类型。
```
typeof "John"                // 返回 string 
typeof 3.14                  // 返回 number
typeof false                 // 返回 boolean
typeof [1,2,3,4]             // 返回 object
typeof {name:'John', age:34} // 返回 object
typeof NaN                   // 返回 number
typeof new Date()            // 返回 object
日期(Date)的数据类型为 object
```
在JavaScript中，数组是一种特殊的对象类型。
<!--more -->

# null和Undefined
null是一个只有一个值的特殊类型。表示一个空对象引用。null 返回是object。
typeof 一个没有值的变量会返回 undefined。
var person;                  // Value is undefined, type is undefined

Undefined 和 Null 的区别

typeof undefined             // undefined
typeof null                  // object
null === undefined           // false
null == undefined            // true

# 类型转换
## 将字符串转换为数字
全局方法 Number() 可以将字符串转换为数字。
```
Number("3.14")    // 返回 3.14
Number(" ")       // 返回 0 
Number("")        // 返回 0
Number("99 88")   // 返回 NaN
```

## 将日期转换为字符串
String(Date())      // 返回 Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)
Date().toString()   // 返回 Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)

Date()方法：
getDate()	从 Date 对象返回一个月中的某一天 (1 ~ 31)。
getDay()	从 Date 对象返回一周中的某一天 (0 ~ 6)。
getFullYear()	从 Date 对象以四位数字返回年份。
getHours()	返回 Date 对象的小时 (0 ~ 23)。
getMilliseconds()	返回 Date 对象的毫秒(0 ~ 999)。
getMinutes()	返回 Date 对象的分钟 (0 ~ 59)。
getMonth()	从 Date 对象返回月份 (0 ~ 11)。
getSeconds()	返回 Date 对象的秒数 (0 ~ 59)。
getTime()	返回 1970 年 1 月 1 日至今的毫秒数。

# 正则表达式
搜索模式可用于文本搜索和文本替换。
在 JavaScript 中，正则表达式通常用于两个字符串方法 : search() 和 replace()。
search() 方法 用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串，并返回子串的起始位置。
replace() 方法 用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。

search()
使用正则表达式搜索 "w3cschool" 字符串，且不区分大小写：
```
var str = "Visit w3cschool";
var n = str.search(/w3cschool/i);

#结果
6
```

replace()
实例
使用正则表达式且不区分大小写将字符串中的 Microsoft 替换为 w3cschool :
```
var	str = "Visit Microsoft!";
var res = str.replace(/microsoft/i, "w3cschool");
结果输出为:
Visit w3cschool!
```
## 修饰符
i	执行对大小写不敏感的匹配。
g	执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。
m	执行多行匹配。

## 正则表达式模式
[abc]	查找方括号之间的任何字符。
[0-9]	查找任何从 0 至 9 的数字。
(x|y)	查找任何以 | 分隔的选项。


元字符	描述
\d		查找数字。
\s		查找空白字符。
\b		匹配单词边界。
\uxxxx	查找以十六进制数 xxxx 规定的 Unicode 字符。


量词	描述
n+		匹配任何包含至少一个 n 的字符串。
n*		匹配任何包含零个或多个 n 的字符串。
n?		匹配任何包含零个或一个 n 的字符串。

test() 方法用于检测一个字符串是否匹配某个模式，如果字符串中含有匹配的文本，则返回 true，否则返回 false。
exec() 方法用于检索字符串中的正则表达式的匹配。
该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null
```
var patt = /e/;
patt.test("The best things in life are free!");
//等价于
/e/.test("The best things in life are free!")

/e/.exec("The best things in life are free!");
结果是e
```

# 异常
try 语句允许我们定义在执行时进行错误测试的代码块。
catch 语句允许我们定义当 try 代码块发生错误时，所执行的代码块。
JavaScript 语句 try 和 catch 是成对出现的。
```
try
  {
  //在这里运行代码
  }
catch(err)
  {
  //在这里处理错误
  }

```

# JSON
JSON 英文全称 JavaScript Object Notation.
JSON 是用于存储和传输数据的格式。
JSON 通常用于服务端向网页传递数据 。
JSON 格式在语法上与创建 JavaScript 对象代码是相同的。
由于它们很相似，所以 JavaScript 程序可以很容易的将 JSON 数据转换为 JavaScript 对象。

## JSON 语法规则
数据为 键/值 对。
数据由逗号分隔。
大括号保存对象
方括号保存数组

```
在以上实例中，对象 "employees" 是一个数组。包含了三个对象。
"employees":[
    {"firstName":"John", "lastName":"Doe"}, 
    {"firstName":"Anna", "lastName":"Smith"}, 
    {"firstName":"Peter", "lastName":"Jones"}
]
```
使用 JavaScript 内置函数 JSON.parse() 将字符串转换为 JavaScript 对象:
```
<p id="demo"></p>

<script>
document.getElementById("demo").innerHTML =
obj.employees[1].firstName + " " + obj.employees[1].lastName;
</script>

结果：
Anna Smith
```

# void
 void 是 JavaScript 中非常重要的关键字，该操作符指定要计算一个表达式但是不返回值。
 ```
<a href="javascript:void(0);">点我没有反应的!</a>
<a href="#pos">点我定位到指定位置!</a>
<br><br><br> <p id="pos">尾部定位点</p>
 ```
而javascript:void(0), 仅仅表示一个死链接。
在页面很长的时候会使用 # 来定位页面的具体位置，格式为：# + id。
如果你要定义一个死链接请使用 javascript:void(0) 。
