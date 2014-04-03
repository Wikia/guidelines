# Wikia SCSS Coding Conventions

This styleguide defines the SCSS coding conventions at Wikia. While it is managed by the Front End Coding Conventions Team, it is here to serve the entire developer community at Wikia. Therefore, if you would like to propose a change, simply create a pull request and tag @wikia-frontenders.

## Our Mission

As a developer I want a clear and well documented guide dealing with coding conventions, patterns and best practices for coding SCSS style sheets at Wikia along with tools to help me in making my code compliant.

## TOC
* [Whitespace and Structure](#whitespace-and-structure)
* [Variables](#variables)
* [Specificity and Nesting](#specificity-and-nesting)
* [Class and ID Naming Conventions](#class-and-id-naming-conventions)
* [Rules from SCSS-Lint Config](#rules-from-scss-lint-config)
 * [BorderZero](#borderzero)
 * [ColorKeyword](#colorkeyword)
 * [Comment](#comment)
 * [Compass Linters](#compass-linters)
 * [DebugStatement](#debugstatement)
 * [DeclarationOrder](#declarationorder)
 * [DuplicateProperty](#duplicateproperty)
 * [EmptyLineBetweenBlocks](#emptylinebetweenblocks)
 * [EmptyRule](#emptyrule)
 * [HexFormat](#hexformat)
 * [IdWithExtraneousSelector](#idwithextraneousselector)
 * [Indentation](#indentation)
 * [LeadingZero](#leadingzero)
 * [NameFormat](#nameformat)
 * [PlaceholderInExtend](#placeholderinextend)
 * [PropertySortOrder](#propertysortorder)
 * [PropertySpelling](#propertyspelling)
 * [SelectorDepth](#selectordepth)
 * [Shorthand](#shorthand)
 * [SingleLinePerSelector](#singlelineperselector)
 * [SpaceAfterComma](#spaceaftercomma)
 * [SpaceAfterPropertyColon](#spaceafterpropertycolon)
 * [SpaceAfterPropertyName](#spaceafterpropertyname)
 * [SpaceBeforeBrace](#spacebeforebrace)
 * [SpaceBetweenParens](#spacebetweenparens)
 * [StringQuotes](#stringquotes)
 * [TrailingSemicolonAfterPropertyValue](#trailingsemicolonafterpropertyvalue)
 * [UrlQuotes](#urlquotes)
 * [ZeroUnit](#urlquotes)
* [Legacy Code](#legacy-code)

## Whitespace and Structure

* Use tabs, not spaces, for indentation.
* Only one rule per line.
* Nested selectors should be indented by one tab.
* Style declarations (properties) should be indented by one tab under their selectors.
* Place all style declarations for a given selector before any nested selectors.

Example:
```scss
.item-wrapper {
  @extend %other-item;
  @include some-mixin;
  border: 1px solid purple;
  max-width: 200px;

  .item {
    display: inline;
    z-index: 2;
  }
}

.more-selectors {
  ...
}
```

## Variables

* SCSS variables should be declared at the top of each file.
* Variables should be lower-dash-case
* If a variable is to be used in more than one extension, you can prefix it with a descriptor, like "color". Otherwise, use more generic variable names.
* If there's a value that is used in more than one selector, like a left/right padding value, create a variable so it can be updated in one place.

```scss
$vertical-padding: 20px;

.my-module {
  padding-left: $vertical-padding;
}
.similar-module {
  padding-left: $vertical-padding;
}
```

## Specificity and Nesting

* Try to avoid prefixing class selectors with their element selector. If you have two elements that have the same class but have different style rules based on their tag, there's probably something wrong.
* Don't use child selectors `>` when you don't need them.
* In general, do not use ```!important```. The only exceptions are overriding 3rd party code and some utitlity classes. If you do use it, add a comment as to why.
* Never use IDs as selectors. Wikia allows users to customize their wikias with css and we want to make it feasible for them to override existing styles.

Example (for the given HTML):
```html
<div class="some-module" id="BestModuleEver">
  <a>Foo</a>
  <ul class="link-list">
    <li>
      <a><span>Bar</span></a>
    </li>
  </ul>
</ul>
```
```scss
// good
.some-module {
  li {
    float: left;

    a {
      text-decoration: underline;
    }
  }

  span {
    font-weight: bold;
  }
}

// bad
#BestModuleEver {
  ul.link-list > li {
    a {
      text-decoration: underline !important;

      span {
        font-weight: bold;
      }
    }
  }
}
```

## Class and ID Naming Conventions
Here are a few simple rules on naming conventions in SCSS:
* Class names should be lower-case-dash
* IDs should be lowerCameCase
* Do not prefix ID and class names with 'wikia' unless you are specifically building products to be used as third party software.

Example:
```html
<div class="item-wrapper" id="myItem">...</div>
```

# Rules from SCSS-Lint Config

Below is a list of linters supported by `scss-lint`, ordered alphabetically. It is derived from causes.com's [scss-linter](https://github.com/causes/scss-lint/blob/master/lib/scss_lint/linter/README.md).


## BorderZero

Prefer `border: 0` over `border: none`.

## DebugStatement

Reports `@debug` statements (which you probably left behind accidentally).

## DeclarationOrder

Write `@extend` statements first in rule sets, followed by property
declarations and then other nested rule sets.

**Bad: `@extend` not first**
```scss
.fatal-error {
  color: #f00;
  @extend %error;

  p {
    ...
  }
}
```

**Good: `@extend` appears first**
```scss
.fatal-error {
  @extend %error;
  color: #f00;

  p {
    ...
  }
}
```

The `@extend` statement functionally acts like an inheritance mechanism, which
means the properties defined by the placeholder being extended are rendered
before the rest of the properties in the rule set.

Thus, declaring the `@extend` at the top of the rule set reminds the developer
of this behavior.

## DuplicateProperty

Reports when you define the same property twice in a single rule set.

**Bad**
```scss
h1 {
  margin: 10px;
  text-transform: uppercase;
  margin: 0; // Second declaration
}
```

Having duplicate properties is usually just an error. However, they can be used
as a technique for dealing with varying levels of browser support for CSS
properties. In the example below, some browsers might not support the `rgba`
function, so the intention is to fall back to the color `#fff`.

```scss
.box {
  background: #fff;
  background: rgba(255, 255, 255, .5);
}
```

In this situation, using duplicate properties is acceptable.

## EmptyLineBetweenBlocks

Separate rule, function, and mixin declarations with empty lines.

**Bad: no lines separating blocks**
```scss
p {
  margin: 0;
  em {
    ...
  }
}
a {
  ...
}
```

**Good: lines separating blocks**
```scss
p {
  margin: 0;

  em {
    ...
  }
}

a {
  ...
}
```

## EmptyRule

Reports when you have an empty rule set.

```scss
.cat {
}
```

## HexFormat

Prefer the shortest possible form for hexadecimal color codes.

**Bad: can be shortened**
```scss
color: #ff22ee;
```

**Good: color code in shortest possible form**
```scss
color: #f2e;
```

## IdWithExtraneousSelector

Don't combine additional selectors with an ID selector. In fact, [never use ID's](http://screwlewse.com/2010/07/dont-use-id-selectors-in-css) as selectors. We reserve ID selectors for user css. Note: scss-lint will only lint for extaneous ID selectors, not ID's as selectors.

**Bad: `.button` class is unnecessary**
```scss
#submit-button.button {
  ...
}
```

**Good: standalone ID selector**
```scss
#submit-button {
  ...
}
```

While the CSS specification allows for multiple elements with the same ID to
appear in a single document, in practice this is a smell.  When
reasoning about IDs (including selector specificity), it should suffice to
style an element with a particular ID based solely on the ID.

Another possible pattern is to modify the style of an element with a given
ID based on the class it has. This is also a smell, as the purpose of a CSS
class is to be reusable and composable, and thus redefining it for a specific
ID is a violation of those principles.

## LeadingZero

Don't write leading zeros for numeric values with a decimal point.

**Bad: unnecessary leading zero**
```scss
margin: 0.5em;
```

**Good: no leading zero**
```scss
margin: .5em;
```

## NameFormat

Functions, mixins, and variables should be declared with all lowercase letters
and hyphens instead of underscores.

**Bad: uppercase characters**
```scss
$myVar: 10px;

@mixin myMixin() {
  ...
}
```

**Good: all lowercase with hyphens**
```scss
$my-var: 10px;

@mixin my-mixin() {
  ...
}
```

Using lowercase with hyphens in CSS has become the _de facto_ standard, and
brings with it a couple of benefits. First of all, hyphens are easier to type
than underscores, due to the additional `Shift` key required for underscores on
most popular keyboard layouts. Furthermore, using hyphens in class names in
particular allows you to take advantage of the
[`|=` attribute selector](http://www.w3.org/TR/CSS21/selector.html#attribute-selectors),
which allows you to write a selector like `[class|="inactive"]` to match both
`inactive-user` and `inactive-button` classes.

The Sass parser automatically treats underscores and hyphens the same, so even
if you're using a library that declares a function with an underscore, you can
refer to it using the hyphenated form instead.

## PlaceholderInExtend

Always use placeholder selectors in `@extend`.

**Bad: extending a class**
```scss
.fatal {
  @extend .error;
}
```

**Good: extending a placeholder**
```scss
.fatal {
  @extend %error;
}
```

Using a class selector with the `@extend` statement statement usually results
in more generated CSS than when using a placeholder selector.  Furthermore,
Sass specifically introduced placeholder selectors in order to be used with
`@extend`.

See [Mastering Sass extends and placeholders](http://8gramgorilla.com/mastering-sass-extends-and-placeholders/).

## PropertySortOrder

Sort properties in a strict order. By default, will require properties be
sorted in alphabetical order, as it's brain dead simple (highlight lines and
execute `:sort` in `vim`), and it can
[benefit gzip compression](http://www.barryvan.com.au/2009/08/css-minifier-and-alphabetiser/).

You can also specify an explicit ordering via the `order` option, which allows
you to specify an explicit array of properties representing the preferred
order. If a property is not in your explicit list, it will be placed at the
bottom of the list, disregarding its order relative to other unspecified
properties.

If you need to write vendor-prefixed properties, the linter will allow you to
order the vendor-prefixed properties before the standard CSS property they
apply to. For example:

```scss
border: 0;
-moz-border-radius: 3px;
-o-border-radius: 3px;
-webkit-border-radius: 3px;
border-radius: 3px;
color: #ccc;
margin: 5px;
```

In this case, this is usually avoided by using mixins from a framework like
[Compass](http://compass-style.org/) or [Bourbon](http://bourbon.io/) so
vendor-specific properties rarely need to be explicitly written by hand.

If you are specifying an explicit order for properties, note that
vendor-prefixed properties will still be ordered based on the example above
(i.e. you only need to specify normal properties in your list).

## PropertySpelling

Reports when you use an unknown CSS property (ignoring vendor-prefixed
properties).

```scss
diplay: none; // "display" is spelled incorrectly
```

Since the list of available CSS properties is constantly changing, it's
possible that you might get some false positives here, especially if you're
using experimental CSS features. If that's the case, you can add additional
properties to the whitelist by adding the following to your `.scss-lint.yml`
configuration:

```yaml
linters:
  PropertySpelling:
    extra_properties:
      - some-experimental-property
      - another-experimental-property
```

If you're sure the property in question is valid,
[submit a request](https://github.com/causes/scss-lint/issues/new)
to add it to the
[default whitelist](data/properties.txt).

## SelectorDepth

Don't write selectors with a depth of applicability greater than 3.

**Bad: selectors with depths of 4**
```scss
.one .two .three > .four {
  ...
}

.one .two {
  .three > .four {
    ...
  }
}
```

**Good**
```scss
.one .two .three {
  ...
}

.one .two {
  .three {
    ...
  }
}
```

Selectors with a large [depth of applicability](http://smacss.com/book/applicability)
lead to CSS tightly-coupled to your HTML structure, making it brittle to change.

Deep selectors also come with a performance penalty, which can affect rendering
times, especially on mobile devices. While the default limit is 3, ideally it
is better to use less than 3 whenever possible.

## Shorthand

Prefer the shortest shorthand form possible for properties that support it.

**Bad: all 4 sides specified with same value**
```scss
margin: 1px 1px 1px 1px;
```

**Good: equivalent to specifying 1px for all sides**
```scss
margin: 1px;
```

## SpaceAfterComma

Commas in lists should be followed by a space.

**Bad: no space after commas**
```scss
@include box-shadow(0 2px 2px rgba(0,0,0,.2));
color: rgba(0,0,0,.1);
```

**Good: commas followed by a space**
```scss
@include box-shadow(0 2px 2px rgba(0, 0, 0, .2));
color: rgba(0, 0, 0, .1);
```

## SpaceAfterPropertyColon

Properties should be formatted with a single space separating the colon from
the property's value.

**Bad: no space after colon**
```scss
margin:0;
```

**Bad: more than one space after colon**
```scss
margin:  0;
```

**Good**
```scss
margin: 0;
```

## SpaceAfterPropertyName

Properties should be formatted with no space between the name and the colon.

**Bad: space before colon**
```scss
margin : 0;
```

**Good**
```scss
margin: 0;
```

## SpaceBeforeBrace

Opening braces should be preceded by a single space.

**Bad: no space before brace**
```scss
p{
  ...
}
```

**Bad: more than one space before brace**
```scss
p  {
  ...
}
```

**Good**
```scss
p {
  ...
}
```

## SpaceBetweenParens

Parentheses should not be padded with spaces.

**Bad**
```scss
@include box-shadow( 0 2px 2px rgba( 0, 0, 0, .2 ) );
color: rgba( 0, 0, 0, .1 );
```

**Good**
```scss
@include box-shadow(0 2px 2px rgba(0, 0, 0, .2));
color: rgba(0, 0, 0, .1);
```

## StringQuotes

String literals should be written with single quotes unless using double quotes
would save on escape characters.

**Bad: double quotes**
```scss
content: "hello";
```

**Good: single quotes**
```scss
content: 'hello';
```

**Good: double quotes prevent the need for escaping single quotes**
```scss
content: "'hello'";
```

Single quotes are easier to type by virtue of not requiring the `Shift` key on
most popular keyboard layouts.

## TrailingSemicolonAfterPropertyValue

Property values should always end with a semicolon.

**Bad: no semicolon**
```scss
p {
  color: #fff
}
```

**Bad: space between value and semicolon**
```scss
p {
  color: #fff ;
}
```

**Good**
```scss
p {
  color: #fff;
}
```

CSS allows you to omit the semicolon if the property is the last property in
the rule set. However, this introduces inconsistency and requires anyone adding
a property after that property to remember to append a semicolon.

## UrlQuotes

URLs should always be enclosed within quotes.

**Bad: no enclosing quotes**
```scss
background: url(example.png);
```

**Good**
```scss
background: url('example.png');
```

Using quoted URLs is consistent with using other Sass asset helpers, which also
expect quoted strings. It also works better with most syntax highlighters, and
makes it easier to escape characters, as the escape rules for strings apply,
rather than the different set of rules for literal URLs.

See the [URL type](http://dev.w3.org/csswg/css-values/#url-value) documentation
for more information.

## ZeroUnit

Omit units on zero values.

**Bad: unnecessary units**
```scss
margin: 0px;
```

**Good**
```
margin: 0;
```

Zero is zero regardless of units.

## Legacy code

Some notes on legacy code:
* We used to have a concept of "major and minor elements". This was good for encouraging modular widgets and css scoping, however we believe that we can still acheive these goals with the more consistant naming conventions described above.
* Always leave a file better than you found it. Please run scss-lint on every scss file you touch. 
