# Fandom Creator Coding Conventions

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
