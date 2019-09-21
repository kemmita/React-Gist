1. Below you will see the difference in syntax between the class and fucntion based components. We can use a stateless function when we are not altering state.
```
class Action extends React.Component{
    render(){
        return (
            <div>
                <button disabled={!this.props.optionsAvailable} onClick={this.props.handleRandomPick}>What Should I do?</button>
            </div>
        )
    }
}

const Action = (props) =>{
    return(
        <div>
            <button disabled={!props.optionsAvailable} onClick={props.handleRandomPick}>What Should I do?</button>
        </div>
    );
};
```
