---
layout: post
title: Testing Directive - Call controller method via isolated scope
date: 2014-06-27 14:19
comments: true
categories: 
---
##Call controller method via isolated scope
```html
<div ng-controller="FruitController" ng-init="init()">
    <h4>Call controller method via isolated scope</h4>
    <label>What's your favorite fruit(name can only contains letter)</label>
    <input type="text" ng-model="newFruit"/>
    <button type="button" 
    	add-fruit-method-isolated 
    	is-valid="isValid(name)" 
    	new-fruit="newFruit" 
    	fruits="fruits">
        validate and add
    </button>
    <ul>
        <li ng-repeat="fruit in fruits track by $index">{{fruit}}</li>
    </ul>
</div>
```
Now comes the final one, we have a reference to the validation method in isolated scope, we want to verify this reference has been called, and with the right arguments.
```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('addFruitMethodIsolated', function () {
        var $scope, element;
        beforeEach(inject(function ($rootScope, $compile) {
            $scope = $rootScope;
            $scope.fruits = [];
            $scope.newFruit = 'apple';
            $scope.isValid = angular.noop;

            element = angular.element('<input type="text" ng-model="newFruit"/>' +
                '<button type="button" ' +
                'add-fruit-method-isolated ' +
                'is-valid="isValid(name)" ' +
                'new-fruit="newFruit" ' +
                'fruits="fruits">' +
                'validate and add</button>');

            $compile(element)($scope);
        }));

        it('should add valid fruit to fruit list when click button', function () {
            var isValid = spyOn($scope, 'isValid').andReturn(true);

            element.filter('button').trigger('click');

            expect(isValid).toHaveBeenCalled();
            expect(isValid.mostRecentCall.args[0]).toBe('apple');
            expect($scope.fruits[0]).toBe('apple');
        });
    });
});
```
Not like verify data change in isolated scope, we don't need to track method on isolated scope, track on method in default scope is enough. One thing interesting is in the implementation, we need to pass a json object as argument(`scope.isValid({name: scope.newFruit})`), the json object is used internally in angular, in the test, what we need to verify is only values(`expect(isValid.mostRecentCall.args[0]).toBe('apple');`). 

Check out the implementation below.
```javascript
angular.module('myApp.directives', [])
    .directive('addFruitMethodIsolated', function () {
        return {
            scope: {
                fruits: '=',
                newFruit: '=',
                isValid: '&'
            },
            link: function (scope, element, attr) {
                element.click(function () {
                    if (scope.isValid({name: scope.newFruit})) {
                        scope.fruits.push(scope.newFruit);
                        scope.$apply();
                    }
                });
            }
        };
    });
```