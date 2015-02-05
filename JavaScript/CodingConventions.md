# Wikia JavaScript Coding Conventions

This styleguide defines the JavaScript coding conventions at Wikia. While it is managed by the Front End Coding Conventions Team, it is here to serve the entire JavaScript developer community at Wikia. Therefore, if you would like to propose a change, simply create a pull request and tag [@wikia-frontenders](https://github.com/wikia-frontenders).

## Our Mission ##

> As a developer, I want a clear and well documented guide covering coding conventions, patterns and best practices for JavaScript development at Wikia along with tools to help me in making my code compliant.

## TOC

* [Language Rules](#language-rules)
  * [Early Returns](#early-returns)
  * [Semicolons](#semicolons)
  * [Function-declarations Within Blocks](#function-declarations-within-blocks)
  * [Try/Catch Blocks](#trycatch-blocks)
  * [Switch Statements](#switch-statements)
  * [Delete Operator](#delete-operator)
  * [Modifying Prototypes of Built-in Objects](#modifying-prototypes-of-built-in-objects)
  * [Maximum Parameters](#maximum-parameters)
* [Style Rules](#style-rules)
  * [White Space Guidelines](#white-space-guidelines)
     * [Bad Examples](#bad-examples)
     * [Good Examples](#good-examples)
     * [Objects](#objects)
     * [Arrays](#arrays)
     * [Functions](#functions)
     * [Multi-line Statements](#multi-line-statements)
     * [Chained Method Calls](#chained-method-calls)
  * [Caching jQuery Objects](#caching-jquery-objects)
  * [Storing Context in a Local Variable](#storing-context-in-a-local-variable)
  * [Comments](#comments)
  * [Naming Conventions](#naming-conventions)
     * [Variables](#variables)
     * [Folders](#folders)
     * [AMD Modules](#amd-modules)
     * [JS Files](#js-files)
  * [Immediately Invoked Function Expressions (IIFE)](#immediately-invoked-function-expressions-iife)
  * [Nested Functions](#nested-functions)
* [Tools](#tools)
* [Resources](#resources)

## Language Rules

Language rules have an impact on functionality. They were chosen based on performance implications and their tendency to reduce bugs.

### Early Returns

If a function has multiple return points, make sure that the type of return value is consistant. It's best to add a JSDoc comment specifying the type of the return value.

```javascript
// bad - sometimes returns undefined and sometimes returns string
function () {
    if (someBool) {
        return;
    }
    return 'This is a string';
}

// good
/**
 * @returns {string}
 */
function () {
    if (someBool) {
        return '';
    }
    return 'Both are strings!';
}
```

### Semicolons

Always use semicolons at the end of every statement. Do not use more than one statement per line.

```javascript
// bad:
var x = y
myFunc()

// also bad:
var x = y; myFunc();

// good:
var x = y;
myFunc();
```

### Function Declarations Within Blocks

Do not declare functions within blocks such as loops and conditionals. This will often lead to unintended consequences.

```javascript
// bad:
if (someBool) {
    function myFunc() {
        // code
    }
}

// also bad:
while (condition) {
    function myFunc() {
        // code
    }
}
```

### Try/Catch Blocks

Avoid using try/catch blocks in performance critical functions and inside loops.

```javascript
// bad
for (var i = 0; i < 10; i++) {
    try {
        // some process
    } catch () {
        // some exception handing
    }
}

// better
try {
    // some process
} catch (e) {
    // some exception handing
}

for (var i = 0; i < 10; i++) {
    // do something based on results of try/catch above
}
```

### Switch Statements

The usage of `switch` statements is generally discouraged, but can be useful when there are a large number of cases - especially when multiple cases can be handled by the same block, or fall-through logic (the `default` case) can be leveraged.

When using `switch` statements:
 - Use `break`, `return` or `throw` for each case other than `default`.
 - The `default` case should always be last.

As with all our whitespace guidelines, switch statements follow JSLint.

```javascript
// bad
switch (foo) {
    default:
        z();
    case 'bar':
    case 'foobar':
        x();
        break;
    case 'baz':
        y();
}

// good
switch (foo) {
case 'bar':
case 'foobar':
	x();
	break;
case 'baz':
	y();
	break;
default:
	z();
}
```

### Delete Operator

Avoid using the delete operator. It is better to set the property to null or some other falsey value.

From Google's style guide:
> "In modern JavaScript engines, changing the number of properties on an object is much slower than reassigning the values. The delete keyword should be avoided except when it is necessary to remove a property from an object's iterated list of keys, or to change the result of if (key in obj)."

__Example:__
```javascript
var myObj = {
  hello: 'hi',
  goodBye: 'bye'
}

// bad
delete myObj.goodBye;

// good
myObj.goodBye = false; // or null or '' etc.
```

### Modifying Prototypes of Built-in Objects

Modifying prototypes is heavily discouraged. For this reason, many JavaScript frameworks, such as jQuery, work on the assumption that no built in Object prototypes are modified.

From Google's style guide:
> "Modifying builtins like Object.prototype and Array.prototype are strictly forbidden. Modifying other builtins like Function.prototype is less dangerous but still leads to hard to debug issues in production and should be avoided."

### Maximum Parameters
Functions should have no more than 4 parameters.  If more than 4 are needed you can create an object that contains the parameters and pass that object to the function.

The one exception right now is AMD module dependencies.

Examples:
```javascript
// bad
function bakeCupcakes(sugar, eggs, milk, icing, flour) {
    //...
}

// good
function bakeCupcakes(ingredients) {
    // do something with ingredients.sugar, etc.
}

// exception: AMD
define('bakecupcakes',
    ['sugar', 'eggs', 'milk', 'icing', 'flour'],
    function(sugar, eggs, milk, icing, flour) {
 // ...
})

```

## Style Rules

Style rules help us write easy to read, well documented, and consistent code.

### White Space Guidelines

Our whitespace guidelines are based on JSLint. There are also some rules listed below that are in addition JSLint.

- Indent with tabs.
- No whitespace at the end of line or on blank lines.
- Lines should be no longer than 120 characters (counting tabs as 4 spaces).
- `if`/`else`/`for`/`while`/`try` always have braces and always go on multiple lines.
- Unary special-character operators (e.g., `!`, `++`) must not have space next to their operand.
- Any `,` and `;` must not have preceding space.
- Any `;` used as a statement terminator must be at the end of the line.
- Any `:` after a property name in an object definition must not have preceding space, and should be followed by one space.
- The `?` and `:` in a ternary conditional must have space on both sides.
- No filler spaces in empty constructs (e.g., `{}`, `[]`, `fn()`)
- New line at the end of each file.

#### Bad Examples

```js

// Bad
if(condition) doSomething();
while(!condition) iterating++;
for(var i=0;i<100;i++) object[array[i]] = someFn(i);

```

#### Good Examples

```js
var i;

if (condition) {
	doSomething();
} else if (otherCondition) {
	somethingElse();
} else {
	otherThing();
}

while (!condition) {
	iterating++;
}

for (i = 0; i < 100; i++) {
	object[array[i]] = someFn(i);
}

try {
	// Expressions
} catch (e) {
	// Expressions
}
```

#### Objects

Object declarations can be made on a single line if they are short (remember the [line length](#white-space-guidelines) limits). When an object declaration is too long to fit on one line, there must be one property per line. Property names only need to be quoted if they are reserved words or contain special characters:

```js
// Bad
var map = { ready: 9,
	when: 4, 'you are': 15 };

// Good
var map = { ready: 9, when: 4, 'you are': 15 };

// Good as well
var map = {
	ready: 9,
	when: 4,
	'you are': 15
};

// More than one var
var map1 = {
		key: 'val'
	},
	map2 = {
		key: 'val'
	};
```
When accessing object properties, dot notation is preferred over brackets.
```js
// Bad
myObj['someProp'];

// Good
myObj.someProp;
```

#### Arrays

No whitespace before the first value or after the last value in an array, unless it spans multiple lines.

```js
// both are fine
someArray = [a, b, 'foo'];

anotherArray = [
    moreStuff,
    maybeAFunctionCall(),
    'other stuff'
];

someArray[1];
```

#### Functions

The function keyword and calling parentheses should always be followed by one space. There should be no space between the name of a function and the left parenthesis of its parameter list. For anonymous functions there should be one space between the word function and the left parenthesis.

```js
function foo(arg1, arg2) {
	...
}

foo(1, 2);

var foo = function () {
	...
}
```

#### Multi-line Statements

When a statement is too long to fit on one line, line breaks must occur after an operator.

```js
// Bad
var html = '<p>The sum of ' + a + ' and ' + b + ' plus ' + c
	+ ' is ' + (a + b + c);

// Good
var html = '<p>The sum of ' + a + ' and ' + b + ' plus ' + c +
	' is ' + (a + b + c);
```
Lines should be broken into logical groups if it improves readability, such as splitting each expression of a ternary operator onto its own line even if both will fit on a single line. If a definition takes up more than one line, declare the variable first and assign its value later.
```js
var foo, baz;

foo = function () {
	someCode();
};

baz = firstCondition(foo) && secondCondition(bar) ?
	doStuff(foo, bar) :
	doOtherStuff(foo, bar);
```

When a conditional is too long to fit on one line, start a new line for the conditions and wrap them as desired. Don't start a new line with an operator.

```js
// bad
if (firstCondition() && secondCondition()
    && thirdCondition()) {
    doStuff();
}

// good
if (
    firstCondition() && secondCondition() &&
    thirdCondition()
) {
    doStuff();
}
```

Break up long strings of text with the plus operator.
```js
// good
var x = 'multi' +
        'line'

// bad
var x = 'multi \
        line';
```

#### Chained Method Calls

When a chain of method calls is too long to fit on one line, there must be one call per line, with the first call on a separate line from the object the methods are called on. If the method changes the context, an extra level of indentation must be used.

```js
elements
	.addClass('foo')
	.children()
		.html('hello')
	.end()
	.appendTo('body');
```

### Comments

Comment early and often! For comments inside functions, use inline comments. For comments about functions and documents, use [JSDoc](http://usejsdoc.org/) style block comments.

```javascript
/**
 * @desc This function bakes cookies
 * @param {string} flavor The flavor of the cookie
 * @return {object} cookie The delicious cookie
 */
function makeCookies(flavor) {
    // create the cookie
    var cookie = {
        type: flavor,
        tastiness: 'delicious'
    };

    // do more stuff annotated by inline comments ...

    return cookie;
}
```

We use JSDoc style comments above function declarations and at the top of files because they make code clear and easy to read, and we'd like to be able to generate JavaScript documentation at some point.

#### Required JSDoc Annotations (when applicable)

* @desc
* @param
* @return

#### Recommended JSDoc Annotations

* @author (at the top of a file)
* @see (for links to documentation)

### Naming conventions

#### Variables

Use lazyCamelCase for all variables with the exception of constructers, which should use UpperCamelCase. Declare all variables using one `var` keyword at the top of their scope context. Declaration and assignment on the same line are allowed.

```javascript
// bad
var foo;
var bar;
var falcor;

// good
var foo,
	bar,
	falcor;

// also acceptable
var foo = 'kyle',
	bar = 'wears',
	falcor = 'shorts';

var foo, bar,
	falcor = 'hairy';

// normal variable casing
var myVariable;

// variable casing for constructors
function MyConstructor() { ... }
```

##### Constants

Constants do not exist in JavaScript so do not use all caps to denote constants. It's okay to treat variables as constants, just keep them in camelCase.

```javascript
// bad
var MY_CONST = 2;

// good
var myConst = 2;
```

##### Acronyms

Avoid acronyms in variable names and be explicit in your variable naming. It should be clear to anyone reading your code what the variable does.

##### Obfuscation

Avoid minification through obfuscation and try to make your code more human readable. Let the minification process handle minifying for production use.

##### Caching jQuery Objects

Reduce the number of DOM queries by caching jQuery objects if the same object will be used more than once. Prefix jQuery object variables with a `$`.
```javascript
// bad
if ($('#foo').is(':visible')) {
    $('#foo').addClass('active');
};

// good
var $foo = $('#foo');
if ($foo.is(':visible')) {
    $foo.addClass('active');
};
```

#### Storing Context in a Local Variable

If you want to store the context of a function in a variable that can be passed to a function called in a different context, call the variable `self`.

Example:
```javascript
function example() {
	var self = this;

	$myObj.on('click', function () {
		// some code...

		self.doSomething();
	});
}
```

#### AMD Modules

AMD module IDs should be all lazyCamelCase. This is in line with the AMD specification for IDs ([Source](https://github.com/amdjs/amdjs-api/wiki/AMD#id-))

```javascript
define('myExtension.myPage' ...)
```
If there is a folder structure within the extension's scripts directory, the module's namespace should match the folder structure.

For example, if the tree looks like this:

    |-- Search
    | |-- scripts
    | | |-- views
    | | | |-- suggestions.js
    | | | |-- form.js
    | | |-- models
    | | | |-- primaryResults.js
    | | | |-- secondaryResults.js

The module names for these files would be:

```javascript
define('search.views.suggestions' ...);
define('search.views.form' ...);
define('search.models.results' ...);
```

If the code is meant to be used site wide or by multiple different extensions, namespace with 'wikia'.

```javascript
define('wikia.myModule')
```

Hint: If it's in the modules folder, it should be namespace with 'wikia'.

#### Folders

For clarity and future-proofness, all javascript files should go into a 'scripts' folder and all stylesheet files should go into a 'styles' folder.  This is different from what we've done in the past, which was putting all scripts into a 'js' folder and all stylesheets into a 'css' folder.

All library files should go inside 'lib' folders. This will make it easier for JSHint to ignore library code.

#### JS Files

All re-usable JavaScript should be written as AMD modules. See the [above section](#amd-modules) for matching file names to module names. File and module ID's should all be in lazyCamelCase ([Source:](https://github.com/amdjs/amdjs-api/wiki/AMD#id-) as per AMD specification)

### Immediately Invoked Function Expressions (IIFE)

Wrap all immediately invoked function expressions with parentheses

```javascript

// good
var a = (function () { return 1; })(),
	b = (function () { return 2; }());

// bad
var a = function () { return 1; }();
```

### Nested Functions

Reduce the number of nested functions by declaring functions early and referencing them later. This applies to asynchronous request callbacks as well as blocks of code that can otherwise be broken down into functional units. This keeps code easy to read and easier to unit test. For more good info on this subject, see http://callbackhell.com.

For dealing with multiple asynchronous callbacks, use the promise pattern. We use jQuery's [Deferred Object](http://api.jquery.com/category/deferred-object/) for this.

```javascript
// bad
$.nirvana.sendRequest({
	// ...
	callback: function () {
		// ... do stuff

		if (foo) {
			// ... do even more stuff
			foo.forEach(function () {
				// ... getting a little too deep here...
			});
		}
	}
});

// good
// Note: functions can be declared as object methods, etc. Simplified for example.
function requestCallback() {
	// ...
	handleFoo();
}

function handleFoo() {
	// ...
	if (foo) {
		foo.forEach(fooLoop);
	}
}

function fooLoop() {
	// ...
}

$.nirvana.sendRequest({
	// ...
	callback: requestCallback
});
```

## Tools

Here at Wikia, we employ a few tools to make compliance with our coding conventions easier. These tools are detailed below.

### [JSHint](http://jshint.com)

JSHint detects errors, prevents potential problems and enforces some of our coding conventions. Our rules are enforced globally in the root level [.jshintrc](https://github.com/Wikia/app/blob/dev/.jshintrc) file. Keep in mind that some extensions have JSHint guidelines of their own.

Run JSHint on the command line or through an editor plugin. More information can be found [here](http://jshint.com/install).

### [JSCS](https://www.npmjs.org/package/jscs)
JSCS is a JavaScript code sniffer that will check for whitespace and style error. The config file, .jscs.json, is located in the root application directory.

### [JS-Beautify](https://www.npmjs.org/package/js-beautify)
JS-Beautify will fix whitespace and style errors in JavaScript files.  It can be run on the command line. More information [here](https://www.npmjs.org/package/js-beautify).

### [EditorConfig](http://editorconfig.org/)

EditorConfig helps enforce whitespace consistency across our repository. Our rules are enforced globally in the root level [.editorconfig](https://github.com/Wikia/app/blob/dev/.editorconfig) file.

Simply [download a plugin](http://editorconfig.org/#download) for your editor of choice. If your editor is not listed, you will need to configure your editor manually to conform to any of our guidelines. If this is the case, please consider opening an issue (https://github.com/editorconfig/editorconfig/issues) or contributing a plugin (http://editorconfig.org/#contributing) to the EditorConfig project. Don't forget to update when changes to the guidelines get rolled out.

## Resources

* [Google's JS Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
* [jQuery's JS Style Gude](https://github.com/jquery/contribute.jquery.org/blob/master/pages/style-guide/js.md)
* [Douglas Crockford's Code Conventions for the JavaScript Programming Language](http://javascript.crockford.com/code.html)
* [AirBnb's JS Style Guide](https://github.com/airbnb/javascript)
* [JSLint](http://www.jslint.com/)

![Wikia](http://i.imgur.com/tVxkjhG.gif)
