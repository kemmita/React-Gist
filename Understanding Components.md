1. You will have an html file such as the one below index.html
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="logo192.png" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div style="display: flex; justify-content:space-evenly; align-content: center; align-items: center;">
    <div id="root"></div>
    <div id="trythis"></div>
    </div>
  </body>
</html>
```
2. There will be a main index.js file to resgister your components.
```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import Second from './Second';
//below we register two components the components will be assigned to an html element that can be found in the index.html file above.
ReactDOM.render(<App />, document.getElementById('root'));
ReactDOM.render(<Second />, document.getElementById('trythis'));

serviceWorker.unregister();
```
3. Now in the component file iotself App.js will contain the html and js functionlity
```js
import React from 'react';
import './App.css';

const num = 8;
function square(num){
  return num*num;
}

function App() {
  return (
    <div className="App">
      {`Num squared is ${square(num)}`}
    </div>
  );
}

export default App;
```
4. There is typically a css file create seperatly for the component
```
//App.css
mainExample{}
```
