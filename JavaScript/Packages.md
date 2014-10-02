In order to support [DRYness](http://en.wikipedia.org/wiki/Don't_repeat_yourself) across the increasing number of applications and services using JavaScript in the Wikia ecosystem, all engineers should consider the following guidelines when writing JavaScript modules.

## Encapsulation, Explicitness and API
When developing your JS modules, aim to encapsulate your modules. If your module requires external input, make sure your module exposes a public API for module consumers to pass in that input explicitly. In the past, the organization has been lax on the use of globals in modules. This is an antipattern that breaks encapsulation and makes it so that your module becomes tightly coupled with it's application context. *Do not use globals in your modules*.

## Packaging and Versioning 
Developers should use [npm](http://www.npmjs.org) and [bower](http://www.bower.io) (for front-end deps) to manage their JavaScript dependencies. All projects, from full Node.js applications to jQuery plugins should have a valid [package.json](https://www.npmjs.org/doc/files/package.json.html) that uses [semantic versioning](http://semver.org/). This is important so that developers can manage and deploy safe, version-locked modules for their applications. You do not have to have your
module in npm or bower's public repositories to manage your package using npm or bower. Both tools support installing packages from github. For instance:
```
// Install npm package from github
$ npm install Wikia/foo --save
// with version
$ npm install Wikia/foo#1.4.0 --save

// or in package.json
"dependencies": {
	"foo": "Wikia/foo"
}

// Install bower package from github (more details here: http://bower.io/docs/api/#install)
$ bower install https://github.com/user/package.git --save
// with version
$ bower install https://github.com/user/package.git#1.4.0

// or in your bower.json
"dependencies": {
	"foo": "http://bower.io/docs/api/#install"
}
```
