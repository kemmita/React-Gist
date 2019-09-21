1. First we will pass state altering methods from the parent component to the child component.
```
class IndecisionApp extends React.Component{
// in our constructor we will need to bind "this" to our handleDeleteOptions method as it will be altering state.
// next we will need to declare our initial state which is an aoptions array that holds two elements.
    constructor(props){
        super(props);
        this.handleDeleteOptions = this.handleDeleteOptions.bind(this);
        this.state = {
            options: ['one', 'two']
        };
    }
    
//below we define the handleDeleteOptions method that was delcared in the constructor. We then use "setState" to return the
//desired state we want, which will end up being an empty array to visualize the options being removed.
    handleDeleteOptions(){
        this.setState(() =>{
            return{
                options: []
            }
        });
    }
 
 //now in our render method of the parent component, it is the goal to take the handleDeleteOptions method we delcared and
 //defined here in the parent component and pass it it a child component so that it way trigger it using an OnClick action.
    render(){
        return(
            <div>
                <Options options={this.state.options} handleDeleteOptions={this.handleDeleteOptions}/>
            </div>
        )
    }
}
```
2. Now in the child component, all we need to is to delcare a constructor and use super(props) t0 gain access to the method passed.
```
class Options extends React.Component{
    constructor(props){
        super(props);
    }
    render(){
        return (
            <div>
                <button disabled={this.props.options <= 0} onClick={this.props.handleDeleteOptions}>Remove All~!~</button>
            </div>
        )
    }
}
```
3. Now lets learn how to alter the state of a parent element using a child component.
```
class IndecisionApp extends React.Component{
//below we use the constructor to bind the addOptions method to "this" giving it the power to alter state
//next we initialize the state of the options array to have no elements.
   constructor(props){
        super(props);
        this.handleAddOption = this.handleAddOption.bind(this);
        this.state = {
            options: []
        };
    }
//this method will accept an argument that will be added to the array using state, using setState and altering the prevState
    handleAddOption(option){
        this.setState((prevState) =>{
           return{
               options: prevState.options.concat(option)
           }
        });
    }

    render(){
    //below we pass the function as a prop 
        return(
            <div>
                <AddOption handleAddOption={this.handleAddOption}/>
            </div>
        )
    }
}

class AddOption extends React.Component{
  //we will need to alter state using a method defined an created here in the child component, so we will need to ensure to 
  //bind "this" to the method addForm
    constructor(props){
        super(props);
        this.addForm = this.addForm.bind(this);
    }
    render(){
        return (
            <div>
            //as you can see we do not call on the parent method passed as a prop on the onsubmit method here, but use the
            //method defined here in the child component for on submit
                <form onSubmit={this.addForm}>
                    <input type="text" name="option"/>
                    <button>Add Option +</button>
                </form>
            </div>
        )
    }
    //when using onSubmit with a form in js you will gain access to e which will contain the value of the input field
    //submitted.
    addForm(e){
        e.preventDefault();
        let option = e.target.elements.option.value.trim();
        let clear = e.target.elements.option.value = '';
        if (option){
        //here is where we call on the parent method which will in the end alter the current state of options.
        //notice how we pass the option submitted by the form to the parent method
            this.props.handleAddOption(option);
            return clear;
        }
    }
}
```
