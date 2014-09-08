# Wikia TypeScript Guidelines

## Table Of Contents
* [Overview](#overview)
* [Tools](#tools)
  * [Compiler](#compiler)
  * [IDEs/Text Editors](#idestext-editors)
  * [Linter](#linter)
* [Functions](#functions)
  * [Lambda Syntax/Arrow Functions](#lambda-syntaxarrow-functions)
  * [Object Method Shorthand](#object-method-shorthand)
* [Typing: Interfaces and Basic Types](#typing-interfaces-and-basic-types)
  * [`any` Typing](#any-typing)
  * [Interfaces](#interfaces)
  * [Basic Types](#basic-types)
* [Generic Types](#generic-types)
* [Class Definitions](#class-definitions)
* [Object-Orientation](#object-orientation)
* [Declaration Files](#declaration-files)

## Overview

* TypeScript is a superset of JavaScript, so do refer to the [Wikia JavaScript Guidelines](https://github.com/Wikia/guidelines/blob/master/JavaScript/CodingConventions.md) wherever applicable
* Before referring to these guidelines, read the [Typescript Handbook](http://www.typescriptlang.org/Handbook) in full to understand what features are available in TS

## Tools

### Compiler

The easiset way to acquire a TypeScript Compiler is as an [NPM package](https://www.npmjs.org/package/typescript). It is also available as a VisualStudio plugin (see TS website).

### IDEs/Text Editors

* PhpStorm/WebStorm supports TypeScript with code completion, refactoring and debugging
* Syntax highlighting is available for Sublime Text/

### Linter

There is a [TypeScript linter available on NPM](https://www.npmjs.org/package/tslint), and also plugins available for various build task runners/build systems.

## Functions

### Lambda Syntax/Arrow Functions

Arrow functions in TypeScript, much like in ES6, are simply syntactic sugar. From the [TypeScript guide](http://www.typescriptlang.org/Handbook#functions-lambdas-and-using-39this39):

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

## Typing: Interfaces and Basic Types

The general rule is that one should aim to have full type coverage over the code, but not to have excessive or unnecessary type annotation.

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

###Interfaces

* For interfaces that are intended for use in more than one place in code, **prefer to declare that interface in a global `.d.ts` typing file rather than a local `.ts` file**. If you use a code-completing IDE like PhpStorm, the types will be visible to you anywhere in the code regardless, so this restraint doesn't make coding in types any harder.

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

Do **not** define types where the variable is assigned inline and the type is a basic type (`boolean`, `number`, `string`, `Array<T>`, or `enum`), or if it is being set to the return value of a function whose return type is annotated.

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

## Generic Types

In general, wherever possible try and use generic types rather than using `any`.

### Functions

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

### Classes

The same rules that went for functions also goes for classes: where multiple fields/parameters/returns are of type `any` are in fact the same type, use a generic type.

### Arrays

Prefer to type-annotate an array of type `T` using the shorthand `T[]`.

```typescript
// Bad
var arr: Array<number>;

// Good
var arr: number[];
```

## Class Definitions

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
	 * @returns What the semantic value of the return is
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

## Object-Orientation

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

## Declaration Files

TypeScript Declaration files (extension `.d.ts.`) allow the TypeScript compiler to know the types of variables and functions from external libraries. They have to be hand-created, but there is also a quite high-quality repository of TS Declaration files called [DefinitelyTyped](http://definitelytyped.org/) which is expansive and well-maintained.

A Declaration file can also be generated for an existing TypeScript file by compiling it with the `--declaration` flag, as in
```
tsc --declaration file.ts
```
