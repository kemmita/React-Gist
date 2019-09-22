1. Below we make an http request that returns an array of strings ;).
```js
import React, { Component } from 'react'
//below we import the child "UserItem" component
import UserItem from './UserItem';
class Users extends Component {
    state = {
        users: [
            {
                id: '1',
                login: 'mojombo',
                avatar_url: 'https://avatars0.githubusercontent.com/u/1?v=4',
                html_url: 'https://github.com/mojombo'
            },
            {
                id: '2',
                login: 'mojombo',
                avatar_url: 'https://avatars0.githubusercontent.com/u/1?v=4',
                html_url: 'https://github.com/mojombo'
            },
            {
                id: '3',
                login: 'mojombo',
                avatar_url: 'https://avatars0.githubusercontent.com/u/1?v=4',
                html_url: 'https://github.com/mojombo'
            }
        ]
    }
    render() {
        return (
            <div>
            //below we map and then create a prop on the child component called user and pass in a user per loop.
                {this.state.users.map(user => (
                    <UserItem key={user.id} user={user} />
                ))}
            </div>
        )
    }
}

export default Users
```
2. Below is the child class.
```js
import React, { Component } from 'react'

class UserItem extends Component {
    render() {
    //below we destruc the props object passed in
        const { login, avatar_url, html_url } = this.props.user;
        return (
            <div className="card text-center">
                <img src={avatar_url} className="round-img" style={{ width: '60px' }} alt="Userimg" />
                <h3>{login}</h3>
                <div>
                    <a href={html_url} className="btn btn-dark btn-sm my-1">More</a>
                </div>
            </div>
        )
    }
}

export default UserItem
```
