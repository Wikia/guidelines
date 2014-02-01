# Wikia SCSS Coding Conventions

This styleguide defines the SCSS coding conventions at Wikia. While it is managed by the Front End Coding Conventions Team, it is here to serve the entire developer community at Wikia. Therefore, if you would like to propose a change, simply create a pull request and tag @wikia-frontenders.

## Our Mission

As a developer I want a clear and well documented guide dealing with coding conventions, patterns and best practices for coding SCSS style sheets at Wikia along with tools to help me in making my code compliant.

## TOC
* [Rules](#rules)
 * [Whitespace and Structure](#whitespace-and-structure)
 * [Variables](#variables)
 * [Specificity and Nesting](#specificity-and-nesting)
 * [Class and ID Naming Conventions](#class-and-id-naming-conventions)

## Rules

### Whitespace and Structure

* Use tabs, not spaces, for indentation.
* Only one rule per line. 
* Nested selectors should be indented by one tab. 
* Style declarations (properties) should be indented by one tab under their selectors. 
* Place one space after the selector and before the open curly bracket.
* Alphabatize style declarations.
* Place all style declarations for a given selector before any nested selectors.
* Place any @extend or @include statements above all style declarations.
* **QUESTION: line breaks before selectors?**

Example:
```scss
.item-wrapper {
  @extend .other-item;
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

### Variables

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

### Specificity and Nesting

* Don't nest more than 3 levels. SCSS nesting should not blindly match HTML structure.
* Try to avoid prefixing class selectors with their element selector. If you have two elements that have the same class but have different style rules based on their tag, there's probably something wrong.

Example:
```scss
// good
.some-module {
  a {
    text-decoration: none;
  }
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
ul.some-module {
  li {
    a {
      text-decoration: underline;

      span {
        font-weight: bold;
      }
    }
  }
}
```

### Class and ID Naming Conventions
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
