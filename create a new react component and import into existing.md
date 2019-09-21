1. We want to import a header component into the main App component. first lets create the new component in its own file
```js
import React, { Component } from 'react'

export class Navbar extends Component {
    render() {
        return (
            <div>
                <h1>Navbar</h1>
            </div>
        );
    }
}

export default Navbar
```
2. Now if we want to use the component above, we will need to import it.
```js
import React from 'react';
import './App.css';
import Navbar from './components/layout/Navbar'

class App extends React.Component {
  render() {
    return (
      <div className='App'>
        <Navbar />
      </div>
    );
  }
}

export default App;
```
3. Now the main component is sent to index.js which hooks it up to the correct html element to render.
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```
4. html
```
<div id="root"></div>
```
