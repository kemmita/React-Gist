1. The goal is to always have your declared state in your main top component, here you would like to keep and alter state. the state will be transfered to different child components that can read or either trigger a method in the main component to change state. Below is the main component.
```js
import React, { Component } from 'react';
import axios from 'axios';
import Users from './components/users/Users';

class App extends Component {
//initialize the state of the users array to be empty
  state = {
      users: []
  };
  
 async componentDidMount(){
     this.setState({loading: true});
     const res = await axios.get(`https://api.github.com/users?        
     client_id=${process.env.REACT_APP_GITHUB_CLIENT_ID}&client_secret=${process.
     env.REACT_APP_GITHUB_CLIENT_SECRET}`);
     //set the state of the users array to be the data returned from the http request
     this.setState({users:res.data})
  }
  render() {
    return (
      <div className='App'>
      //wanting to pass the current state of 
        <div className="container">
            //below we call on the user component and assign a prop to it called users, this prop will continue to listen
            //to the state of the users array and will update when ever the state is changed.
            <Users users={this.state.users} />
        </div>
      </div>
    );
  }
}

export default App;
```
2. Below in the child component, "Users" we obtain the props passed and use/read them accordingly
```js
import React from 'react'
import UserItem from './UserItem';

const Users = (props) => {
    return (
        <div>
           {props.users.map(user => (<UserItem key={user.id} user={user} />))}
         </div>
        )
};

export default Users
```
