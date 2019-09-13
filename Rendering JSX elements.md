1. To render a jsx element you will need to name an html element so it can be referenced wihtin the jsx.
```
<div id="root"></div>
```
2. Below is the jsx code. In order to have multiple html elements, they must be wrapped in a main div tag.
```
const decision = {
    title: 'Indecision App!',
    subtitle: 'Lorem ipsum dolor sit amet.'
};
const item = ['item one', 'item two'];
let template = (
    <div>
        <h1>{decision.title}</h1>
        <p>{decision.subtitle}</p>
        <ol>
            {item[0]}
        </ol>
    </div>
);

ReactDOM.render(template, document.getElementById('root'));
```
