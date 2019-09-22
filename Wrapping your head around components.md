1. We will tackle this from the top down. Firs we will see that we are using a master "index.html" file with an element containing the id of root.
```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="theme-color" content="#000000" />
  <meta name="description" content="Web site created using create-react-app" />
  <link rel="apple-touch-icon" href="logo192.png" />
  <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
  <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.8.2/css/all.css"
    integrity="sha384-oS3vJWv+0UjzBfQzYUhtDYW+Pj2yciDJxpsK1OYPAYjqT085Qq/1cq5FLXAZQ7Ay" crossorigin="anonymous" />
  <title>Github Finder</title>
</head>

<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
</body>

</html>
```
2. Next we will curuse over to our master js file "index.js" below you will see App component being attached to the html div elemewnt of root so that it can be rendered. Currently our site consits of a single page "App"
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```
3. Now we get to the component that acts as our entire page wrapper. when a user hits our site, this is the page they see. Within that page can be several different components that can have themselves deeper nested components. Notice how we import App.css at the top of the file, this style sheet will be in charge of styling this page. alson note on the top of the page the components being imported.
```js
import React, { Component } from 'react';
import './App.css';
import Navbar from './components/layout/Navbar'
import Users from './components/users/Users'

class App extends Component {
  render() {
    return (
      <div className='App'>
        <Navbar title="GitHub Finder" />
        <Users />
      </div>
    );
  }
}

export default App;
```
4. Next we will move onto component users which is one of the main components for this page. To populate this component, we import a child component, "UserItem" which wil get generated 3 times based on the size of our users aray below.
```js
import React, { Component } from 'react'
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
            <div className="userContainer">
                {this.state.users.map(user => (
                    <UserItem key={user.id} user={user} />
                ))}
            </div>
        )
    }
}

export default Users
```
4. Lastly we hit our grnadchild component with respect to App component. This component will be generated three times based on the array mapped in the parent component above.
```js
import React, { Component } from 'react'

class UserItem extends Component {
    render() {
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
