---
layout: post
title: Testing Directive - Call controller method in default scope
date: 2014-06-27 14:17
comments: true
categories: javascript angular directive
---
## Call controller method in default scope
```html
<div ng-controller="FruitController" ng-init="init()">
    <h4>Call controller method in default scope</h4>
    <label>What's your favorite fruit(name can only contains letter)</label>
    <input type="text" ng-model="newFruit"/>
    <button type="button" add-fruit-method>validate and add</button>
    <ul>
        <li ng-repeat="fruit in fruits track by $index">{{fruit}}</li>
    </ul>
</div>
```
This time, we want to validate the fruit name use typed in first, if it only contains letter, then it's eligible to add in, otherwise nothing happen. The validation logic is defined in controller, we need to test the validation method is called inside our directive.
```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('addFruitMethod', function () {
        var $scope, element;
        beforeEach(inject(function ($rootScope, $compile) {
            $scope = $rootScope;
            $scope.fruits = [];
            $scope.newFruit = 'apple';
            $scope.isValid = angular.noop;

            element = angular.element('<input type="text" name="fruit" id="fruitDefault" ng-model="newFruit"/><button type="button" add-fruit-method>validate and add</button>');
            $compile(element)($scope);
        }));

        it('should add valid fruit to fruit list when click button', function () {
            var isValid = spyOn($scope, 'isValid').andReturn(true);

            element.filter('button').trigger('click');

            expect(isValid).toHaveBeenCalled();
            expect($scope.fruits[0]).toBe('apple');

        });

        it('should reject invalid fruit when click button', function () {
            var isValid = spyOn($scope, 'isValid').andReturn(false);

            element.filter('button').trigger('click');

            expect(isValid).toHaveBeenCalled();
            expect($scope.fruits.length).toBe(0);
        });
    });
});
```
It's very similar with testing manipulate data on default scope, the only thing different is we spy on the validation method to verify it has been called, also we let the spy object return the corresponding result to execute each branch.

