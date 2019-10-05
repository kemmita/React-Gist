1. see setup redux before this
```js
import {GET_LOGS, SET_LOADING, LOGS_ERROR, ADD_LOG, DELETE_LOG, SET_CURRENT, CLEAR_CURRENT, UPDATE_LOG} from './types';

//pass in the id of the log we want to remove
export const deleteLog = (id) => async dispatch =>{
    try {
        //make an http request to the server
        await fetch(`/logs/${id}`, {
            method: 'DELETE',
        });
        //we will dispatch to our reducer that we will be disapatching a type of "DELETE_LOG" and that they payload sent
        // will be the id.
        dispatch({
            type: DELETE_LOG,
            payload: id
        });
    } catch (e) {
        dispatch({
            type: LOGS_ERROR,
            payload: e.response.data
        });
    }
};
```
2. Now in the log reducer
```js
import {DELETE_LOG} from '../actions/types';

const initialState = {
    logs: null,
    current: {},
    loading: false,
    error: null
};

export default(state = initialState, action) =>{
    switch (action.type) {
        case DELETE_LOG:
            return{
            // fetch the current state of the logs
                ...state,
            // filter the logs and delete the log sent using the log.id    
                logs: state.logs.filter(log => log.id !== action.payload),
            };
        default:
            return state;
    }
}
```
3. Now we can use the action in our component, remeber that we will need to import connect so that we can gain access to the action we created, "deleteLog".
```js
import React from 'react';
import Moment from 'react-moment';
import { connect } from 'react-redux';
import { deleteLog} from "../../actions/logActions";
import M from 'materialize-css/dist/js/materialize.min';

const LogItem = ({log, deleteLog}) => {
    const onDelete = () =>{
    // within an onclick method we call on the action we created.
        deleteLog(log.id);
        M.toast({html: 'log deleted'});
    };
    return (
        <li className='collection-item'>
            <div>
                <a href="#edit-log-modal" className={`modal-trigger ${log.attention ? 'red-text' : 'blue-text'}`} onClick={() => setCurrent(log)}>{log.message}</a>
                <br/>
                <span className='grey-text'>
                    <span className="black-text">ID #{log.id} </span>
                    last updated by: {''}
                    <span className="black-text">{log.tech + ' '}</span>
                    on {''}
                    <Moment format='MMMM Do YYYY, h:mm:Sss a'>{log.date}</Moment>
                </span>
                <a href="#!" onClick={onDelete} className="secondary-content">
                    <i className="material-icons grey-text">delete</i>
                </a>
            </div>
        </li>
    );
};

export default connect(null, {deleteLog}) (LogItem);
```
