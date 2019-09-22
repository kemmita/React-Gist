1. The goal is to change the state of the users from an empty array to the data returned back from our search
```js
import React, { Component } from 'react';
import axios from 'axios';
import Search from './components/users/Search';

class App extends Component {
//below we initialize the state of users to be an empty array
  state = {
      users: []
  };
//this method will be triggered and will change the state of the users array to contain the data of the returned data from
//the http request. The goal is to write to this array using a seperate component. notice the userText param. this will be
//sent from the child component to run the query
  searchUsers = async (userText) =>{
      const res = await axios.get(`https://api.github.com/search/users?q=${userText}&client_id=${process.env.REACT_APP_GITHUB_CLIENT_ID}&client_secret=${process.env.REACT_APP_GITHUB_CLIENT_SECRET}`);
      this.setState({users:res.data.items})
  };

  render() {
    return (
      <div className='App'>
        <Navbar title="GitHub Finder" />
        <div className="container">
        //below we do not pass the props and its stat, but pass the method to the child component that will
        //change/write to the state
            <Search searchUsers={this.searchUsers}/>
        </div>
      </div>
    );
  }
}

export default App;

```
2. child component
```js
import React, { Component } from 'react';


class Search extends Component {
//here we declare the onSubmit function remember that we recieved a function from the parent component,
//we can call on this fucntion using this.props as it was passed as a prop. Pass the value submitted by this form
//as the argument which will in turn trigger the function passed from the parrent ultimatley altering the state of the users
//array living in the parent component.
    onSubmit = (e) =>{
        e.preventDefault();
        this.props.searchUsers(e.target.text.value);
        this.setState({text: ''});
    };

    render() {
        return (
            <div>
              //to do this, we will need to create an onSubmit function that will run when the form is submitted.
                <form className="form" onSubmit={this.onSubmit}>
                    <input type="text" name="text" placeholder="Search Users..."/>
                    <input type="submit" value="Search" className="btn btn-dark btn-block"/>
                </form>
            </div>
        );
    }
}

export default Search;
```
