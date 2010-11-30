# Ecmascript 5
Being built on top of V8, node.js supports many Ecmascript 5 features. Let's do a quick overview of them along with some fundamental javascript syntax and concepts to rapidly get acquainted with them.

## Javascript Refresher
### Functions 
Functions in javascript are actually objects. This means they can be assigned to variables, passed around, and even have methods and attributes on them that can be invoked separately. First, lets take a look at a simple function definition that sums 2 numbers together

	function sum(a, b){
		return a + b;
	}
	
Which naturally you might call like this:
	
	console.log(sum(2,4)); 

What the former does is just assign the function to a variable named sum. We could also define the same function as 

	var sum = function(a,b){ return a + b; }

and it will work all the same. A function can also be defined anonymously as well and executed on the spot.

	var sumOfNumbers = (function(a,b){ return a+b})(10, 15);
	console.log(sumOfNumbers);

Since functions are just objects, they can also be passed to other functions to be called there. 

	var sum = function(a,b){return a+b; }
	var product = function(a,b){ return a * b; }
	
	function operate(a, b, cb){
		return cb(a,b);
	}
	
	console.log(operate(5,5, sum));
	console.log(operate(5,5, product));

In this example, you may have noticed that the third parameter to operate was named cb (for callback). This can really be anything (and you should name it to something meaningful within its context) but cb is a useful convention to convey that it is a callback (and short to write too!). 

A function also has a few interesting variables for us to pay attention to within its scope. Chief amongs these is arguments, which points to an object representing the arguments passed to a function.
	
	function sum(){
		return arguments[0] + arguments[1];
	}
	
	console.log(sum(3,4));

This also allows for creating functions that can have a variable number of arguments. Up until now our simple sum function has been constrained to only taking two arguments. Lets remove that constraint:
	
	function sum(){
		var sum = 0;
		for(var i = 0; i < arguments.length; i++){
			sum += arguments[i];
		}
		return sum;
	}
	
	console.log(sum(2,3,9,4,2));

Now let us also cover the two alternative ways to invoke a function rather than just calling it directly. We could change the previous example to take advantage of an existing method on Array named reduce (which we will cover later) by invoking call on the function (which takes a first argument to represent the scope of this and followed by variable number of arguments to pass to it). 
	function sum(){
		var reduce = Arrray.prototype.reduce; 
		return reduce.call(arguments, function(a,b){
			return a+b;
		}, 0);	
	}

	console.log(sum(9,2,3,4,1));


