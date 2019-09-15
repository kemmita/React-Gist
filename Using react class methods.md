```js
class Options extends React.Component{
    render(){
        return (
            <div>
                <button onClick={this.removeAll}>Remove All~!~</button>
                {this.props.options.map((option, i) => <Option key={option + i} option={option}/>)}
            </div>
        )
    }

    removeAll(){
        console.log('Removed All!');
    }
}
```
