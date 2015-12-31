title: Event
date: 2015-07-29 18:44:03
tags: javascript
---
## 事件

### 1 事件冒泡

### 2 事件处理程序（事件侦听器）
#### 2.1 HTML Event Handler
```js
<input type="button" id ="btn" value="click me" onclick="alert(this.value)"> //this -> current element
```

```js
<input type="button" value="点击我" onclick="showMsg()">

<script type="text/javascript">
	function showMsg(){
		alert(event.target.value);
	}
</script>
```
* 缺点:
1) 时差问题 (解决：try catch)
```js
<input type="button" value="点击我" onclick="try{showMsg(); }catch(ex){}">
```
2) html 和 js 紧密耦合

#### 2.2 DOM0级 event handler
每个元素都有（包括 window , body）自己的事件处理程序属性,
DOM0 事件函数被认为是元素的方法, 所以函数作用域的该元素, 也就是说, handler中的this引用当前元素;
同时可以删除事件 handler
只能添加一个事件处理程序
```js
var btn = document.getElementById('btn');
btn.onclick = function(){
	alert(this.id); // btn
}
btn.onclick = null; // 删除事件处理程序
```

#### 2.3 DOM2级事件处理程序 
1 )所有DOM节点中都包含下面两个方法
* addEventListener()
* removeEventListener()
参数: 要处理的事件名, 事件处理函数, 布尔值(true: 捕获阶段调用处理函数; false:冒泡阶段调用处理函数)
2 ) 可以同时添加多个事件处理程序, 依次顺序触发
```js
btn.addEventListener('click', function(){ // 'click' , not 'onclick'
	alert(this.id); // this -> current element
},false);
btn.addEventListener('click', function(){
	alert('第二个事件处理函数');
}, false);
```
3) removeEventListener() 必须和 addEventListener事件处理程序相同
```js
var btn = document.getElementById('btn');
var handler = function(){
	alert(this.id); // btn
}
btn.addEventListener('click', handler, false);
btn.removeEventListener('click', handler, false);
```

#### 2.4 IE事件处理程序 
1) attachEvent()
2) detachEvent()
参数: 要处理的事件名, 事件处理函数 (IE8之前事件默认添加到冒泡阶段)
```js
var btn = document.getElementById('btn');
var handler = function(){
	alert(this == window); // this - > window 
}
btn.attachEvent('onclick', handler); // "onclick"
btn.detachEvent('onclick', handler);
```
3) 和DOM2区别  
* `on`click 
* 事件处理程序会在全局作用域下运行 this 指向 window
* 添加多个事件， 会以相反的顺序触发

##### 2.5 跨浏览器 event handler
```js
var EventHandler = {
    bind:function(el, ev, fn){
        if(window.addEventListener){ // modern browsers including IE9+
            el.addEventListener(ev, fn, false);
        } else if(window.attachEvent) { // IE8 and below
            el.attachEvent('on' + ev, fn);
        } else {
            el['on' + ev] = fn;
        }
    },
 
    unbind:function(el, ev, fn){
        if(window.removeEventListener){
            el.removeEventListener(ev, fn, false);
        } else if(window.detachEvent) {
            el.detachEvent('on' + ev, fn);
        } else {
            elem['on' + ev] = null; 
        }
    },
 
    stop:function(ev) {
        var e = ev || window.event;
        e.cancelBubble = true;
        if (e.stopPropagation) e.stopPropagation();
    }
}
```

### 事件对象 Event Object
#### DOM event object 
* event.type 事件类型	
* event.target vs event.currentTarget
```js
// click btn button
document.body.addEventListener('click', function(ev){
	console.log(ev.target); // btn 
	console.log(ev.currentTarget); //document.body
	console.log(this); // document.body
}, false);
```
* event.preventDefault() vs event.stopPropagation()

* event.eventPhase

#### IE event object
event 作为window的一个属性存在   var event = window.event;
ev.type // ev.type
ev.srcElement // ev.target
ev.returnValue == fasle; //  ev.preventDefault()
ev.cancelBubble == true; //  ev.stopPropagation()

#### 跨浏览器事件对象


#### 事件类型

	