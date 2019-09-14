1. below we use the js map function to loop through our options array located in our app object. Please not the key that must be defined in the element when looping through the array.
```
let app = {
    title: 'decision title!',
    subtitle: 'subtitle one here',
    options: ['ones', 'two']
};

    const template = (
        <div>
            <h1>{app.title}</h1>
            <ul>
                {app.options.map((option, i) => {return <li key={option + i}>{option}</li>;})}
            </ul>
        </div>
    );
    ReactDOM.render(template, document.getElementById('root'));
```
