---
layout: post
title: Python Built-in Functions
date: 2012-11-20 21:13
comments: true
categories: python
---

* `cmp(x, y)`

#Attribute
* `delattr(object, name)`  
    `delattr(x, 'foobar')` is equivalent to `del x.foobar`.
* `getattr(object, name[, default])`
* `setattr(object, name, value)`
* `hasattr(object, name)`
* `repr(object)`  
This is the same value yielded by conversions (reverse quotes).

#Type
* `basestring`  
`isinstance(obj, *basestring*)` is equivalent to isinstance(obj, (str, unicode)).
* `isinstance(object, classinfo)`
* `issubclass(class, classinfo)`
* `super(type[, object-or-type])`
```python
class C(B):
    def method(self, arg):
        super(C, self).method(arg)
```
* `type(object)` `type(name, bases, dict)`  
With one argument, return the type of an object.  
With three arguments, return a new type object.  
```python
# the following two statements create identical type objects
class X(object):
    a = 1
X = type('X', (object,), dict(a=1))
```

#Type Cast
* `unichr(i)`
* `chr(i)`  
Return a string of one character whose ASCII code is the integer
```python
print chr(97)
>>'a'
```
* `ord(c)`
* `int(x)`
* `hex(x)`
* `float(x)`
* `long(x)`
* `oct(x)`

##Collection
* `tuple([iterable])`  
Return a tuple whose items are the same and in the same order as iterable‘s items.  
`tuple` is an immutable sequence type.
```python
list1 = ['edfeng', 'tyzhang', 'zming']
tuple(list1)
>>('edfeng', 'tyzhang', 'zming')
```
* `range(stop)`
  `range(start, stop[, step])`  
If the start argument is omitted, it defaults to 0.
* `enumerate(sequence, start=0)`  
Return an enumerate object. 
```
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))
>>[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
list(enumerate(seasons, start=1))
>>[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```
* `reversed(seq)`  
Return a reverse `iterator`. 
* `sorted(iterable[, cmp[, key[, reverse]]])`  
In general, the key and reverse conversion processes are much faster than specifying an equivalent cmp function. 
* `next(iterator[, default])`  
Retrieve the next item from the iterator by calling its next() method.
* `iter(o[, sentinel])`  
Return an iterator object. If the second argument, sentinel, is given, then o must be a callable object. The iterator created in this case will call o with no arguments for each call to its next() method; if the value returned is equal to sentinel, StopIteration will be raised, otherwise the value will be returned.
```python
with open('mydata.txt') as fp:
    for line in iter(fp.readline, ''):
        process_line(line)
```

* `filter(function, iterable)`
* `map(function, iterable, ...)`  
Apply function to every item of iterable and return a list of the results.
```python
list1 = ['edfeng', 'tyzhang', 'zming']
list2 = [100, 50, 80]
map(lambda name, score : name + '-' + str(score), list1, list2)
>>['edfeng-100', 'tyzhang-50', 'zming-80']
```
equivalent to this:
```python
list1 = ['edfeng', 'tyzhang', 'zming']
list2 = [100, 50, 80]
[name + '-' + str(score) for name, score in zip(list1, list2)]
>>['edfeng-100', 'tyzhang-50', 'zming-80']
```
* `reduce(function, iterable[, initializer])`  
`reduce` just like reduce function in couchdb, it takes a list and returns a single value.  
`reduce(lambda x, y: x+y, [1, 2, 3, 4, 5])` calculates `((((1+2)+3)+4)+5)`.
* `min()/max()`
`min(iterable[, key=func])`  
`min(arg1, arg2, *args[, key=func])`
```python
result = [('tester150411@gmail.com', '62', '624'), ('tester150411@gmail.com', '62', '528')]

print "Max time:",
print max(result, key=lambda x: int(x[2]))

print "Min time:",
print min(result, key=lambda x: int(x[2]))
```

#Decorators
* `classmethod(function)`  
A class method receives the class as implicit first argument, just like an instance method receives the instance. To declare a class method, use this idiom:
```python
class C:
    @classmethod
    def f(cls, arg1, arg2, ...): ...
```

* `staticmethod(function)`  
A static method does not receive an implicit first argument. To declare a static method, use this idiom:
```python
class C:
    @staticmethod
    def f(arg1, arg2, ...): ...
```
* `property`
```python
class C(object):
    def __init__(self):
        self._x = None

    # c.x will invoke this method
    @property
    def x(self):
        ""I'm the 'x' property.""
        return self._x

    # c.x = value will invoke this method
    @x.setter
    def x(self, value):
        self._x = value

    # del c.x will invoke this method
    @x.deleter
    def x(self):
        del self._x
```

#Math
* `pow(x, y[, z])`  
* `sum(iterable[, start])`

#IO
* `print(*objects, sep=' ', end='\n', file=sys.stdout)`
```python
from __future__ import print_function
print('a', 'b', 'c', sep='**')
>>a**b**c
```
    Note This function is not normally available as a built-in since the name print is recognized as the print statement. To disable the statement and use the print() function, use this future statement at the top of your module.  

* `raw_input([prompt])`
Read from console
```python
s = raw_input('--> ')
>>--> Monty Python's Flying Circus
s
>> "Monty Python's Flying Circus"
```
