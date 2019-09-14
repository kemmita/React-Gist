1. We cannot use things like if or for. We need to do that in a function and then call that funciton in the react template
look at part two below
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
2. We can use js operators in the react templates to check conditions
```
let template = (
    <div>
        <h1>{decision.title.length >= 20 ? 'Click to view title' : decision.title}</h1>
        <p>{chkSubtitle(decision.subtitle)}</p>
        <ol>
            <li>{item[0]}</li>
        </ol>
    </div>
);

ReactDOM.render(template, document.getElementById('root'));
```
3. if you simply want to check a boolean to see if you should display an element or not, use the React && trick!
```
let template = (
    <div>
        {decision.subtitle && <p>{decision.subtitle}</p>}
        <ol>
            <li>options array here</li>
        </ol>
    </div>
);

ReactDOM.render(template, document.getElementById('root'));
```
