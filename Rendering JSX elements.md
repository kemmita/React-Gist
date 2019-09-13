1. To render a jsx element you will need to name an html element so it can be referenced wihtin the jsx.
```
<div id="root"></div>
```
2. Below is the jsx code. In order to have multiple html elements, they must be wrapped in a main div tag.
```
let template = (
    <div>
        <h1>Hey therse</h1>
        <p>this is jsx bitches</p>
    </div>
);

ReactDOM.render(template, document.getElementById('root'));
```
