1. There may be some casses where we will want to keep some routes private from the others, one Route may shar a navbar, and the
home page may not share that Navbar, we can do that like so below. First we see the index file with that wraps the main app component
in BrowserRouter.
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
2. 

```ts
import React, {useEffect, Fragment, useContext} from 'react';
import NavBar from '../../features/nav/NavBar'
import {Route, RouteComponentProps, withRouter} from "react-router-dom";
const App: React.FC<RouteComponentProps> = ({location}) => {
  const activityStore = useContext(ActivityStore);

  useEffect (() =>{
      activityStore.loadActivities();
  }, [activityStore]);

  if (activityStore.loadingInitial) return <LoadingComponent content={'Loading Activities...'} />;
    return (
        <Fragment>
            //any path that contains only the "/" will be directed to the home page.
            <Route exact path='/' component={HomePage}/>
            //andy path great than "/" will be directed to the component with a matching path below
            <Route path={'/(.+)'} render={() => (
                <Fragment>
                    <NavBar />
                    <Container style={{marginTop: '7em'}}>
                        <Route exact path='/activities' component={ActivityDashboard}/>
                        <Route exact path='/activity/:id' component={ActivityDetails}/>
                        <Route key={location.key} exact path={['/createActivity', '/manage/:id']} component={ActivityForm}/>
                    </Container>
                </Fragment>
            )}/>
        </Fragment>
    );
};

export default withRouter(observer(App));
```
