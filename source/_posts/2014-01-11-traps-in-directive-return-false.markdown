---
layout: post
title: Traps in Angular Directive -- Return False
date: 2014-01-11 20:44
comments: true
categories: javascript angularjs directive
---
## Return false will prevent default browser behavior
I was blocked for whole afternoon by a very weird phenomenon, two radio buttons(with the same name), one of them can never be checked after you click it, at the last, I found out the root cause is the browser's default behavior is prevented by the `return false` statement in directive.

Let's see what a normal radio button group should be:
```html
<body>
    <div>
        <div>
            <input type="text" name="" id="">
        </div>
        <div>
            <input type="radio" name="gender" id="female">
            <label for="female">Female</label>
            <input type="radio" name="gender" id="male">
            <label for="male">Male</label>
        </div>
    </div>
</body>
```
check the [demo](http://jsbin.com/iKelIko/2/edit?html,css,js,output), click a radio button can make it checked.

Now let's make some trouble, if we click a text input field, we want to show a console log say 'input field clicked', if other type input component is clicked, do nothing. let's write a directive to handle this.
```html
<body ng-app="Demo" ng-controller="DemoController">
    <div tell-me>
        <div>
            <input type="text" name="" id="">
        </div>
        <div>
            <input type="radio" name="gender" id="female">
            <label for="female">Female</label>
            <input type="radio" name="gender" id="male">
            <label for="male">Male</label>
        </div>
    </div>
</body>
```
```javascript
angular.module('Demo', [])
    .controller('DemoController', ['$scope',
        function ($scope) {}
    ])
    .directive('tellMe', [
        function () {
            return {
                link: function (scope, element, attr) {
                    element.bind('click', function () {
                        var target = angular.element(event.target);
                        if (!target.is(':text')) {
                            return false;
                        } else {
                            console.log('input field clicked');
                        }
                    });
                }
            };
        }
    ]);
```
Check the [demo](http://jsbin.com/alovifeJ/4/edit?output), you'll see the radion button group is not functional well, one of them can never be checked, this is all because we use `return false` in directive.

After we replace `return false` with `return`, everything back to normal, check again [here](http://jsbin.com/alovifeJ/5/edit?output)
