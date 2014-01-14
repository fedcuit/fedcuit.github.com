---
layout: post
title: Master AngularJS - directives
date: 2014-01-07 20:12
comments: true
categories: angularjs javascript
---

I've been using angularjs for several months, at the beginning, i always think everything in jQuery way, since I've used jQuery heavily on all of my previous projects, but day by day
I became like angularjs, i realize the shortcoming of jQuery: you will not know who is responsile for the event happens on this element until you see the event binding in jQuery code;
lots of manipulation is based on css selector which is fragile; it's also hard and tricky to transfer data between different element ...

So I decide to write a series of articles to record what I've learned in the past months, these articles are not for beginners, I assume you have a basic concept with AngularJS, but still don't know 
how to write AngularJS in the right way. Let's start with **directives** .

## Directive Definition Object

A directive usually appears as a element/tag name or attribute, it's used to add additional functionality to the element, like, `<a toggle-background/>`, the directive toggleBackground will switch the 
background color of the current page once you click it, it's more clear than implement the same logic with jQuery, you will understand what will happen when you see the directive on the link.

Let's take a closer look to directives, below is a simple directive defination.
```javascript
angular.module("app", []).directive("alert", [
    function () {
        return {
            restrict: 'EA',
            link: function (scope, elm, attr) {}
        };
    }
]);
```
## Link Function
we can ignore the `restrict` property, by default a directive can appear as a attribute, let's goes into the most important part in a directive: the **link function** .

The link function take three parameters: `scope`, `elm`, `attr`, the last two are easy to understand:

* *`elm`* the jQuery object representation of the element which this directve blongs to

Take below code as an example, then most common use of directive is to do something against the element it decorated, in below example, we add autocomplete functionality for a input field.
```html
<body ng-app="DemoApp">
    <div>
        What's your favorite programming language:
        <input type="search" search-language/>
    </div>
</body>
```
```javascript
angular.module('DemoApp', [])
    .directive('searchLanguage', [
        function () {
            return {
                link: function (scope, ele, attr) {
                    jQuery(ele).autocomplete({
                        source: ["c++", "java", "php", "coldfusion", "javascript", "asp", "ruby"]
                    });
                }
            };
        }
    ]);
```
See demo [here](http://jsbin.com/iZAxoVEF/1/edit)

* *`attr`* all the attributes on this element, it's a map of attribute name and value, given `<a type="text" some-directive/>`, `attr.type` will return `text`
`attr` are used to pass additional information to directive, it has the same functionallity with `jQuery(element).attr('attrName')`, but more convenient.

In below exmaple, we config the autocomplete dropdown is triggered at least user input 3 characters.
```html
<body ng-app="DemoApp">
    <div>
        What's your favorite programming language:
        <input type="search" search-language min-length="3"/>
    </div>
</body>
```
```javascript
angular.module('DemoApp', [])
    .directive('searchLanguage', [
        function () {
            return {
                link: function (scope, ele, attr) {
                    jQuery(ele).autocomplete({
                        source: ["c++", "java", "php", "coldfusion", "javascript", "asp", "ruby"],
                        minLength: attr.minLength
                    });
                }
            };
        }
    ]);
```
see demo [here](http://jsbin.com/emivixEz/1/edit)

## (Isolated) Scope
Why we need scope for directive ? First, let's devide all kinds of directves into two groups:

* *`without scope`*
  If what we do in this the directive is only DOM manipulation, then `elm` and `attr` is enough for use, we don't need to declare a isolated scope.

* *`with scope`*
  If we need to manipulate angular model in directive, then we need to declare a scope.

directive which don't need to use scope is easy to understand, usually we bind event listener on the link function, but directive which need scope requires more practice to master it, we also can 
devide this kind of directives into two groups: `manipulate model in controller` and `call method in controller`

#### Manipulate Model in Controller
access model in controller need to establish a isolated scope, there are two ways:

* `@[attributeName]` return the value of that attributeName, the value is a plain string, it's the same as using `attr.attributeName`
* `=[attributeName]` two way binding, first get the value of that attributeName, then evaluate the value in controller scope, changing the value in directive will reflect in controller scope

```html
<body ng-app="DemoApp">
    <div ng-controller="DemoController">
        Programming language (up to five):
        <input type="search" ng-model="profile.newLanguage">
        <input type="button" 
        	value="Add" 
        	add-language 
        	languages="profile.languages" 
        	new-language="profile.newLanguage" />
        <div>
            <ul>
                <li ng-repeat="language in profile.languages">{{language}}
                </li>
            </ul>
        </div>
    </div>

</body>
```

```javascript
angular.module('DemoApp', [])
    .controller('DemoController', ["$scope",
        function ($scope) {
            $scope.profile = {};
            $scope.profile.languages = [];
        }
    ])
    .directive('addLanguage', [
        function () {
            return {
                scope: {
                    languages: '=',
                    newLanguage: '='
                },
                link: function (scope, ele, attr) {
                    ele.on('click', function () {
                        scope.languages.push(scope.newLanguage);
                        scope.$apply();
                    });
                }
            };
        }
    ]);
```
check the [demo](http://jsbin.com/emivixEz/5/edit?html,js,output) here.

In the above code, we establish a isolateds scope to setup a bridge with `languages` and `newLanguage` in controller's scope, then we can manipulate them.

#### Call Method in Controller
* `&[attributeName]` return the value of that attributeName, the value is a function reference which points to the a method whose name same as the value in controller.

we are two type of invocation of controller method: `without parameters` and `with parameters`.

#### without parameters
```html
<body ng-app="DemoApp">
    <div ng-controller="DemoController">
        Programming language (up to five):
        <input type="search" ng-model="profile.newLanguage">
        <input type="button" 
        	value="Add" 
        	add-language 
        	languages="profile.languages" 
        	send-signal-to-server="sendSignalToServer()" 
        	new-language="profile.newLanguage" />
        <div>
            <ul>
                <li ng-repeat="language in profile.languages">{{language}}
                </li>
            </ul>
        </div>
    </div>
</body>
```
```javascript
angular.module('DemoApp', [])
    .controller('DemoController', ["$scope",
        function ($scope) {
            $scope.profile = {};
            $scope.profile.languages = [];
            $scope.clearValue = function () {
                $scope.newLanguage = "";
            };
            $scope.sendSignalToServer = function () {
                console.log('sending signal to server');
            };
        }
    ])
    .directive('addLanguage', [
        function () {
            return {
                scope: {
                    languages: '=',
                    newLanguage: '=',
                    sendSignalToServer: '&'
                },
                link: function (scope, ele, attr) {
                    ele.on('click', function () {
                        scope.languages.push(scope.newLanguage);
                        scope.sendSignalToServer();
                        scope.$apply();
                    });
                }
            };
        }
    ]);
```

see demo [here](http://jsbin.com/ErASoja/2/edit?html,js,output).

we want to call a methhod in controller which send some signal to server from our directive, we declare an attribute `send-signal-to-server` whose value is the method name is controller, as a result, `scope.sendSignalToServer` hold a reference to method `sendSignalToServer()` .
#### with parameters
Continue with the above example, we change the controller method `sendSignalToServer()` to accept two parameters, pass parameters from directive to controller is a lillte tricky.
```html
<body ng-app="DemoApp">
    <div ng-controller="DemoController">
        Programming language (up to five):
        <input type="search" ng-model="profile.newLanguage">
        <input type="button" 
        	value="Add" 
        	add-language 
        	languages="profile.languages" 
        	send-signal-to-server="sendSignalToServer(param1, param2)" 
        	new-language="profile.newLanguage" />
        <div>
            <ul>
                <li ng-repeat="language in profile.languages">{{language}}
                </li>
            </ul>
        </div>
    </div>

</body>
```
```javascript
angular.module('DemoApp', [])
    .controller('DemoController', ["$scope",
        function ($scope) {
            $scope.profile = {};
            $scope.profile.languages = [];
            $scope.clearValue = function () {
                $scope.newLanguage = "";
            };
            $scope.sendSignalToServer = function (param1, param2) {
                console.log('sending signal to server', param1, param2);
            };
        }
    ])
    .directive('addLanguage', [
        function () {
            return {
                scope: {
                    languages: '=',
                    newLanguage: '=',
                    sendSignalToServer: '&'
                },
                link: function (scope, ele, attr) {
                    ele.on('click', function () {
                        scope.languages.push(scope.newLanguage);
                        scope.sendSignalToServer({
                            "param1": "123",
                            "param2": "456"
                        });
                        scope.$apply();
                    });
                }
            };
        }
    ]);
```
see [demo](http://jsbin.com/ErASoja/3/edit?html,css,js,output)

as you can see, we need to define placeholder for the argument list in the attribute, and in the directive, when we are going to call that method, we need to construct a object which use the place holder as keys, and your real parameters as values.

If you're patient enough to read to here, i belieave you've got a basic concept how to write directive in the right way. But keep in mind, don't use too many directives on one element, it's difficult to understand which directive is responsible for which functionality, thus increase the effort to maintain the code, also your directive could impact the native angular directive, so first try to use angular native directives(e.g. ng-click, ng-init), if it can not fit your requirements, write you own.