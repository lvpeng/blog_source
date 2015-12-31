title: 'Module'
date: 2015-07-17 19:45:11
categories: 编程
tags: [javascript, module]

---
### Modern Modules
Various module dependency loaders/managers essentially wrap up this pattern of module definition into a friendly API. Rather than examine any one particular library, let me present a very simple proof of concept for illustration purposes (only):

```js
var MyModule = (function Manager(){
	var modules = {};
	
 	function define(name, deps, tmpl){
 		for (var i = 0; i < deps.length; i++) {
 			deps[i] = modules[deps[i]];
 		};
 		modules[name] = impl.apply(impl, deps);

    function get(name){
    	return modules[name];
    }

    return {
    	define: define,
    	get: get
    }

})();

// foo module
MyModule.define('foo', [], function(){
	function hello(who){
		return "hello," + who; 
	}
	return {
		hello: hello
	}
})

MyModule.define('bar', ['foo'], function(foo){
	function awesome(){
		console.log(foo.hello().UpperCase());
	}
	return {
		awesome: awesome
	}
})


var foo = MyModule.get('foo');
var bar = MyModule.get('bar');

foo.hello('lp');
bar.awesome('lb');

```
