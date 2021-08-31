## Navigation
-  [Setup](#Setup)
-  [React Router](#React-Router)
-  [Components](#Components)
    - [Components and Props](#Components-and-Props)
    - [Class Components](#Class-Components)
    - [Function Components](#Function-Components)
    - [Hooks](#Hooks)
    - [Organization](#Organization)
-  [AJAX/API](#AJAX/API)
-  [CRUD](#CRUD)
    - [Create](#Create)
    - [Delete](#Delete)
    - [Update/Edit](#Update/Edit)
- [File tree/Hierarchy/Summary](#File-tree/Hierachy)
## Setup

Create React app
```
npx create-react-app my-app-name
```

Install react Express
```
npm i react-router-dom
```
Start React
```
npm start
```
## React Router

React Router allows us to simulate different routes in our app.

Import it into index.js
```
// imports it under the variable name "Router"
import { BrowserRouter as Router } from 'react-router-dom';
```
Wrap App in the React Router.
```
// stil index.js
ReactDOM.render(
  <React.StrictMode>
    <Router >
      <App />
    </Router>
  </React.StrictMode>,
  document.getElementById('root')
);
```
Make routes inside of our root page. (app.js)

To do so, we use the Switch component to wrap the pages that we want to be able to switch between, so import Switch.
```
// import both Switch and Route
import { Switch, Route } from 'react-router-dom';
```
Now we can wrap our routes in the Switch component
```
<div className="container">
    <Header/>
    <Switch>
        // <Route path='route' compoment={component to render}>
        <!-- It will look for partial matches as well, so our home route must have 'exact' to ensure it does not render when we don't want it to -->
        <Route exact path='/' component={ Home }/>
        <Route path='/todos' component={ TodosContainer }/>
    </Switch>
</div>
```
### Linking to routes
Note that any component we want to be rendered throughout all pages should be outside of the Switch component. Import Link and set the routes.
```
import { Link } from 'react-router-dom';

const Header = () => {
  return (
    <header>
      <h1>ToDo</h1>
      <nav> 
        <Link to='/'>Home</Link>
        <Link to='/todos'>Todos</Link>
      </nav>
    </header>
  );
};

export default Header;
```
## Components
Create your components in a separate directory. Perhaps in a components folder.
Ensure that you are exporting the component and importing it in the parent.
```
// import (in parent)
import Home from './components/Home';
import TodosContainer from './containers/TodosContainer';
// export (in the component)
export default Home
export default TodosContainer
```
### Components and Props
While not used in modern React, there are plenty of code bases that use the class component/function component paradigm. In this paradigm, Class components are used when there is data that is changed (state) and function components are for static components.
### Class Components
TODO: state and lifecycle
```
// Basic setup
import React from 'react'
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
export default Welcome
```
### Function Components
```
// Basic setup
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
export default Welcome
```
### Hooks
Under Construction
### Organization
- route config

While it is fine to have our routes organized in our app.js, for larger projects it may help to refactor our code slightly. 

We can make a config to control all of our routes.

``` src/config/routes.js ```

Move our routes and our imports into this js file and export it like so
```
import { Switch, Route } from 'react-router-dom';
import Home from '../components/Home';
import TodosContainer from '../containers/TodosContainer';

export default (
  <Switch>
    <Route exact path='/' component={ Home }/>
    <Route path='/todos' component={ TodosContainer }/>
  </Switch>
);
```
Now import it into our app
```
import React from 'react';
import Header from './components/Header';
import routes from './config/routes';
function App() {
  return (
    <div className="container">
      <Header/>
      { routes }
    </div>
  );
};
```
## AJAX/API
Use axios to make fetch calls. Install it like so:
```
npm install axios
```
In this example we will use axios to fetch data and store it into a models directory.
``` src/models/Todo.js ```
In this file we will make create a model with a method to make an axios call.
```

import axios from 'axios';
const endPoint = `https://super-crud.herokuapp.com/todos`;

class TodoModel {
// create a class method with static, call it 'all' and then have it make a get.
  static all = () => {
    let request = axios.get(endPoint);
    return request;
  };
};

export default TodoModel;
```
Now in Todo's container import the TodoModel and make the api call
```
import React, { Component } from 'react';
import TodoModel from '../models/Todo';

class TodosContainer extends Component {
  render() {
      /* the axios get method is used by TodoModel, but this gives us access to the response here in TodosContainer */
    TodoModel.all().then((res) => {
      console.log(res);
    });
    return (
      <div className='todosContainer'>
        <h2>This is a todos container</h2>
      </div>
    );
  };
};

export default TodosContainer;
```
### Rendering from the model
Create a component to render the data.

```src/components/Todo.jsx```

```
import { Component } from 'react';

class Todo extends Component {
  render() {
    return (
        /* We are passing data through props, so the parent component (todosContainer) will have to pass it the necessary info */
      <li data-todos-index={this.props.todo._id}>
        <span className="todo-item">{this.props.todo.body}</span>
      </li> 
    );
  };
};

export default Todo;
```
Create a component to render all of the data at once

``` src/components/Todos.jsx```
```
import React from 'react';
import Todo from './Todo';
// this is a component that takes an array of data and maps it to an ul 
const Todos = (props) => {
  let todos = props.todos.map((todo) => {
    return (
      <Todo
        key={todo._id}
        todo={todo} />
    );
  });

  return (
    <ul>
      {todos}
    </ul>
  );
};

export default Todos;
```
Now rendering it in the parent container.
```
import { Component } from 'react';
import TodoModel from '../models/Todo';
import Todos from '../components/Todos';

class TodosContainer extends Component {
    // Use state to store array of data
  state = {
    todos: []
  }
    /* DidMount() runs upon the creation of the component, ensuring that the api call is done immediately when needed */
  componentDidMount() {
    this.fetchData();
  };
  // Runs our fetch call and adds it to our state.
  fetchData = () => {
    TodoModel.all().then((res) => {
      this.setState ({
        todos: res.data.todos,
      });
    });
  };
  
  render() {
    return (
      <div className="todosComponent">
        <Todos
        // passes our state down as props
          todos={this.state.todos} />
      </div>
    );
  };
};

export default TodosContainer;
```



### Other Axios stuff
// todo await

On axios's 'all' method. Axios allows the user to make multiple requests in tandem with the ```axios.all()``` method. This will then return a single object with all the returned data.
```
// execute simultaneous requests 
axios.all([
  axios.get('https://api.github.com/users/mapbox'),
  axios.get('https://api.github.com/users/phantomjs')
])
.then(responseArr => {
  //this will be executed only when all requests are complete
  console.log('Date created: ', responseArr[0].data.created_at);
  console.log('Date created: ', responseArr[1].data.created_at);
});

// logs:
// => Date created:  2011-02-04T19:02:13Z
// => Date created:  2017-04-03T17:25:46Z
```
If you wanted the the responses to be in different variables, you can use the ```axios.spread()``` method.
```
axios.all([
  axios.get('https://api.github.com/users/mapbox'),
  axios.get('https://api.github.com/users/phantomjs')
])
.then(axios.spread((user1, user2) => {
  console.log('Date created: ', user1.data.created_at);
  console.log('Date created: ', user2.data.created_at);
}));

// logs:
// => Date created:  2011-02-04T19:02:13Z
// => Date created:  2017-04-03T17:25:46Z
```
## Crud
### Create
Create a component to allow creation of data.

React has a very specific way to do forms, denoted 'controlled forms'

``` src/components/CreateTodoForm.jsx ```
```
import { Component } from 'react';

class CreateTodoForm extends Component {
    // create empty todo state
  state = {
    todo: '',
  };
  /* 
  called on form input changing
  sets state to the value of input
  finally the "value={this.state.todo}" in the input is updated to reflect state
  */
  onInputChange = (event) => {
    this.setState({
      todo: event.target.value,
    });
  };
  
  onFormSubmit = (event) => {
      // always include the preventDefault for form submission
    event.preventDefault();
    // create temp variable holding the value of state
    let todo = this.state.todo;
    // call the createTodo method in our props
    this.props.createTodo(todo);
    // reset our state
    this.setState({
      todo: '',
    });
  };
  
  render() {
    return (
      <div>
        <form onSubmit={this.onFormSubmit} id="taskForm">
          <input  
            onChange={this.onInputChange} 
            type="text" id="newItemDescription" 
            placeholder="What do you need to do?" 
            value={this.state.todo}
          />
          <button type="submit" id="addTask" className="btn">Add Todo</button>
        </form>
      </div>
    );
  };
};

export default CreateTodoForm;
```

Note that since we haven't created the createTodo() method yet, we will have to go back to TodoContainer and make it there. By creating a function inside of the parent component, this allows us to pass state from the child to the parent

Import the todoForm
```
import CreateTodoForm from '../components/CreateTodoForm';
```
instatiate the createTodo method
```
createTodo = (todo) => {
    let newTodo = {
        body: todo,
        completed: false,
    };
    // call the create method in our TodoModel
    TodoModel.create(newTodo).then((res) => {
        // Create temp variable with our state
        let todos = this.state.todos;
        // add our data from the reponse
        todos.push(res.data);
        //update state
        this.setState({ todos: todos });
    });
};
```
Update the render to include TodoForm, and pass our createTodo down as props
```
render() {
  return (
    <div className="todosComponent">
      <CreateTodoForm
        createTodo={this.createTodo} />

      <Todos
        todos={this.state.todos} />
    </div>
  );
};
```
Now we note that our TodoModel doesn't actually have the create() method, so we add it now.
Add a method to the model that does the following
```
static create = (todo) => {
  let request = axios.post(endPoint, todo);
  return request;
};
```
axios.post() accepts two parameters. The endpoint to post data to and the data to post.
### Delete
Update the todo component to allow for deletion. Because each todo is created dynamically with components, we can target each one more easily
```
class Todo extends Component {
  // delete handler unfunction
  deleteClickedTodo = () => {
  // calls the deleteTodo in props.
    this.props.deleteTodo(this.props.todo);
  };
  
  render() {
    return (
      <li data-todos-index={this.props.todo._id}>
        <span className="todo-item">{this.props.todo.body}</span>
        <!-- Create a span element that calls delete on click -->
        <span
          className='remove'
          onClick={this.deleteClickedTodo}>
          Remove
        </span>
      </li> 
    );
  };
};
```
In our create all component 'Todos', we now need to pass down that 'delete' props function that we passed down. This ensures that all components we generate will have that function in their props.
```
let todos = props.todos.map((todo) => {
  return (
    <Todo
      key={todo._id}
      todo={todo}
      deleteTodo={props.deleteTodo}/>
  );
});
```
Now we need to create the function itself in the parent component. In TodosContainer, add the delete method and pass it down as props for the Todos component
```
deleteTodo = (todo) => {
  // calling another method in our TodoModel, which will need to be created.
    TodoModel.delete(todo).then((res) => {
      /* creates a temp variable that takes the form of an array with our deleted todo filtered out*/
        let todos = this.state.todos.filter((todo) => {
          /*
          This uses the callback function of the filter method. It checks to see if the id todo at the filter index matches the response's (deleted item)id. 
          If it doesn't, then it is added to the filtered array. If it matches it is not included.
           */
          return todo._id !== res.data._id;
        });
        // updates state with this array
        this.setState({todos});
    });
};
```
Create our delete method in our Model
```
  static delete = (todo) => {
    /* 
    Because axios uses the REST paradigm, we can delete data based off the endpoint
    endPoint/id, which we specify below
    */
    let request = axios.delete(`${endPoint}/${todo._id}`);
    return request;
  };
```

### Update/Edit
Add a update method to TodoContainer and pass it down as props
```
  updateTodo = todo => {
    const isUpdatedTodo = t => {
        return t._id === todo._id;
    };
  <Todos
    todos={ this.state.todos }
    updateTodo={ this.updateTodo } 
    deleteTodo={ this.deleteTodo }
  />
```
update the Todos components
```
//....
 let todos = props.todos.map((todo) => {
      return (
        <Todo
          key={todo._id}
          todo={todo}
          deleteTodo={props.deleteTodo}
          updateTodo={props.updateTodo} 
          />
      );
    });
//...
```
The Todo component also needs updating. It needs the update function as well as a toggleable display for the edit form

Add state for the hidden form, a function to toggle visibility and a update component form.

```
  state = {
      formStyle: {
        display: 'none',
      },
  };

  toggleBodyForm = () => {
    this.state.formStyle.display === 'block'
    ? this.setState({ formStyle: {display: 'none'} })
    : this.setState({ formStyle: {display:'block'} });
  };
```
The component call can take the form:
```
<TodoForm 
  todo={this.props.todo}
  style={this.state.formStyle}
  autoFocus={true}
  buttonName="Update Todo!"
  updateTodo={this.props.updateTodo}
  toggleBodyForm={this.toggleBodyForm} 
/>
```
and don't forget to import and export it.

The Todo form itself looks like below.

It pretty much takes the same form as before, but it will toggle visibility on submit
```
//TodoForm.jsx
import { Component } from 'react';

class TodoForm extends Component {
  state = {
    todo: '',
  };

  onChange = (event) => {
    this.setState({
      todo: event.target.value,
    });
  };

  onSubmit = (event) => {
    event.preventDefault();
    const todo = this.props.todo;
    todo.body = this.state.todo;
    this.props.updateTodo(todo);
    this.setState({ todo: '' });
    this.props.toggleBodyForm();
  };

  render() {
    return (
      <div style={this.props.style} className='todoForm'>
        <form onSubmit={ this.onSubmit }>
          <input
            autoFocus={this.props.autoFocus}
            onChange={ this.onChange }
            placeholder='Write a todo here ...'
            type='text'
            value={this.state.todo} />
          <button type='submit'>Save</button>
        </form>
      </div>
    );
  };
};

export default TodoForm;
```
Finally, update our model with the update method.
```
  static update = (todo) => {
    let request = axios.put(`${endPoint}/${todo._id}`, todo);
    return request;
  };
```
## File Tree/Hierachy
```
 src
  ├─
  ├── App.css
  ├── App.js
  ├── App.test.js
  ├── components
  │   ├── CreateTodoForm.jsx
  │   ├── Header.jsx
  │   ├── Home.jsx
  │   ├── Todo.jsx
  │   ├── TodoForm.jsx
  │   └── Todos.jsx
  ├── config
  │   └── routes.js
  ├── containers
  │   └── TodosContainer.jsx
  ├── images
  │   ├── grass.jpg
  │   └── grass.jpg:Zone.Identifier
  ├── index.css
  ├── index.js
  └─── models
       └── Todo.js
```

![component hierachy](/images/react/todoapp-tree.png)

Our root has a very simple hierachy as so.



### TodosContainer 
TodosContainer is where most of the work is done.
![Todo Container](/images/react/component-hierachy-tree.png)

- Contains state with the array of Todos that is passed down as props to Todos
- Makes an api call on component creation with componentDidMount() and sets it to the state
- contains create method and passes it down as props to CreateTodoForm
- contains update and remove methods and passes them down as props to Todos
### TodoModel
- TodoModel ```/src/config/Todo.js```
  - Class that contains the axios methods and the endpoint
    - get/post/delete/put = index/create/delete/update
### CreateTodoForm
- CreateTodoForm ```/src/components/CreateTodoForm```
  - Receives ```createTodo()``` as props
  - Form to create new Todo
  - Contains state that has the value of the form input
    - onChange in input field, calls function to set state to input field, and sets input field to match
  - OnSubmit, calls the createTodo function that was passed down as props
### Todos
- Todos.jsx ```/src/components/Todos```
  - Receives the ```todo``` array, ```deleteTodo()``` and ```updateTodo()``` as props
  - Maps the received props to individual Todo components.
    - Creates an unordered list with Todos and passes down a key, the data from one element of the received props, and both the update and the delete methods.
### Todo
- Todo.jsx ```/src/components/Todo```
  - Receives the ```todo``` array, ```deleteTodo()``` and ```updateTodo()``` as props
  - Contains formStyle as state
    - css style to toggle display
    - contains method to toggle display between 'none' and 'block'
  - Creates a list element that contains
    - unique item index
    - todo data
    - delete button
    - edit button
      - edit button calls toggle display function
      - display contains TodoForm
        - passes the ```updateTodo()``` and ```toggleBodyForm()``` down as props
### TodoForm
- TodoForm.jsx ```/src/components/TodoForm```
  - Receives one ```todo```, ```toggleBodyForm()``` and ```updateTodo()``` as props
  - Form to update Todo
    - Contains state that has the value of the form input
    - onChange in input field, calls function to set state to input field, and sets input field to match
   - OnSubmit
    - creates temp variable and set it to ```props.todo``` 
      - change the temp variable body to match value from form
    - calls the updateTodo function that was passed down as props
    - resets local state
    - calls ```toggleBodyForm()``` to toggle visibility

  
