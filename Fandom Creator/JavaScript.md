# JavaScript Guidelines

These coding conventions are for JavaScript inside the Fandom Creator Repo.

| Guidleine | Requirement Level |
| --- | ----------- |
| [Binding this](#binding-this) | MUST |
| [Static Data Within a Class](#static-data-within-a-class) | SHOULD |

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


## React Components

Our goal when building react components should be to make them small, composable, and reusable. 

* **SHOULD** [Use Composition over Inheritance](https://reactjs.org/docs/composition-vs-inheritance.html)
** **SHOULD** Use [hocs](https://reactjs.org/docs/higher-order-components.html) and [render props](https://reactjs.org/docs/render-props.html) to create small reusable components. 
* **SHOULD** avoid refs [React Docs](https://reactjs.org/docs/refs-and-the-dom.html#dont-overuse-refs)
* **MUST** Use [function components](https://reactjs.org/docs/components-and-props.html#function-and-class-components) when possible
