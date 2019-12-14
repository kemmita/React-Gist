1. Run command 
```
npm i react-final-form final-form
and then to handle date inputs
npm i react-widgets react-widgets-date-fns
after the command above you will see this message
"react-widgets-date-fns@4.0.26 requires a peer of date-fns@2.0.0-alpha.13 but none is installed. You must install peer dependencies yourself."
run command
npm i date-fns@2.0.0-alpha.13
npm install @types/react-widgets
```
2. Below is the index.tsx file, we will bring in date-fns imports via an import
```ts
//here is the import
import 'react-widgets/dist/css/react-widgets.css'
import dateFnsLocalizer from 'react-widgets-date-fns';

dateFnsLocalizer();

export const history = createBrowserHistory();

ReactDOM.render(<Router history={history}> <ScrollToTop>  <App /> </ScrollToTop> </Router>, document.getElementById('root'));

serviceWorker.unregister();
```
3. Create a new decleration file titled react-wdgets-date-fns.d.ts in a new driectory titled "typings-custom"
```ts
declare module 'react-widgets-date-fns';
```
4. in tsconfig.json
```json
  "include": [
    "src",
    "typings-custom"
    if that does not work use
    ./typings-custom/**/*.ts
  ]
```
5. 
      
