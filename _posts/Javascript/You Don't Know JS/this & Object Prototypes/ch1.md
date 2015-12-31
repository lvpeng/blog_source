title: 'this'
date: 2015-07-26 14:46:11
categories: 编程
tags: [javascript, this , context]
---
this
  : automatically defined in the scope of every function
  what it refers to : context
### WHY this ?
var me = {
  name : "peng"
};

function identity(){
  return this.name.toUpperCase();
}
identity.call(me)

//or we can also do like this
function identify(ctx){
  return ctx.name.toUpperCase();
}
identify(me)

* however , this mechanism provides a more elegant way of passing an object reference, leading to cleaner API design and easier re-use *

this -> context -> scope


### to review js by reading underscore.js library
