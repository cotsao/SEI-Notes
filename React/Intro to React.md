## Navigation
-  [Setup](#Setup)
-  [Index.js](#Index.js)
-  [App.js](#App.js)
-  [Virtual DOM](#Virtual-DOM)
## Setup
[React Documentation](https://reactjs.org/)
- Install React developer tools if you don't have it already
    - React Developer Tools
- Create new React environment
    ```
    npx create-react-app my-app
    ```
- Change into the directory and start with
    ```
    npm start
    ```
    - Server is now running on http://localhost:3000

## Index.js
- Link app.js
    ```
    import App from './App';
    ```
- Render page
```
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```
    - Takes two parameters
        - component to render
        - element to render to
## App.js
- Where we define functions
    ```
    function App() {
        return (
            <div className="App">
            <h1>Some markup</h1>
            </div>
        );
    }
    ```
    - Functions like these are called React components
        - Returns JSX, which is similar to html
        - Returned JSX can only return one parent element at a time.
            - Put multiple elements in a div or something if you want to return multiple things
            - Can also use 'React fragments' which dont render as HTML
                ```
                function App() {
                    return (
                        <>
                            <h1>My React App</h1>
                            <p>This is a paragraph tag</p>
                        </>
                    );
                }
                ```
### Functional components
- Can create a function and export to app.js like so:
    ```
    function Person() {
        return (
            <div>
                <h2>Name: Flynn</h2>
                <p>Favorite Color: Blue</p>
            </div>
        );
    }        
    export default Person;
    ```
    - note the function is capitalized and the JSX is wrapped in parentheses
    - can call function in app.js like:
        ```
            import Person from './components/Person';
            import './App.css';

            function App() {
                return (
                    <div className="App">
                    <h1>My React App</h1>
                    <Person />
                    <Person />
                    <Person />
                    </div>
                );
            }
            export default App;
        ```
- note: export w/
```
export default App;
```
### Class based components
- import react and export component
    ```
    import React from 'react'
    export default Person
    ```
- Create class
    ```
    class Person extends React.Component{
        render(){
            return(

            )
        }
    }
    ```
- import into app.js
    ```
    import React, { Component } from 'react';
    import Person from './Person';
    ```
### Functional or Class?
1. Functional component
    - Are best for  components that are presentational in nature
        - plaintext etc
        - Not true so much in modern times
            - hooks used instead
2. Class-based Components
    - Are used when you need components to have more functionality
        - function()
        - lifecycle methods
        - fetch()
### React looping
map function
    - array.map
## Virtual DOM
Renders the page in a virtual DOM. This is nice because we can now update DOM elements as we need instead of refreshing the page or having hidden static elements.