# Wikia App JavaScript modules conventions

This styleguide defines the JavaScript modules strucutre conventions for Wikia App (https://github.com/Wikia/app).

## TOC

* [Files/directories structure](#filesdirectories-structure)
* [Entry point files](#entry-point-files)
* [Modules](#modules)
* [Tracking](#tracking)


## Files/directories structure

### Keep all JS in scripts folder

Keep all JavaScript files in `scripts` folder which should be located in extension's root directory (`extensions/wikia/MyExtensions/scripts`)

### Entry-point files

All entry points file-names should start with `index.`. If there is only one entry point file it should be named `index.js`

```javascript
// bad
foo.js
entry.js
start.js
moduleName.js

// good
index.js

// good only if there are more than one entry point files
index.example.js
index.foo.js
```

## Entry point files

### Keep them small

Keep entry point files as small as possible. They should usually just execute some initialization functions from other files.
```
TODO: what if we want to have just one js file in extension?
```

## Modules

### Name should start with extension name

Each defined Modil module name should start with extension name prefix.

```javascript
// bad
define('tracking', ['dependency'], function (dependency) {});

// good
define('MyExtensionName.tracking', ['dependency'], function (dependency) {});
```

Read more here: https://github.com/Wikia/guidelines/blob/master/JavaScript/CodingConventions.md#amd-modules

### Define module as class

JS module should be define as class. Currently we use ES5 so should define them using `function` keyword.

```javascript
// good
define('MyExtensionName.tracking', ['dependency'], function (dependency) {
  function Tracking () {
    // constructor
  }
  
  Tracking.prototype.myInstanceMethod = function () {
    // public non-static method
  };
  
  Tracking.myStaticFunction = function () {
    // public static method
  };
  
  function _myPrivateFunction () {
    // private function
  }
  
  return Tracking;
});
```

### Private function names should start with underscore

All private function should start with underscore (`_`).

```javascript
// bad
function myPrivateFunction () {}

// good
function _myPrivateFunction () {}
```

## Tracking

Keep all tracking in separated file(s). Tracking file-names should start with `tracking.`. If there is only one file that contains tracking functionality it should be named `tracking.js`.

