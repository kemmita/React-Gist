0. install axios
```
npm i axios
```
1. GET Parent
```
import React, { Component } from 'react';
import axios from 'axios';
import Users from './components/users/Users'

class App extends Component {
  // the state of the users and loading will change so we will set the inital state in the parent component here.
  state = {
      users: []
  };
  //we will use componentDidMount to set the state of users after using axios for the GET request
  async componentDidMount(){
    //below we make the request and setState of users to the data returned from the GET request. The request contains
    //40 objects that will be contained in the users array.
    const res = await axios.get('https://api.github.com/users');
  }
  render() {
    return (
      <div className='App'>
        <Navbar title="GitHub Finder" />
        <div className="container">
            //now pass the current state of the users to the child component Users
            <Users users={this.state.users} />
        </div>
      </div>
    );
  }
}

export default App;

```
1.2 GET Child
```
import React, { Component } from 'react'
import UserItem from './UserItem';
class Users extends Component {
    render() {
        return (
        //we do not need state for reading the data passed, so we will use props to gain access to the data passed.
        //this is an arry that is passed so we will use map to loop through the array. Each time we loop, we will call
        //the grandchild component UserItem and pass the useritem component the user data specific to the current loop.
            <div>
                {this.props.users.map(user => (
                    <UserItem key={user.id} user={user} />
                ))}
            </div>
        )
    }
}

export default Users

```
1.3 GET Grandchild
```
import React from 'react'
//lastly we pass the props from the child/parent component above and are used here as props again as nothing is being changed.
const UserItem = (props) =>{
        const { login, avatar_url, html_url } = props.user;
        return (
            <div className="card text-center">
                <img src={avatar_url} className="round-img" style={{ width: '60px' }} alt="Userimg" />
                <h3>{login}</h3>
                <div>
                    <a href={html_url} className="btn btn-dark btn-sm my-1">More</a>
                </div>
            </div>
        )
};

export default UserItem

```
