---
layout: post
title: Several ways to define ajax interceptors in AngularJS
date: 2013-11-21 21:53
comments: true
categories: angularjs, ajax
---

```javascript
var myApp = angular.module("myApp", []);
myApp.config(['$httpProvider',
    function ($httpProvider) {
        var $http,
            interceptor = ['$q', '$injector',
                function ($q, $injector) {
                    function success(response) {
                        // get $http via $injector because of circular dependency problem
                        $http = $http || $injector.get('$http');
                        if ($http.pendingRequests.length < 1) {
                            console.log('ajax cal respond with success');
                        }
                        return response;
                    }

                    function error(response) {
                        console.log('x', response);
                        // get $http via $injector because of circular dependency problem
                        $http = $http || $injector.get('$http');
                        if ($http.pendingRequests.length < 1) {
                            console.log('ajax call respond with error');
                        }
                        return $q.reject(response);
                    }

                    return function (promise) {
                        console.log('before send ajax request');
                        return promise.then(success, error);
                    };
                }
            ];

        $httpProvider.responseInterceptors.push(interceptor);
    }
]);
```

Check out the [demo](http://jsbin.com/aVAteLEH/3/embed?html,js)
