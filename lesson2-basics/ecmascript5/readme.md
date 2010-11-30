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

This also has a companion method, reduceRight(), which has the same behavior except that it works from right to left instead of left to right.

Arrays are also filterable via the filter() method, which returns a collection contsining elements of which the callback returned true for. For example, to return an array of all even numbers and an array of all even indexes:
	
	var evenNumbers = [1,4,8,9,10].filter(function(num){
		return num % 2 == 0;
	});
	
	console.log("even numbers: " + evenNumbers);

	console.log("even indexes: " + [1,4,8,9,10].filter(function(num, index){
		return index % 2 == 0;
	}));

There are also two method that return true based on whether the provided predicate is true for some or all the elements in the array. For example, to determine if some or all of the elements in an array are even:
	
	if([1,2,3].some(function(a){ return a % 2 == 0})){
		console.log("some of the numbers are even");
	}
	
	if([4,2,8].every(function(a){ return a % 2 == 0})){
		console.log("all of the numbers are even");
	}

Two final methods I'd like to cover that aren't part of ES5 Array Extras but are just useful methods overall are shift and unshift. Shift will remove the first element from the array and return it, while unshift prepends to it. 
	
	var x = [1,2];
	x.unshift(3);
	console.log(x);
	
	console.log(x.shift());
	console.log(x);


### JSON
This isn't too earth changing and if you're familiar with javascript chances are you've used it for quite awhile. JSON is a global object that contains two methods for serializing and unserializing javascript objects from JSON strings which is useful for transfering them between different processes or between client and server. For example:

	// serialize
	var person = {name:"James", age:30},
	  serialized = JSON.stringify(person),
	  unserialized = JSON.parse(serialized);
	
	console.log(serialized);
	console.log(unserialized);
	console.log(unserialized.name);

Just something useful to keep in mind is that if you are parsing a json string directly (say, from a file) make sure it adheres to the JSON format (meaning the keys must be quoted).

### Object
Before delving into the new Ecmascript 5 Object features let's quickly recap javascript objects and how to create them.

#### Literal Object
Literal objects are created in one fell swoop and only exist for that instance.
	var person = {
		name:'James', 
		greet:function(){
			return "My name is " + this.name;
		}
	};
	
	console.log(person.greet());

	person.name = 'The Dude';

	console.log(person.greet());

#### Prototype Object

	function Person(name){
		this.name = name;
	}
	Person.prototype.greet = function(){
		return "My name is " + this.name;
	}

	var dude = new Person("The Dude");
	console.log(dude.name);
	console.log(dude.greet());

Here we create an object prototype that multiple instances can be created from using the new keyword. member variables can be assigned using this and methods can be created either by adding them to the prototype or by assigning them to this. The difference is that if they are added to the prototype outside of the constructor they are only defined once... if assigned to this inside the constructor then they will be defined for each new object created. The latter can be useful if you wanted to encapsulate a variable.

	function Person(name){
		this.greet = function(){
			return "My name is " + name;
		}
	}
	
	var dude = new Person("James");
	console.log(dude.name); // undefined, there is no property name
	console.log(dude.greet());

#### Immutability
There are a few different ways to make objects immutable in javascript. You may have noticed that, by default, objects are completely mutable as we were able to change property values at any time. However there's a handy utility method on Object that "freezes" an object instance to prevent modiification.

	var person = {name:'James', age:30};
	
	Object.freeze(person);
	
	person.age = 31;
	
	console.log(person.age); // prints 30, cannot be changed

A certain gotcha (which exists with immutibility in general) is that while the property cannot be re-assigned, it's values can can change. For example, say we had an object with a property that contained an array.
	
	var deck = Object.freeze({cards:[1,2,3,4,5]});
	
	deck.cards = [1,2]; // doesn't work
	console.log(deck.cards); // prints original array

	deck.cards.unshift(6);
	console.log(deck.cards); // prints array with new value on it
	
Another way to achieve immutibility is by defining a getter but no setter. Here's an example using __defineGetter__
	function Person(name){
		this.__defineGetter__('name', function(){
			return name;
		});
	}
	
	var person = new Person('James');
	console.log(person.name); 
	
	person.name = 'Jim'; // throws exception, no setter

Since we're on the subject, lets cover ES5 getters and setters

#### Getters / Setters
ES5 provides some pretty nifty mechanisms for defining getters and setters if you want more fine grained control over how property access operates (as we saw with __defineGetter__). These are useful for creating readOnly properties as well as fined tuned setters. Here's a few illustrations.
	
	function Person(){
		var name = '';
		this.__defineSetter__('name', function(n){
			name = n.toUpperCase();
		});
		
		this.__defineGetter__('name', function(){
			return name.toLowerCase();
		});
	}
	
	var person = new Person();
	person.name = 'James';
	console.log(person.name);
	
Or the object literal format
	var site = {
		host:'localhost'
		,port:7010
		,get location(){
			return 'http://'+host+':'+port;
		}
		,set location(hostAndPort){
			var parts = hostAndPort.replace("http://").split(':');
			this.host = parts[0];
			this.port = parts[1];
		}
	};

	site.location = 'http://example.com:8080';
	console.log(site.host);
	console.log(site.port);
	console.log(site.location);

The literal notation can be a little intimidating, but it makes creating getter/setter methods nice and concise.

	
