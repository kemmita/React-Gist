1. To render a jsx element you will need to name an html element so it can be referenced wihtin the jsx.
```
<div id="root"></div>
```
2. Below is the jsx code
```
var template = <p>this is jsx bitches</p>;

ReactDOM.render(template, document.getElementById('root'));
```
