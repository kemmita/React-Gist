1. Again we need to rerender the jsx elements with a component to bind and rerender the data correctly.
```
const onFormSubmit = (e) => {
    e.preventDefault();
    const option = e.target.elements.option.value;
    if (option){
       app.options.push(option);
       e.target.elements.option.value = '';
       renderApp();
    }
};
const renderApp = () => {
    const template = (
        <div>
            <h1>{app.title}</h1>
            <form onSubmit={onFormSubmit}>
                <input type="text" name="option"/>
                <button>Add Option</button>
            </form>
        </div>
    );
    ReactDOM.render(template, document.getElementById('root'));
};
renderApp();
```
