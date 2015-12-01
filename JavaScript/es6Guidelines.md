Table Of Contents
===
* [General Guidelines](#general-guidelines)
  - [`const` and `let` over `var`](#const-and-let-over-var)
  - [Arrow functions gotchas](#arrow-functions-gotchas)
  - [Default Exports](#default-exports)
  - [Classes VS Modules](#classes-vs-modules)
  - [Imports first](#imports-first)
* [ESLint](#eslint)
  - [How to read .eslint.rc](#how-to-read-eslintrc)
  - [Cascading and Hierarchy](#cascading-and-hierarchy)
  - [Defined globals](#defined-globals)
  - [Most important enabled rules](#most-important-enabled-rules)
  - [Links](#links)


## General Guidelines

### `const` and `let` over `var`

Recommended article about var/let/const - [erric-elliot/javascript-es6-var-let-or-const](https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75#.uw6acfhkw)

We recommend following rule: `const > let > var`.  
We listed some useful tips to help you decide which declaration to use.

1.  **Are you going to reassign the variable's value?**  
    **no)** use const  
    **yes)** use let

    _"No reassign" means - you are not going to change the reference._  

    **Something like that is correct:**

    ```javascript
    const point = {
      x: 5,
      y: 5
    };
    point.x = 10
    ```

   **However, something like this is not:**

    ```javascript
    const shape = 'triangle';
    shape = 'circle';
    ```

2.  **Always use one let/const/var declaration in block scope**  
    You should put lets and consts on top of the block.  
    Even though consts and lets are not hoisted like vars it's a good practice to put those declarations on top of the block to increase code readability.

    Want to learn more about scoping? Read [this article](http://www.2ality.com/2015/02/es6-scoping.html)

3.  **Try to keep let/const/var scope as small as possible**

4.  **When to use `var`?**

    **Never**. No, seriously. There are almost no valid use cases for `var` right now.

### Arrow functions gotchas
1.  **Lexical `this` Binding**

    `this` is inherited from the scope of which the arrow function is defined

2.  **`this` cannot be changed**

    this cannot be changed inside the function

3.  **Implied Returns**

    Curly braces are optional for arrow functions, they can be left off to create a super simple function. In those types of definitions, the result of the expression is always returned.

    Example:

    ```javascript
    let sum = (x, y) => x + y;
    ```


4.  **`arguments` gotcha**

    Check out following example: (code comes from [Ooyala Player](https://github.com/Wikia/mercury/blob/dev/front/scripts/mercury/modules/VideoPlayers/Ooyala.js))
    ```javascript
    setupPlayer() {
      this.params = $.extend(this.params, {
        onCreate: (...args) => {
          return this.onCreate.apply(this, args);
        }
      });
    ```

    Note the usage of `...args` instead of `arguments` keyword. **Why?**

    Well, we used `arguments` and it turned out that **arrow functions don't have lexical `arguments`**. So instead `arguments` from setupPlayer were used. What we really wanted was to use `arguments` passed to onCreate hook called by Ooyala, hence usage of [rest parameter](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/rest_parameters).

Recommended read:
* http://blog.getify.com/arrow-this/
* http://toddmotto.com/es6-arrow-functions-syntaxes-and-lexical-scoping/
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions

### Default Exports

**Always add export default**

If you don't know which property should be exported as the default one, decide which one is the best representative of particular module or which one is going to be used externally most often.

### Classes VS Modules

**Favor Modules over Classes.**

However, the question remains, when I should use class?  
To avoid bias instead of answering this question, we link few articles which should help you decide whether to use class or module.

*   [i-would-never-argue-that-es6-classes-are-clearly-a-better-choice-than-composition-modules-or-prototypal-OO](https://medium.com/@rauschma/i-would-never-argue-that-es6-classes-are-clearly-a-better-choice-than-composition-modules-or-891e462da85b#.bfhfjc74j)
*   [a-simple-challenge-to-classical-inheritance-fans](https://medium.com/javascript-scene/a-simple-challenge-to-classical-inheritance-fans-e78c2cf5eead#.vh341a5iq)[](https://medium.com/javascript-scene/a-simple-challenge-to-classical-inheritance-fans-e78c2cf5eead#.vh341a5iq)

### Imports first

**Imports should be the first thing you put in your files.**  

_Note: ES6 imports are declarative and meant for static analysis. They cannot be dynamic._  

## ESLint

### How to read .eslint.rc

Here is small piece of our .eslint.rc file:
```json
"no-process-exit": 0,
"no-restricted-modules": 0,
"no-sync": 0,
"array-bracket-spacing": [2, "never"]
```
What all those things mean?

*   the key is the name of the rule - `"no-process-exit"`
*   the number after key means:  

    *   0 - turn the rule off
    *   1 - turn the rule on as a warning (doesn't affect exit code)
    *   2 - turn the rule on as an error (exit code is 1 when triggered)
*   `[2, "never"]` - rule is turned on as an error and plugin called `never` is on 

List of all available rules with descriptions is available [here](http://eslint.org/docs/rules/).


### Cascading and Hierarchy

The configuration cascade works by using the closest `.eslintrc` file to the file being linted as the highest priority, then any configuration files in the parent directory, and so on.

### Defined globals

*   / - [/.eslintrc#L11](https://github.com/Wikia/mercury/blob/dev/.eslintrc#L11)
*   /front - [/front/.eslintrc#L7](https://github.com/Wikia/mercury/blob/dev/front/.eslintrc#L7)
*   /server/ - [/server/.eslintrc](https://github.com/Wikia/mercury/blob/dev/server/.eslintrc)

### Most important enabled rules

*   **Prefer object shorthand**

    How to do things right:

    ```javascript
    const bar = 'bar',
        foo = 'foo';

    return {
      foo,
      bar,
      fizz: 'fizz'
    };
    ```

    How **not** to do things:

    ```javascript
    const bar = 'bar',
        foo = 'foo';

    return {
      foo: foo,
      bar: bar,
      fizz: 'fizz'
    };
    ```

*   **Prefer dot notation in objects**

    How to do things right:

    ```javascript
    point.x = 7;
    ```

    How **not** to do things:
    ```javascript
    point['x'] = 7;
    ```

*   **New line after variables declaration**

    How to do things right:

    ```javascript
    sum() {
      const point = {
        x: 5,
        y: 7
      };

      //...rest of method's body
    }
    ```

    How **not** to do things:

    ```javascript
    sum() {
      const point = {
        x: 5,
        y: 7
      };

      //...rest of method's body
    }
    ```

*   **Use `()=>` when possible**

    if you don't know if you should use arrow function read the section about [Arrow functions gotchas](#arrow-functions-gotchas). If you need to rely on something arrow function doesn't provide use regular function declaration

*   **Only one `var/let/const` declaration inside block**

    More information in [`const` and `let` over `var`](#const-and-let-over-var) section

### Links

*   [eslint docs](http://eslint.org/)
