---
layout: post
title: Testing Directive - Basic Dom Manipulation
date: 2014-06-27 14:08
comments: true
categories: angular javascript directive
---
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

