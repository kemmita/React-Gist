1. We cannot use things like if or for. We need to do that in a function and then call that funciton in the react template
```
// JSX
let decision = {
    title: 'Indecision App!',
    subtitle: ''
};
let item = ['item one', 'item two'];

const chkSubtitle = (subtitle) => {
    return !subtitle || /^\s*$/.test(subtitle) ? 'No subtitle provided' : subtitle;
};

let template = (
    <div>
        <h1>{decision.title}</h1>
        <p>{chkSubtitle(decision.subtitle)}</p>
        <ol>
            <li>{item[0]}</li>
        </ol>
    </div>
);

ReactDOM.render(template, document.getElementById('root'));
```
