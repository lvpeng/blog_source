title: 'this(context) && scope'
date: 2015-07-08 12:46:11
categories: 编程
tags: [javascript, this , context]
---
```js
//https://www.youtube.com/watch?v=fjJoX9F_F5g
//http://stackoverflow.com/questions/15455009/js-call-apply-vs-bind
//http://m.51cto.com/?l=3&ref=www_iphone&order=4&dict=22&tj=www_normal_4_0_10_title&cltj=cloud_title&src=http%3A%2F%2Fdeveloper.51cto.com%2Fart%2F201503%2F466978.htm#m/http://developer.51cto.com/art/201503/466978.htm
//http://stackoverflow.com/questions/3889570/what-is-the-difference-between-this-this-and-this

// context === this

console.log(this); // root scope is window	
////////////////////////////////

function foo(){
	console.log(this); 
}

foo(); // equal to window.foo();

///////////////////////////////

var obj = {
	foo: function(){
		console.log(this);
	}
};

obj.foo();  // Object

///////////////////////////////
//call , apply , bind : set 'this'(fn of context) -- > change context dynamic
var obj = {
	foo : function(one, two, three){
		console.log(this === window);
	}
};
obj.foo.call(window, 1,2,3);
obj.foo.apply(window, [1,2,3]);


Function.prototype.bind = function(ctx) {
	console.log(this);
    var fn = this;
    return function() {
    	console.log(this);
        fn.apply(ctx,arguments);
    };
};
	
var bindFoo = obj.foo.bind(window);
bindFoo();

///////////////////////////////

// $("li").on('click', function(){
// 	console.log(this); //not $(this) !!
// })

$("li").on("click", function(){
	var currentTimes = parseInt($(this).find("span").html());
	$(this).find("span").html(currentTimes+1);
})
```
