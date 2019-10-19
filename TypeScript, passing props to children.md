1. In our main App component, we will define a few params name and age. We want to pass this to our children components.
```ts
import React from 'react';
import CarItem from './CarItem'

const App: React.FC = () => {
  
  //here we define the vars to be passed to the child component "CarItem"
  const name: string = "Russell";
  const age: number = 90;
  
  return (
    <div className="App">
      <ul>
                            //Below is the CarItem child component and we send the props down from app to the child      
        {cars.map((car) => <CarItem key={age+1} age={age} name={name} />)}
      </ul>
    </div>
  );
};

export default App;

```
2. Below we will see how to access our props passed from the parent to the child
```ts
import React from 'react';

//making this work is quite simple, create an Interface of IProps and declare the vars and datatypes that will be
//passed from the parent.
interface IProps {
    name: string,
    age: number
}
//below we define the IProps
const CarItem: React.FC<IProps> = (props) => {

    return (
        <div className="App">
            <h1>{props.name} {props.age}</h1>
        </div>
    );
};

export default CarItem;
```
