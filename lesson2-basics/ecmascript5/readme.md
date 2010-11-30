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
		var reduce = Array.prototype.reduce; 
		return reduce.call(arguments, function(a,b){
			return a+b;
		}, 0);	
	}

	console.log(sum(9,2,3,4,1));

It's okay if this is a little confusing... the idea here is that the first argument to call is replacing references to "this" in an existing function (reduce) which normally points to an actual array when called on an array instance. Here's a slightly contrived example:
	
	function concat(a){
		return this +" "+ a;
	}
	
	console.log(concat("World"));
	
	console.log(concat.call("Hello", "World"));

When you run this, the first log will print '[object global] World' because by default this will resolve to the global scope if the function isn't attached to any prototype or object (we'll cover this in detail later). The second one on the other prints 'Hello World' because we've set the context of this to be "Hello". 

The second way of invoking a method is by calling apply, which like call takes a context for "this" as a first parameter and then takes an argument object for the second parameter. This is quite useful for passing arguments through to another function when they're unknown. As an example, say we want to produce the square of a sum of numbers.
	
	function squareOfSum(){
		var sumOfNumbers = sum.apply(null, arguments);
		return sumOfNumbers * sumOfNumbers;
	}
	
	console.log(squareOfSum(2,3,4,5));

One other interesting concept to cover is the functions that return functions and function scope. Here's an interesting example function that returns a new function that can add to a running sum:
	
	function createSum(a){
		return function(b){
			return a += b
		}
	}

	var update = createSum(3);
	
	console.log(update(10));
	console.log(update(5));

	console.log(createSum(3)(4));

#### New Ecmascript 5 Feature
Finally, let's close this refresher on functions with a new feature available as part of ES5 in node.js. bind() allows you to create partial functions by binding values to the parameters and creating a new function. In the real world this can be useful when you want to use an existing function by default the first parameter to some value because it always gets that value passed in. 

	var fivePlus = sum.bind(5);
	
	console.log(fivePlus(10));

### Array Extras

In a previous example I used the Array method reduce with the promise that I'd cover it later and now I will. Ecmascript5 includes a handy new feature called Array Extras which are really just new methods added to Array to make it, well, cooler to work with. 

Let's start in with the forEach method. This takes a callback function that is called for each element of the array and accepts three arguments: the current element being iterated over, its index within the array, and the actual array being traversed. For an example:

	[9,2,3,10].forEach(function(number, index){
		console.log(number + " is in index " + index);
	});

A common programming operation is to create a collection of elments from an existing collection. An order system might take an array of order ids and return an array of Order objects for example. The map() function is a simple way to create a new array that is a transformation of an existing array and its callback takes the same arguments that forEach's callback does. To illustrate this lets take an array of numbers and generate an array of their squares:
	var squares = [1,2,3,4,5].map(function(a){
		return a * a;
	});
	
	console.log(squares);
	
Another common operation is to combine the elements of an array into a single value. The reduce() method does this by taking a callback that gets exececuted for each element, left to right. It also takes an optional initialValue to start off with. Here's an example of flattening an array of arrays.

	var flattened = [[1,2], [3,2], [5,1]].reduce(function(previous, current, index, array){
		return previous.concat(current);
	});

Or summing a list of numbers, setting the initial value to 1
	console.log([1,2,3].reduce(function(a,b){
			return a + b;
		}, 1);

