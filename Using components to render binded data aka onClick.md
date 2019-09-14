1. the goal is to set a counter to 0 and to increase and decrease the count with buttons.
```
let count = 0;
// below is our method to increase the count. In order to this we need to increase the count by one and then rerender the
// component.
const increaseCount = () =>{
    count++;
    renderCounterApp();
};

const decreaseCount = () =>{
    if (count === 0){
        return count;
    }
    count--;
    return renderCounterApp();
};

// Here is our component that wraps all of our jsx elements rendered to the screen.
const renderCounterApp = () =>{

    const templateTwo = (
        <div>
            <h1>Count: {count}</h1>
            <button onClick={increaseCount}>Increase Count+</button> <button onClick={decreaseCount}>Decrease Count-</button>
        </div>
    );

    ReactDOM.render(templateTwo, document.getElementById('root'));
};
// this will need to be called everytime at the end of the file to render the componenet
renderCounterApp();
```
