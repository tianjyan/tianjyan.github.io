---
layout: post
title: Node向Angular传值方式简析
date: 2016-09-22 21:15:58
tags: [Node]
categories: [JavaScript]
---

# 写在前面
JavaScript社区真是越来越火了，通过各种丰富的扩展可以完成从前端到后端再到桌面的各种实现。

## 从前端框架角度看
MVVM的数据绑定的思想从2005年微软提出到现在，至今在UI架构方面，我还没见过一门语言超过这个。Google的Angular极大的方便了数据的呈现，vue就更是一个纯粹的MVVM框架。

## 从后端开发的角度看
Node的快速发展使得前端开发者第一次有了机会能够亲自尝试一下后端开发的乐趣，借助Google的V8引擎，在面对高并发的场景下，Node处理的游刃有余。

## 从语言角度看
C#之父Anders主导设计的TypeScript作为JavaScript的一个超集，使JavaScript真正能从类型系统的层面上和Java等强类型语言站在了一样的高度，极大地提高了开发效率。其他的诸如Dart和CoffeeScript也让大家看到了JavaScript的不同的实现。

## 从移动端看
Facebook的React Native和阿里巴巴的Weex也使得前端开发者能够轻易地创建Android和ios应用。

## 从桌面端看
诸如Visual Studio Code和Slack等明星应用，也让开发者们相信，使用Electron开发跨平台的桌面应用也是个不错的选择。


参考信息：  
[The state of the Octoverse 2016](https://octoverse.github.com/)  
[Angular](https://github.com/angular/angular)  
[vue](https://github.com/vuejs/vue)  
[react](https://github.com/facebook/react)  
[ant-design](https://github.com/ant-design/ant-design)  
[jQuery](https://github.com/jquery/jquery)  
[NodeJs](https://github.com/nodejs/node)  
[TypeScript](https://github.com/Microsoft/TypeScript)  
[CoffeScript](https://github.com/jashkenas/coffeescript)  
[dart](https://github.com/dart-lang/sdk)  
[React Native](https://github.com/facebook/react-native)  
[Weex](https://github.com/alibaba/weex)  
[Electron](https://github.com/electron/electron)  
[NW.js](https://github.com/nwjs/nw.js)  
<!-- more -->

# 简介
最近用Angular和Express搭建应用，当碰到数据绑定的时候，遇到了一些问题，原因是我Express从后台取到数据之后，怎么把数据给页面不明白了。我在尝试了多种实现之后，总结了下面三种方式：
* Angular在Controller中直接拿Express的Response给的值
* 界面完成加载后，AngularController中发送http请求，取得数据
* 使用ng-init拿到数据

# 方法1
首先我在app.js文件中构建一个Http Get的方法：

```JavaScript
app.get('/sample1', function(req, res){
    res.render('sample1', {
        "title": mockdata.title,
        "users": mockdata.users
    });
});
```

因为我使用了ejs的模板引擎，所以我可以在页面中直接title的值：
```html
<h1>
    <%=title%>
</h1>
```

这时候我再在Angular的Controller中取得users的数据：
```html
<script>
    angular.module("defaultapp", [])
        .controller("defaultcontrol", function($scope, $http) {
            $scope.users = <%-JSON.stringify(users)%>;
        });
</script>
```

值得注意的是，因为ejs的取值方式从html变成了JavaScript，所以我们的标签应该写成`<%-%>`而不是`<%=%>`，否则我们会在控制台看见这样的错：

![image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2016-09-22-pass2angular-01.png)

然后我们在使用ng-repeat就能拿到生成一个列表了：
```html
<ul>
    <li ng-repeat="user in users">
        {{user.username}} : {{user.password}}
    </li>
</ul>
```

![image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2016-09-22-pass2angular-02.png)

# 方法2
方法2是用Controller再发送Http request的方式实现的。现在app.js文件中构造两个Http Get的方法：
```JavaScript
app.get('/sample2', function(req, res){
    res.render('sample2', {
        "title": mockdata.sample2
    });
});

app.get('/sample2/users', function(req, res){
    res.json(mockdata.users);
});
```

然后ejs文件中的Controller代码：

```html
<script>
    angular.module("defaultapp", [])
        .controller("defaultcontrol", function($scope, $http) {
            $http.get('/sample2/users').success(function(data) {
                $scope.users = data
            });
        });
</script>
```

结果：

![image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2016-09-22-pass2angular-03.png)

# 方法3
第三个方法应该是最简单有效，相比1，不需要将代码耦合到Controller中，相比2，不需要再发送一个http的请求，在body添加ng-init即可：

```html
<body ng-app="defaultapp" ng-controller="defaultcontrol" ng-init="users=<%=JSON.stringify(users)%>">
    <h1>
        <%=title%>
    </h1>
    <ul>
        <li ng-repeat="user in users">
            {{user.username}} : {{user.password}}
        </li>
    </ul>
    <script>
        angular.module("defaultapp", [])
            .controller("defaultcontrol", function($scope, $http) {
                
            });
    </script>
</body>
```

结果：
![image](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/images/2016-09-22-pass2angular-04.png)

# 总结
本文描述了三种Express向页面传值的方式，其实三种方式都是适用的，这完全取决于使用者如何取舍。个人倾向于适用第三种方式。

# 源码下载
[源码](https://raw.githubusercontent.com/tianjyan/tianjyan.github.io/master/attachments/2016-09-22-pass2angular-code.zip)

