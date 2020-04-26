title: AngularJS_MVC
date: 2016-01-04 12:00:35
tags:
 - Angularjs
categories: [web]

---

MVC只是手段，终极目标是模块化和复用。
angluarjs中的mvc全部借助于$scope来实现的。

# MVC
模型 - model
模型是负责管理应用程序的数据。它响应来自视图的请求，同时也响应指令从控制器进行自我更新。

视图 - view
在一个特定的格式的演示数据，由控制器决定触发显示数据。它们是基于脚本的模板系统，如JSP，ASP，PHP，非常容易使用AJAX技术的集成。

控制器 - controller
控制器负责响应于用户输入并执行交互数据模型对象。控制器接收到输入，它验证输入，然后执行修改数据模型的状态的业务操作。
<!--more -->

# 例子
```
<html>
<head>
<title>Angular JS Controller</title>
</head>
<body>
<h2>AngularJS Sample Application</h2>
<div ng-app="" ng-controller="studentController">

Enter first name: <input type="text" ng-model="student.firstName"><br><br>
Enter last name: <input type="text" ng-model="student.lastName"><br>
<br>
You are entering: {{student.fullName()}}
</div>
<script>
function studentController($scope) {
   $scope.student = {
      firstName: "Mahesh",
      lastName: "Parashar",
      fullName: function() {
         var studentObject;
         studentObject = $scope.student;
         return studentObject.firstName + " " + studentObject.lastName;
      }
   };
}
</script>
<script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.2.15/angular.min.js"></script>
</body>
</html>

```
