
### Creating Default Parameters for Funtions

As you know, functions can take a set number of parameters, and require that all of them be present before it can be executed successfully. However, you might sometimes run into situations where you want to provide a default value for a parameter or take a variable number of parameters. Unfortunately, Javascript does not have a built-in way to do that. However, people have developed idioms to compensate.

The first idiom is giving a default value for the last parameter. This is done by checking if the last parameter is `undefined` and setting it to a default value if it is. Sometimes people use the idiom: `optionalParameter = optionalParameter || defaultValue`. This can have some undesirable behavior when they pass values that are equal to false such as `false`, `0`, and `""`. A better way to do this is by explicitly checking that the optional parameter is `undefined`. 

Save the following code in a test file, and notice the differing behavior:

    example = function (optionalArg) {
      optionalArg = optionalArg || "The default is: no parameter was passed";
      console.log(optionalArg);
    }

    betterExample = function (optionalArg) {
    if (typeof optionalArg === 'undefined') {
      optionalArg = "The default is: truly, no parameter was passed";
    }
    console.log(optionalArg);
	}

	console.log("Without parameters:");
	example();
	betterExample();

	console.log("\nWith paramater:");
	example("A parameter was passed!");
	betterExample("A parameter was passed!");

	console.log("\nEmpty String:");
	example("");
	betterExample("");

See the difference? 

If the optional value is in the middle of the argument list, it can cause some undesired effects, since all the parameters are shifted over. The optional parameter is not the `undefined` value in this case&mdash;the last parameter is the `undefined` one. You have to check if the last parameter is `undefined` and then manually fix all the other parameters before continuing in the code. This example shows you how to do that:

	example = function (param1, optParam, callback) {
    if (typeof callback === 'undefined') {
      // only two paramaters were passed, so the callback is actually in `optParam`
      callback = optParam;
      //give `optParam` a default value
      optParam = "and a default parameter";
    }
    callback(param1, optParam);
	}

	example("This is a necessary parameter", console.log);
	example("This is a necessary parameter", "and an optional parameter", console.log);

More complicated cases require more code and can obscure the meaning of what you are trying to do. It then becomes a good idea to use helper functions. For example, suppose we wanted to take a variable amount of parameters and pass them all to the callback. You could try to accomplish this by manipulating the `arguments` variable. However, it is just easier to use the [vargs](https://github.com/cloudhead/vargs) module. As you can see by this code, it makes the whole process a little simpler:

	var Args = require("vargs").Constructor;

	example = function () {
    var args = new Args(arguments);
    args.callback.apply({},args.all);
	}

	example("The first parameter", console.log);
	example("The first parameter", "and second parameter", console.log);
	example("The first parameter", "and second parameter", "and third parameter", console.log);
	example("The first parameter", "and second parameter", "and third parameter", "etc", console.log);