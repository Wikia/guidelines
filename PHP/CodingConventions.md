# Wikia PHP Coding Conventions

This file documents Wikia's PHP coding conventions.  To propose changes to this document, please open a pull request
with your changes and tag [@engineers](https://github.com/orgs/Wikia/teams/engineers) for review.


## TOC

* [Base Rules](#base-rules)
* [Additional Rules](#additional-rules)
  * [Function Length](#function-length)
  * [Type-Checking and Assertions](#type-checking-and-assertions)
* [Tools](#tools)
  * [MediaWiki PHP Style Helper](#mediawiki-php-style-helper)

## Base Rules

Wikia will continue to follow the MediaWiki style guidelines for PHP writing code. These guidelines for PHP can be
found in the [MediaWiki Coding Conventions](http://www.mediawiki.org/wiki/Manual:Coding_conventions/PHP) manual.

## Additional Rules

### Function Length

Functions should have a limited length.  This limit is loosely defined as one “page”, where a page is the number
of lines that fit into your editor window or less.  This definition is intentionally vague to allow flexibility but the
spirit of this guideline is that a developer should not have to scroll to see a full function definition.

If a single function becomes too long it should be broken up into smaller functions which the original function can
call.

### Type-Checking and Assertions

When validating function input types, prefer [type hinting](http://php.net/manual/en/language.oop5.typehinting.php) over
[```instanceof```](http://php.net/manual/en/internals2.opcodes.instanceof.php) or [```assert```](http://php.net/manual/en/function.assert.php)
checks where possible. Inside a function, use ```assert``` for type checking and other programming errors and never
silently fail. **NEVER** pass a string to ```assert```. Example:

```php
use Wikia\Util\Assert;

function getOtherClass( MyClass $a ) { // will fail automatically if $a is not an instance of MyClass
	$b = doSomething( $a );

	Assert::boolean( $b instanceof SomeOtherClass );
	// do something with SomeOtherClass
}

function doSomething( MyClass $a ) {
	if ( $a->someCondition() ) {
		return new SomeOtherClass( $a );
	}

	return null;
}
```

## Tools

### MediaWiki PHP Style Helper

MediaWiki provides a code formatting helper ([stylize.php](https://git.wikimedia.org/blob/mediawiki%2Ftools%2Fcode-utils.git/master/stylize.php))
which is provided via a submodule in this repo under `mediawiki/tools/code-utils`. To use `stylize.php` on your code
before you check in:

```sh
git clone git@github.com:Wikia/guidelines.git /usr/wikia/source/guidelines
cd /usr/wikia/source/guidelines; git submodule init && git submodule update
cd /usr/wikia/source/wiki
../guidelines/PHP/bin/php-mediawiki-stylize /path/to/your/file.php
```

If you want to stylize all of the edits that you have before you stage them you can do the following:

```sh
cd /usr/wikia/source/wiki
git diff --name-only | grep php | while read -r i; do ../guidelines/PHP/bin/php-mediawiki-stylize “$i”; done
```
