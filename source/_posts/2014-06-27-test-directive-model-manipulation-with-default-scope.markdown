---
layout: post
title: Testing Directive - Model manipulation with default scope
date: 2014-06-27 14:13
comments: true
categories: 
---
## Model manipulation with default scope
```html
<div ng-controller="FruitController" ng-init="init()">
    <h4>Model manipulation with default scope</h4>
    <label>What's your favorite fruit</label>
    <input type="text" ng-model="newFruit"/>
    <button type="button" add-fruit-default>Add</button>
    <ul>
        <li ng-repeat="fruit in fruits track by $index">{{fruit}}</li>
    </ul>
</div>
```
Let's try something advanced, we have a input box to type in a fruit name and a button, after click the button, the value of the input box will added into the fruit list.

How to write test for this directive? If the fruit list on the scope will have the fruit we passed in after we click the button, then we think it's working well.
```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('addFruitDefault', function () {
        var $scope, element;
        beforeEach(inject(function ($rootScope, $compile) {
            $scope = $rootScope;
            $scope.fruits = [];
            $scope.newFruit = 'apple';

            element = angular.element('<input type="text" name="fruit" id="fruitDefault" ng-model="newFruit"/><button type="button" add-fruit-default>Add</button>');
            $compile(element)($scope);
        }));

        it('should add fruit to fruit list when click button', function () {
            element.filter('button').trigger('click');

            expect($scope.fruits[0]).toBe('apple');
        });
    });
});
``` 
Not like the first test code, this one needs to verify the data on scope, we need a real scope, also we need to initialize the state of the scope, after we compile the html fragment using the scope, interact with the DOM will affect the scope.

