# Ember.js Coding Conventions

## Table of Contents
* [What is Ember](#what-is-ember)
* [Naming Conventions](#naming-conventions)
* [Accessing Ember Namespace](#accessing-ember-namespace)
* [Declaring Ember Objects](#declaring-ember-objects)
* [Accessors and Mutators](#accessors-and-mutators)
* [Native Prototype Modifications](#native-prototype-modifications)

## What is Ember?
> Ember.js is an open-source client-side JavaScript web application framework based on the model-view-controller (MVC) software architectural pattern. It allows developers to create scalable single-page applications[1] by incorporating common idioms and best practices into a framework that provides a rich object model, declarative two-way data binding, computed properties, automatically-updating templates powered by Handlebars.js, and a router for managing application state. [(Wikipedia)](https://docs.google.com/a/wikia-inc.com/document/d/1c2o5ewMOkHFwrNrQy60bo20a0zO3E-tqNWQ6fiU2NC8/edit#)

## Naming Conventions
We follow the canonical naming conventions established by the Ember Core Team. The full Ember naming conventions documentation can be found [here](http://emberjs.com/guides/concepts/naming-conventions/).

### File naming convention:
All file names should be dashed lowercased

```bash
//bad
app/components/myComponent.js

//good
app/components/my-components.js
```

### Route naming conventions:
Both route names and paths should be dasherized.
No trailling slash on the end of the path.

```javascript
// good
this.route('infobox-builder', {
	path: '/infobox-builder/:template_name'
});

// bad
this.route('infobox-builder', {
	path: '/infoboxBuilder/:template_name'
});

// bad
this.route('infoboxBuilder', {
	path: '/infoboxBuilder/:template_name'
});
```
##### When path is the same as route name (client-Ember-side only), there's no need to specify it:
```javascript
// good
this.route('article-preview');

// bad
this.route('article-preview', {
	path: '/article-preview'
});
```
##### Dynamic segments in paths should be underscored.
```javascript
// good
this.route('mainPageCategory', {
	path: '/main/category/:category_name'
});

// bad
this.route('mainPageCategory', {
	path: '/main/category/:category-name'
});

// bad
this.route('mainPageCategory', {
	path: '/main/category/:categoryName'
});
```

More info:
* Ember routing: https://guides.emberjs.com/v2.3.0/routing/defining-your-routes/

## Accessing Ember Namespace
When accessing the `Ember` object, always use import and object destructuring.

```javascript
// bad
const FooView = Ember.View.extend();

// good
import Ember from 'ember';
const {View} = Ember;

const FooView = View.extend();
```

## Declaring Ember Objects
In Ember objects, declare in this order:
* Object properties
* Calculated property declarations
* `actions` hash
* `observer` declarations
* Overloaded functions (`click`/`mouseUp` handlers in `Ember.View` objects, etc.)
* Call `reopenClass` to declare additional functions

Other notes:
* Always use `Ember.Object.extend` to create Ember objects
* When extending built-in Ember classes, export the new class from its module

## Accessors and Mutators
Always use `extend` and `get` to add and `set` to access properties in `Ember.Object`s. There may be unexpected side effects if the dot or bracket operators are used, because Ember sometimes chooses to store properties in sub-objects.

When setting multiple properties on an `Ember.Object` instance, use [`setProperties`](http://emberjs.com/api/classes/Ember.Object.html#method_setProperties).

```javascript
import Ember from 'ember';
const {Object: EmberObject, Logger} = Ember;

const Person = EmberObject.extend({
	// Default values
	name: '',
	occupation: '',
	address: ''
})

const batman = Person.create();

// good
batman.set('name', 'Bruce Wayne');

// good -- set is chainable
batman.set('occupation', 'Vigilante')
	.set('address', 'Wayne Mansion');

// good -- set can add previously undefined properties
batman.set('toolbelt', ['batarang', 'bat gas', 'grappling hook']);

// good
Logger.info(batman.get('toolbelt').length); // Logs '3'

// bad
batman.toolbelt = ['balloon', 'silly putty'];

// bad
Logger.info(batman.toolbelt.length) // Might log '3', might throw an error

// setting multiple properties
// good
batman.setProperties({
	sidekick: 'Robin',
	realName: 'Bruce Wayne'
});

// bad
batman.set('sidekick', 'Robin');
batman.set('realName', 'Bruce Wayne');
```

## Native prototype modifications
Never use native prototype extensions provided by Ember. These are disabled. 

```javascript
// bad
function () {
	/* function body */
}.observes('foo.bar');

// good
import Ember from 'ember';
const {observer} = Ember;

observer('foo.bar', function () {});
```
