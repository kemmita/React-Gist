1. Run command.
```
npm i react-router-dom
```
2. Below is our main HTML file Index
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
3. Below is our index.js that connects our main react file "App" to our html element "root"^^
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```
4. Below is our main js file APP, this will be the file that we delcare our routes and render the components we assign to those routes. Along with defining the routes and components designated to those routes, App.js will also handle all State
```js
import React, { Component } from 'react';
import axios from 'axios';
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom'
import Navbar from './components/layout/Navbar';
import Home from './components/page_components/Home'
import About from './components/page_components/About'
import User from './components/users/User'
import './App.css';

class App extends Component {
  state = {
      users: [],
      user: {},
      loading: false,
      alert: false,
      alertBody: {msg: '', type: ''}
  };
  
  render() {
    return (
        <Router>
          <div className='App'>
            <Navbar title="GitHub Finder"/>
            <div className="container">
                <Switch>
                //if you need to pass props to the components in a route,  use the route syntax below
                    <Route exact path='/' render={props => (
                        <Home
                            alert={this.state.alert}
                            alertBody={this.state.alertBody}
                            searchUsers={this.searchUsers}
                            setAlert={this.setAlert}
                            loading={this.state.loading}
                            users={this.state.users}
                        />
                    )}/>
                    //if you are not passing state/props, simply use the syntax below
                    <Route exact path='/about' component={About}/>
                    <Route exact path='/user/:login' render={props =>(
                        <User getUser={this.getUser} user={this.state.user} loading={this.state.loading}/>
                    )}/>
                </Switch>
            </div>
          </div>
        </Router>
    );
  }
}

export default App;

```
5. As long as the component is nested within the initial Router above it can use Link to navigate to differen components.
```js
import React from 'react'
import { Link } from 'react-router-dom'
const Navbar = (props) => {
        return (
            <nav className="navbar bg-primary">
                <h1 id="logo">
                    <Link to='/'>{props.title}</Link>
                </h1>
                <ul>
                    <li>
                        <Link to='/'>Home</Link>
                    </li>
                    <li>
                        <Link to='/about'>About</Link>
                    </li>
                </ul>
            </nav>
        );
};

export default Navbar
```
