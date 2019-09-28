1. Here is our parent component. Notice on line 3 how we import the useState hook from react.
```js
import React, { useState } from 'react';
import axios from 'axios';
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom'
import User from './components/users/User'
import './App.css';

const App = () =>{
//Belowe we set our user variable and its a initial value of an empty object. We also define the method that will be used to 
//alter the state of the user var, we name it "setUser" according to convention.
  const [user, setUser] = useState({});
  const [repos, setRepos] = useState([]);
  const [loading, setLoading] = useState(false);

//below we create a getUser function to fetch a user from the github api. We then call upon our useState funciton we defined
//above on line 12 to set the state of the user "setUser"
  const getUser = async (username) =>{
      setLoading(true);
      const res = await axios.get(`https://api.github.com/users/${username}?   client_id=${process.env.REACT_APP_GITHUB_CLIENT_ID}&client_secret=${process.env.REACT_APP_GITHUB_CLIENT_SECRET}`);
      setUser(res.data);
      setLoading(false);
  };

  const getUserRepos = async (username) =>{
      setLoading(true);
      const res = await axios.get(`https://api.github.com/users/${username}/repos?per_page=5&sort=created:asc&client_id=${process.env.REACT_APP_GITHUB_CLIENT_ID}&client_secret=${process.env.REACT_APP_GITHUB_CLIENT_SECRET}`);
      setRepos(res.data);
      setLoading(false);
  };

    return (
        <Router>
          <div className='App'>
            <div className="container">
                <Switch>
                    <Route exact path='/user/:login' render={props =>(
                    //now we want to send the user state to our child component User. we simply assign a prop which we define
                    //as user and then pass in our user state which on line 12 we named "user"
                        <User
                            {...props}
                            getUser={getUser}
                            getUserRepos={getUserRepos}
                            repos={repos}
                            user={user}
                            loading={loading}
                        />
                    )}/>
                </Switch>
            </div>
          </div>
        </Router>
    );
};

export default App;

```
2. To access the state in our child component, simply use props.user.example
```js
import React, { Fragment, useEffect } from 'react';
import Spinner from '../layout/Spinner'
import Repos from '../repos/Repos'
import { Link } from 'react-router-dom'

const User = (props) => {

    useEffect(() => {
        props.getUser(props.match.params.login);
        props.getUserRepos(props.match.params.login);
        // eslint-disable-next-line
    }, []);

    if (props.loading){
        return <Spinner />
    }

    return (
        <Fragment>
            <Link to='/' className='btn btn-light'>
                Back To Search
            </Link>
            Hireable: {' '}
            {props.user.hireable ? <i className='fas fa-check text-success'/> : <i className='fas fa-times-circle text-danger'/>}
            <div className="card grid-2">
                <div className="all-center">
                    <img src={props.user.avatar_url} alt="avatar" className="round-img" style={{ width: '150px' }}/>
                    <h1>{props.user.name}</h1>
                    <p>Location: {props.user.location}</p>
                </div>
                <div>
                    {props.user.bio &&
                    <Fragment>
                        <h3>Bio</h3>
                        <p>{props.user.bio}</p>
                    </Fragment>}
                    <a href={props.user.html_url} className='btn btn-dark my-1'>Visit Github Profile</a>
                    <ul>
                        <li>
                            <strong>Username:</strong> {props.user.login}
                        </li>
                        <li>
                            <strong>Company:</strong> {props.user.company}
                        </li>
                        <li>
                            <strong>Blog:</strong> {props.user.blog}
                        </li>
                    </ul>
                </div>
            </div>
            <div className="card text-center">
                <div className="badge badge-primary">Followers: {props.user.followers}</div>
                <div className="badge badge-success">Following: {props.user.following}</div>
                <div className="badge badge-light">Public Repos: {props.user.public_repos}</div>
                <div className="badge badge-dark">Public Gists: {props.user.public_gists}</div>
            </div>

            <Repos repos={props.repos}/>
        </Fragment>
    );
};

export default User;
```
