# Ember.js Coding Conventions

## Table of Contents
* [What is Ember](#what-is-ember)
* [Accessing Ember Object](#accessing-ember-object)
* [Native Prototype Modifications](#native-prototype-modifications)
* [Logging](#logging)

## What is Ember?
> Ember.js is an open-source client-side JavaScript web application framework based on the model-view-controller (MVC) software architectural pattern. It allows developers to create scalable single-page applications[1] by incorporating common idioms and best practices into a framework that provides a rich object model, declarative two-way data binding, computed properties, automatically-updating templates powered by Handlebars.js, and a router for managing application state. [(Wikipedia)](https://docs.google.com/a/wikia-inc.com/document/d/1c2o5ewMOkHFwrNrQy60bo20a0zO3E-tqNWQ6fiU2NC8/edit#)

## Accessing Ember Object
When accessing the Ember global object, always use the terse alias to the Ember global object: `Em`. For example:

```typescript
// bad
var FooView = Ember.View.extend();

// good
var FooView = Em.View.extend();
```
## Native prototype modifications
* Use Ember’s additions to native prototypes, for instance:

```typescript
// bad
Em.observer('foo.bar', function () {});

// good
function () {
	/* function body */
}.observes(‘foo.bar’);
```

## Logging
Use `Ember.Logger` instead of `console.log` everywhere applicable.
In Ember objects, declare in this order:
* Object properties
* Calculated property declarations
* `actions` hash
* `observer` declarations
* Overloaded functions (`click`/`mouseUp` handlers in `View` objects, etc.)
* Call `reopenClass` to declare additional functions

