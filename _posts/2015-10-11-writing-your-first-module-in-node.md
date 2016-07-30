---
title:  "Writing your first module in Node.js"
date:   2015-10-11 19:18:00
description: Really understand modules in Node.js by writing your own and looking deeper into how require and module.exports work
---

Modules are new to JavaScript and are now apart of the <a class='external' href="http://www.ecma-international.org/ecma-262/6.0/">ECMAScript 2015 (ES6) standard.</a> Modules are used a lot in Node 
so it's really important that you understand how they work. A module is simply a reusable block of code whose existance does not accidentally impact upon other code. This basically means that you can have two modules which both use the same variables and they won't clash. JavaScript uses <a class='external' href="http://www.commonjs.org/">CommonJS</a> modules which are just an agreed upon standard for how code modules should be structured. 

Lets start by writing our own very simple module so we can get an overview of how they work and are used. You need two files for this so go ahead and navigate to the directory you want to use for this tutorial and create the two files.

{% highlight console %}
$ touch app.js hello.js
{% endhighlight %}

The module we are going to write is going to be very simple and just console.log 'Hello', not very original I know. So in hello.js lets simply

{% highlight javascript %}
console.log('hello');
{% endhighlight %}

Now we have written the simply console.log lets require it in our app.js
{% highlight javascript %}
require('./hello.js');
{% endhighlight %}
Require is a core node function that takes a string which represent the location of the file we want to use. The './' simply means in the same directory as the current file. app.js and hello.js are in the same directory in our case, so we use ./. Now if we invoke the hello function in app.js what do you think will happen?

{% highlight javascript %}
hello();
{% endhighlight %}

Go ahead and run this using

{% highlight console %}
$ node app.js
{% endhighlight %}


Uh oh! We get a Refernce Error that hello is not defined. Why is this? Well this happens because we haven't exported our code, it's not a module yet. If we remember what a module is we realise that this is a good thing. Remember that a modules code should not unintentionally affect other code. So here because we haven't exported the hello function is doesn't exist in app.js, this means we could have our own hello function in app.js that would work as we intended it to. We can have variables with the same name in different files because modules are self contained. At the moment we simply have two JavaScript files one with the console.log and one with the require and the function call. The problem here is that hello.js isn't exporting anything so app.js can't use it. Lets fix this so we can use the hello function in app.js.

Lets wrap our console.log in a function expression.

{% highlight javascript %}
var hello = function(){
	console.log('hello');
}
{% endhighlight %}

Now in our hello.js lets export the module using module.exports.

{% highlight javascript %}
module.exports = hello;
{% endhighlight %}

So now if we run app.js we will see 'hello' is logged to the console. What's happening here? Well the module.exports = hello; line will return the result of the hello function. So wherever we require this file and invoke the function we will see 'hello' being logged to the console. Require is just a function that you pass a path too, when you set it to a variable it means you can call the function in that file, remember that functions are first-class in JavaScript so we can pass functions as variables.

What do we do then if we want to have our own hello function in app.js? Well lets start off by writing that function.
{% highlight javascript %}
var hello = require('./hello.js');

var hello = function(){
	console.log("I'm the hello function in app.js!");
};

hello();
{% endhighlight %}

What do you think is going to happen here? What happens here is we overwrite the requires hello function with the hello function in app.js. We haven't overwritten the hello function in hello.js only the variable that returns this function. To fix this all we have to do is rename the require variable and we can use both of the hello functions in the same file.
{% highlight javascript %}
var hi = require('./hello.js');

var hello = function(){
	console.log("I'm the hello function in app.js!");
};

hello();
hi();
{% endhighlight %}

And now we see that we can use both of the functions with no conflict.

{% highlight console %}
I'm the hello function in app.js!
hello
{% endhighlight %}

So all you have to do to make a files function available to another file is module.export that function and require it in another file. But what is really going on in Node when we do this?


We are about to go deep into Node here so hold on tight and keep yours arms in the vehicle at all times! Lets step through the program with a debugger. I'm using <a class='external' href="https://code.visualstudio.com/">Visual Studio Code</a>, it's free and runs on both Windows & Mac.

<img class="imagecenter" src="/assets/images/debug1.png" alt="">


You can see here that when the require is called we have lots of variables that are made avaliable to us. You can see we have the directory name, the filename, exports which is an empty object, the hello variable itself, module, require which is a core node function and this.


<img class="imagecenter" src="/assets/images/debug2.png" alt="">

When we step inside the require function we head into the Node core, into a file called <a class='external' href="https://github.com/nodejs/node/blob/master/lib/module.js">module.js.</a> You can see here that require is just a function that takes a path as the argument. This require function just returns what comes back from the real require function. This is exactly how we use require in our app.js.

<img class="imagecenter" src="/assets/images/debug3.png" alt="">

You can see here that require is a prototype of Module. So every module you create gets an object created to represent it and that object has access to a require method through prototypal inheritence. This require function is just a wrapper for the load function, you see that in line 365.

Lets step into that load function.

<img class="imagecenter" src="/assets/images/debug3.1.png" alt="">

This is where we can start to see what is happening. You can see that the filename variable is being set here in line 286.
There is a lot going on here which we don't need to know about so let's move on.



<img class="imagecenter" src="/assets/images/debug4.png" alt="">

You can see here that the object that represents my hello module is being created here with the new constructor.



<img class="imagecenter" src="/assets/images/debug3.2.png" style="height:126px width:274px" alt="">


 Now we have this module object we can see that it has the exports object which is an empty object. So when you use module.exports, you are really asking the module object to give you its exports property. This is the same way you access a functions name using the .name that we saw in my <a class='external' href="http://timmknight.github.io/2015/first-class-functions-javascript/">article about first-class functions.</a>

Going further on we are actually going to try and load that file. In our case hello.js. Remember that the filename variable was set eariler.

<img class="imagecenter" src="/assets/images/debug3.4.png" alt="">

With our file loaded lets move on a bit more.

<img class="imagecenter" src="/assets/images/debug5.png" alt="">

This is an interesting part. When we require a file we don't actually have to put '.js' at the end of because Node will assume you are requiring a JavaScript file. So we could just

{% highlight javascript %}
var hi = require('./hello');
{% endhighlight %}

and Node will assume we mean hello.js. This does mean however that if we want a non-js file we will have to add .json etc. You can start to see that what is going on in Node isn't all magic.


Now we get to the point where we are going to compile our code through <a class='external' href="https://code.google.com/p/v8/">Google's V8 engine.</a> We can see that compile takes in a content variable which you can see contains our code.

<img class="imagecenter" src="/assets/images/debugcompile.png" alt="">

<img class="imagecenter" src="/assets/images/debugcompile1.png" alt="">

If we go into the compile function we can see that eventually our code is put into a wrapper. This allows Node.js to do things to it. So Node dosen't just pass our hello function to the V8 engine it pass a lot more which is what allows use to use Node's awesome features!




<img class="imagecenter" src="/assets/images/debugcompile2.png" alt="">

If we move on into the wrapper.

<img class="imagecenter" src="/assets/images/debugcompile3.png" alt="">

We can see what's going on. The script variable is our script from hello.js. The NativeModule.wrapper[0] is the string that is highlighted and NativeModule.wrapper[1] is the rest of the string. This is what gets passed to the V8 engine. A string with our code in it. The string has a function which contains our code.



This means that EVERYTHING we write in Node is run in V8 inside a function.



If we keep going eventually we get into where our code is run. At this point module.exports still doesn't equal our code. But we now have the return of our code.

<img class="imagecenter" src="/assets/images/debug8.png" alt="">



But if we keep going we will get to this point.

<img class="imagecenter" src="/assets/images/debug6.png" alt="">

Where module.exports is being returned. The exports property on the module object, because remember module is an object with an exports property. This is the thing that is being changed when we call the require function.

And eventually you get to the point where require has finished. It's gone and got our hello.js, loaded the contents, wrapped it, run it and then returned it on the moduel.exports property which is our function.

So we now run the hello modules function.

<img class="imagecenter" src="/assets/images/debug7.png" alt="">

And now we will see

{% highlight console %}
hello
{% endhighlight %}


Take a deep breath as we have done a lot here. Essentially what happens is Node changes what module.exports returns! All require does is give you back whatever you change module.exports to. See it really isn't that difficult!

- You pass a path to the require function



- The path being your file which will contain the code you want to use

- module.exports is what the require function then returns


- Which is what allows you to use the code in your file
