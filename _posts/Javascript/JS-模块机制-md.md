title: 'JS:模块机制.md'
date: 2015-07-03 10:38:23
categories: [编程]
tags: [javascript, file module loader] 
---
[Include a JavaScript file in another JavaScript file?](http://stackoverflow.com/questions/950087/include-a-javascript-file-in-another-javascript-file?page=1&tab=votes#tab-top)


JavaScript has no import, include, or require. There are other ways for JavaScript to include external JavaScript contents, though.

**Ajax Loading**
Load an additional script with an Ajax call and then use `eval`. This is the most straightforward way, but it is limited to your domain because of the JavaScript sandbox security model. Using eval also opens the door to bugs and hacks.

**Jquery Loading**
The jQuery library provides loading functionality in one line:

```javascript
$.getScript("my_lovely_script.js", function(){
  alert("Script loaded but not necessarily executed.");	
});
```

**Dynamic Script Loading**
Add a script tag with the script URL in the HTML. To avoid the overhead of jQuery, this is an ideal solution.

The script can even reside on a different server. Furthermore, the browser evaluates the code. The `<script>` tag can be injected into either the web page `<head>`, or inserted just before the closing `</body>` tag.

Both of these solutions are discussed and illustrated in [*JavaScript Madness: Dynamic Script Loading*](http://unixpapa.com/js/dyna.html).

**Detecting when the script has been executed**
Now, there is a big issue you must know about. Doing that implies that you remotely load the code. Modern web browsers will load the file and keep executing your current script because they load everything asynchronously to improve performance. (This applies to both the jQuery method and the manual dynamic script loading method.)

For example: `my_lovely_script.js` contains `MySuperObject`:

```javascript
var js = document.createElement("script");	

js.type = "text/javascript";
js.src = jsFilePath;

document.body.appendChild(js);

var s = new MySuperObject();

Error : MySuperObject is undefined
```
Then you reload the page hitting `F5`. And it works! Confusing...
###So what to do about it ?
Well, you can use the hack the author suggests in the link I gave you. In summary, for people in a hurry, he uses en event to run a callback function when the script is loaded. So you can put all the code using the remote library in the callback function. For example:
```javascript
function loadScript(url, callback){
	// Adding the script tag to the head as suggested before
	var head = 	document.getElementsByTagName("head")[0];
	var script = document.createElement('script');
	script.type = 'text/javascript';
	script.src = url;

	// Then bind the event to the callback function.
    // There are several events for cross browser compatibility.
    script.onreadystatechange = callback;
    script.onload = callback;

    // Fire the loading
    head.appendChild(script);
}
```

Then you write the code you want to use AFTER the script is loaded in a lambda function: