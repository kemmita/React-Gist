1. Withot the constructor syntax below, we would not have access to this.props within our methods.
```js
class Options extends React.Component{
    constructor(props){
        super(props);
        this.removeAll = this.removeAll.bind(this);
    }
    render(){
        return (
            <div>
                <button onClick={this.removeAll}>Remove All~!~</button>
            </div>
        )
    }

    removeAll(){
        console.log(this.props.options);
    }
}
```
