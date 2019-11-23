1. Run commands
```
npm i react-router-dom
npm install @types/react-router-dom
```
2. In the index.tsx file surround your app component with BrowserRouter
```ts
import React from 'react';
import ReactDOM from 'react-dom';
import {BrowserRouter} from 'react-router-dom';
import './app/layout/index.css';
import App from './app/layout/App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<BrowserRouter> <App /> </BrowserRouter>, document.getElementById('root'));

serviceWorker.unregister();
```
3. Now inside of our App component we will define the routes and the components that will belong to said route.
```ts
import React, {useEffect, Fragment, useContext} from 'react';
import {Route} from "react-router-dom";

const App = () => {
  if (activityStore.loadingInitial) return <LoadingComponent content={'Loading Activities...'} />;
    return (
        <Fragment>
            <NavBar />
            <Container style={{marginTop: '7em'}}>
            //below we have three different paths with their defined components.
                <Route exact path='/' component={HomePage}/>
                <Route exact path='/activities' component={ActivityDashboard}/>
                <Route exact path='/createActivity' component={ActivityForm}/>
            </Container>
        </Fragment>
    );
};

export default observer(App);
```
4. Below is our navbar component that will contain the buttons responsible for transfering the users to the correct component.
```ts
import React, {useContext} from 'react';
import {Menu} from "semantic-ui-react";
import Container from "semantic-ui-react/dist/commonjs/elements/Container";
import Button from "semantic-ui-react/dist/commonjs/elements/Button";
import ActivityStore from '../../app/stores/activityStore'
import {observer} from "mobx-react-lite";
import {NavLink} from "react-router-dom";

interface IProps {}

const NavBar: React.FC<IProps> = (props) => {
    const activityStore = useContext(ActivityStore);
    return (
        <Menu fixed='top' inverted>
            <Container>
            <Menu.Item header exact as={NavLink} to='/'>
                <img src="/assets/logo.png" alt="logo" style={{marginRight: '10px'}}/>
                Reactivities
            </Menu.Item>   
            //use as={NavLink} and the to='' attribute to configure which path the button should use.
            //notice the values in this to attribute match with the routes defined in the App component above.
            <Menu.Item name='Activities' as={NavLink} to='/activities'/>
            <Menu.Item>
                <Button as={NavLink} to='/createActivity' positive content='Create Activity' />
            </Menu.Item>
            </Container>
        </Menu>
    );
};

export default observer(NavBar);
```
