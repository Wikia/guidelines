# Wikia PHP Coding Conventions

This file documents Wikia's PHP coding conventions.  To propose changes to this document, please open a pull request
with your changes and tag [@engineers](https://github.com/orgs/Wikia/teams/engineers) for review.


## TOC

* [Base Rules](#base-rules)
* [Additional Rules](#additional-rules)
  * [Function Length](#function-length)
  * [Line Length](#line-length)
  * [Conditional Logic](#conditional-logic)
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
spirit of this guideline is that a developer should not have to scroll vertically to see a full function definition.

If a single function becomes too long it should be broken up into smaller functions which the original function can
call.

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
