# Lesson 1: Getting Started
Obviously you'll need node installed on your computer to actually learn it, so do that first. If you already have node installed, skip down to the secion titled REPL. Otherwise, prepare to install node.js. :)

## Installing Node.js
The official way to obtain node.js is to either clone the source repository for it from github or download the tarball from http://nodejs.org.

For this tutorial we will be using v0.2.5 rather than v0.3.0+ to avoid any potential gotchas. 

### Via git
	git clone git://github.com/ry/node.git
	git checkout v0.2.5 

### Via the site
	Click the download link, extract to a directory and cd to it.

Installation will vary based on your environment but luckily there is some really good documentation on the site to help with the installation process. Select your target environment below.

* [Instructions for Cygwin] [1] 
* [Mac OSX / Linux] [2]

Be wary of potential installation issues. See https://github.com/ry/node/wiki/Installation-Notes for some of the problems you may encounter and their solutions.

Given the installation has completed successfully you should now have a node binary on your system. Type node from a console to enter the REPL (Read Eval Print Loop) interface that lets you try node.js out interactively. Try out a few snippets of javascript.
	
	> [2,3,4].map(function(num){ return num * num; });
	[4, 9, 16]
	> console.log('hello world')
	hello world

You're now good to go on with the rest of the tutorial. Feel free to play around with the REPL interface a bit, otherwise move on to [lesson 2] [3].


[1]: https://github.com/ry/node/wiki/Building-node.js-on-Cygwin-(Windows)
[2]: https://github.com/ry/node/wiki/Quick-and-easy-installation
[3]: ../lesson2-basics/