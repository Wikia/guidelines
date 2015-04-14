# Wikia TypeScript Guidelines

## Table Of Contents
* [Overview](#overview)
  * [What is TypeScript?](#what-is-typescript)
  * [Why TypeScript?](#why-typescript)
  * [TS &#x2283; JS](#ts--js)
  * [Handbook](#handbook)
  * [Formatter](#formatter)
* [Tools](#tools)
  * [tsc](#tsc)
  * [IDEs/Text Editors](#idestext-editors)
  * [TSLint](#tslint)
  * [tsfmt](#tsfmt)
* [Typing](#typing)
  * [`any` Typing](#any-typing)
  * [Interfaces](#interfaces)
  * [Basic Types](#basic-types)
  * [Generic Types](#generic-types)
    * [Generic Functions](#generic-functions)
    * [Generic Classes](#generic-classes)
    * [Generic Arrays](#generic-arrays)
* [Functions](#functions)
  * [Lambda Syntax/Arrow Functions](#lambda-syntaxarrow-functions)
  * [Object Method Shorthand](#object-method-shorthand)
* [Classes and Object Orientation](#classes-and-object-orientation)
  * [Class Definitions](#class-definitions)
  * [Object-Orientation](#object-orientation)
* [Modules](#modules)
* [Declaration Files](#declaration-files)

## Overview

### What is TypeScript?

> TypeScript is a typed superset of JavaScript that compiles to plain JavaScript. (typescriptlang.org)

### Why TypeScript?

Why would a programmer use TypeScript instead of vanilla JavaScript?

* It has type-checking, which...
  * reduces the number of runtime type errors
  * removes the necessity of checking types in code
  * generates self-documenting code (no need to maintain comments on the type of a parameter)
* It unlocks power of the Object-Oriented paradigm for JavaScript
* It has built-in mechanisms for encapsulation/information hiding
* All JS is valid TS, so it is a hop skip and a jump from coding JS to coding TS

### TS &#x2283; JS

Since TypeScript is a superset of JavaScript, refer to the [Wikia JavaScript Guidelines](../JavaScript/CodingConventions.md) wherever applicable.

### Handbook

Before referring to these guidelines, read the [TypeScript Handbook](http://www.typescriptlang.org/Handbook) in full to understand what features are available in TypeScript.

## Tools

### tsc

The easiset way to acquire a TypeScript Compiler is by downloading the [NPM package](https://www.npmjs.org/package/typescript). It is also available as a VisualStudio plugin (see TS website).

### IDEs/Text Editors

* PhpStorm/WebStorm supports TypeScript with code completion, refactoring and debugging
* Syntax highlighting is available for Sublime Text/

### TSLint

There is a [TypeScript linter available on NPM](https://www.npmjs.org/package/tslint), and also plugins available for various build task runners/build systems. Check out included `tslint.json` file for recommended settings.

### tsfmt

There is a [TypeScript formatter available on NPM](https://www.npmjs.org/package/tsfmt). Check out included `tsfmt.json` for recommended settings.

## Typing

The general rule is that one should aim to have full type coverage over the code, but not to have excessive or unnecessary type annotation.

### Basic Types

Do **not** annotate types where the variable is assigned inline and the type is a basic type (`boolean`, `number`, `string`, `Array<T>`, or `enum`), or if it is being set to the return value of a function whose return type is annotated.
```typescript
// Bad
var str: string = 'hello world',
num: number = 1;

// Also bad
function fn (): number;
var foo: number = fn();

// Good
var str = 'hello world',
 num = 1;
 
// Also good
function fn (): number;
var foo = fn();
```

### Interfaces

* For interfaces that are intended for use in more than one place in code, **prefer to declare that interface in a global `.d.ts` typing file rather than a local `.ts` file** (see [Declaration Files](#declaration-files)).
* Interface names should be PascalCase:

	```typescript
	interface Person {
		name: string;
	}
	```
* Where function return types are objects, **do** define the type as an interface at the top of the file (it will be scoped to the file).

	```typescript
	// Bad
	function performOperation (): {str: string, num: number} {
		// Code which returns an object
	}

	// Also bad
	function performOperation (): {
		str: string;
		num: number;
	} {
		// Code which returns an object
	}

	// Good:
	interface SomeType {
		str: string;
		num: number;
	}

	function performOperation (): SomeType {
		// Code which returns an object
	}
	```

Format interfaces the same as regular JavaScript objects, i.e. one space after the colon and no spaces before. Sort keys alphabetically.

	```typescript
	interface FooBar {
		myLongerVar: any;
		myVar: string;
		optionalVar?: string;
	}
	```

*Notable TypeScript feature*: redefinition of an interface will extend the original definition. See [TypeScript Handbook: Declaration Merging](http://www.typescriptlang.org/Handbook#declaration-merging).

### `any` Typing

* **Do not** allow implicit `any` typing. Always type variables and functions where it is possible to misinterpret. Additionally, turn on compiler warning flags for implicit `any` types (if possible in your TS compiler).

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

* **Do** provide comments for something like a union type, which has a defined return type that just can't be expressed in TypeScript yet.

	```typescript
	// @returns Promise<Person> | Person
	function getPerson (): any {
		// datastore retreival code
	}
	```

### Generic Types

In general, wherever possible try and use generic types rather than using `any`.

#### Generic Functions

Example paraphrased from [TypeScript Handbook](#http://www.typescriptlang.org/Handbook#generics-hello-world-of-generics)

```typescript
/**
 * Bad -- we know that `arg` and the `return` value are the same type,
 * they should be set to the same generic type
 */
function identity (arg: any): any {
    return arg;
}

// Good
function identity<T> (arg: T): T {
    return arg;
}
```

#### Generic Classes

The same rules that went for functions also goes for classes: where multiple fields/parameters/returns are of type `any` are in fact the same type, use a generic type.

#### Generic Arrays

Prefer to type-annotate an array of type `T` using the shorthand `T[]`.

```typescript
// Bad
var arr: Array<number>;

// Good
var arr: number[];
```

## Functions

### Lambda Syntax/Arrow Functions

Arrow functions in TypeScript, much like in ES6, are simply syntactic sugar. From the [TypeScript Handbook](http://www.typescriptlang.org/Handbook#functions-lambdas-and-using-39this39):

> To fix [the context of an anonymous function], we switch the function expression to use the lambda syntax ( ()=>{} ) rather than the JavaScript function expression. This will automatically capture the `this` available when the function is created rather than when it is invoked.

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

### Object Method Shorthand

In both class definitions and object literal declarations, prefer the object method shorthand form of function declaration. See the [Class Definitions](#class-definitions) section for more info on class declaration style. For object literals:

```typescript
var literal = {
	// Bad
	foo: function () {
	
	},
	
	// Good
	foo () {
	
	}
}
```

## Classes and Object Orientation

### Class Definitions

* Class names should be in `PascalCase`
* The order of the class definition should be something like as follows (the exact ordering is not so important as picking an order and sticking with it):
  * Public class properties
  * Private class properties
  * `constructor`
  * Static functions
  * Public instance methods
  * Private instance methods
* Class properties should all have types and semantic comments
* All class functions (both static and instance)
  * Should be in object function shorthand (`functionName () {...}`)
  * Should be in `camelCase`
  * Should have JSDoc comments as described in Wikia JavaScript guidelines
* There should be no newlines between properties, but newlines between the last property and the constructor, between the constructor and methods, and between all the other methods.

	```typescript
	Class SomeClass {
		// This is a property whose type is defined
		property: string;
		// Bad
		propertyWithNoType;
		// Here is a private property
		private propertyWhichIsPrivate;

		constructor () {
			// Initialize object
		}

		static someStaticMethod () {
		
		}

		/**
		 * @desc This is a method. Note it’s defined in object function shorthand
		 * @returns some meaningful description of return value
		 */
		someMethod (parameter: string) {
				// Code
		}

		// Bad
		someOtherMethod: function () {
			// Code
		}
		
		// Also bad
		function someOtherMethod () {
			// Code
		}
		
		private somePrivateMethod () {
		
		}
	}
	```

### Object-Orientation

Wherever applicable, use the Object-Oriented Paradigm to your advantage. Typescript contains both the `extends` and `implements` class-building keywords, which function similarly to how Java's `extends` and `implements` keywords work. As a refresher, if `A` and `B` are classes):

`A extends B`:

* Is for when `A` [Is-a](http://en.wikipedia.org/wiki/Is-a) `B`
* Using this keyword gives every `B` every property and method of `A`, and preserves types and method implementations
* Cannot be used if `B` is an `interface`, since `interface`s don't include method implementations

`A implements B, C, ...`:

  * Is for when either `A` contains the same properties as more than one class (i.e., multiple inheritance from `B`, `C`, and any other implemented classes) AND/OR
  * `A` should provide its own implementation for the methods in `B`, `C`, etc.
  * Can be used where `B`, `C`, etc. are `interface`s *or* `class`es
  
Note: The [TypeScript Handbook entry on Mixins](http://www.typescriptlang.org/Handbook#mixins) provides a neat way to inherit from multiple classes using `extends` *while preserving method implementations*.

## Modules

Modules are TypeScript's mechanism for encapsulation. They are designed to replace [Immediately Invoked Function Expression](../JavaScript/CodingConventions.md#immediately-invoked-function-expressions-iife) (and, in fact, compile to IIFEs).  They are to be used wherever information hiding is needed.
* They are declared as blocks with the `module` keyword
* Each module should have **its own file**.
* Should have a block comment describing the use of the module
* Items in the module that should be externally visible are prefaced with the `export` keyword.
* Module names should be in `PascalCase`
* All exports should be placed at the bottom of the file, with one space and a block comment for each export, so that it is clear what is being exported

Example taken and modified from [TypeScript Handbook: Modules](http://www.typescriptlang.org/Handbook#modules)

```typescript
/**
 * @desc Module which provides classes for string validation
 */
module Validation {
	// Regex matching one or more upper or lower case letters
	var lettersRegexp = /^[A-Za-z]+$/;
	// Regex matching one or more digits 0-9
	var numberRegexp = /^[0-9]+$/;

	/**
	 *  @desc Interface for String validators
	 */
	export interface StringValidator {
		/**
		 * @returns whether or not the string s is valid
		 */
		isAcceptable (s: string): boolean;
	}

	/**
	 * @desc validator for letters-only strings
	 */
	export class LettersOnlyValidator implements StringValidator {
		/**
		 * @desc tests whether all the characters in s are letters
		 */
		isAcceptable (s: string) {
			return lettersRegexp.test(s);
		}
	}

	/**
	 * @desc validator for numbers
	 */
	export class ZipCodeValidator implements StringValidator {
		/**
		 * @desc tests whether all of the characters in s are numbers
		 * and also if s is 5 characters long
		 */
		isAcceptable (s: string) {
			return s.length === 5 && numberRegexp.test(s);
		}
	}
}
```

## Declaration Files

TypeScript Declaration files (extension `.d.ts.`) allow the TypeScript compiler to know the types of variables and functions from external libraries. They have to be hand-created, but there is also a quite high-quality repository of TS Declaration files called [DefinitelyTyped](http://definitelytyped.org/) which is expansive and well-maintained.

A Declaration file can also be generated for an existing TypeScript file by compiling it with the `--declaration` flag, as in
```
tsc --declaration file.ts
```
