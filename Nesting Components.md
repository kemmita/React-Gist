1. To nest a component, simply call on the component class name as an html tag in the parents jsx.
```js
class Header extends React.Component{
    render(){
        return (
            <div>
                <h1>Indecision App</h1>
                <h2>Put your life in the hands of a computer</h2>
            </div>
        );
    }
}

class Action extends React.Component{
    render(){
        return (
            <div>
                <button>What Should I do?</button>
            </div>
        )
    }
}

class Option extends React.Component{
    render(){
        return(
            <div>
                <p>Option Component Here</p>
            </div>
        )
    }
}
class Options extends React.Component{
    render(){
        return (
            <div>
                <p>Options Component Here</p>
                <Option/>
            </div>
        )
    }
}

class AddOption extends React.Component{
    render(){
        return (
            <div>
                <p>Add Option Component Here</p>
            </div>
        )
    }
}
// This parent component renders all of the above children components.
class IndecisionApp extends React.Component{
    render(){
        return(
            <div>
                <Header/>
                <Action/>
                <Options/>
                <AddOption/>
            </div>
        )
    }
}

ReactDOM.render(<IndecisionApp />, document.getElementById('root'));
```
