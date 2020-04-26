title: AngularJS入门
date: 2015-12-31 12:00:35
tags:
 - Angularjs
categories: [web]

---
AngularJS 是一个 JavaScript 框架。它是一个以 JavaScript 编写的库。

## Angularjs的特性：
MVC，模块化，指令系统，双向数据绑定

MVC：
Model 数据模型层
View 视图层，负责展示
Controller 业务逻辑和控制逻辑
<!--more -->

## 实例1：
```
<!DOCTYPE html>
<html >
<head>
    <meta charset="UTF-8">
    <title>Angularjs</title>
</head>
<body>
    <div ng-app="">
        <p>在输入框中尝试输入：</p>
        <p>姓名：<input type="text" ng-model="name"></p>
        <p ng-bind="name"></p>
    </div>
    <script src="http://apps.bdimg.com/libs/angular.js/1.3.9/angular.min.js"></script>
    <!--<script src="http://code.angularjs.org/angular-1.0.1.min.js"></script>-->
</body>
</html>
```
分析：
ngApp 是一种简单的、通用的方式启用Angularjs应用，一般我们放在 body 或 html标签上面，标记ng-app告诉AngularJS处理整个HTML页并引导应用。

文本输入指令`<input ng-model="name" />`绑定到一个叫name的模型变量。
ng-model 指令把输入域的值绑定到应用程序变量 name。

ng-bind 指令把应用程序变量 name 绑定到某个段落的 innerHTML。

载入AngularJS脚本
`<script src="http://apps.bdimg.com/libs/angular.js/1.3.9/angular.min.js"></script>`

ng-init 指令初始化 AngularJS 应用程序变量。

AngularJS 表达式用两个大括号括起来的。

注意：
HTML5 允许扩展的（自制的）属性，以 data- 开头。
AngularJS 属性以 ng- 开头，但是您可以使用 data-ng- 来让网页对 HTML5 有效。

## AngularJS 应用
AngularJS 模块（Module） 定义了 AngularJS 应用。
AngularJS 控制器（Controller） 用于控制 AngularJS 应用。
ng-app指令定义了应用, ng-controller 定义了控制器。

html文件
```
<!DOCTYPE html>
<html >
<head>
    <meta charset="UTF-8">
    <title>Angularjs</title>
</head>
<body>
    <div ng-app="myapp" ng-controller="myctrl">
        名： <input type="text" ng-model="firstname"><br>
        姓： <input type="text" ng-model="lastname"><br>
        姓名： {{ firstname + " " + lastname }}
    </div>
    <script src="http://apps.bdimg.com/libs/angular.js/1.3.9/angular.min.js"></script>
    <script src="helloAngular.js"></script>
</body>
</html>
```


helloAngular.js
```
var app = angular.module('myapp', []);
app.controller('myctrl', function($scope){
    $scope.firstname = "vike";
    $scope.lastname = "lee";
});
```


 