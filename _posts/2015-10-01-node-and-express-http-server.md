---
title:  "HTTP server with Node.JS and Express"
date:   2015-10-01 19:18:00
description: Building a HTTP server with Node.JS and Express
---

<a class='external' href="https://nodejs.org" target="_blank">Node.js</a> is a great open-source runtime environment for developing server-side web applications in JavaScript. Node is great, it's the 'in' thing at the moment and is being used by a lot of companies. <a class='external' href="http://venturebeat.com/2012/01/24/why-walmart-is-using-node-js/">Walmart, </a><a class='external' href="https://www.paypal-engineering.com/2013/11/22/node-js-at-paypal/" target="_blank">Paypal, </a><a class='external' href="https://azure.microsoft.com/en-us/develop/nodejs/" target="_blank">Microsoft, </a><a class='external' href="http://venturebeat.com/2011/08/16/linkedin-node/" target="_blank">LinkedIn, </a><a class='external' href="http://venturebeat.com/2012/01/24/node-at-google-mozilla-yahoo/" target="_blank">Google, Mozilla & Yahoo</a> have all found cases to use it. 

There are a lot of use cases for Node.js but it shouldn't be used for every task you think of. It's not something you'd want to use for CPU-Intensive operations as this will annul most of it's advantages and there are far better alternatives. You should always pick the best tool for the job rather than a new cool tool or the tool you're best as using. The use cases where Node has really become one go tos is for building fast, scalable network applicaitons. Looking at the <a class='external' href="https://blog.risingstack.com/what-is-nodejs-used-for-the-2015-nodejs-overview-report/" target="_blank">RisingStack's blog</a> overview of Node.js in 2015 you can see that it's being used a lot for creating REST APIs, real-time services, microservices and tooling. 

Node is an asynchronous, event-drive framework that has been built upon Chrome's JavaScript engine. Under the hood it's a lot of C/C++ but this isn't something we need to focus on. Node is single-threaded and uses a concurrency model based on an event loop. It is non-blocking, so you don't have to wait for a task to be completed before starting a new one, but instead it registers a callback and lets the program continue. This means it can handle concurrent operations without multiple threads of execution, so it can scale pretty well.

With that brief summary lets take a look at building a HTTP server with Node.js.

To install Node.js if you don't already have it simply go to the <a class='external' href="https://nodejs.org/en/download/" target="_blank">Node.js</a> website and get the installer.

For the simple HTTP server you'll need three files: index.html, package.json and server.js. Put these all in the same directory.

The index.html file will be the file we are going to serve up.

{% highlight html %}
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>HTTP server with Node.js</title>
</head>
<body>
	<h1>Hello World!</h1>
</body>
</html>
{% endhighlight %}

package.json defines the name of our node app and the file that will be run.
{% highlight json %}
{
	"name": "http-server",
	"main": "server.js"
}
{% endhighlight %}

Our server.js file with plain node.js.
{% highlight javascript %}
//require http & filesystem modules
var http = require('http'),
    fs   = require('fs');

//create server
http.createServer( function (request, response) {

	//write to our server & set config
	response.writeHead(200, {
		'Content-Type': 'text/html',
		'Access-Control-Allow-Origin' : '*'
		});

	//get the index.html file
	var readStream = fs.createReadStream(__dirname +'/index.html');

	//send index.html to the client
	readStream.pipe(response);
}).listen(2000);
{% endhighlight %}

In your terminal navigate to the directory you created the files in and type in
{% highlight console %}
 $ node server.js
{% endhighlight %}


Now you can navigate to <a class='external' href="http://localhost:2000" target="_blank">localhost:2000</a> and see the server in action! Now that was nice and easy, but it gets even easier with the popular minimalist framework express. 

To install express simply
{% highlight console %}
 $ npm install express
{% endhighlight %}

Now for our express server. In server.js go ahead and replace all the code with
{% highlight javascript %}
//require expess & path
var express = require('express'),
    app     = express(),
    path    = require('path');

	//send index.html to the client
	app.get('/', function (request, response) {
		response.sendFile(path.join(__dirname + '/index.hmtl'));
	});

	app.listen(2000);
{% endhighlight %}

Go ahead and
{% highlight console %}
 $ node server.js
{% endhighlight %}

Then refresh <a class='external' href="http://localhost:2000" target="_blank">localhost:2000</a> and that's all there is to making a HTTP server with express.
