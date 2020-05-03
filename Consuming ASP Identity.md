1. Ensure that you have finished "Final Forms.md" first.
2. First install Revalidate https://www.npmjs.com/package/revalidate
3. We will need two new interfaces IUser and IUserFormValues
```ts
export interface IUser {
    username: string;
    displayName: string;
    token: string;
    image?: string;
}

export interface IUserFormValues{
    email: string;
    password: string;
    displayName?: string;
    username?: string;
}
```
4. This step will add a bit more complexity, but in the end will clean some things up, createa rootStore in mobx.
```ts
import ActivityStore from "./activityStore";
import UserStore from "./userStore";
import {createContext} from "react";
import {configure} from "mobx";
import CommonStore from "./commonStore";
import ModalStore from "./modalStore";
configure({enforceActions: "always"});
export class RootStore {
    //For each store we create, we will need to create a class prop of type NStore like we do below. These stores will be created
    below.
    activityStore: ActivityStore;
    userStore: UserStore;
    commonStore: CommonStore;
    modalStore: ModalStore;

    constructor() {
        this.activityStore = new ActivityStore(this);
        this.userStore = new UserStore(this);
        this.commonStore = new CommonStore(this);
        this.modalStore = new ModalStore(this);
    }
}

export const RootStoreContext = createContext(new RootStore());
```
5. The ModalStore will allow us to control the modal used for register and login from mobx
```ts
import {RootStore} from "./rootStore";
import {action, observable} from "mobx";

export default class ModalStore{
    rootStore: RootStore;

    constructor(rootStore: RootStore) {
        this.rootStore = rootStore;
    }

    @observable.shallow modal = {
        open: false,
        body: null
    };

    @action openModal = (content: any) =>{
        this.modal.open = true;
        this.modal.body = content;
    };

    @action closeModal = () =>{
        this.modal.open = false;
        this.modal.body = null;
    };
}
```
6. The common store will be ussed to control the token funcitonlity.
```ts
import {RootStore} from "./rootStore";
import {action, observable, reaction} from "mobx";

export default class CommonStore {
    rootStore: RootStore;

    constructor(rootStore: RootStore) {
        this.rootStore = rootStore;
        reaction(
            () => this.token,
            token =>{
                if (token) {
                    window.localStorage.setItem('jwt', token);
                } else {
                    window.localStorage.removeItem('jwt');
                }
            }
        )
    }

    @observable token: string | null = window.localStorage.getItem('jwt');
    @observable appLoaded = false;

    @action setToken = (token: string | null) =>{
        this.token = token;
    };

    @action setAppLoaded = () =>{
        this.appLoaded = true;
    };
}
```
7. Below is the UserStore 
```ts
import {action, computed, observable, runInAction} from "mobx";
import {IUser, IUserFormValues} from "../Interfaces/user";
import agent from "../api/agent";
import {RootStore} from "./rootStore";
import { history } from '../..';

export default class UserStore
{
    rootStore: RootStore;
    constructor(rootStore: RootStore){
        this.rootStore = rootStore
    }

    @observable user: null | IUser = null;

    @computed get isLoggedIn() {return !!this.user};

    @action login = async (values: IUserFormValues) => {
        try {
            const user = await agent.User.login(values);
            runInAction('Return user after successful login', () =>{
                this.user = user;
                this.rootStore.commonStore.setToken(user.token);
                this.rootStore.modalStore.closeModal();
                history.push('/activities');
            });
        } catch (e) {
            runInAction('Error with user login', () =>{
                throw e;
            });
        }
    };

    @action register = async (values: IUserFormValues) =>{
        try {
            const user = await agent.User.register(values);
            runInAction('Register user success', () =>{
                this.user = user;
                this.rootStore.commonStore.setToken(user.token);
                this.rootStore.modalStore.closeModal();
                history.push('/activities');
            });
        }catch (e) {
            runInAction('Error with user Registration', () =>{
                throw e;
            });
        }
    };

    @action logout = () =>{
        this.rootStore.commonStore.setToken(null);
        this.user = null;
        history.push('/');
    };

    @action getCurrentUser = async () =>{
        try {
            const user = await agent.User.current();
            runInAction('Current user returned', () =>{
               this.user = user;
            });
        } catch (e) {
            runInAction('Error fetching current user', () =>{
               console.log(e);
            });
        }
    }
}
```
8. Below is the App.tsx file, note the notes below
```ts
import React, {Fragment, useContext, useEffect} from 'react';
import NavBar from '../../features/nav/NavBar'
import {Container} from "semantic-ui-react";
import {observer} from "mobx-react-lite";
import {Route, RouteComponentProps, Switch, withRouter} from "react-router-dom";
import HomePage from "../../features/home/HomePage";
import ActivityDashboard from "../../features/activities/dashboard/ActivityDashboard";
import ActivityForm from "../../features/activities/form/ActivityForm";
import ActivityDetails from "../../features/activities/details/ActivityDetails";
import NotFound from "./NotFound";
import {ToastContainer} from "react-toastify";
import WhatTheHeck from "./WhatTheHeck";
import {RootStoreContext} from "../stores/rootStore";
import LoadingComponent from "./LoadingComponent";
import ModalContainer from "../common/modals/ModalContainer";
const App: React.FC<RouteComponentProps> = ({location}) => {
    const rootStore = useContext(RootStoreContext);
    const {commonStore, userStore} = rootStore;
    //This useEffect below is important in regard to keeping our user signed in. If app is reloaded, we will call the user store
    // which will get the current user if anyone is logged in, if not, the app will take them to the home screen.
    //it is able to get the current user as even though they are refreshing the pass, their token is still in memory, and we
    //use the data within the token to go and fetch the user from the api.
    useEffect(() =>{
       if (commonStore.token){
           userStore.getCurrentUser().finally(() => commonStore.setAppLoaded());
       }else {
           commonStore.setAppLoaded();
       }
    }, [commonStore, userStore]);

    if (!commonStore.appLoaded){
        return <LoadingComponent content='Loading Cool Application' />
    }

    return (
        <Fragment>
            //the modalContainer must sit ontop of the other components so that when the user needs to login or register,
            //the modal will be technically at the top level.
            <ModalContainer />
            <ToastContainer position={"bottom-right"} />
            <Route exact path='/' component={HomePage}/>
            <Route path={'/(.+)'} render={() => (
                <Fragment>\
                    <NavBar />
                    <Container style={{marginTop: '7em'}}>
                        <Switch>
                            <Route exact path='/activities' component={ActivityDashboard}/>
                            <Route exact path='/activity/:id' component={ActivityDetails}/>
                            <Route exact path={['/createActivity', '/manage/:id']} component={ActivityForm}/>
                            <Route exact path={['/theHeck']} component={WhatTheHeck}/>
                            <Route component={NotFound}/>
                        </Switch>
                    </Container>
                </Fragment>
            )}/>
        </Fragment>
    );
};

export default withRouter(observer(App));
```
9. Modal Container
```ts
import React, {useContext} from 'react';
import {observer} from "mobx-react-lite";
import {Modal} from "semantic-ui-react";
import {RootStoreContext} from "../../stores/rootStore";

const ModalContainer: React.FC = () => {
    const rootStore = useContext(RootStoreContext);
    const {modalStore} = rootStore;

    return (
        <Modal open={modalStore.modal.open} onClose={modalStore.closeModal} size='mini'>
            <Modal.Content>
                {modalStore.modal.body}
            </Modal.Content>
        </Modal>
    );
};

export default observer(ModalContainer);
```
10. Below is the HomePage component that will contain the buttons that will trigger the modals.
```ts
import React, {Fragment, useContext} from 'react';
import {Container} from "semantic-ui-react";
import {Link} from "react-router-dom";
import {Button, Segment, Image, Header} from "semantic-ui-react";
import {RootStoreContext} from "../../app/stores/rootStore";
import LoginForm from "../user/LoginForm";
import RegisterForm from "../user/RegisterForm";

const HomePage: React.FC = () => {
    const rootStore = useContext(RootStoreContext);
    const{userStore, modalStore} = rootStore;

    return (
        <Segment inverted textAlign='center' vertical className='masthead' >
            <Container text>
                <Header as='h1' inverted>
                    <Image size='massive' src='/assets/logo.png' alt='logo' style={{marginBottom: 12}}/>
                    Reactivities
                </Header>
                {userStore.isLoggedIn && userStore.user ?
                    <Fragment>
                        <Header as='h2' inverted content={`Welcome Back ${userStore.user.displayName}`} />
                        <Button as={Link} to='/activities' size='huge' inverted>
                            Go to activities!
                        </Button>
                    </Fragment> :
                    <Fragment>
                        <Header as='h2' inverted content='Welcome to Reactivities' />
                        <Button onClick={() => modalStore.openModal(<LoginForm />)} size='huge' inverted>
                            Login
                        </Button>
                        <Button onClick={() => modalStore.openModal(<RegisterForm />)} size='huge' inverted>
                            Register
                        </Button>
                    </Fragment>
                }
            </Container>
        </Segment>
    );
};

export default HomePage;

```
11. Below is the ErrorMessage component we will use for the forms
```ts
import React from 'react';
import {observer} from "mobx-react-lite";
import {AxiosResponse} from "axios";
import {Message} from "semantic-ui-react";

interface IProps {
    error: AxiosResponse,
    text: string
}

const ErrorMessage: React.FC<IProps> = (props) => {
    return (
        <Message error>
            <Message.Header>{props.error.statusText}</Message.Header>
            {props.text && <Message.Content content={props.text}/>}
        </Message>
    );
};

export default observer(ErrorMessage);

```
12. Below is the registration from
```ts
import React, {useContext} from 'react';
import {observer} from "mobx-react-lite";
import {RootStoreContext} from "../../app/stores/rootStore";
import {Form as FinalForm, Field} from 'react-final-form';
import {Button, Form, Header} from 'semantic-ui-react';
import TextInput from "../../app/common/form/TextInput";
import {IUserFormValues} from "../../app/Interfaces/user";
import {FORM_ERROR} from "final-form";
import {
    combineValidators,
    composeValidators,
    isRequired,
    createValidator
} from "revalidate";
import ErrorMessage from "../../app/common/form/ErrorMessage";

const customIsRequired = isRequired({ message: 'Required' })

const isValidEmail = createValidator(
    message => value => {
        if (value && !/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$/i.test(value)) {
            return message
        }
    },
    'Invalid email address'
);

const isVlaidPassword = createValidator(
    message => value => {
        if (value && !/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{6,}$/i.test(value)) {
            return message
        }
    },
    'Invalid password'
);

const validate = combineValidators({
    email: composeValidators(
        customIsRequired,
        isValidEmail
    )(),
    password: composeValidators(
        customIsRequired,
        isVlaidPassword
    )('password'),
    displayName: isRequired('displayName'),
    username: isRequired('username'),
});

const RegisterForm: React.FC = (props) => {
    const rootStore = useContext(RootStoreContext);
    const {userStore} = rootStore;
    return (
        <FinalForm
            validate={validate}
            onSubmit={(values:IUserFormValues) => userStore.register(values).catch(err => ({
                [FORM_ERROR] : err
            }))}
            render={({handleSubmit, submitting, submitError, invalid, pristine, dirtySinceLastSubmit,values}) =>(
                <Form onSubmit={handleSubmit} error>
                    <Header content='Register Now!' textAlign='center' style={{color: '#1E6F9D'}} size={'large'}/>
                    <Field name='email' component={TextInput} placeholder='Email' />
                    <Field name='displayName' component={TextInput} placeholder='DisplayName' />
                    <Field name='username' component={TextInput} placeholder='Username' />
                    <Field name='password' type='password' component={TextInput} placeholder='Password' />
                    {submitError && !dirtySinceLastSubmit && <ErrorMessage error={submitError.message} text={'If you got this far, you forgot to place an uppercase letter in the password!'}/>}
                    <br/>
                    <Button fluid disabled={invalid && !dirtySinceLastSubmit || pristine} style={{backgroundColor: '#1E6F9D', color: 'white'}} content='Register' loading={submitting}/>
                </Form>
            )}
        />
    );
};

export default observer(RegisterForm);

```
13. Below is the Login Form
```ts
import React, {useContext} from 'react';
import {observer} from "mobx-react-lite";
import {RootStoreContext} from "../../app/stores/rootStore";
import {Form as FinalForm, Field} from 'react-final-form';
import {Button, Form, Header} from 'semantic-ui-react';
import TextInput from "../../app/common/form/TextInput";
import {IUserFormValues} from "../../app/Interfaces/user";
import {FORM_ERROR} from "final-form";
import {combineValidators, isRequired} from "revalidate";
import ErrorMessage from "../../app/common/form/ErrorMessage";

const validate = combineValidators({
   email: isRequired('email'),
   password: isRequired('password')
});

const LoginForm: React.FC = (props) => {
    const rootStore = useContext(RootStoreContext);
    const {userStore} = rootStore;
    return (
        <FinalForm
            validate={validate}
            onSubmit={(values:IUserFormValues) => userStore.login(values).catch(err => ({
                [FORM_ERROR] : err
            }))}
            render={({handleSubmit, submitting, form, submitError, invalid, pristine, dirtySinceLastSubmit}) =>(
                <Form onSubmit={handleSubmit} error>
                    <Header content='Login Now!' textAlign='center' style={{color: '#1E6F9D'}} size={'large'}/>
                    <Field name='email' component={TextInput} placeholder='Email' />
                    <Field name='password' type='password' component={TextInput} placeholder='Password' />
                    {submitError && !dirtySinceLastSubmit && <ErrorMessage error={submitError.message} text={'Invalid email or password'}/>}
                    <br/>
                    <Button fluid disabled={invalid && !dirtySinceLastSubmit || pristine} style={{backgroundColor: '#1E6F9D', color: 'white'}} content='Login' loading={submitting}/>
                </Form>
            )}
        />
    );
};

export default observer(LoginForm);
```
14. Below is the agent, note the notes.
```ts
import axios, { AxiosResponse } from 'axios';
import { history } from '../..';
import { toast } from 'react-toastify';
import {IActivity} from "../Interfaces/activity";
import {IUser, IUserFormValues} from "../Interfaces/user";

axios.defaults.baseURL = 'https://localhost:44396/api';

//this will ensre that we send the token up with each request to the api
axios.interceptors.request.use((config) =>{
    const token = window.localStorage.getItem('jwt');
    if (token){
        config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
}, error => {return Promise.reject(error)});

axios.interceptors.response.use(undefined, error => {
    if (error.message === 'Network Error' && !error.response) {
        toast.error('Network error - make sure API is running!')
    }
    const {status, data, config} = error.response;
    if (status === 400 || 404 && config.method === 'get' && data.errors.hasOwnProperty('id')) {
        history.push('/notfound')
    }
    if (status === 500) {
        toast.error('Server error - check the terminal for more info!')
    }
    throw error;
});

const responseBody = (response: AxiosResponse) => response.data;

const sleep = (ms: number) => (response: AxiosResponse) =>
    new Promise<AxiosResponse>(resolve => setTimeout(() => resolve(response), ms));

const requests = {
    get: (url: string) => axios.get(url).then(sleep(300)).then(responseBody),
    post: (url: string, body: {}) => axios.post(url, body).then(responseBody),
    put: (url: string, body: {}) => axios.put(url, body).then(responseBody),
    del: (url: string) => axios.delete(url).then(responseBody)
};

const User = {
  current: (): Promise<IUser> => requests.get('/user'),
  login: (user: IUserFormValues): Promise<IUser> => requests.post('/user/login', user),
  register: (user: IUserFormValues): Promise<IUser> => requests.post('/user/register', user)
};

export default {
    Activities,
    User
}
```
15 Below are the changeds we used for email confirmation. Start with the Agent class
```ts
axios.interceptors.response.use(undefined, error => {
    if (error.message === 'Network Error' && !error.response) {
        toast.error('Network error - make sure API is running!')
    }
    const {status, data, config} = error.response;
    if (status === 400 || 404 && config.method === 'get' && data.errors.hasOwnProperty('id')) {
        history.push('/notfound')
    }
    if (status === 500) {
        toast.error('Server error - check the terminal for more info!')
    }
    // checking to see if the email not confirmed error is returned.
    if (status === 401) {
        toast.error('Pleas confirm your email address!')
    }
    throw error;
});
```
16. in the user store I added this
```ts
    // will need this for our HomePage Component
    @observable registerComplete: boolean = false;
    
    // modified the register action to this
        @action register = async (values: IUserFormValues) =>{
        try {
            await agent.User.register(values);
            runInAction('Register user success', () =>{
                this.registerComplete = true;
                history.push('/');
            });
        }catch (e) {
            runInAction('Error with user Registration', () =>{
                throw e;
            });
        }
    };
```
17. Below is the homepage, note the changes.
```ts
import React, {Fragment, useContext, useEffect} from 'react';
import {Container} from "semantic-ui-react";
import {Link} from "react-router-dom";
import {Button, Segment, Image, Header} from "semantic-ui-react";
import {RootStoreContext} from "../../app/stores/rootStore";
import LoginForm from "../user/LoginForm";
import RegisterForm from "../user/RegisterForm";
import {observer} from "mobx-react-lite";

const HomePage: React.FC = () => {
    const rootStore = useContext(RootStoreContext);
    const{userStore, modalStore} = rootStore;
    
    // added a useeffect to check if registration form was submited successfuly. If so, I open the loginmodal
    useEffect (() =>{
        if (userStore.registerComplete) {
            modalStore.openModal(<LoginForm />)
        }
    }, );

    return (
        <Segment inverted textAlign='center' vertical className='masthead' >
            <Container text>
                <Header as='h1' inverted>
                    <Image size='massive' src='/assets/logo.png' alt='logo' style={{marginBottom: 12}}/>
                    Reactivities
                </Header>
                {userStore.isLoggedIn && userStore.user ?
                    <Fragment>
                        <Header as='h2' inverted content={`Welcome Back ${userStore.user.displayName}`} />
                        <Button as={Link} to='/activities' size='huge' inverted>
                            Go to activities!
                        </Button>
                    </Fragment> :
                    <Fragment>
                        <Header as='h2' inverted content='Welcome to Reactivities' />
                        <Button onClick={() => modalStore.openModal(<LoginForm />)} size='huge' inverted>
                            Login
                        </Button>
                        <Button onClick={() => modalStore.openModal(<RegisterForm />)} size='huge' inverted>
                            Register
                        </Button>
                    </Fragment>
                }
            </Container>
        </Segment>
    );
};
// made the homepage component an observer
export default observer(HomePage);
```
