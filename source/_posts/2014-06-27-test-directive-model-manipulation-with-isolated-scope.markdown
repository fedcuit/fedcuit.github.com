---
layout: post
title: Testing Directive - Model manipulation with isolated scope
date: 2014-06-27 14:15
comments: true
categories: angular directive javascript
---
## Model manipulation with isolated scope
```html
<div ng-controller="FruitController" ng-init="init()">
    <h4>Model manipulation with isolated scope</h4>
    <label>What's your favorite fruit</label>
    <input type="text" ng-model="newFruit"/>
    <button type="button" add-fruit fruits="fruits" new-fruit="newFruit">Add</button>
    <ul>
        <li ng-repeat="fruit in fruits track by $index">{{fruit}}</li>
    </ul>
</div>
```
The above directive manipulate data in default scope, now we create a isolated scope for the directive, the test point change to verify the data on isolated scope.
```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('addFruit', function () {
        var $scope, element;
        beforeEach(inject(function ($rootScope, $compile) {
            $scope = $rootScope;
            $scope.fruits = [];
            $scope.newFruit = 'apple';
            element = angular.element('<input type="text" ng-model="newFruit"/><button type="button" add-fruit fruits="fruits" new-fruit="newFruit">Add</button>');

            $compile(element)($rootScope);
        }));
        it('should add fruit to fruit list when click button', function () {
            element.filter('button').trigger('click');

            expect(element.scope().fruits[0]).toBe('apple'); // use element.scope() to access isolated scope
            expect($scope.fruits[0]).toBe('apple'); // also verify default scope is updated
        })
    });
});
```
In the test, we setup the surrounding scope, then verify both the default scope and isolated scope are updated.(we use `element.scope()` to access the isolated scope).

