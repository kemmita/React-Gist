1. We will need to run two functions passed as props from the parent component on page load. In class components, we could use onComponentDidMount, but by using hooks and fucntional components, we can use a react function called "useEffect"
```js
import React, { useEffect } from 'react';


const User = (props) => {

    useEffect(() => {
        props.getUser(props.match.params.login);
        props.getUserRepos(props.match.params.login);
        // eslint-disable-next-line
    }, []);
};
```
