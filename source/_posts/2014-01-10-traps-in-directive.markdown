---
layout: post
title: Traps in Angular Directive
date: 2014-01-10 10:10
comments: true
categories: angularjs directive javascript
---

I was trapped in angular directive this work, after struggled for hours, I noticed below traps in angular directive.

## Directive with isolated scope will impact native angular directive
If your own directive has a isolated scope,  then it will impact native angular directive, which means, sometime, ngModel, ngDisabled suddenly doesn't work, because they're impacted by your directive.
take below as an example:

We have a input field to type in a programming language, click the 'Add' button will add it into a list(as it's a simple demo, so data validation is not concerned)

```html
<body ng-app="DemoApp">
    <div ng-controller="DemoController">
        What's your favorite programming language (up to five):
        <input type="search" ng-model="profile.newLanguage" />
        <input type="button" 
        	value="Add" 
        	add-language 
        	languages="profile.languages" 
        	new-language="profile.newLanguage" />
        <div>
            <ul>
                <li ng-repeat="language in profile.languages">{{language}}</li>
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

we put a directive `addLanuage` on the button, which will get the value in the input field and add it to language list, due to we need to operate the language list, so we use a isolated scope to access it inside the directive.

Try it yourself. [Demo](http://jsbin.com/emivixEz/5/embed?output)

Now the new requirement comes, a user only allow to fill up to five programming languages, we need to disable the `Add` button after user have input 5 languages.

Seems a small change will fit the new requirement, ngDisabled should solve this.

```html
<input 
	type="button" 
	value="Add" 
	add-language 
	ngDisabled="reachThreshold()" 
	languages="profile.languages" 
	new-language="profile.newLanguage" />
```

```javascript
angular.module('DemoApp', [])
    .controller('DemoController', ["$scope",
        function ($scope) {
            $scope.profile = {};
            $scope.profile.languages = [];
            $scope.reachThreshold = function () {
                return $scope.profile.languages.length === 5;
            };
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
Play with the updated code you'll find out `ngDisabled` is not working!
[Demo](http://jsbin.com/itOTase/3/edit?output)

What can we do to save the `ngDisabled` damaged by isolated scope.
```html
<body ng-app="DemoApp">
    <div ng-controller="DemoController">
        Programming language:
        <input type="search" ng-model="profile.newLanguage" />
        <input type="button" 
        	value="Add" 
        	add-language 
        	needs-disabled="reachThreshold()" 
        	ng-disabled="needsDisabled" 
        	languages="profile.languages" 
        	new-language="profile.newLanguage" />
        <div>
            <ul>
                <li ng-repeat="language in profile.languages">{{language}}</li>
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
            $scope.profile.languages = ['a', 'a', 'a', 'a', 'a'];
            $scope.reachThreshold = function () {
                return $scope.profile.languages.length === 5;
            };
        }
    ])
    .directive('addLanguage', [
        function () {
            return {
                scope: {
                    languages: '=',
                    newLanguage: '=',
                    needsDisabled: '='
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
Now `ngDisabled` works, [Demo](http://jsbin.com/ONiFagE/2/edit?html,js,output)

As you can see from the code, we declare a new attribute `needs-disabled` which use `reachThreshold()` as it's value, then we set `needsDisabled` to `ng-disabled`, the last thing is to declare the new attribute in directive's scope, in this way, `ngDisabled` back again.
## NO Multiple isolated scope
If you put more than one directive on a element, and each of them has a isolated scope, angular will fail and complain multiple isolated scope on one element.
## Return false will prevent default browser behavior
