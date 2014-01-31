# Wikia SCSS Coding Conventions

This styleguide defines the SCSS coding conventions at Wikia. While it is managed by the Front End Coding Conventions Team, it is here to serve the entire developer community at Wikia. Therefore, if you would like to propose a change, simply create a pull request and tag @wikia-frontenders.

## Our Mission

As a developer I want a clear and well documented guide dealing with coding conventions, patterns and best practices for coding SCSS style sheets at Wikia along with tools to help me in making my code compliant.

## TOC
* [Rules](#rules)
 * [Whitespace and Structure](#whitespace-and-structure) 
 * [Naming Conventions](#naming-conventions)

## Rules

### Whitespace and Structure

* Use tabs, not spaces, for indentation.
* Only one rule per line. 
* One line break before each selector. 
* Nested selectors should be indented by one tab. 
* Style declarations (properties) should be indented by one tab under their selectors. 
* Place one space after the selector and before the open curly bracket.
* Alphabatize style declarations.
* Place all style declarations for a given selector before any nested selectors.
* Place any @extend or @include statements above all style declarations.

Example:
```scss
.item-wrapper {
  @extend .other-item;
  @include some-mixin;
  border: 1px solid purple;
  
  .item {
    display: inline;
  }
}

.more-selectors {
  ...
}
```

### Naming Conventions
Here are a few simple rules on naming conventions in SCSS:
* Class names should be lower-case-dash
* IDs should be lowerCameCase
* Do not prefix ID and class names with 'wikia' unless you are specifically building products to be used as third party software. 

Example: 
```html
<div class="item-wrapper" id="MyItem">...</div>
```
A note on legacy code:

We used to have a concept of "major and minor elements". This was good for encouraging modular widgets and css scoping, however we believe that we can still acheive these goals with the more consistant naming conventions described above. 
