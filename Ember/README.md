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

### Route naming conventions:
On both the server-side and client-side of Mercury application, route handlers should be camelCased and route paths should be dasherized.
No trailling slash on the end of the path.

Server side:
```javascript
	// good
	{
		method: 'POST',
		path: '/article-preview',
		handler: articlePreview
	}

	// bad
	{
		method: 'POST',
		path: '/articlePreview',
		handler: articlePreview
	}

	// bad
	{
		method: 'POST',
		path: '/articlePreview',
		handler: article-preview
	}

	// bad
	{
		method: 'POST',
		path: '/article-preview',
		handler: articlePreview/
	}
```
Client side:
```javascript
	// good
	this.route('articlePreview', {
		path: '/article-preview'
	});

	// bad
	this.route('article-preview', {
		path: '/article-preview'
	});
```

##### Wildcards in paths should be camelCased.
```javascript
Server side:
	// good
	{
		method: 'GET',
		path: `${localSettings.apiBase}/article/comments/{articleId}/{page?}`,
		handler: getArticleCommentsHandler
	},

	// bad
	{
		method: 'GET',
		path: `${localSettings.apiBase}/article/comments/{article-id}/{page?}`,
		handler: getArticleCommentsHandler
	},
```
Client side:
```javascript
	//good
	this.route('mainPageCategory', {
		path: '/main/category/:categoryName'
	});

	//bad
	this.route('mainPageCategory', {
		path: '/main/category/:category-name'
	});
```

## Accessing Ember Namespace
When accessing the `Ember` global object, always use its abbreviated alias: `Em`.

```javascript
// bad
var FooView = Ember.View.extend();

// good
var FooView = Em.View.extend();
```

## Declaring Ember Objects
In Ember objects, declare in this order:
* Object properties
* Calculated property declarations
* `actions` hash
* `observer` declarations
* Overloaded functions (`click`/`mouseUp` handlers in `Em.View` objects, etc.)
* Call `reopenClass` to declare additional functions

Other notes:
* Always use `Em.Object.extend` to create Ember objects
* When extending built-in Ember classes, declare the new class as a member of the global `App` object

## Accessors and Mutators
Always use `extend` and `get` to add and `set` to access properties in `Em.Object`s. There may be unexpected side effects if the dot or bracket operators are used, because Ember sometimes chooses to store properties in sub-objects.

When setting multiple properties on an `Ember.Object` instance, use [`setProperties`](http://emberjs.com/api/classes/Ember.Object.html#method_setProperties).

```javascript
App.Person = Em.Object.extend({
	// Default values
	name: '',
	occupation: '',
	address: ''
})

var batman = App.Person.create();

// good
batman.set('name', 'Bruce Wayne');

// good -- set is chainable
batman.set('occupation', 'Vigilante')
	.set('address', 'Wayne Mansion');

// good -- set can add previously undefined properties
batman.set('toolbelt', ['batarang', 'bat gas', 'grappling hook']);

// good
Em.Logger.info(batman.get('toolbelt').length); // Logs '3'

// bad
batman.toolbelt = ['balloon', 'silly putty'];

// bad
Em.Logger.info(batman.toolbelt.length) // Might log '3', might throw an error

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
Avoid using native prototype extensions provided by Ember. Ember (and ECMAScript too!) are moving towards decorator syntax and we will support that as well.

```javascript
// bad
function () {
	/* function body */
}.observes('foo.bar');

// good
Em.observer('foo.bar', function () {});
```
