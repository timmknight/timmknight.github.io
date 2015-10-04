---
title:  "Scraping the web with Node.js."
date:   2015-10-03 19:18:00
description: A web scraper that gets the price of an amazon product and send me an sms if it changes.
---
Scraping the web with node is really straight forward. I'm going to show you 
how to build a cool node app that will check the price of a book on 
<a target="_blank" href="http://www.amazon.co.uk/dp/1118531647/?tag=timmknightgit-21">Amazon</a> 
and send you a text message if it changes.

To start off we need to create two files: scraper.js and package.json.

The package.json file contains the name of the app and the main file it will run.
So my package.json looks like this:
{% highlight json %}
{
    "name": "amazonscraper",
    "main": "scraper.js"
}
{% endhighlight %}
Feel free to change the name of the porject and the file name. There are lots of optional fields you can put
in here such as your version and author but I'm going to stick to the basics.

With our package.json sorted lets go into scraper.js. The first thing we need to do in this file 
is require all the packages we are going to use.
{% highlight javascript %}
var fs = require('fs'),
    request = require('request'),
    cheerio = require('cheerio');
{% endhighlight %}

So for the app we are using FileSystem, Request and Cheerio. There is one more to come but 
we will get to that later. <a target="_blank" href="https://nodejs.org/api/fs.html">FileSystem</a> 
 allows you to easily deal with files in node. <a target="_blank"  href="https://github.com/request/request">Request</a> makes it simple to make http calls and
 <a target="_blank" href="https://github.com/Cheeriojs/Cheerio">Cheerio</a> allows you to use 
 jQuery on the server side. Now we need to install these and add them to our package.json:

{% highlight console %}
$ npm install request cheerio --save
{% endhighlight %}

The --save automatically adds them to your package.json which makes life easier. Your package.json should now look like this:

{% highlight json %}
{
	"name": "amazonscraper",
	"main": "scraper.js",
	"dependencies": {
	    "cheerio": "^0.19.0",
	    "request": "^2.64.0"
    }
}
{% endhighlight %}

Notice there is no FileSystem dependency that's becasue FileSystem comes with node. With our package.json all sorted out lets head into scraper.js.

Now we are set up lets think about how we are going to achieve the goal here. The are a few major steps in this project:


- Get the price of the amazon product.


- Compare this against the last price we had for that product.


- Send the text if it's changed.

So lets start by getting the price of the amazon product. The product I'm going to use 
is a really great book on 
<a target="_blank" href="http://www.amazon.co.uk/dp/1118531647/?tag=timmknightgit-21">JavaScript & jQuery.</a>
Our first aim is to get the price of the product. So the first thing we have to do is get the HTML for that
page. In scraper.js underneath the requires put:

{% highlight javascript %}
	url = 'http://www.amazon.co.uk/gp/product/1118531647';
	request(url, function(error, response, html) {
		console.log(html);
	});
{% endhighlight %}

Now to run the program simply type into the console

{% highlight console %}
$ nodemon scraper.js
{% endhighlight %}

If you get an error here you need to run:

{% highlight console %}
$ npm install nodemon -g
{% endhighlight %}

Nodemon is a great package that will run your node server and restart it when your file changes.

If you check out your console now you should see the HTML has been printed out.
That was pretty simple, all we needed to do was set the 'url' variable to our link, 
use request to go to the url, pass in a callback to log the html to the console. 

So now we have got the HTML for the page we can use Cheerio to grab the price.
 Cheerio allows us to use jQuery selectors to navigate through the HTML we have.

If we inspect the page and look at the price section we see that is has a span with the class 'inlineBlock-display' which contains a span with the class 'a-color-price'. 

<img class="imagecenter" src="/assets/images/amazonprice.png" style="height:236px;width:428px;" alt="">

This will allow us to select this elements text using jQuery thanks to Cheerio. So this will give us the price for us to use.


{% highlight javascript %}
request(url, function(error, response, html) {
	if (!error && response.statusCode == 200) {
		var $ = cheerio.load(html);
		$('span.inlineBlock-display span.a-color-price').each(function(i, element) {
			var el = $(this);
			var price = el.text();
			console.log(price);
		})
	}
});
{% endhighlight %}

So here I've added some validation in to check that we don't get an error and that we get a statusCode of 200 which means success. I then load
the html into Cheerio which allows me to use jQuery selectors, so I'm selecting a span with class "inlineBlock-display" and within that a span with class "a-color-price".
I select that element, set it to the variable el and then grab the text and set it
to the price variable and log it to the console. 

Now we have the price lets save it into a json file so we can compare it when we scrape the price again. 

{% highlight javascript %}
request(url, function(error, response, html) {
    var price;
    var json = {
        price: ""
    };
    if (!error && response.statusCode == 200) {
        var $ = cheerio.load(html);
        $('span.inlineBlock-display span.a-color-price').each(function(i, element) {
            var el = $(this);
            var price = el.text();
            json.price = price;

            fs.writeFile('price.json', JSON.stringify(json, null, 4), function(err) {
                console.log('Price saved in price.json file');
            });
        })
    }
});
{% endhighlight %}

Firstly we create our price variable, then we create our JSON object which stores the price, which is blank at present.
 We then set our price variable to equal the scrapped text
we then add this to our json. Then we create our JSON with node fs libary
 using the writeFile function which take three parameters.


1: The name of the file.


2: JSON.stringify which makes our JSON easier to read. It itself takes three parameters: the data, a replacer which we set to null, and the space which will be the whitespace used in the file.


3: A callback function to let us know the status of our function.

So if you've still got nodemon running a price.json should have been created and look like this:
{% highlight json %}
{
    "price": "£18.35"
}
{% endhighlight %}

The price might be different, but luckily we are making an app that we text you if it does change.

Now lets compare a newly scrapped price against our current price. The best thing to do is to go into your
JSON file and change the price yourself so you can easily test it.
{% highlight javascript %}
fs.readFile('price.json', function(err, data) {
    if (err) throw err;
    var obj = JSON.parse(data);
    if (obj.price != price) {
        console.log('Price has changed.');
        fs.writeFile('price.json', JSON.stringify(json, null, 4), function(err) {
            console.log('Price saved in price.json file');
        });
    }
})
{% endhighlight %}

So here we read the price.json file and if we get an error log it to the console. Then we parse the JSON into an object
so we can access the data, obj.price access the price in the JSON file. The variable obj being the JSON object. Then we check if the value
in the JSON differs from the value we have just scraped which is stored in the price variable. If it is different we console.log a message
and then save the new price into the JSON. So go ahead and change the price in your price.json and run the program again, nodemon won't look for 
changes in anything other than scraper.js. You can just save scraper.js without any changes rahter than,
restarting nodemone. You should see the 'Price has changed.' message, now go ahead and check the JSON file. The price should now be the same as on <a target="_blank" href="http://www.amazon.co.uk/dp/1118531647/?tag=timmknightgit-21">Amazon</a>.

Now we have everything in place we can send a text if the price changes. To do this we will use a service called <a target="_blank" href="https://www.twilio.com/">Twilio</a> it's free for developers to test with so don't worry about the cost.
So head over to <a target="_blank" href="https://www.twilio.com/">Twilio</a> and register an account. You will need to give Twilio your mobile number so they can verify it, this allows you to send free messages to yourself. Once you've made an account and verified your number go to your account
settings page. In your account setting you will have a section called API credentials with two boxes Live and Test. We will be using the test for this. In scraper.js we need to require Twilio. 

{% highlight javascript %}
var accountSid = 'SECRET';
var authToken  = 'SECRET';

var fs = require('fs'),
    request = require('request'),
    Cheerio = require('Cheerio'),
    client = require('twilio')(accountSid, authToken);
{% endhighlight %}

You will notice that in the require I have SECRET, SECRET. These are my API credentials, you will need to put your own in.
The first SECRET being your Test AccountSID, the second SECRET being your Test AuthToken. You get both of these from the account settings page on Twilio. Now lets install Twilio.


{% highlight console %}
$ npm install twilio --save
{% endhighlight %}

Now to send a message with the change in price all we need to do it 
{% highlight javascript %}
if (obj.price != price) {
	console.log('Price has changed sending text!');

	client.messages.create({
	from: 'SECRET', //Your Twilio number
	to: 'SECRET', //Your phone number
	body: "The price has changed to: " + price + " from " + obj.price
	});

	fs.writeFile('price.json', JSON.stringify(json, null, 4), function(err) {
	console.log('Price saved in price.json file');
	});
}
{% endhighlight %}

Now change the price in your price.json and save your server.js or restart nodemon.


Now we obviously don't want to have to save the scraper.js file or restart nodemon everytime we want to check the price. We could just check the product page after all.
So lets fix that, so we run the scrape every hour.
{% highlight javascript %}
var mins = 60,
    the_interval = mins * 60 * 1000;
setInterval(function() {
	//......
}, the_interval);
{% endhighlight %}

With this all in place every hour your file will scrape Amazon an text you if the price changes.



