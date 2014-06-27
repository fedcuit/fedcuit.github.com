---
layout: post
title: Testing With Angular -- Directive
date: 2014-01-19 21:22
comments: true
categories: javascript angular directive 
---

As a Java developer, I am familiar with test driven development with Java language, but for angular, I'm not, sometime it's even hard to write angular test after the implementation is done.
So I'll write a series of articles about how to test with angular, basically, I'd like to include the test strategy for directives, controllers and services, this article will begin with how to test angular directive.

Let's check out the below examples form easy to hard.

## Basic Dom Manipulation
```html
<div>
    <h4>Basic Dom Manipulation</h4>
    <input type="text" name="search" id="search"/>
    <button type="button" clear-search>Clear</button>
</div>
```
We're going to write a directive for the button, after click it, the search box wil be clear.
To demonstrate DOM manipulation, we'll not set `ngModel` for the search box. Let's write out the test first.

```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('clearSearch', function () {
        var $scope, element;
        beforeEach(inject(function ($rootScope, $compile) {
            $scope = $rootScope;
            element = angular.element('<input type="text"/><button type="button" clear-search>Clear</button>');

            $compile(element)($scope);
        }));
        it('should clear search box when click clear button', inject(function ($rootScope, $compile) {
            var val = spyOn(jQuery.fn, 'val');
            var prev = spyOn(jQuery.fn, 'prev');

            element.filter('input').val('some value');
            element.filter('button').trigger('click');

            expect(val).toHaveBeenCalled();
            expect(val.mostRecentCall.args[0]).toBe('');
        }));
    });
});
```
To be honest, directive which only manipulate DOM doesn't need scope, so we can remove scope in test, the test still can pass.
```javascript
describe('directives', function () {
    beforeEach(module('myApp.directives'));

    describe('clearSearch', function () {
        var element;
        beforeEach(inject(function ($rootScope, $compile) {
            element = angular.element('<input type="text"/><button type="button" clear-search>Clear</button>');

            $compile(element)({});
        }));
        it('should clear search box when click clear button', inject(function ($rootScope, $compile) {
            var val = spyOn(jQuery.fn, 'val');
            var prev = spyOn(jQuery.fn, 'prev');

            element.filter('input').val('some value');
            element.filter('button').trigger('click');

            expect(val).toHaveBeenCalled();
            expect(val.mostRecentCall.args[0]).toBe('');
        }));
    });
});
```
So we have include `jQuery` in application(_sorry, I did_), we just need to spy on jQuery method then verify.

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