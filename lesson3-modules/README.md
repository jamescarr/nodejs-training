# Modules
Modules are an important part of node.js and know how to use them (and use them well) are a big part of developing with node.js. For the most part node.js implements the CommonJS module standard with a few liberties, chief amongst these is that it doesn't support the module names and specific modules defined within the standard because they don't fit with node's design philosophy. 

Earlier I mentioned the global module, so now would be a good time for us to explore it in detail.

## module
Open up the REPL and type module. This will print out a dump of the module object's structure. You should see something like this:

	{ id: 'repl'
	, exports: 
	   { writer: [Function]
	   , REPLServer: [Function: REPLServer]
	   , start: [Function]
	   }
	, parent: undefined
	, moduleCache: {}
	, filename: null
	, loaded: true
	, exited: false
	, children: []
	}

* id represents the id of the module. more on this later.
* exports are variables (be they methods, objects, etc) exposed outside of the module to be publicly visible. 
* parent - if  module is required inside another module, this will be populated with the module that required it. Is undefined for the top level module.
* filename - name of the file that defines the module, undefined if created via other means
* loaded - boolean indicating whether or not node has included and compiled the module
* exited - indicates whether the module has been exited (usually happens during shutdown phase
* children - any modules that this module has included.

### Exercise: Create a module
A module can be created by creating a file with any javascript in it (variables, functions, objects, etc) and assigning what you want to expose to exports. For a naive example:

hello.js
-------
var message = "Hello World";

exports.sayHello = function(){
  return message;
}

app.js
-------
var hello = require(__dirname+"/hello")

console.log(hello.sayHello());

For the exercise create a module that has a function that takes a number and sums the divisors passed to it. The app including it should look something like this:

var example = require('example');

var sum = example.sumOfDivisors(30);

console.log(sum); // prints out 210

## Using 3rd Party modules
As you can notice modules can be any javascript file and just required into a script to be loaded. The question is outside of the core modules how can you use modules others have shared or even share your own modules? Just sharing random javascript files around is a little haphazard! :)

NPM is a package manager for node.js much like ruby gems or debian's apt-get. You can go to http://npmjs.org to find out details and install it. Once it is installed, try these commands out on the command line to explore it.

  npm
  
  npm ls

  npm ls redis
  
  npm ls express
  
  npm install express

Now go to the console and type
  require('express');


