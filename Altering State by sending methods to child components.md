1. App.tsx
```ts
import React, { useState, useEffect, Fragment } from 'react';
import axios from 'axios';
import {IActivity} from "../Interfaces/activity";
import NavBar from '../../features/nav/NavBar'
import {Container} from "semantic-ui-react";
import ActivityDashboard from "../../features/activities/dashboard/ActivityDashboard";

const App = () => {
  const [activities, setActivities] = useState<IActivity[]>([]);
  //below we create the var and method
  const [selectedActivity, setSelectedActivity] = useState<IActivity | null>(null);

  //Here we create the method that will alter the setSelectedActivity method 
  const handleSelectActivity = (id: string): void =>{
      setSelectedActivity(activities.filter(a => a.id === id)[0]);
  };

  useEffect (() =>{
          axios.get<IActivity[]>('https://localhost:44396/api/activities').then(response => {
             setActivities(response.data);
          });
  }, []);

    return (
        <Fragment>
            <NavBar />
            <Container style={{marginTop: '7em'}}>
            //pass the method as a prop
            <ActivityDashboard selectedActivity={selectedActivity!} activities={activities} selectActivity={handleSelectActivity}/>
            </Container>
        </Fragment>
    );
};

export default App;
```
2. Child component
```ts
import React from 'react';
import {Grid} from "semantic-ui-react";
import {IActivity} from "../../../app/Interfaces/activity";
import ActivityList from "./ActivityList";
import ActivityDetails from "../details/ActivityDetails";
import ActivityForm from "../form/ActivityForm";

interface IProps {
    activities: IActivity[];
    //in order to pass the method from parent to child we need to set the method and its type in IProps
    selectActivity: (id: string) => void;
    selectedActivity: IActivity;
}

const ActivityDashboard: React.FC<IProps> = (props) => {
    return (
        <Grid>
            <Grid.Column width={10}>
                //pass the selectActivity to the grand child component as a prop again
                <ActivityList activities={props.activities} selectActivity={props.selectActivity}/>
            </Grid.Column>
        </Grid>
    );
};

export default ActivityDashboard;
```
3. Grandchild component
```ts
import React from 'react';
import { Item } from 'semantic-ui-react'
import Button from "semantic-ui-react/dist/commonjs/elements/Button";
import Label from "semantic-ui-react/dist/commonjs/elements/Label";
import Segment from "semantic-ui-react/dist/commonjs/elements/Segment";
import {IActivity} from "../../../app/Interfaces/activity";

//again in the interface declare the method passed from the component above and its type
interface IProps {
    activities: IActivity[];
    selectActivity: (id: string) => void;
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
                            //to trigger that method create an anonymous function within the onclick event and
                            //execute the mathod passed from the parent component, selectActivity
                            <Button onClick={() => props.selectActivity(activity.id)} floated='right' content='view' color='blue'/>
                            <Label basic content={activity.category}/>
                        </Item.Content>
                    </Item>
                ))}
            </Item.Group>
        </Segment>
    );
};

export default ActivityList;
```
