---
title:  "Multiples of 3 & 5"
date:   2015-07-11 19:18:00
description: Project Euler problem 1
---

In this series of posts I'm going to work through some of the challenges on <a class='external' href="http://projecteuler.net" target="_blank">Project Euler</a> in Ruby and JavaScript. Project Euler is a great resource for those who are learning to code or for those looking to tune up their skills.
When I was first learning to code one of the biggest challenges I faced was translating my new found knowledge from sites such as <a class='external' href="https://teamtreehouse.com" target="_blank">Teamtreehouse</a>, <a class='external' href="http://codeschool.com/" target="_blank">Code School</a>  and <a class='external' href="http://lynda.com/" target="_blank">Lynda</a> 
to solving real problems. These sites are great for absolute beginners to pick up the syntax and basic principles and even experienced developers looking to learn something new.

However for those who have enough knowledge of the syntax but no real experience the biggest issue with these
sites is the lack of projects or problems to solve with your new skills. Project Euler is a great companion to these sites as you can put your skills to the test by solving problems they have to offer.

Ask anyone how they learnt a new skill and they will all say the same thing: practice, practice and more practice. The old mantra *"practice makes perfect"* really does ring true for learning to code.
The more you can put your skills to practice by solving problems the more you will develop. I highly encourage everyone who is learning to code to take on these small challenges, some are simple, others will really test you.
The important thing when taking on these challenges is to not give up, you will struggle at times. Take a small break, go for a walk, come back and try again. If you still can't solve the problem Google will always be there for
you to fall back on.


The first challenge is the <a class='external' href="http://projecteuler.net/problem=1" target="_blank">Multiples of 3 and 5</a>.

>If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23. Find the sum of all the multiples of 3 or 5 below 1000.

The problem is very similar to the classic <a class='external' href="http://c2.com/cgi/wiki?FizzBuzzTest" target="_blank">FizzBuzz</a> problem that seems to be a really popular interview question.

Ruby
{% highlight ruby %}
count = 0

(1...1000).each do |i|

	if i % 3 == 0 && i % 5 == 0
		count += i
	elsif i % 5 == 0
		count += i
	elsif i % 3 == 0
		count += i
	end		
end

puts count
#=> 233168
{% endhighlight %}

JavaScript
{% highlight javascript %}
var count = 0

for (var i = 0; i < 1000; i++) {
	if (i % 3 == 0 && i % 5 ==0) {
		count += i;
	}
	else if(i % 5 == 0){
		count += i;
	}
	else if(i % 3 == 0){
		count += i
	}
};

console.log(count);
//=> 233168
{% endhighlight %}

As you can see the way I solved the problem is the same in both languages.

My thought process was as follows:

-Define the count variable, this will be the running sum.

-Loop through the numbers.

-Check to see if the current number (i) divided by 3 and 5 has nothing left over, if that's true and i to the count.

-Check to see if i divided by 5 has nothing left over, if true add i to the count.

-Check to see if i divided by 3 has nothing left over, if true add i to the count.

-Puts/console.log the count. The return is implicit is Ruby and JavaScript so there is no need to "return count".


And that's that! The problem itself is really simple and a nice problem for beginners to tackle. If you've got an interesting solution feel free to share it in the comments.
