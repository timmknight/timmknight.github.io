---
title:  "Getting to grips with Closures"
date:   2016-07-30 19:18:00
description: JavaScript Closures are on of the most misunderstood and difficult concepts for beginners, so lets change that.
disqus: true
---

Closures are often used in JavaScript but something that beginners often struggle with. It's fair to say that there are some more experienced developers who don't really understand how closures work but often implement them. If you truly understand something you should be able to explain it to someone else, do you think you understand Closures enough to teach someone else? If not after this article certainly will.

The excellent Mozilla development documentation describes Closures as
> <a class='external' href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures" target="_blank">Closures are functions that refer to independent (free) variables. In other words, the function defined in the closure 'remembers' the environment in which it was created.</a>

This is decent summary, just so long as you already understand how Closures work.

In order to understand Closures you first need to have an understanding of <a class='external' href="http://timmknight.github.io/2015/first-class-functions-javascript/">First-Class Functions</a> and the <a class='external' href="http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/">Exection Context.</a>

In a lot of ways a Closure is essentially a function that returns another function. As functions are objects in JavaScript they can be returned.

{% highlight javascript %}
function saySomething(whattosay) {
	return function(name) {
		console.log(whattosay + ' ' + name);
	}
}
{% endhighlight %}

If you run the saySomething function you will see that you are returned the anonymous function.

{% highlight javascript %}
console.log(saySomething());

// returned anonymous function
function(name) {
	console.log(whattosay + ' ' + name);
}
{% endhighlight %}

Now that you can see a function is being returned we can simply invoke it the same way we would any other function.

{% highlight javascript %}
console.log(saySomething('Hi')('Tim'));
=> Hi Tim
{% endhighlight %}

We can see here that something unusual is happening, the anonymous function is able to see the whattosay variable that it isn't passed directly to it. Assigning the saySomething() function to a variable will allow us to examine whats going in more detail.

{% highlight javascript %}
var sayHello = saySomething('Hello');
{% endhighlight %}

Now we can say hello to anyone we like using

{% highlight javascript %}
sayHello('Paul');
=> Hello Paul
{% endhighlight %}

Whenever a function is executed an execution context is created, within this context variables are made available to that function. Here's an example to demonstrate this

{% highlight javascript %}
var bonjour = 'hello';
function num(n) {
	console.log(n);
}
console.log(bonjour);
num(1);
=> hello
=> 1

console.log(n);
Uncaught ReferenceError: n is not defined
{% endhighlight %}

When we run the script the first execution context that is created will be the global context which will contain the bonjour variable. This is the highest level and if you're running this in a browser the global exectuion context will be the window object.

Global execution context:

	- this
	- bonjour = 'hello'
	- num = function num(n)

The 'this' variable in the global execution context is the window.

When the JavaScript engine gets to the num(1); line another execution context will be created for the num function. Within this context the n variable will be available.

num() execution context:

	- this
	- n = 1
The 'this' variable in the num() execution context is the num function itself.

These execution contexts mean that the num() function has access to the n variable and the bonjour variable, because they're in the global context. If you think of the global context as being above the num context - the num function can reach up and access the bonjour variable. But the global  context cannot reach down into the num function and so you get the reference error when you try to access n from the global context. It's important to understand this and how the execution context makes variables avilable - this is known as <a class='external' href="http://www.amazon.co.uk/gp/product/B00IV3J2A2/?tag=timmknightgit-21">scope.</a> Essentially you can look up the scope chain to find variables but you cannot look down it. Global variables are availble everywhere because they are at the top of the scope chain.

Now we know a bit more about scope and execution context lets go back to our saySomething function. We assigned the variable sayHello to the saySomething function with the parameter 'Hello', so now all we need to do to is call sayHello(name) to say hello to anyone we want.

<img class="imagecenter" src="/assets/images/numcontext.png" alt="">

{% highlight javascript %}
function saySomething(whattosay) {
	return function(name) {
		console.log(whattosay + ' ' + name);
	}
}

sayHello('Paul');
=> Hello Paul
{% endhighlight %}

When we call the sayHello name what execution contexts do you think will be made available? We know that the global context will always be made available, as its created when you run any JavaScript program.


Global execution context:

	- this
	- sayHello = function(name)
	- saySomething = function saySomething(whattosay)

In the global context we have the this variable which is the window, the sayHello variable which points to a the saySomething function with the parameter 'Hello' and the saySomething variable which points to the saySomething function with no set parameter. This means we can invoke the sayHello & the saySomething functions becuase they're avaliable in the global context.

When the JavaScript engine gets to the sayHello('Paul'); line what context do you think will be created here?

saySomething execution context:

	- whattosay = 'Hello'

In this context we have the variable whattosay which is set to 'Hello', the anonymous function is also returned. After the anonymous function is returned the saySomething execution context is removed as it's finished and is no longer needed. Every exeuction context has a space in memory where the variables are stored, so even though the saySomething context has been removed the whattosay variable is still available to functions that were created within this context. Normally the JavaScript engine would clear the whattosay variable out through a process called garbage collection.

We now go back into the global execution context. When we hit the sayHello line, which is a function available in the global execution context it's exectuion context will be created

sayHello execution context:

	- name = 'Paul'

still available to functions created in the saySomething context:

	- whattosay = 'Hello'

Global execution context:

	- this
	- sayHello = function(name)
	- saySomething = function saySomething(whattosay)


So now as the JavaScript engine hits the console.log(whattosay + ' ' + name); the engine simply goes up the chain. The whattosay variable is not available in the sayHello Context so it looks up the chain for it - and guess what it finds it because it's still left over from the saySomething context. So even though the sayHello function doesn't have a whattosay variable in it's context it can look up through the scope chain until it finds it and use it.

<img class="imagecenter" src="/assets/images/saysomething.png" alt="">

The sayHello context still has a reference to its outer lexical environment and the variables within it. In other words even though the saySomething function's execution context has finished any functions created inside it will still have a reference to the saySomething's memory and the variables within it. So the sayHello function still has access to the whattosay variable because it's in the outer lexical environment. This is known as an execution context that has closed in its outer variables. Which is why this is called a Closure! The JavaScript engine is very clever and will always make sure that functions have access to the variables they need.

It's important to remember that you can only look up the scope chain and only within the functions lexical environment. You can't look down or across into another execution context.
