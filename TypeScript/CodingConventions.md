#TypeScript Language Rules

* Refer to [JavaScript](https://github.com/Wikia/guidelines/blob/master/JavaScript/CodingConventions.md) style guidelines wherever applicable
* Use object method shorthand where applicable

## TOC
* [Lambda Syntax/Arrow Functions](#lambda-syntaxarrow-functions)
* [Typing: Interfaces and Basic Types](#typing-interfaces-and-basic-types)
  * [`any` Typing](#any-typing)
  * [Interfaces](#interfaces)
  * [Basic Types](#basic-types)
* [Class Definitions](#class-definitions)

## Lambda Syntax/Arrow Functions

Arrow functions in TypeScript, much like in ES6, are simply syntactic sugar. From the [TypeScript guide](http://www.typescriptlang.org/Handbook#functions-lambdas-and-using-39this39):

> To fix [the context of an anonymous function], we switch the function expression to use the lambda syntax ( ()=>{} ) rather than the JavaScript function expression. This will automatically capture the 'this' available when the function is created rather than when it is invoked.

What this means is that the following piece of TS code:


```typescript
var obj = {
	foo: null, 
	bar () {
		// parent scope	
		var xhr = $.get('/example');
		xhr.success((data) => {
			// The value of this correctly refers to parent scope
			this.foo = data;
		});
	}
}
```

compiles to:

```javascript
var obj = {
	foo: null, 
	bar: function () {
		// parent scope
var _this = this;	
		var xhr = $.get('/example');
		xhr.success((data) => {
			// TypeScript aliases the correct reference of this to:
			_this.foo = data;
		});
	}
}
```


Here is the small list of guidelines regarding lambda syntax:

* **Do not** use it to declare functions in object literals
* **Do not** use it to declare functions in the global scope
* **Do** use it in place of standard JS anonymous function notation for callbacks, wherein the correct value of `this` will be stored by the TypeScript compiler

## Typing: Interfaces and Basic Types

### `any` Typing

* **Do** not allow implicit `any` typing. Always type variables and functions where it is possible to misinterpret. Additionally, turn on compiler warning flags for implicit `any` types (if possible in your TS compiler).

* **Do** define type of variables when they are declared but not initialized

```typescript
// Bad:
var str;
// Other code
str = 'hello world';

// Good:
var str: string;
// Other code
str = 'hello world';
```

###Interfaces

* For interfaces that are intended for use in more than one context, store the interface in a global definition file appropriate for your project.

* Interface names should be PascalCase:
```typescript
interface Person {
	name: string;
}
```

* Where function return types are objects, **do** define the type as an interface at the top of the file (it will be scoped to the file).

```typescript

// Bad
func (): {str: string, num: number} {
	// Code which returns an object
}

// Also bad
func (): {
	str: string,
	num: number
} {
	// Code which returns an object
}

// Good:
interface SomeType {
	str: string,
	num; number
}

func (): SomeType {
	// Code which returns an object
}
```

### Basic Types

* Do **not** define types where the variable is assigned inline and the type is basic/primitive/obvious

```typescript
// Bad:
var str: string = 'hello world',
num: number = 1;

// Good:
var str = 'hello world',
 num = 1;
```


## Class Definitions

* Class names should be in **PascalCase**
* The order of the class definition should be as follows:
  * Class properties
  * `constructor`
  * Class methods
* Class properties should all have types and semantic comments
* Class methods should all be in object function shorthand
* There should be no spaces between properties, but spaces between the last property and the constructor, between the constructor and methods, and between all the other methods.

```typescript

Class SomeClass {
	// This is a property whose type is defined
	property: string,
	// Bad
	propertyWithNoType,

	constructor () {
		// Initialize object
	},

	/**
	 * @desc This is a method. Note it’s defined in object function shorthand
	 * @returns What the semantic value of the return is
	 */
	someMethod () {
			// Code
	},

	// Bad
	someOtherMethod: function () {

	}
}
```
