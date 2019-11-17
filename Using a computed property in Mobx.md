1. Computed props are props that are fetched after a computation has been done on them. Below is the activity sotre
```ts
import {observable, action, computed} from "mobx";
import {createContext} from "react";
import {IActivity} from "../Interfaces/activity";
import agent from "../api/agent";

class ActivityStore{
    @observable activities: IActivity[] = [];
    
    //this will be retuned as a prop, not a function, this is important as we will need to map over these.
    //the computation performed is to return a list of activities in order by date.
    @computed get activitiesByDate() {
        return this.activities.sort((a,b) => Date.parse(a.date) - Date.parse(b.date));
    };

    @action selectActivity = (id: string) =>{
         this.selectedActivity = this.activities.find(a => a.id === id);
         this.setEditMode(false);
    };
}

export default createContext(new ActivityStore());
```
2. Below is the component that will use the computed prop above.
```ts
import React, {useContext} from 'react';
import {observer} from "mobx-react-lite";
import ActivityStore from '../../../app/stores/activityStore'


const ActivityList: React.FC<IProps> = (props) => {
    const activityStore = useContext(ActivityStore);
    return (
        <Segment clearing>
            <Item.Group divided>
            //here we call the computed prop
                {activityStore.activitiesByDate.map(activity =>(
                    <Item key={activity.id}>
                        <Item.Content>
                            <Item.Header as='a'>{activity.title}</Item.Header>
                            <Item.Meta>{activity.date}</Item.Meta>
                            <Item.Description>
                                <div>{activity.description}</div>
                                <div>{activity.city}, {activity.venue}</div>
                            </Item.Description>
                            <Button onClick={() => activityStore.selectActivity(activity.id)} floated='right' content='view' color='blue'/>
                            <Button onClick={() => props.deleteActivity(activity.id)} floated='right' content='delete' color='red'/>
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
