1. run command
```
npm i redux react-redux redux-thunk redux-devtools-extension
```
2. first we will need to create a redux store.
```js
import { createStore, applyMiddleware } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import thunk from 'redux-thunk';
import rootReducer from './reducers';


const initialSate = {};

const middleWare = [thunk];

const store = createStore(rootReducer, initialSate, composeWithDevTools(applyMiddleware(...middleWare)));

export default store;
```
3. Then create a directory called reducers and place an index.js file in it to act as the rootReducer. The rootReducer will be resposnisble for registering all of the reducers for the application.
```js
import { combineReducers } from 'redux';
import logReducer from './logReducer';

export default combineReducers({
    // this is what we will call in our component 'log' if they call on log in their component, they will have access to the
    //logReducer
    log: logReducer
});
```
4. Now create your logReducer
```js
import {GET_LOGS, SET_LOADING, LOGS_ERROR} from '../actions/types';
// delcare the initial state of your global variables
const initialState = {
    logs: null,
    current: null,
    loading: false,
    error: null
};

//below we use a switch to map the actions that are tirggered.
export default(state = initialState, action) =>{
    switch (action.type) {
        case SET_LOADING:
            return {
                ...state,
                loading: true
            };
        case LOGS_ERROR:
            console.error(action.payload);
            return {
                ...state,
                error: action.payload
            };
        case GET_LOGS:
            return {
                ...state,
                logs: action.payload,
                loading: false
            };
        default:
            return state;
    }
}
```
5. Now in a new direcotry we created actions, we will create our logActions that will be reosponsible for triggering the logreducer
```js
import {GET_LOGS, SET_LOADING, LOGS_ERROR} from './types';
// the getLogs method below makes an http call to our server and then dispatchers the data returned to the logReducer
// we need to delcare the Type so that the switch will know which to choose above and then we suply the payload with 
// the data returned.
export const getLogs = () => async dispatch =>{
        try {
            setLoading();

            const res = await fetch('/logs');
            const data = await res.json();

            dispatch({
                type: GET_LOGS,
                payload: data
            });
        } catch (e) {
            dispatch({
               type: LOGS_ERROR,
               payload: e.response.data
            });
        }
};

export const setLoading = () =>{
  return {
      type: SET_LOADING
  }
};
```
6. In the actions directory, create a types.js file if you notice above throughout the code, you will have seen "UPPERCASE_EXAMPLE" these are the types we will now specify them in the file below.
```js
export const GET_LOGS = 'GET_LOGS';
export const ADD_LOG = 'ADD_LOG';
export const DELETE_LOG = 'DELETE_LOG';
export const SET_CURRENT = 'SET_CURRENT';
export const CLEAR_CURRENT = 'CLEAR_CURRENT';
export const UPDATE_LOG = 'UPDATE_LOG';
export const CLEAR_LOGS = 'CLEAR_LOGS';
export const SET_LOADING = 'SET_LOADING';
export const LOGS_ERROR = 'LOGS_ERROR';
export const SEARCH_LOGS = 'SEARCH_LOGS';
export const GET_TECHS = 'GET_TECHS';
export const ADD_TECH = 'ADD_TECH';
export const DELETE_TECH = 'DELETE_TECH';
export const TECHS_ERROR = 'TECHS_ERROR';
```
7. Now in our main App.js component where we would delcare a router, we need to wrap it in a provider and define the store we created at the begining of this gist.
```js
import React, {Fragment, useEffect} from 'react';
import 'materialize-css/dist/css/materialize.min.css';
import M from 'materialize-css/dist/js/materialize.min'
import SearchBar from './components/layout/SearchBar'
import Logs from './components/logs/Logs'
import AddBtn from './components/layout/AddBtn'
import AddLogModal from './components/logs/AddLogModal'
import EditLogModal from './components/logs/EditLogModal'
import AddTechModal from './components/techs/AddTechModal'
import TechListModal from './components/techs/TechListModal'
import { Provider } from 'react-redux';
import store from './store';

const App = () => {
    useEffect(() =>{
        M.AutoInit();
    });
    return (
        <Provider store={store}>
            <Fragment>
                <SearchBar />
                <div className='container'>
                    <Logs />
                    <AddBtn />
                    <AddLogModal />
                    <EditLogModal />
                    <AddTechModal />
                    <TechListModal />
                </div>
            </Fragment>
        </Provider>
    );
};

export default App;

```
8. now we want to gain access to the global state of logs in our logs.js component, but this could be for any component. If a user component needed to gain access to this state, they would implment it the same way as we are going to below.
```js
import React, {useEffect} from 'react';
// the two imports below connect and getLogs are needed for the state dealio here.
import { connect } from 'react-redux';
import { getLogs } from "../../actions/logActions";
import LogItem from './LogItem'
import Preloader from '../layout/Preloader'

//even though they are imported, you will still need to gain access to the data provided by redux as a prop. You
//can see props s being passed in below.
const Logs = (props) => {

    useEffect(() =>{
       //below we are using our getLogs action from redux by calling on it as a prop
       props.getLogs();
       // eslint-disable-next-line
    }, []);


    //below we access all of our log data via props.log.example
    if (props.log.loading || props.log.logs === null){
        return <Preloader />;
    }

    return (
        <ul className='collection with-header'>
            <li className='collection-header'>
                <h4 className='center'>System Logs</h4>
            </li>
            {!props.log.loading && props.log.logs.length === 0 ? (<p className='center'>No logs to show...</p>) : (props.log.logs.map(log => <LogItem log={log} key={log.id} />))}
        </ul>
    );
};

below is needed for redux to work state.log corelates to reducers/index.js "log:logReducer"
const mapStateToProps = state => ({
    log: state.log
});

//below syntax is needed to make this work. Notice you do need to specify the method fromr redux but not the props?
export default connect(mapStateToProps, { getLogs })(Logs);
```
