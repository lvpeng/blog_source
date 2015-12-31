title: js基础总结
date: 2015-07-08 11:13:05
categories: 编程
tags: [javascript, 语法基础总结]
---

#### 变量 Variable

- 基本数据类型(值类型): Undefined , Null , String , Number , Boolean
- 复杂数据类型(引用类型) : Array, Object , Function  (Array, Function 本质也是Object)
- ECMAScript 不支持任何创建自定义类型的机制
- typeof : typeof null , typeof [], typeof {} , typeof /a-z/g    //"object"   
- Null 类型:  1) 如果定义的变量准备将来用于保存对象，那么最好显示的将该变量初始化为null 2) null : 空对象指针
- Boolean: ""(空字符串) , 0 , NaN, null , undefined, false
- Number:  1) NaN == NaN 2) isNaN() 3) 数值转换 : Number() , *parseInt()* 指定基数 : parseInt("123blue",10), parseFloat(): parseFloat(32.2.56),   ps: 第一个小数点有效 ; 只解析十进制数(不需要传基数参数);
- String : 1) 字符串是不可变的  `var a = 'abc'; a[0] =d ; a ; // 'abc'`  `var a = 'abc'; a = a + 'd'; a; // 'abcd' `  2) String()/toString()  `var a = 8; a.toString(2); // '1000'`
- Object 1)var o = new Object() 2) constructor , hasOwnProperty(*propName*) **用途： 过滤原型属性**, isPrototypeOf(object), propertyIsEnumerable(*propName*), toLocaleString(), toString(), valueOf()

- 操作符 1) +-*/% : +操作符 string > number > boolean ; -操作符 number > string > boolean
2) == != : only value 先转换，后比较 ; === !== : both value & type 只比较，不转换
` "55" == 55; "55" === 55`  ` null == undefined ; null === undefined`
- typeof []  // "object"   -> Array.isArray([]) // true
typeof {}  // "object"
typeof NaN
- instanceof
- typeof !!5   //** !!的作用是把一个其他类型的变量转成的bool类型。**
  ```js
  // Is a given variable an object?
  _.isObject = function(obj) {
    var type = typeof obj;
    return type === 'function' || type === 'object' && !!obj;
  };
  ```
#### 引用类型 VS 值类型
> No, JS doesn't have pointers.

*JS中没有指针，只有传值（value)与传址（reference引用）的区别*
对于引用类型的值， 可以为其添加属性和方法，也可以删除和改变其属性和方法

*
Objects are passed around by passing a copy of a reference. The programmer cannot access any C-like "value" representing the address of the object.
Within a function one may change the contents of an passed object via that reference, but you cannot modify the reference that the caller had, because your reference is only a copy
*

```js
var foo = {'bar': 1};

function tryToMungeReference(obj) {
    obj = {'bar': 2};  // won't change caller's object
}

function mungeContents(obj) {
    obj.bar = 2;       // changes _contents_ of caller's object
}

tryToMungeReference(foo);
foo.bar === 1;   // true - foo still references original object

mungeContents(foo);
foo.bar === 2;  // true - object referenced by foo has been modified
```
```
var a = [1,2,3,4] //a不仅是数组，还是个对象，实际上a就是对[1,2,3,4]的引用
var b=a
var c=a
//以上两条赋值语句建立了b与c 对a即[1,2,3,4]的引用,无论改变a 还是b抑或c 都是对[1,2,3,4]的操作，这就是传址(堆中操作)

var d=a[1] //则是把a[1]的值"1"传递给d,对d的改变则不会影响a[1],即所谓的传值(栈中操作)

```


### this (context) & Object Prototypes
```js
function foo() {
    foo.count = 4; // `foo` refers to itself
}

setTimeout( function(){
    // anonymous function (no name), cannot
    // refer to itself
}, 10 );
```

```js
function foo() {
    console.log( this.a );
}

var obj = {
    a: 2,
    foo: foo
};

var bar = obj.foo; // function reference/alias!

var a = "oops, global"; // `a` also property on global object

bar(); // "oops, global"
```

```js
function foo() {
    console.log( this.a );
}

function doFoo(fn) {
    // `fn` is just another reference to `foo`
    fn(); // <-- call-site!
}

var obj = {
    a: 2,
    foo: foo
};

var a = "oops, global"; // `a` also property on global object

doFoo( obj.foo ); // "oops, global"
```


```js
var foo = {
  bar : 'baz'
}
for (var key in foo) {
  console.log(key); // bar
}

Object.prototype.say = 'hello';
for (var key in foo) {
  console.log(key); // bar, say
}
// filter properties inherited through the prototype chain:
for (var key in foo) {
  if(foo.hasOwnProperty(key)){
       console.log(key); // bar
  }
}
```



###

```javascript
// Is a given value an array?
// Delegates to ECMA5's native Array.isArray
_.isArray = nativeIsArray || function(obj) {
  return toString.call(obj) === '[object Array]';
};
```

##### Use instanceof for custom types:

var ClassFirst = function () {};
var ClassSecond = function () {};
var instance = new ClassFirst();
typeof instance; // object
typeof instance == 'ClassFirst'; //false
instance instanceof Object; //true
instance instanceof ClassFirst; //true
instance instanceof ClassSecond; //false
##### Use typeof for simple built in types:

'example string' instanceof String; // false
typeof 'example string' == 'string'; //true

'example string' instanceof Object; //false
typeof 'example string' == 'object'; //false

true instanceof Boolean; // false
typeof true == 'boolean'; //true

99.99 instanceof Number; // false
typeof 99.99 == 'number'; //true

function() {} instanceof Function; //true
typeof function() {} == 'function'; //true
##### Use instanceof for complex built in types:

/regularexpression/ instanceof RegExp; // true
typeof /regularexpression/; //object

[] instanceof Array; // true
typeof []; //object

{} instanceof Object; // true
typeof {}; //object
And the last one is a little bit tricky:

typeof null; //object
