1. To create a component we must use the syntax below
```
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

class Options extends React.Component{
    render(){
        return (
            <div>
                <p>Options Component Here</p>
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

// if we want to use the components above we ill need to use them in a jsx template that is assigned to an html element root
const jsx = (
  <div>
      <Header/>
      <Action/>
      <Options/>
<AddOption/>
</div>
);
ReactDOM.render(jsx, document.getElementById('root'));
```
2. Below is the html
```
<div id="root"></div>
```
