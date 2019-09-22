1. in the parent component, we will create a method call searchUsers that will take ina single argument, a string. 
```js
import React, { Component } from 'react';
import Search from './components/users/Search';

class App extends Component {
  //here we create the method
  searchUsers = (userText) =>{
        console.log(userText)
  };

  render() {
    return (
      <div className='App'>
        <div className="container">
            //below we send the method to the child component as a prop.
            <Search searchUsers={this.searchUsers}/>
        </div>
      </div>
    );
  }
}

export default App;

```
2. Child component "Search"
```js
import React, { Component } from 'react';


class Search extends Component {
//below we use a form to alter the state of a field we named text
    state = {
        text: ''
    };
//this method on submit is fired when the form is submitted, when it is submitted, we call the method that was created in the
//parent component "searchUsers" we call it as a prop as it was passed as one and then pass an argumen, a string. In the 
//end, the string pushed back up to the parent where it is consolelogged
    onSubmit = (e) =>{
        e.preventDefault();
        this.props.searchUsers(this.state.text);
        this.setState({text: ''});
    };

    onChange = (e) => this.setState({text: e.target.value});

    render() {
        return (
            <div>
                <form className="form" onSubmit={this.onSubmit}>
                    <input type="text" name="text" placeholder="Search Users..." value={this.state.text} onChange={this.onChange}/>
                    <input type="submit" value="Search" className="btn btn-dark btn-block"/>
                </form>
            </div>
        );
    }
}

export default Search;
```
