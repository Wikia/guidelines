# JavaScript Guidelines

These coding conventions are for JavaScript inside the Fandom Creator Repo.

## Binding this

See: [Proposal: JavaScript Binding](https://wikia-inc.atlassian.net/wiki/spaces/CAKE/pages/312344881/Proposal+JavaScript+Binding)

Class functions will use the standard ES6 syntax. The public class fields / class arrow method syntax **MUST** be used when binding is necessary.

```JS
class Toggle extends React.Component {
  // Automatically bound in the constructor / copied to each instance
  // only use when binding is required
  handleClick = (event) => {
      return this.props.hello;
  };
  
  // Lives on the prototype and is not copied to each instance
  renderHelper() {
      return this.props.hello;
  }
  
  render() {
    return (
      <button onClick={this.handleClick}> {this.renderHelper()} </button>
    );
  }
}
```

## Instance fields in a class

[Proposal](https://github.com/Wikia/guidelines/pull/134)

Similar to the public class instance methods, instance fields do not need to be set in the constructor. All instance fields **MUST** be initialized in the root of the class. Fields **MUST** be declared as "instance fields" over setting them in the constructor (even when a constructor is required for other reasons). 

Babel ultimately transpiles them _nearly_ the same code (https://babeljs.io/docs/en/babel-plugin-proposal-class-properties) Which is a current stage 3 proposal to add to javascript (https://github.com/tc39/proposal-class-fields)  

```JS
// bad
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = { stateField: true };
    this.anotherInstanceField = 'bob';
  }
}
```

```JS
// good
class Toggle extends React.Component {
  state = { stateField: true };
  anotherInstanceField = 'bob';
}
```

## Static Data Within a Class

Static data within a JS class **SHOULD** live within a const(s) at the top of the file. If needed outside of this class add an export or consider moving it to it's own file.

```JS
// add a const object at the top of the file with an optional export if needed
export const toggleData = {
    label: 'toggle',
};
 
export default class Toggle extends React.Component {
  render() {
    return (<div> {toggleData.label} </div>);
  }
}
```

## Imports

JS imports **SHOULD** be listed alphabetically, grouped by their source, and separated by a blank line. Source order is: 
* Imports from `node_modules`
* Local imports
* CSS imports

**Example:**
```
import PropTypes from 'prop-types';
import React from 'react';

import { LOGIN_STATES } from 'Constants';
import { simpleConnect } from 'reducers/reduxHelpers';

import './BaseCard.scss';
```

Note that alphebatizing is based on the import directory, then file name.
