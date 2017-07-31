# Modularize our Code

In an earlier tutorial, when we created RepLogApp, we put all of our code into a self-executing function, which starts up here. Then, down here at the bottom, we passed in our dependencies, which before a recent change, included jQuery and SweetAlert. This gave us a nice, isolated place to write our code, a place where we knew exactly what dependencies we needed. Also, it made sure that we couldn't do something stupid like accidentally say "$ = null" in here and break the rest of our application. If we had done that without the self-executing block, that would have actually overridden the global $ variable and caused problems. Inside the self-executing block, it only applies to things inside of that function.

Well, we don't need this anymore, and the reason is that webpack treats all of our files like modules, which means that they are in their own isolated environment. Sure, we can still use global variables if we want to. For example, we are still relying on this global routing variable. If we start to refactor that code and instead use require statements, then this becomes unnecessary. Remove the self-executing function. Then, I will indent everything to the root. This still does the same thing as before. It just doesn't give us the isolation, because we don't need the isolation.

Now, at the bottom, you notice that we're still seeing "window.RepLogApp = RepLogApp," and that's because in index.htmltwig, we're actually relying on this RepLogApp variable to be available globally. It has to be a global variable. We do not want to deal with global variables anymore. At this point, RepLogApp says it's being loaded by webpack. It's a module, so instead of saying window.RepLogApp, we should say "module.Exports =RepLogApp." It means that if anything requires this file, they can get our class, and we're not modifying anything in the global scope. As soon as we try that, our code is going to be super broken. RepLogApp is not defined. The reason this is happening is because inside of our template, we are relying on RepLogApp to be a global variable. Now, this is just a well-behaved module. It just exports a variable if somebody wants to use it.

What we need to do to truly modernize our code is to remove all of the JavaScript from our template, like this, and use proper requires. First, in the JS directory, I'm actually going to create a new file called rep_log.js. This is going to be our new entry. In fact, I'll go to webpackconfig.js right now and change this to rep_log.js. Inside here, I'm going to move all of this script and paste it. Now, effectively, in index.htmltwig, we're grabbing the rep_log.js file, which is now going to be the built version of this file. Inside here, if you look closely, we're depending on two things. We're depending on $. This means a "const $ = require jQuery." We're also relying on the RepLogApp class itself, which is totally fine, because we're now exporting that. So "const RepLogApp = require ./RepLogApp," and that's it.

Now, you notice, if we look at our watch, there's no errors here, but we do actually need to Control + C this and rerun it. That's because we just made a change to our rep_log, our webpackconfig.js file. Webpack doesn't see that until we actually restart. Okay, finally, refresh, and everything still works. This is huge, because now, we have no code inside of our template at all. We include our entry, and this is a very common pattern that I'll file. I'll have one entry with document.ready if I need it, and it will call out and actually boot up my application.

So now if you look at our JS directory, we have a layout, login, and rep_log files. If you look at our JS directory, the rep_log is our entry point, but the RepLogApp and the RepLogAppHelper are not entries. They're meant to be used by other code. To make that a little bit more clear, I'm going to create a new directory called Components. The name of that is not important. I'm going to drag those two files in there. So now we have our entry at our root, and then, it's making use of these components. These components are truly standalone components. Anybody could use them. To get that to work, all we need to do is update the path to have ./Components/RepLogApp. The require statement to RepLogApp itself to RepLogHelper still works, because they're in the same directory. When we refresh, everything still works, and our setup is starting to look pretty awesome.

Next, we need to talk about how we can have multiple entries, because right now, this only works for our rep_log page. What about our login page, which actually has its own JavaScript?
