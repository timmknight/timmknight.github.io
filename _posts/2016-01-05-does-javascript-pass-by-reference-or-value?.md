---
title:  "Does JavaScript pass by value or reference?"
date:   2016-01-05 19:18:00
description: Understand exactly how JavaScript passes variables
disqus: true
---

The super quick answer to this question is that primitive types such as strings, numbers and boolean are passed by value but objects and arrays are passed by reference.

However do you really understand what passing by value or reference really means? If you don't you can get confused when you're not getting the value you expect in your code. This is also the kind of question that will often be asked in an interview so it's really important to have a good understanding of the two terms.

<h3>Pass by Value</h3>

Passing by value means that you don't affect variables that are outside of your scope. Lets take a look at a quick example.

{% highlight javascript %}
var myValue = 1;
function passByValue(myValue){
	myValue = 2;
}
passByValue(myValue);
console.log(myValue);
=> 1
{% endhighlight %}

Here we create a variable called myValue and set it to 1. We then create the passByValue function which takes the myValue function and set it to equal 2. Finally we call the passByValue function passing it myValue and log the value of myValue to the console. The variable myValue is still one because primitives are passed by value. You're essentially creating a copy of myValue which is passed to the function. So anything that is done to myValue within the function will not change the myValue that is outside the function because of it's scope and the fact its passed by value.  

<h3>Pass by reference</h3>


Objects unlike primitive types are passed by reference. This means that a variable points to a reference of that object rather than a copy of that object. So if you were to change the value of that objects property this changes that object wherever it's referenced. Lets take a look at another example.

{% highlight javascript %}
var myObject = {};
function passByReference(myObject){
	myObject.num = 2;
}
passByReference(myObject);
console.log(myObject);
=> Object {num: 2}
{% endhighlight %}

Here we have an empty object with the really original name myObject. The passByReference function takes a parameter in this case the myObject and sets the property num to equal 2. The passByReference function is then called and the myObject is printed to the console.

We can see that myObject.num is 2. This is because myObject is passed to the function by reference rather than value. It's essentially telling the JavaScript engine where to find myObject in memory rather than copying it. It's pointing to the myObject obejct not a copy of it.

There is one final thing to look at with passing by reference so lets look at another example.

{% highlight javascript %}
var myObject = {'num': 2};
function passByReference(myObject){
	myObject = {'num': 1}
}
passByReference(myObject);
console.log(myObject);
=> Object {num: 2}
{% endhighlight %}

Here inside the function we are trying to change what myObject points to. In JavaScript when we pass by reference we can't then change what the myObject variable points to we can only alter the properties of the original object.

To sum up: primitives are passed by value. Objects and arrays (non-primitives) are passed by reference.
