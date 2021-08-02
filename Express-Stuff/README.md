## Navigation:

-  [Create New Server](#Create-New-Server)
-  [Setup](#Setup)

-  [MVC Practicies](#MVC-Practices)


-  [Restful Routes](#Restful-routes)
-  [Optional](#Optional)
-  [Linking](#Linking)
## Create-New-Server
1. npm init
2. npm install express
3. npm install ejs
4. npm install method-override
5. npm install
    - installs dependencies in package.json
## Setup
- Server.js
    - required modules
        ```
        const express = require('express')        
        const methodOverride = require('method-override');
        const rowdy = require('rowdy-logger') (optional rowdy)
        ```
    - database and models
         ```
        const varName = require('./models/varLocation.js')
        const varController = require('./controllers/varController.js');
         ```
    - variables
        ```
        const app = express()
        const port = 4000
        const rowdyResults = rowdy.begin(app)
        ```
    - middleware
        ```
        app.set('view engine', 'ejs');
        app.use(methodOverride('_method'));
        app.use(express.urlencoded({ extended: false })) 
        app.use('/url', varController);
        ```
    - start server
        ```
        app.listen(port, () => {
            console.log(`Example app listening at http://localhost:${port}`);
            rowdyResults.print()
        });
        ```

- varController.js
    - required modules
        ```
        const express = require('express');
        const router = express.Router();
        ```
    - database
        ```
        const varName = require('../models/varLocation.js');
        ```
    - export
        ```
        module.exports = router;
        ```
- database
    - export database
        ```
        module.exports = varName
        ```

## MVC-Practices

1. Make a separate directory for models, views, and controller
    -models = data, views = pages, controller = organization for server.js
2. Models
    - Create file for data (e.g. data.js)
    - [Export the data then link it to page that needs it](#Linking)
3. Views
    - Create new .ejs under views directory for each page needed
    - [Link to controller](#Linking)
    - Display page with render, and pass required data
        - e.g. res.render('show.ejs', { exportVarName: importVar })
4. Controller
    - Create file for controller (e.g. controller.js)
    - [Link](#Linking)
        - data
        - server
    - setup
    - Routes will be placed here, e.g. our restful routes


## Restful-routes
Restful routes are a convention for certain URL pathways to do certain things.
| **URL** | **HTTP Verb** |  **Action** | **Description** |
|------------|-------------|------------|------------|
| /url/         | GET       | [index](#index)  | Retrieve many/all items
| /url/new         | GET       | [new](#new)   | Retrieve a form that can be used to create a new fruit
| /url          | POST      | [create](#create)   | Send data to create a new fruit
| /url/:id      | GET       | [show](#show)       | Retrieve one item
| /url/:id/edit | GET       | [edit](#edit)       | Retrieve a item and a prepopulated form that can be used to edit that item
| /url/:id      | PATCH/PUT | [update](#update)    | Send data to update an existing item
| /url/:id      | DELETE    | [destroy](#destroy)  | Remove a item
### index
- create the route
    ```
    router.get('/url', (req, res) => {
        res.render('index.ejs', { exportVarName: importVarName });
    })
    ```
    - export the whole object
        - object will be accessed in index.ejs with 'exportVarName'
    - render the page
- In the .ejs, use a for loop to access all the desired data
- Link to different Routes
    - Create - link to /url/new with a tag
    - Loop through data with for loop
    - Delete - add form to delete 
        ```
         <form action="/url/<%= i %>?_method=DELETE" method="POST">
            <input type="submit" value="Delete item">
        </form>
        ```
    - Edit
        - link to edit page with a tag
### new
- create the route 
    ```
    router.get('/fruits/new', (req, res) => {
        res.render('new.ejs')
    })
    ```
- in the .ejs, create a form to pass data
    ```
        <form action="/index_url" method="POST">
            <label for="var">Name:</label> // match label to input name
            <input name="var" type="text"> // match name to obj variable
            <br>
            <input type="submit" value="Create Item">
            <label for="checkbox_var">value</label>
            <input name="checkbox_var" type="checkbox">
            <br>
        </form>
    ```

    
### create
- create route
    ```
    router.post('/', (req, res) => {  
        varName.push(req.body)
        res.redirect('/fruits') 
    })
    ```
    - pushes data from form into varName and redirects to index
    - may have to modify data from req.body (e.g. checkboxes)
        ```
            if (req.body.bool === 'on') {
                req.body.bool = true;
            } else {
                req.body.bool = false;
            }
        ```
    


### show
- create route
    ```
    router.get('/url/:itemIndex', (req, res) => {
        res.render('show.ejs', {
            oneItem: varName[req.params.itenIndex],
            index: req.params.capyIndex
    })
    ```
- create the .ejs and access the specific object with the index passed
    ```
        <p>The Capy at index <%=index%> is: oneItem.name</p>
    ```
### edit
- create route
    ```
        router.get('/:itemIndex/edit', (req, res) => {
            res.render('edit.ejs', {
                oneItem: varName[req.params.itemIndex],
                index: req.params.itemIndex
            });
        });
    ```
    - serve a form to submit data
        - pass index, and obj at the index
- create .ejs page
    - modify data with form
        ```
            <form action="/capy/<%= index %>?_method=PUT" method="POST">
                <label for="name">Name:</label>
                <input name="name" type="text" value="<%= varName.name %>">
                <br>        
                <label for="cute">cute?</label>
                <input name="cute" type="checkbox" <%= varName.value ? "checked" : "" %>>
                <br>
                <input type="submit" value="Update Item">
            </form>
        ```
        - checkbox contains ternary operator to set checkbox to checked or blank
- need a route to handle the put action at update
### update
- update route
    ```
        router.put('/:itemIndex', (req, res) => {
            varName[req.params.itemIndex] = req.body;
            res.redirect('/url/' + req.params.itemIndex);
        })
    ```
- may need to modify data again
    - checkbox
        ```
            if (req.body.checkbox === 'on') {
                req.body.checkbox = true;
            } else {
                req.body.checkbox = false;
            }
        ```
- redirects to the page at the index
### destroy
- route
    ```
    router.delete('/:itemIndex', (req, res) => {
        const itemIndex = req.params.itemIndex; //get index
        varName.splice(itemIndex, 1); //splice array at index
        res.redirect('/url');}) //redirect
    ```

## Optional
- npm install --save rowdy-logger
    - var rowdy = require('rowdy-logger')
    - var rowdyResults = rowdy.begin(app)
    - rowdyResults.print()

## Linking 