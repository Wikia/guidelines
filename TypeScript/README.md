#TypeScript Language Rules

* Refer to JavaScript style guidelines wherever applicable
* Use object method shorthand where applicable

## Lambda Syntax/Arrow Functions

Arrow functions in TypeScript, much like in ES6, are simply syntactic sugar. From the [TypeScript guide](http://www.typescriptlang.org/Handbook#functions-lambdas-and-using-39this39):

> To fix [the context], we switch the function expression to use the lambda syntax ( ()=>{} ) rather than the JavaScript function expression. This will automatically capture the 'this' available when the function is created rather than when it is invoked.


* **Do not** use them in object literals
* **Do not** use them in the global scope
* **Do** use them in place of anonymous functions in callbacks, wherein the correct value of `this` will be stored by the TypeScript compiler. 


```typescript
var obj = {
	foo: null, 
	bar () {
		// parent scope	
		var xhr = $.get(‘/example’);
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
		var xhr = $.get(‘/example’);
		xhr.success((data) => {
			// TypeScript aliases the correct reference of this to:
			_this.foo = data;
		});
	}
}
```

## Typing: Interfaces and Basic Types
* **Do** not allow implicit `any` typing. Always type variables and functions where it is possible to misinterpret. Additionally, turn on compiler warning flags for implicit `any` types (if possible in your TS compiler).

* **Do** define type of variables when they are declared but not initialized

```typescript
// Bad:
var str;
// Other code
str = ‘hello world’;

// Good:
var str: string;
// Other code
str = ‘hello world’;
```

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

* Do **not** define types where the variable is assigned inline and the type is obvious

```typescript
// Bad:
var str: string = ‘hello world’,
num: number = 1;

// Good:
var str = ‘hello world’,
 num = 1;
```
