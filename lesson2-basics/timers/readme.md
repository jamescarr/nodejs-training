# Timers
Timers work the same in node.js as they do on V8. setTimeout and setInterval are both implemented and work the same as their client side variants do and take the same arguments. 

## setTimeout
The setTimeout function simply executes a function after a specified time period in ms have elapsed

	setTimeout(function, timeout)

## setInterval
setInterval wil call the specified callback periodically using the time period passed in to the second argument. 

	setInterval(function, timeout);


Both of these functions return a handle that can be used to either prevent the function specified by setTimeout from occuring or stop the function being executed by setInterval. The functions are clearTimeout and clearInterval, respectively. 

## Example
Here's an example that prints the current date/time every second and stops after the fourth call.

	var callback = function(){
		console.log(new Date());
		if(this.calls++ >=4){
			clearInterval(handle);
		}
	}.bind({calls:0});

	var handle = setInterval(callback, 1000);

## Of Note
When running a node.js script the runtime behavior is altered based on using these functions. For example if you use setTimeout in a script, the script will not finish executing until that timeout executes (given everything else has finished). Likewise, setInterval will keep the script running indefinitely until the interval is cleared, the script is explicitly exited (using process.exit()), or an error occurs.
