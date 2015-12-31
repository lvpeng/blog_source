title: 'underscore源码阅读'
date: 2015-03-08 11:26:34
categories: 编程
tags: [underscore]
---
1. ECMAScript 5 native function

var
  nativeIsArray      = Array.isArray,
  nativeKeys         = Object.keys,
  nativeBind         = FuncProto.bind,
  nativeCreate       = Object.create;



2
```javascript
var property = function(key) {
  return function(obj) {
    return obj == null ? void 0 : obj[key];
  };
};

// Helper for collection methods to determine whether a collection
// should be iterated as an array or as an object
//Related:http://people.mozilla.org/~jorendorff/es6-draft.html#sec-tolength
// Avoids a very nasty iOS 8 JIT bug on ARM-64. #2094
var MAX_ARRAY_INDEX = Math.pow(2, 53) - 1;
var getLength = property('length');
var isArrayLike = function(collection) {
  var length = getLength(collection);
  return typeof length == 'number' && length >= 0 && length <= MAX_ARRAY_INDEX;
};

// The cornerstone, an `each` implementation, aka `forEach`.
// Handles raw objects in addition to array-likes. Treats all
// sparse array-likes as if they were dense.
_.each = _.forEach = function(obj, iteratee, context) {
  iteratee = optimizeCb(iteratee, context);
  var i, length;
  if (isArrayLike(obj)) {
    for (i = 0, length = obj.length; i < length; i++) {
      iteratee(obj[i], i, obj);
    }
  } else {
    var keys = _.keys(obj);
    for (i = 0, length = keys.length; i < length; i++) {
      iteratee(obj[keys[i]], keys[i], obj);
    }
  }
  return obj;
};
```

-> 自己写的类似demo
```javascript
var foo = function(type){
	return function(value){
		return typeof value == type;
	}
}

var bar = foo('string');
bar({});
```
