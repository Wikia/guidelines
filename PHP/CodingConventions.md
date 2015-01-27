# Wikia PHP Coding Conventions

This file documents Wikia's PHP coding conventions. To propose changes to this document, please open a pull request
with your changes and tag [@engineers](https://github.com/orgs/Wikia/teams/engineers) for review.


## TOC

* [Base Rules](#base-rules)
* [Additional Rules](#additional-rules)
  * [Function Length](#function-length)
  * [Line Length](#line-length)
  * [Conditional Logic](#conditional-logic)
  * [Array Syntax](#array-syntax)
  * [Type-Checking and Assertions](#type-checking-and-assertions)
* [Tools](#tools)
  * [MediaWiki PHP Style Helper](#mediawiki-php-style-helper)

## Base Rules

Wikia will continue to follow the MediaWiki style guidelines for PHP writing code. These guidelines for PHP can be
found in the [MediaWiki Coding Conventions](http://www.mediawiki.org/wiki/Manual:Coding_conventions/PHP) manual.

## Additional Rules

### Function Length

A function definition should be easy for a reader to digest.  This means two things:

* A function should not do too much
* A reader should not have to scroll to see the entire definition

For the first point, "Clean Code" ([PDF](https://one.wikia-inc.com/wiki/File:Clean_Code_Book.pdf)) sums this up nicely:

> Functions should do one thing.  They should do it well.  They should do it only.

If a function does more than one thing, it should be split into smaller functions which the original function can
call.

To the second point, a function should be less than a page in length.  Anything longer almost certainly does more than
one thing, and forces a reader to scroll to view the whole function.

### Line Length

Lines should be limited to 120 characters.  This limit is chosen as a typical IDE default for line breaks.  The spirit
of this guideline is that a developer should not have to scroll horizontally to see the end of a line.

If a single line becomes too long, newlines should be added at appropriate breakpoints.

### Conditional Logic

Conditional tests should not do more than two checks on a single line.  If a conditional requires more than two checks,
one of the following remedies should be used:
 
* each check should be split onto its own line
* the single conditional should be split into separate conditionals of two or less checks
* all checks replaced by a function call

Example:

```php
// Bad
if ( F::App()->wg->User->can( 'edit' ) && F::App()->wg->Skin->getSkinName() == 'oasis' && empty( F::app()->wg->NoExternals ) {
   // do something
}

// Good - each check split onto its own line
if ( F::App()->wg->User->isAllowed( 'edit' ) &&
     F::App()->wg->Skin->getSkinName() == 'oasis' &&
     empty( F::app()->wg->NoExternals )
   ) {
    // do something
}

// Good - single conditional split into separate conditionals of two or less checks
if ( !F::App()->wg->User->isAllowed( 'edit' ) ) {
    return;
}
if ( F::App()->wg->Skin->getSkinName() == 'oasis' && empty( F::app()->wg->NoExternals ) {
    // do something
}

// Good - checks replaced by a function call
if ( oasisUserCanEdit() ) {
    // do something
}

function oasisUserCanEdit() {
    return (
        F::App()->wg->User->isAllowed( 'edit' ) &&
        F::App()->wg->Skin->getSkinName() == 'oasis' &&
        empty( F::app()->wg->NoExternals )
    );
}

```

### Array Syntax

PHP supports 2 forms of syntax for arrays, ```array()``` and the shorter ```[]``` which was introduced in PHP 5.4.
At Wikia we favor the 2nd version and actively try and convert old style arrays to the new style. If you encounter
old style arrays during development, please convert them to the new style.

In regards to syntax, padding should be added for both array definitions, as well as anonymous arrays. Note that
padding should not be applied to array indexing.

```php
// good
$foo = [ 'bar', 'baz', 'zed' ];
myFunctionCall( [ 'needs', 'an', 'array' ] );
$foo['theDude'] = 'abides';

// bad
$foo = ['bar', 'baz', 'zed'];
myFunctionCall( ['needs', 'an', 'array'] );
$foo[ 'theDude' ] = 'abides';
```

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
which is provided via a sub-module in this repo under `mediawiki/tools/code-utils`. To use `stylize.php` on your code before you check in:

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
