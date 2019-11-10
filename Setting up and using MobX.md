1. First install Mobx.
```
npm i mobx mobx-react-lite
```
2. Create an interface for an Activity so that TS will know what an Acitivity should look like.
```ts
export interface IActivity {
    id: string,
    title: string,
    description: string,
    category: string,
    date: string,
    city: string,
    venue: string
}
```
3. Create an agent.ts file that will make the http requests to the api
```ts
import axios, {AxiosResponse} from 'axios';
import {IActivity} from "../Interfaces/activity";

axios.defaults.baseURL = 'https://localhost:44396/api';

const responseBody = (response: AxiosResponse) => response.data;

const requests = {
    get: (url: string) => axios.get(url).then(sleep(1000)).then(responseBody),
};

const Activities = {
    list: (): Promise<IActivity[]> => requests.get('/activities'),
};

export default {
    Activities
}

```
4. Next create a ActivityStore that will be responsible for handling the state of an activity.
```ts
import {observable, action} from "mobx";
import {createContext} from "react";
import {IActivity} from "../Interfaces/activity";
import agent from "../api/agent";

class ActivityStore{
    //below is the variable that will contain the current state of the activities array/list
    @observable activities: IActivity[] = [];
    
    //below is how we create an action/method in mobx.
    @action loadActivies = () =>{
        agent.Activities.list().then(activities => {
            activities.forEach(activity =>{
                this.activities.push(activity);
            });
        });
    }

}

export default createContext(new ActivityStore());
```
5. Now Import the store into the parent/main component. Any component that interacts with a mobx store should be made an observable
```ts
import React, {useState, useEffect, Fragment, useContext} from 'react';
import agent from '../api/agent';
import {IActivity} from "../Interfaces/activity";
import NavBar from '../../features/nav/NavBar'
import {Container} from "semantic-ui-react";
import ActivityDashboard from "../../features/activities/dashboard/ActivityDashboard";
import LoadingComponent from "./LoadingComponent";
import ActivityStore from '../stores/activityStore'
import {observer} from "mobx-react-lite";
const App = () => {
  //creat an instance of the mobx store we created
  const activityStore = useContext(ActivityStore);
  
  //Using the useEffect hook, we call the action/method we created in the mobx store
  useEffect (() =>{
      activityStore.loadActivies();
  }, [activityStore]);
  
    return (
        <Fragment>
            <NavBar openCreateForm={handleOpenCreateForm}  />
            <Container style={{marginTop: '7em'}}>
                //below we pass the activites using the store variable activities as a prop.
                <ActivityDashboard activities={activityStore.activities}/>
            </Container>
        </Fragment>
    );
};

export default observer(App);
```
6. Below is the child component, we do not call instantiate the store again as we are reciving the activities as props. we will still
need to make the component an observable
```ts
import React from 'react';
import {Grid} from "semantic-ui-react";
import {IActivity} from "../../../app/Interfaces/activity";
import ActivityList from "./ActivityList";
import {observer} from "mobx-react-lite";

//create an inreface for the prop(s) passed.
interface IProps {
    activities: IActivity[];
}

const ActivityDashboard: React.FC<IProps> = (props) => {
    return (
        <Grid>
            <Grid.Column width={10}>
            //we pass them again to another component as a prop.
                <ActivityList activities={props.activities}/>
            </Grid.Column>
        </Grid>
    );
};
//make sure this component and any component interacting with mobx is an observer
export default observer(ActivityDashboard);
```
7. Lastly in the grand child component.
```ts
import React from 'react';
import { Item } from 'semantic-ui-react'
import Label from "semantic-ui-react/dist/commonjs/elements/Label";
import Segment from "semantic-ui-react/dist/commonjs/elements/Segment";
import {IActivity} from "../../../app/Interfaces/activity";
import {observer} from "mobx-react-lite";

interface IProps {
    activities: IActivity[];
}
const ActivityList: React.FC<IProps> = (props) => {

    return (
        <Segment clearing>
            <Item.Group divided>
                {props.activities.map(activity =>(
                    <Item key={activity.id}>
                        <Item.Content>
                            <Item.Header as='a'>{activity.title}</Item.Header>
                            <Item.Meta>{activity.date}</Item.Meta>
                            <Item.Description>
                                <div>{activity.description}</div>
                                <div>{activity.city}, {activity.venue}</div>
                            </Item.Description>
                            <Label basic content={activity.category}/>
                        </Item.Content>
                    </Item>
                ))}
            </Item.Group>
        </Segment>
    );
};
//observer
export default observer(ActivityList);
```
