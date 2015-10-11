---
title:  "First-Class Functions and Function Expressions in JavaScript"
date:   2015-10-10 19:18:00
description: Looking at first-class functions and function expressions in JavaScript to get a deeper understanding
---

In JavaScript functions are first-class. This mean that with functions you can do everything you can do with other types such as assigning them to variables.



Wikipeida on first-class functions:
> <a href="https://en.wikipedia.org/wiki/First-class_function">In computer science, a programming language is said to support first-class functions (or function literal) if it treats functions as first-class objects. Specifically, this means that the language supports constructing new functions during the execution of a program, storing them in data structures, passing them as arguments to other functions, and returning them as the values of other functions.</a>

In JavaScript functions are objects. So when you 
create a function you are actually creating an object. This object will have certain properties one of which will be the function. Lets take a look at this.

Lets first make a simple function statement. You've all seen function statements, it's one of the first things you will learn in JavaScript. Functions Statements don't result in a value until they are exectued. Function statements are also known as function declarations so if you see this don't get confused.

{% highlight javascript %}
// function statement aka function declaration
function hello() {
	console.log('Howdie');
}
{% endhighlight %}

If you ran this nothing would happen because it's not been invoked. Remeber function statments don't return a value until they are exectuted. If we were to invoke this we would be creating an object which has some properties one of which will be the 'hello' function. So rather than calling the hello function, which will just console.log 'Howdie', lets log the function itself.

{% highlight javascript %}
console.log(hello);
=> function hello() {
	console.log('Howdie');
}
{% endhighlight %}
If you run this in a browser you will see the function itself being returned. Now remeber that functions are objects which have properties. So lets see one of those properties.

{% highlight javascript %}
console.log(hello.name);
=> hello
{% endhighlight %}
function.name is a now part of the <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name">ECMAScript 2015 (ES6) standard.</a> But it has been around in a JavaScript a while. This name value is always equivalent to the identifier that follows the function keyword.

Lets add another property 
{% highlight javascript %}
hello.language = 'English';
console.log(hello.language);
=> English
{% endhighlight %}

To really prove that functions are objects lets use Node.Js to show the object itself. Change the file to just console.log(hello). Run your file using

{% highlight console %}
$ node filename.js
{% endhighlight %}

What you will now see in the console is the object itself

{% highlight console %}
{ [Function: hello] language: 'English'}
{% endhighlight %}



So again we see that functions are object that have properties. One of those properties being the function itself which you can invoke. This is very powerful because it means functions are first-class and we can do lots of cool things with them. Lets take a look at some of the ways functions are used in JavaScript. You've already seen function statements, these are pulled into memory when it's execution context is created. This means that they can be called before they're created. This is known as hoisting or more techinally function declaration hoisting. 

Or as Nicholas Zakas put it in his fantastic book: 

> <a href="http://www.amazon.com/gp/product/1118026691/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1118026691&linkCode=as2&tag=blog0a05a-20&linkId=6KJBKAOWF36GTSIU">"This doesn't throw an error because the function declaration is read first before the code begins to exectue."</a>
{% highlight javascript %}
hello();

function hello() {
	console.log('Hello');
}

=> Hello
{% endhighlight %}

You've most likely seen function expressions as well. An expression is a unit of code that returns a value.
{% highlight javascript %}
a = 1;
=> 1
{% endhighlight %}

A function expression is a block of code that results in a value.

{% highlight javascript %}
var logNum = function(){
	console.log(1);
}
logNum();
=> 1 
{% endhighlight %}

Here a function is created and assigned to the variable logNum. Remeber that functions can do anything other types in JavaScript can do. Now this function is considered an anonymous function because it has no name. So if you were to 

{% highlight javascript %}
console.log(logNum.name);
{% endhighlight %}

You would get an empty string back - it's anonymous. When you run a JavaScript program all variables are first set to undefined. Only once they are assigned in the program are they assigned a value.

{% highlight javascript %}
logNum();
var logNum = function(){
	console.log(1);
}
{% endhighlight %}

This will cause an error 

{% highlight console %}
TypeError: logNum is not a function
{% endhighlight %}

This is because when JavaScript runs it is run line by line, so when the program gets to logNum(); it is undefined. Only once we assign the logNum variable to the function does it point to the function.

Finally lets pass a function as a variable.
{% highlight javascript %}
function logText(func) {
	func();
}
{% endhighlight %}

Here the variable func is going to be a function. So the parameter we pass to logText will be a function itself, we can do this because functions are first-class. We then invoke the function within the logText function. So lets give this a go.

{% highlight javascript %}
logText( function (){
	console.log('Functions are first-class in JavaScript!');
});
{% endhighlight %}

And I'm sure you know what we are going to get back here.
{% highlight console %}
Functions are first-class in JavaScript!
{% endhighlight %}

So lets summarise first-class functions:

Just like other variables in JavaScript functions 

- are an instance of the Object type

- have propeties and a link back to its constructor method

- can be stored in a variable

- can be passed as a parameter to another function
