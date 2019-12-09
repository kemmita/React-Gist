1. First install
```
npm install react-toastify
```
2. In the index.tsx file import toastify
```ts
import React from 'react';
import ReactDOM from 'react-dom';
import {Router} from 'react-router-dom';
import 'react-toastify/dist/ReactToastify.min.css'
import './app/layout/index.css';
import App from './app/layout/App';
import * as serviceWorker from './serviceWorker';
import ScrollToTop from "./app/layout/ScrollToTop";
import {createBrowserHistory} from "history";

export const history = createBrowserHistory();

ReactDOM.render(<Router history={history}> <ScrollToTop>  <App /> </ScrollToTop> </Router>, document.getElementById('root'));

serviceWorker.unregister();
```
3. Then in App.tsx add it above the routes
```ts
import {ToastContainer} from "react-toastify";

    return (
        <Fragment>
            <ToastContainer position={"bottom-right"} />
            <Route exact path='/' component={HomePage}/>
            <Route path={'/(.+)'} render={() => (
                <Fragment>
                    <NavBar />
                    <Container style={{marginTop: '7em'}}>
                        <Switch>
                            <Route exact path='/activities' component={ActivityDashboard}/>
                            <Route exact path='/activity/:id' component={ActivityDetails}/>
                            <Route exact path={['/createActivity', '/manage/:id']} component={ActivityForm}/>
                            <Route component={NotFound}/>
                        </Switch>
                    </Container>
                </Fragment>
            )}/>
        </Fragment>
    );
```
4. Now within any file we want that deals with the components surrounded by the router, we can use toasitfy.
```ts
import {toast} from "react-toastify";
axios.interceptors.response.use(undefined, (error) => {
    if(error.response.status === 404 || error.response.status === 400) {
       history.push('/notfound');
    }
    if (error.response.status === 500){
        history.push('/theHeck');
        //any page that generates a 500 error, will have the error red box pop up in the bottom right corner of the page
        //as that is where we defined it to be in file App.tsx
        toast.error('Server Error - check thy terminal for more info!');
    }
});
```
