1. Run command 
```
npm i react-final-form final-form
and then to handle date inputs
npm i react-widgets react-widgets-date-fns
after the command above you will see this message
"react-widgets-date-fns@4.0.26 requires a peer of date-fns@2.0.0-alpha.13 but none is installed. You must install peer dependencies yourself."
run command
npm i date-fns@2.0.0-alpha.13
npm install @types/react-widgets
npm i revalidate
npm i @types/revalidate
```
2. Below is the index.tsx file, we will bring in date-fns imports via an import
```ts
//here is the import
import 'react-widgets/dist/css/react-widgets.css'
import dateFnsLocalizer from 'react-widgets-date-fns';

dateFnsLocalizer();

export const history = createBrowserHistory();

ReactDOM.render(<Router history={history}> <ScrollToTop>  <App /> </ScrollToTop> </Router>, document.getElementById('root'));

serviceWorker.unregister();
```
3. Create a new decleration file titled react-wdgets-date-fns.d.ts in a new driectory titled "typings-custom"
```ts
declare module 'react-widgets-date-fns';
```
4. in tsconfig.json
```json
  "include": [
    "src",
    "typings-custom"
    //if that does not work use
    ./typings-custom/**/*.ts
  ]
```
5. Below is the form component
```ts
import React, {useContext, useEffect, useState} from 'react';
import {Segment} from "semantic-ui-react";
import Form from "semantic-ui-react/dist/commonjs/collections/Form";
import Button from "semantic-ui-react/dist/commonjs/elements/Button";
import {ActivityFormValues} from "../../../app/Interfaces/activity";
import {v4 as uuid} from 'uuid';
import {observer} from "mobx-react-lite";
import ActivityStore from '../../../app/stores/activityStore'
import {RouteComponentProps} from "react-router";
import Grid from "semantic-ui-react/dist/commonjs/collections/Grid";
import {Form as FinalForm, Field} from 'react-final-form'
import TextInput from "../../../app/common/form/TextInput";
import TextAreaInput from "../../../app/common/form/TextAreaInput";
import SelectInput from "../../../app/common/form/SelectInput";
import DateInput from "../../../app/common/form/DateInput";
import LoadingComponent from "../../../app/layout/LoadingComponent";
import {combineValidators, composeValidators, hasLengthGreaterThan, isRequired} from 'revalidate';

const validate = combineValidators({
    title: isRequired('The event title is required'),
    category: isRequired('Category'),
    description: composeValidators(
        isRequired('Description'),
        hasLengthGreaterThan(4)({message: 'Description needs to be at least 5 characters'}))
    (),
    city: isRequired('City'),
    venue: isRequired('Venue'),
    date: isRequired('Date'),
    time: isRequired('Time')
});

interface IDetailParams {
    id: string
}

const ActivityForm: React.FC<RouteComponentProps<IDetailParams>> = (props) => {
    const activityStore = useContext(ActivityStore);

    const [activity, setActivity] = useState(new ActivityFormValues());
    const [loading, setLoading] = useState(false);
    useEffect (() =>{
        setLoading(true);
        if (props.match.params.id){
            activityStore.loadActivity(props.match.params.id).then((activity) => setActivity(
                new ActivityFormValues(activity)
            )).finally(() => setLoading(false));
        }else{
            setLoading(false);
            setActivity(new ActivityFormValues());
        }
    }, [activityStore, props.match.params.id]);

    const combineDateAndTime = (date: Date, time: Date): Date =>{
        const timeString = time.getHours() + ':' + time.getMinutes() + ':00';
        const year = date.getFullYear();
        const month = date.getMonth() + 1;
        const day = date.getDate();
        const dateString = `${year}-${month}-${day}`;

        return new Date(dateString + ' ' + timeString);
    };

    const handleFinalFormSubmit = (values: any) =>{
          const dateAndTime = combineDateAndTime(values.date, values.time);
          const {date, time, ...activity} = values;
          activity.date = dateAndTime;
          if (!activity.id){
              let newActivity = {
                  ...activity,
                  id: uuid()
              };
              activityStore.createActivity(newActivity);
          } else {
              activityStore.editActivity(activity);
          }
    };

    const redirectAfterCancel = () =>{
        activity.id ? props.history.push(`/activity/${activity.id}`) :
                      props.history.push(`/activities`);
    };
    if (loading) return <LoadingComponent content={'Loading Activities...'} />;

        return (
            <Grid>
                <Grid.Column width={'10'}>
                    <Segment clearing>
                        <FinalForm validate={validate} initialValues={activity} onSubmit={handleFinalFormSubmit} render={(props) =>(
                            <Form onSubmit={props.handleSubmit}>
                                <Field name='title' placeholder='title' value={activity.title} component={TextInput}/>
                                <Field name='description' placeholder='Description' value={activity.description} component={TextAreaInput} rows={4}/>
                                <Field name='category' placeholder='Category' value={activity.category} component={SelectInput} />
                                <Form.Group widths={'equal'}>
                                    <Field date={true} name='date' placeholder='Date' value={activity.date} component={DateInput}/>
                                    <Field time={true} name='time' placeholder='Time' value={activity.time} component={DateInput}/>
                                </Form.Group>
                                <Field name='city' placeholder='City' value={activity.city} component={TextInput}/>
                                <Field name='venue' placeholder='Venue' value={activity.venue} component={TextInput}/>
                                <Button disabled={props.invalid || props.pristine} floated='right' type='submit' positive content='Submit'/>
                                <Button onClick={redirectAfterCancel} style={{backgroundColor: 'red', color: 'white'}} floated='right' type='submit' content='Cancel'/>
                            </Form>
                        )} />
                    </Segment>
                </Grid.Column>
            </Grid>
        );
};

export default observer(ActivityForm);
```
6. Below is the DateInput component
```ts
import React from 'react'
import { FieldRenderProps } from 'react-final-form';
import { FormFieldProps, Form, Label } from 'semantic-ui-react';
import {DateTimePicker} from 'react-widgets';

interface IProps extends FieldRenderProps<any, HTMLElement>, FormFieldProps {}

const DateInput: React.FC<IProps> = ({input,width,placeholder,date = false,time = false,meta: { touched, error },...rest}) => {
    return (
        <Form.Field error={touched && !!error} width={width}>
            <DateTimePicker placeholder={placeholder} value={input.value || null} onChange={input.onChange}
                            onBlur={input.onBlur} onKeyDown={(e) => e.preventDefault()} date={date} time={time} {...rest} />
            {touched && error && (
                <Label basic color='red'>
                    {error}
                </Label>
            )}
        </Form.Field>
    )
};

export default DateInput

```
7. Below is the SelectInput component
```ts
import React from 'react';
import {FieldRenderProps} from "react-final-form";
import {FormFieldProps, Form, Select} from "semantic-ui-react";
import Label from "semantic-ui-react/dist/commonjs/elements/Label";

interface IProps extends FieldRenderProps<string, HTMLElement>, FormFieldProps{}

const SelectInput: React.FC<IProps> = (props) => {

    return (
        <Form.Field error={props.meta.touched && !!props.meta.error} width={props.width}>
            <Select value={props.input.value} onChange={(e, data) => props.input.onChange(data.value)} placeholder={props.placeholder} options={categories}/>
            {props.meta.touched && props.meta.error && (
                <Label basic color='red'>
                    {props.meta.error}
                </Label>
            )}
        </Form.Field>
    );
};

const categories = [
    {key: 'drinks', text: 'Drinks', value: 'drinks'},
    {key: 'culture', text: 'Culture', value: 'culture'},
    {key: 'film', text: 'Film', value: 'film'},
    {key: 'food', text: 'Food', value: 'food'},
    {key: 'music', text: 'Music', value: 'music'},
    {key: 'travel', text: 'Travel', value: 'travel'}
];

export default SelectInput;
```
8. Below is the textarea component
```ts
import React from 'react';
import {FieldRenderProps} from "react-final-form";
import {FormFieldProps, Form} from "semantic-ui-react";
import Label from "semantic-ui-react/dist/commonjs/elements/Label";


interface IProps extends FieldRenderProps<string, HTMLElement>, FormFieldProps{}

const TextAreaInput: React.FC<IProps> = (props) => {
    return (
        <Form.Field error={props.meta.touched && !!props.meta.error} width={props.width}>
            <textarea rows={props.rows} {...props.input} placeholder={props.placeholder} />
            {props.meta.touched && props.meta.error && (
                <Label basic color='red'>
                    {props.meta.error}
                </Label>
            )}
        </Form.Field>
    );
};

export default TextAreaInput;
```
9. Below is the text input component
```ts
import React from 'react';
import {FieldRenderProps} from "react-final-form";
import {FormFieldProps, Form} from "semantic-ui-react";
import Label from "semantic-ui-react/dist/commonjs/elements/Label";


interface IProps extends FieldRenderProps<string, HTMLElement>, FormFieldProps{}

const TextAreaInput: React.FC<IProps> = (props) => {
    return (
        <Form.Field error={props.meta.touched && !!props.meta.error} width={props.width}>
            <textarea rows={props.rows} {...props.input} placeholder={props.placeholder} />
            {props.meta.touched && props.meta.error && (
                <Label basic color='red'>
                    {props.meta.error}
                </Label>
            )}
        </Form.Field>
    );
};

export default TextAreaInput;

```
