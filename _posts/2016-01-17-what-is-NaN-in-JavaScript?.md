---
title:  "What is the NaN type in JavaScript"
date:   2016-01-17 19:18:00
description: Understand exactly what NaN is and how to check for it
disqus: true
---

Unsurprisingly NaN stands for not a number, so I'm sure you can guess what it is supposed  to represent. There are however, a few
oddities when it comes to NaN so it's not as simple as it seems at first glance. Time to head into the JavaScript console and
check NaN out in a bit more detail.

{% highlight javascript %}
NaN
=> NaN
{% endhighlight %}

So NaN itself will return the property of NaN, which is what you would expect. What would you expect the typeof(NaN) to be?

{% highlight javascript %}
typeof(NaN)
=> "number"
{% endhighlight %}

Remember how I said NaN isn't as simple as it seems? The type of NaN is actually a number which is really strange given it's name. NaN is mainly used to define a number that isn't a number, that doesn't really make sense but lets look at an example.

{% highlight javascript %}
"hello"/10
=> NaN
{% endhighlight %}

So you can see here that if we try to perform a mathematical operation on a string such as "hello" the result will be NaN because the string cannot be implicitly coerced to a number. Implicit conversion is when the JavaScript engine attempts to convert things to the correct type. If you had the string "1" and attempted to perform a mathematical operation on it the JavaScript engine will try and convert that string into a number.

{% highlight javascript %}
"1"/10
=> 0.1
{% endhighlight %}

You can see here that the string "1" is converted into a number whereas "hello" cannot be converted into a number so the result is NaN.

One of the most confusing things about NaN is how is compares to other types in JavaScript. You would expect NaN to only return true when comparing it with types other than numbers. However in JavaScript when you compare NaN against anything it will return false.

{% highlight javascript %}
NaN == 1234
=> false

NaN == true
=> false

NaN == false
=> false

NaN == "Hello World!"
=> false
{% endhighlight %}

This even happens when you compare NaN against NaN. Which can often be really confusing as you would expect NaN == NaN to be true.  NaN != NaN, remember I did say NaN is a bit odd.

{% highlight javascript %}
NaN == NaN
=> false
{% endhighlight %}

So what do you do if you want to check if something is not a number?

JavaScript has an inbuilt method which isn't exactly perfect...

{% highlight javascript %}
isNaN(NaN)
=> true

isNaN(100)
=> false

isNaN("100")
=> false
{% endhighlight %}

These examples return exactly what we would expect, for the isNaN("100") the string "100" is being implicitly coerced into the number 100, so we are essentially doing isNaN(100). Let's try this again with a string that cannot be coerced into a number.

{% highlight javascript %}
isNaN("hello")
=> true
{% endhighlight %}

Which is a bit odd because NaN and the string "hello" are not the same type. What is actually happening here is "hello" is being coerced into a number by the JavaScript engine.

{% highlight javascript %}
Number("100")
=> 100

Number("hello")
=> NaN
{% endhighlight %}

So when we call isNaN("hello") what is actually happening is
{% highlight javascript %}
isNaN(Number("hello"))
=> true
{% endhighlight %}

or rather

{% highlight javascript %}
isNaN(NaN)
=> true
{% endhighlight %}

Which is a bit confusing and also makes the isNaN function rather useless. As it may return true if the parameter is a string, which is rather unhelpful. Fear not though as there is a way to tell if something is NaN in JavaScript!

So how do we check for NaN?

The best way to check for NaN is to use the fact that NaN is the only type in JavaScript that is treated unequal to itself. Lets check that out.

{% highlight javascript %}
var notANumber = NaN

notANumber !== notANumber
=> true
{% endhighlight %}

Here we are checking that the variable notANumber does not equal itself, so as NaN == NaN returns false we can check that NaN !== NaN and the return value will be true, it's not a number.

{% highlight javascript %}
var aNumber = 999

aNumber !== aNumber
=> false
{% endhighlight %}

aNumber is a number so asking if it does not equal itself will return false.

{% highlight javascript %}
var aString = "Hello World"

aString !== aString
=> false
{% endhighlight %}


{% highlight javascript %}
var aCoercedNumber = "111"

aCoercedNumber !== aCoercedNumber
=> false
{% endhighlight %}

So anything that compares against itself using the !== operator will return false expect NaN which will return true.
{% highlight javascript %}
var notANumber = NaN

notANumber !== notANumber
=> true
{% endhighlight %}

So because of the quirk that NaN does not equal NaN we can determine if something is NaN using !==, as unless it is NaN it will return false.
