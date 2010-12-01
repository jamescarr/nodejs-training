# Globals
Now that we've covered some of the ES5 features available in node.js it is time to actually move on to some of the node specific stuff. You might have noticed the previous examples that used console.log to display text to the console. This is just one of the several global objects available within node.js

Just about every javascript interpreter has some form of global objects (in the browser global is usually window and global variables are attached to window) and node.js is no different. Here we'll cover the core global variables:

 * console
 * process
 * require
 * __filename
 * __dirname
 * module

## console
The console object is similar in functionality to the console object in google chrome or firefox's firebug. It allows logging to the console (with different log levels) as well as other handy features like timers.

To log a message to the console, try one of the following:
	console.log('hello world');
	console.info('hello world');
	console.warn('hello world');
	console.error('hello world');

console.dir is interesting as it prints the structure of the object passed to it out to the console. Try a few of these:
	console.dir({a:'some string' b:function(){}});
	console.dir(module);
	console.dir(console);

An important caveat to pay heed to is that (at least at the time of this writing) console.dir prints [Circular] for instances it has already seen.
	
	console.dir([ ['a', 'b'], ['a', 'b']]);
	
	var x = ['a', 'b'];
	console.dir([x,x,x]);

Finally, the last feature of console that might be of interesst to us is time and timeEnd, which starts a timer with a specified name that ends and prints the time elapsed in miliseconds to the console when timeEnd is called.

	console.time('a');
	console.time('b');

	console.timeEnd('b');
	console.timeEnd('a');
	
	console.timeEnd();

	console.time();
	console.timeEnd();


## require
you will be using require quite a bit. Like the name implies it is for requiring modules for use within a script, throwing an exception if the module is not found (more on modules in the next lesson). It also has several features outside of just including scripts that we will discuss. 

To include a module, we can do the following:
	
	var http = require('http');
	
	console.dir(http);

This will include the module and namespace it under the variable http. The variable that holds it doesn't have to be the same as the module name, but it is a common (and useful) convention. 

This operation is synchronous and can alternatively be done asynchronously. Another benefit of this is that the module will be locally scoped to the callback. The callback takes two parameters: an Error if an error occurred importing the module (for example, file not found) and the module if provided. The presence of once means the other will be null.

	require.async('http', function(err, http){
		// do stuff with http here.
	});

When a module name is passed to require, it searches the require.path from the top down until it finds the module it is looking for. Go ahead and take a look at it:
	
	console.log(require.paths);

The require.paths can be added to to include additional directories to search for modules in. Since it searches from the top down it is best to prepend your paths to the beginning of it to avoid collision.

	require.paths.unshift('/foo/bar/modules');
	
This isn't quite ideal as most of the time your modules and 3rd party modules will exist in the current working directory. Let's cover how to do that next.


## __filename and __dirname
__filename and __dirname represent the filename of the executing script and the directory in which it resides, respectively. A very common use of __dirname is to include user scripts onto the require path relative from the current script directory. 

	require.paths.unshift(__dirname+'/lib/');
	var foo = require('foo');


## process
The global vaariable process is an object that is a reprsentation of the process the current script is running in. This has many useful methods and read only attributes regarding the process and the system it is executing in.

### process.argv
This is an array of the arguments passed to the commandline when the script executed. Create a file with the following:
	
	console.log("Hello " + process.argv[2]);
	
	console.log("Command ran: " + process.argv[0]);
	console.log("script name: " + process.argv[1]);

Save the file (something like demo.js or whatever) and execute the following from the commandline:
	node demo.js YourName

#### Exercise
Write a script that takes a variable number of arguments of numbers, tally them up and print them to the console. For example:
	
	node sum.js 5 4 4 10
	
Outputs
	The sum of these numbers is 23

### process.openStdin
openStdin will open a readable stream to capture user input from the command line. You can then listen for the data event, which will fire on each line feed [THIS IS INCORRECT. CHECK THE DOCS].

	var stdin = process.openStdin();
	
	stdin.on('data', function(input){
		console.log(input.toUpperCase());
	});
The converse of this is process.stdout, which is a writable stream that you can write to.

	process.write('Hello\n');
	
### process.env
This points to the same thing as running env on the command line... it contains a map of all the environment variables.

	console.dir(process.env);
	console.log(process.env.PATH);
	console.log(process.env.USERNAME);
	
A useful convention is to create an environment variable named NODE_ENV to specify if the environment is 'development' or 'production'. It isn't a standard yet but most node apps inspect that variable to determine if they are in develop or production, defaulting to development if it is not defined.

### process.nextTick
process.nextTick takes a callback that will execute in the next "tick" allowing for asynchronous behavior. Here's an example:
	console.log(1);
	process.nextTick(function(){
		console.log(2);
	});
	console.log(3);


### Events
There are several events that one can listen to on process (more on events later) which provide a useful way to "plug in" to different events the process can emit. Here are a few of them.

#### exit
Fired when the process is about to exit. The callback will execute right before exit so this eans that the event loop terminates after it finishes.

	process.on('exit', function(){
		setTimeout(function(){
			console.log('this will never execute');
		}, 1);
		console.log("exitting...");
	});

#### uncaughtException
This will get fired when an exception is thrown and finds its way to the event loop (which typically will crash the process and spit the exception out the console). If a listener is added for this event then the default behavior will not occur.

	process.on('uncaughtException', function (err) {
  		console.error('Caught exception: ' + err);
	});
	
	var range = [1..10]; // this doesn't exist and causes an error
	console.log('never executes');

#### Signal Events
You can also attach listeners onto the process for POSIX signal events as well.

	process.on("SIGSEV", function(){
		console.log("Process received a SIGSEV signal");
	});
### Further Exploration
There are a lot of other operations, go to the REPL and type process.[TAB] to see a list of other methods available, such as cwd(), kill, pid, and exit





