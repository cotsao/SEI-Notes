## Navigation:

-  [Create New Server](#Create-New-Server)
-  [Setup](#Setup)
-  [MVC Practicies](#MVC-Practices)
-  [Restful Routes](#Restful-routes)
-  [Optional](#Optional)
-  [Linking](#Linking)
## Create-New-Server
1. npm init
    - create new npm package
2. npm install express
    - install express
3. npm install ejs
    - install ejs
4. npm install method-override
    - install necessary package to use methods that aren't 'get' and 'post'
5. npm install 
    - installs dependencies in package.json
    - not needed if creating new server from scratch
## Setup
- Server.js
    - required modules
        ```
        const express = require('express')         
        const methodOverride = require('method-override');
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
        ```
    - middleware
        ```
        app.set('view engine', 'ejs'); // allow usage of ejs
        app.use(methodOverride('_method')); // allow usage of non-post/get
        app.use(express.urlencoded({ extended: false })) // allow usage of forms
        app.use('/url', varController); // link to controller
        ```
    - start server
        ```
        app.listen(port, () => {
            console.log(`Example app listening at http://localhost:${port}`);
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

Make a separate directory for models, views, and controller
- models = data
- views = pages
- controller = organization for server.js



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
- [back to table](#Restful-routes)
### new
- create the route 
    ```
    router.get('/url/new', (req, res) => {
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
- [back to table](#Restful-routes)

    
### create
- create route
    ```
    router.post('/', (req, res) => {  
        varName.push(req.body)
        res.redirect('/url') 
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
            oneItem: varName[req.params.itemIndex],
            index: req.params.itemIndex
    })
    ```
- create the .ejs and access the specific object with the index passed
    ```
        <p>The item at index <%=index%> is: oneItem.name</p>
    ```
- [back to table](#Restful-routes)
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
                <label for="checkbox_var">cute?</label>
                <input name="checkbox_var" type="checkbox" <%= varName.value ? "checked" : "" %>>
                <br>
                <input type="submit" value="Update Item">
            </form>
        ```
        - checkbox contains ternary operator to set checkbox to checked or blank
- need a route to handle the put action at update
- [back to table](#Restful-routes)
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
- [back to table](#Restful-routes)
### destroy
- route
    ```
    router.delete('/:itemIndex', (req, res) => {
        const itemIndex = req.params.itemIndex; //get index
        varName.splice(itemIndex, 1); //splice array at index
        res.redirect('/url');}) //redirect
    ```
- [back to table](#Restful-routes)

## Optional
- Rowdy Logger to view Routes
    - install rowdy in main directory
        ```
        npm install --save rowdy-logger
        ```
    - inside server.js
        ```
            var rowdy = require('rowdy-logger') //modules
            var rowdyResults = rowdy.begin(app)
        ```
    - inside listen method
        ```
         rowdyResults.print()
        ```
- Linking CSS
    - copy pasted instructions from search
        - Create a new folder named 'public' if none exists. 
        - Create a new folder named 'css' under the newly created 'public' folder
        - create your css file under the public/css path
        - On your html link css i.e <link rel="stylesheet" type="text/css" href="/css/style.css">
        // note the href uses a slash(/) before and you do not need to include the 'public'
        - On your app.js include : app.use(express.static('public'));
    - relevant code
        - middleware
            ```
            app.use(express.static(__dirname + '/public'));
            ```
        - in html(.ejs) files
            ```
            <link rel="stylesheet" type="text/css" href="css/style.css" />
            ```

## Linking 
In general linking is done by exporting the target file and then using a require statement in the desired location.