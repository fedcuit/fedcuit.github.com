---
layout: post
title: 在JS string.replace() 中使用捕获组
date: 2016-03-14 21:34
comments: true
categories: js
---
上周在写代码的时候要在`componentWillUpdate`方法中判断一下当前`state`/`props`有没有改变, 这个判断其实跟`PureRenderMixin`干的是完全一样的事情, 但是`PureRenderMixin`是在`shouldComponentUpdate`这个life cycle method里执行的, 所以并不能直接拿来用. 跑去看了一下`PureRenderMixin`的实现, 发现它对`state`/`props`的比较是依赖于一个`shallowEqual`方法, 而这个方法在`fbjs`里面, [fbjs](https://github.com/facebook/fbjs)是Facebook维护的一个实用类库的集合, 所以今天决定读一读这个项目, 希望能从中学到一些新的东西.

那先来分享一下学到的第一个新东西吧.


##`String.replace()`##
之前对这个函数的认识比较简单, 只知道可能将指定的字符串或pattern替换成另外一个字符串, 如:
```javascript
console.log('AlphaGo vs Lee Sedol'.replace('Go', 'Google'))
// "AlphaGoogle vs Lee Sedol"
```
或者是:
```javascript
console.log('188-0000-0000'.replace(/-/, '+'))
// "188+0000+0000"
console.log('188-0000-0000'.replace(/-/g, '+'))
// "188+0000+0000"
```
需要注意的是, 正则表达式没有使用`g`选项的时候, `replace`只会替换第一处匹配.

今天学到的高级用法是`replace`函数还可以用一个函数来返回替换量, 而且这个函数的参数是整个匹配还有这个匹配中的所有捕获组, 比如`fbjs`中的[camelize](https://github.com/facebook/fbjs/blob/master/src/core/camelize.js#L24)函数:

```javascript
const _hyphenPattern = /-(.)/g;

/**
 * Camelcases a hyphenated string, for example:
 *
 *   > camelize('background-color')
 *   < "backgroundColor"
 *
 * @param {string} string
 * @return {string}
 */
function camelize(string) {
  return string.replace(_hyphenPattern, function(_, character) {
    return character.toUpperCase();
  });
}
```
这里 `_hyphenPattern` 用来匹配连字符写法, 并将连字符写法替换成驼峰写法, 我们无法通过简单将`-`替换成空白字符来实现, 因为我们还需要将`-`后面的字母大写, 这个时候可以接受捕获组的函数上可以出场了. 在 `_hyphenPattern` 的定义里面, `-`后面的那个字母是被定义成一个捕获组, 它会作为第二个参数传入replacement function(第一个参数是匹配的字符串), replacement function将这个字母大写之后返回, 就实现了将`-` + 任意字母 替换成了一个大写之后的字母的.