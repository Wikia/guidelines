# JavaScript Guidelines

These coding conventions are for JavaScript inside the Fandom Creator Repo.

| Guidleine | Requirement Level |
| --- | ----------- |
| [Binding this](#binding-this) | MUST |
| [Static Data Within a Class](#static-data-within-a-class) | SHOULD |
| [Prop Types](#prop-types) | SHOULD |

## Binding this

See: [Proposal: JavaScript Binding](https://wikia-inc.atlassian.net/wiki/spaces/CAKE/pages/312344881/Proposal+JavaScript+Binding)

Class functions will use the standard ES6 syntax. The public class fields / class arrow method syntax may be used only when binding is necessary.

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

## Static Data Within a Class

Static data within a JS class should live within a const(s) at the top of the file. If needed outside of this class add an export or consider moving it to it's own file.

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

JS imports should be listed alphabetically, grouped by their source, and separated by a blank line. Source order is: 
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

## Prop Types

PropTypes **SHOULD** be listed at the bottom of every file - where props are made available - before the class export. Regardless of whether it is a React class component or a functional component. Default props **SHOULD** be listed afterwards if required. These **SHOULD** also be listed in alphabetical order.

```
Nav.propTypes = {
    height: PropTypes.number,
};

Nav.defaultProps = {
    height: 35,
};

export default Nav;
```
