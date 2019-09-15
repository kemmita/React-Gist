1. Below we make an http request that returns an array of strings.
```js
class IndecisionApp extends React.Component{
    render(){
        const appCall = {
            options: ["Option One", "Option Two"]
        };
        return(
            <div>
                <Action/>
                <Options options={appCall.options}/>
                <AddOption/>
            </div>
        )
    }
}
```
2. As you can see above we pass the arrays to the child class Options.
```js
class Options extends React.Component{
    render(){
        return (
            <div>
                {this.props.options.map((option, i) => <Option key={option + i} option={option}/>)}
            </div>
        )
    }
}
```
3. Above you can see the child class Options calls it's child component Option for each itteration of the array.
```js
class Option extends React.Component{
    render(){
        return(
            <p>{this.props.option}</p>
        )
    }
}
```
