title: Object Prototypes
date: 2015-07-10 15:53:08
categories: 编程
tags: [javascript]
---

### Object.prototype.hasOwnPrototype()
- obj.hasOwnProperty(prop)
我们在js中可能经常会用到for in来遍历对象中的属性，当然for in中得到的属性，只能是**可枚举的属性**，for in的时候，它会把对象的属性(包括原型的属性)遍历一遍，看面看示例就明白了
```js
var array = [];
array.push(1);
array.push(2);
array.push(3);
for(var key in array) {
  console.log(key + ":" + array[key]);
}

```
此时会输出什么呢？当然是0:1 1:2 2:3
但是如果在for in之前加上 Array.prototype.say = "hello";
再次运行会输出什么呢？

0:1 1:2 2:3 say:hello

看到了吧，这个时候，它会输出原型的属性
在很多时候，我们不需要遍历它原型的属性，还有一个原因就是，*我们现在用到的对象，我们不能保证，其他开发人员，有没有，在它的原型上加一些属性呢？* 所以呢，我们就过滤一下我们对象的属性吧，这个时候就用到了hasOwnProperty方法，如下：

```js
for(var key in array){
 if(array.hasOwnProperty(key)) {
  console.log(key +":"+ array[key]); //0:1 1:2 2:3
 }
}
```

### Object.prototype.propertyIsEnumerable()
propertyIsEnumerable() 是用来检测指定的属性名是否是当前对象可枚举的自身属性。如果检测到了,返回true,否则返回false. 
语法
** obj.propertyIsEnumerable(*prop*) **
描述
每个对象都有 propertyIsEnumerable 方法。该方法可以判断出指定的属性是否是自身的可枚举属性，也就是说该属性是否可以通过 for...in 循环等遍历到，不过有些属性虽然可以通过 for...in 循环遍历到，但因为它们不是自身属性，而是从原型链上继承的属性,所以该方法也会返回false。如果对象没有指定的属性，该方法返回 false。

1.这个属性必须是可枚举的,也就是自定义的属性,可以通过for..in循环出来的. 
2.这个属性必须属于实例的,并且不属于原型. 

只要符合上面两个要求,就会返回true; 
```javascript
function MyObject() { 
  this.name = "我是实例的属性"; 
} 
var obj = new MyObject(); 
alert(obj.propertyIsEnumerable("name"));//true 
MyObject.prototype.say = "我是原型的属性"; 
alert(obj.propertyIsEnumerable("say")); //false 
for (var i in obj) { 
  alert(i);//name,say 
} 
```

### hasOwnProperty , propertyIsEnumerable 区别
hasOwnProperty will return true even for non-enumerable "own" properties (like length in an Array). propertyIsEnumerable will return true only for enumerable "own" properties. (An "enumerable" property is a property that shows up in for..in loops and such.)
```js
var a = [];
a.hasOwnProperty('length');    // true
a.propertyIsEnumerable('length');   //false
```

You can use "defineProperty" to define properties that are not enumerable; see this reference at MDN.
```js
Object.defineProperty(obj, "hideMe", { value: null, enumerable: false });
```
That's like:
`
obj.hideMe = null;
`
except the property won't show up in for ... in loops, and tests with propertyIsEnumerable will return false.

This whole topic is about features not available in old browsers, if that's not obvious.


### Object.prototype.isPrototypeOf()
http://stackoverflow.com/questions/8453887/why-is-it-necessary-to-set-the-prototype-constructor

http://stackoverflow.com/questions/1249531/how-to-get-a-javascript-objects-class?lq=1