# Wikia Handlebars Guidelines
## Table of Contents
* [Quotes](#quotes)
* [Indentation](#indentation)

### Quotes
HTML attribute values should be wrapped in double quotes. Strings inside handlebars helpers should use single quotes. 

Example: 
```Handlebars
<div class="my-div">{{svg 'chevron' role='img' class='icon arrow-left'}}</div>
```

The reason for this convention is mainly just to keep things consistent, but also: 
* Developers might be used to seeing HTML with double quotes because Chrome and Firefox convert single to double quotes in developer tools. 
* Since handlebars tags are script helpers, using single quotes for parameters seems more appropriate. 
* This also helps to distinguish between the two types of syntax within one file. 

### Indentation
Indent HTML and handlebars inside `{{#if}}` blocks. 

Example: 
```handlebars
<p>Some HTML</p>
{{#if foo}}
  <p>Foo</p>
{{#else}}
  <p>Not Foo</p>
{{/if}}
```
