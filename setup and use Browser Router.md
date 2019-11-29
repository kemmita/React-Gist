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
5.Creating a Dynamic route with an id
```ts
import React, {useEffect, Fragment, useContext} from 'react';
const App = () => {
  const activityStore = useContext(ActivityStore);

  useEffect (() =>{
      activityStore.loadActivities();
  }, [activityStore]);

  if (activityStore.loadingInitial) return <LoadingComponent content={'Loading Activities...'} />;
    return (
        <Fragment>
            <NavBar />
            <Container style={{marginTop: '7em'}}>
                //the path uses a ":" which singifies a url param so this will be localhost:3000/activities/17263hshs
                <Route exact path='/activity/:id' component={ActivityDetails}/>
            </Container>
        </Fragment>
    );
};

export default observer(App);
```
6. Below is the button that will direct the user to the component along with the id needed for the dynamic path
```ts
import React, {useContext} from 'react';
interface IProps {}
const ActivityList: React.FC<IProps> = (props) => {
    const activityStore = useContext(ActivityStore);
    return (
        <Segment clearing>
            <Item.Group divided>
                {activityStore.activitiesByDate.map(activity =>(
                    <Item key={activity.id}>
                        <Item.Content>
                            <Item.Header as='a'>{activity.title}</Item.Header>
                            <Item.Meta>{activity.date}</Item.Meta>
                            <Item.Description>
                                <div>{activity.description}</div>
                                <div>{activity.city}, {activity.venue}</div>
                            </Item.Description>
                            //Below we use string interpolation to set the dynamic id needed for the component to render correctly below
                            <Button as={Link} to={`/activity/${activity.id}`} floated='right' content='view' color='blue'/>
                            <Label basic content={activity.category}/>
                        </Item.Content>
                    </Item>
                ))}
            </Item.Group>
        </Segment>
    );
};

export default observer(ActivityList);
```
7. Here we will use that id set above in the route path to load the correct data within the ActivityDetails component.
```ts
import React, {useContext, useEffect} from 'react';
import {Card, Image} from "semantic-ui-react";
import Button from "semantic-ui-react/dist/commonjs/elements/Button";
import {observer} from "mobx-react-lite";
import ActivityStore from '../../../app/stores/activityStore'
import {RouteComponentProps} from "react-router";
import LoadingComponent from "../../../app/layout/LoadingComponent";
//Interface is needed to define our id will be of typer string 
interface IDetailParams {
    id: string
}

//use RouteComponentProps<IDetailParams> to gain access to the param field
const ActivityDetails: React.FC<RouteComponentProps<IDetailParams>> = (props) => {
    const activityStore = useContext(ActivityStore);
    const {activity, loadActivity, loadingInitial} = activityStore;
    
    //using the "useEffect" funciton, we grab the id from the url on component render to then go into 
    //our api and fetch the correct activity before the component finishes rendering.
    useEffect (() =>{
        loadActivity(props.match.params.id);
    }, [loadActivity, props.match.params.id]);



    if (loadingInitial || !activity) return <LoadingComponent content='Loading Activity...' />;

    return (
        <Card fluid>
            <Image src={require(`C:/Users/16512/source/repos/React_Core_App/client/public/assets/categoryImages/${activity!.category}.jpg`)} wrapped ui={false} />
            <Card.Content>
                <Card.Header>{activity!.title}</Card.Header>
                <Card.Meta>
                    <span>{activity!.date}</span>
                </Card.Meta>
                <Card.Description>
                    {activity!.description}
                </Card.Description>
            </Card.Content>
            <Card.Content extra>
                <Button.Group widths='2'>
                    <Button onClick={() => activityStore.setEditMode(true)} basic color='blue' content='Edit'/>
                    <Button onClick={() => activityStore.selectActivity('')} basic color='grey' content='Cancel'/>
                </Button.Group>
            </Card.Content>
        </Card>
    );
};

export default observer(ActivityDetails);
```
8. Below is how you redirect after a submit of a form. 
```ts
import {RouteComponentProps} from "react-router";

const ActivityForm: React.FC<RouteComponentProps> = (props) => {
    const activityStore = useContext(ActivityStore);

    //the funciton below would handle the submit of the form
    const handleSubmit = () =>{
      if (activity.id.length === 0){
          let newActivity = {
              ...activity,
              id: uuid()
          };
          //once the form has been submitted and the data is stored, we will use a promise to use a route push to redirect the user.
          activityStore.createActivity(newActivity).then(() => props.history.push(`/activity/${newActivity.id}`));
      } else {
          activityStore.editActivity(activity).then(() => props.history.push(`/activity/${activity.id}`));
      }
    };

export default observer(ActivityForm);

```
