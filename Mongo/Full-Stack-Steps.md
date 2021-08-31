## Navigation:
- [Installations and Setup](#Installations-and-Setup)
- [File-Structure](#File-Structure)
- [Server-Linkage](#Server-Linkage)
- [server.js](#server.js)
- [Controllers](#Controllers)
- [Models](#Models)
- [index.js](#index.js)
- [Model-Schema](#Model-Schema)
- [Views](#Views)
- [Routes](#Routes)
- [Restful-Routes](#Restful-Routes)

## Installations-and-Setup
1. create new npm package
    ```
    npm init
    ```
 
2. install express
    ```
    npm install express
    ```
3. install ejs
    ```
    npm install ejs
    ```
4. install necessary package to use methods that aren't 'get' and 'post'
    ```
    npm install method-override
    ```
5. install mongoose
    ```
    npm install mongoose
    ```
6. Create .gitignore 
    ```
    touch .gitignore
    ```
    - add node_modules and whatever else 
7. start mongo 
    ```
    sudo service mongodb start
    ```
8. create git repository
    ```
    git init
    ```
9. Optional Rowdy
    ```
    npm install --save rowdy-logger
    ```
## File-Structure
```
├── controllers
│   └── fruitsController.js // handles routing for the url 
├── models
│   ├── Fruit.js // creates Schema and exports model, creates documents inside collection
│   └── index.js // connects mongo to everything else
├── package-lock.json
├── package.json
├── server.js  // connect to localhost (connected to index.js/controller)
└── views
    └── fruits // contains views used by fruitsController
        ├── edit.ejs
        ├── index.ejs
        ├── new.ejs
        └── show.ejs
```
## Server-Linkage
![server linking](/images/mongo/server_linking.png)  
Server.js runs the server itself. It contains important things such as most of the required modules and middleware. It is linked to your controllers to do most of the work.


Controllers handle what to do with the routing to the url path you specify in the server.js. For our purposes, it will link to the views directory to render our .ejs's and will link to the models so it has access to the data we want.  


Index.js runs the Mongo connection. We start the Mongo connection here, and imports a Mongoose model. We will also export the Data.js that is linked here so it can be accessed by the controller.


Data.js is where we require our mongoose package. We create a new Schema to define what our objects should look like, and then create a new Model of that schema. By doing so, we also establish which collection our data will use in MongoDb. Finally, the model we made is exported for use.
```
const Example = mongoose.model('collection', Schema);
```
## server.js

- required modules
    ```
    const express = require('express')         
    const methodOverride = require('method-override');
    const rowdy = require('rowdy-logger')
    ```
- link controller
    ```
    const varController = require('./controllers/Controller.js');
    ```
- set variables
    ```
    const app = express() 
    const port = 4000
    const rowdyResults = rowdy.begin(app)
    ```
- middleware
    ```
    app.use(methodOverride('_method')); // allows usage of non-post/get methods
    app.use(express.urlencoded({ extended: false }))  //allows req.body to draw form data
    app.use('/url', Controller); // set the url controller will use
    ```
- configuration
    ```
    app.set('view engine', 'ejs'); // changes express default view engine to ejs
    ```
- homepage
    ``` 
    app.get('/', (req, res) => {
        res.send('Welcome to CapyTown');
    })
    ```
- start server
    ```
     app.listen(port, () => {
            console.log(`Example app listening at http://localhost:${port}`);
            rowdyResults.print()
    });
    ```
## Controllers
- required modules
    ```
    const express = require('express');
    const db = require('../models/index.js')
    const router = express.Router();
    ```
- contains whatever routes we want to use, e.g. [Restful-Routes](#Restful-Routes)
- export
    ```
    module.exports = router;
    ```
## Models
### index.js
- required modules
    ```
    const mongoose = require('mongoose');
    ```
- get connection address to MongoDb
    ```
    const connectionString = 'mongodb://localhost:27017/exampledb';
    ```
- connect and hide deprecation warnings
    ```
    mongoose.connect(connectionString, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useCreateIndex: true,
    useFindAndModify: false,
    });
    ```
- listen for connection and use callback function
    ```
    mongoose.connection.on('connected', () => {
        console.log(`Mongoose connected to ${connectionString} 🥭`);
    });
    ```
- export data model for use
    ```
    module.exports = {
    DataName: require('./Data.js')
    }
    ```
### Model-Schema
- required modules
    ```
    const mongoose = require('mongoose');
    ```
- define the Schema
    ```
    const exampleSchema = new mongoose.Schema({
            stringA: { type:String, required: true}, //required
            stringB: { type: String, default: 'str' }, //set default to str
            arr: [{type: String, type: Number}], // arr w/ object with string/num
            date: {type: Date}, // date object
            bool: Boolean
            obj: {
                str: String,
                num: Number
            }
        })
    ```
- create a model with specified schema
    ```
    const Example = mongoose.model('Example', exampleSchema)
    ```
- export the model 
    ```
    module.exports = Example
    ```

## Routes
General route notes:

It is a good idea to add navigation links to different routes in each of your pages. e.g.

```
<header>
    <nav>
        <ul>
            <li><a href="/url">All items</a></li>
            <li><a href="/url/new">Create a items</a></li>
        </ul>
    </nav>
</header>
```
ejs partials


## Views

### Restful Routes
| **URL** | **HTTP Verb** |  **Action** | **Description** |
|------------|-------------|------------|------------|
| /url/         | GET       | [index](#index)  | Retrieve many/all items
| /url/new         | GET       | [new](#new)   | Retrieve a form that can be used to create a new fruit
| /url          | POST      | [create](#create)   | Send data to create a new fruit
| /url/:id      | GET       | [show](#show)       | Retrieve one item
| /url/:id/edit | GET       | [edit](#edit)       | Retrieve a item and a prepopulated form that can be used to edit that item
| /url/:id      | PATCH/PUT | [update](#update)    | Send data to update an existing item
| /url/:id      | DELETE    | [destroy](#destroy)  | Remove a item

Note by default express will start looking for ejs files in the views directory. Some modifications to filepaths may be necessary to render the right .ejs

Also note that with 

```
app.set('view engine', 'ejs')
```
the default view engine has been set to .ejs, so 

```
res.render('/templates/index.ejs')
```
can be simply written as  
```
res.render('/templates/index')
```
with the same result.
```
app.set('views', './views') // default
res.render('index.ejs') // looks for /views/index.ejs
res.render('/templates/index.ejs') //looks for /views/templates/index/ejs.
app.set('views', './templates') //changes default to templates
res.render('index.ejs') // looks for /templates/index.ejs
```

Finally, note that we are calling Express methods, so depending on the file, the variable calling the methods may change.
```
const express = require('express');
const router = express.Router();
//will use
router.get...
const express = require('express')   
const app = express()
//will use
app.get...
```

#### index
[index.ejs](#index.ejs)  
```
router.get('/', (req, res) => { // get url path
    db.Data.find({}, (err, allData) => { //allData is whatever mongo returns
        if(err){return console.log(err)}
        else{
            res.render('index.ejs', { allData: allData }) //pass allData to index.ejs
        }    
    })
})
```
#### new
[new.ejs](#new.ejs) 

```
router.get('/new', (req, res) => { //get url path
  res.render('new.ejs') //render our new page
})
```
#### create
```
router.post('/', (req, res) => { 
  db.Date.create(req.body, (err, createdItem) => { //create new obj in our database with form data
    if (err) {return console.log(err)}
    else{
        res.redirect('/url') //redirect on success back to index page
    }  
  });
})
```
#### show
[show.ejs](#show.ejs)


```
router.get('/:dataId', (req, res) => { // grab id from url
  db.Data.findById(req.params.dataId, (err, foundItem) => { //find obj with unique Id from database
    if(err) return console.log(err);
    res.render('show.ejs', { oneItem: foundItem }) //pass that obj to our show.ejs page and render it
  })  
})
```

#### update
[edit.ejs](#edit.ejs)
```
// Serves a form to get info to update with
router.get('/:dataId/edit', (req, res) => { //get id from url
  db.Data.findById(req.params.dataId, (err, foundItem) => { //find item in DB with id
    if(err) return console.log(err);
    res.render('edit.ejs', { oneItem: foundItem }); //render our edit page and pass the found item to it.
  })
});
```
```
// Update the data for a particular item
router.put('/:dataId', (req, res) => {
    db.Data.findByIdAndUpdate(req.params.dataId, req.body, (err, updatedItem) => {
    if(err) return console.log(err);
    res.redirect(`/url/${req.params.dataId}`)
  })
})
```
#### destroy
```
router.delete('/:dataId', (req, res) => { //grab id from url
  db.Data.findByIdAndDelete(req.params.dataId, (err) => { //delete item from database with specified id
    if(err) return console.log(err);
    res.redirect('/url'); //redirect to index page
  })
})
```
### index.ejs
- HTML boilerplate
- Display Header
- Display Items

Same as before, but this time we want the index to be the item's unique id given by mongo, as accessed by ._id

```
<h1>Items:</h1>
<% for(let i = 0; i < allItems.length; i++) { %>
    <a href="/Data/<%= allItems[i]._id  %>">
        <h2><%= allItems[i].name %></h2>
    </a>

    <form action="/fruits/<%= allItems[i]._id %>?_method=DELETE" method="POST">
        <input type="submit" value="Delete this Item">
    </form>

    <a href="/url/<%= allItems[i]._id  %>/edit">Edit this Fruit</a>
<% } %>
```
### new.ejs
same as before
### show.ejs
same as before
### edit.ejs
same as before, but ensure that we use ._id like in index.