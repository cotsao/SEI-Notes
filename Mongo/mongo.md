## Navigation
-  [Setup](#Setup)
-  [Connect to Database](#Connect-to-Database)
-  [Schema](#Schema)
-  [Queries](#Queries)
## Setup
- npm install mongoose
- start mongo 
    ```
    sudo service mongodb start
    ```
## Connect-to-Database
Connection file connects to the MongoDB
- db.js
    - connect to database
    ```
    const mongoose = require('mongoose')
    ```
    - get address
    ```
    const connectionString = 'mongodb://localhost:27017/test';
    ```
    - launch connection and hide deprecation warning
    ```
    mongoose.connect(connectionString, { 
	    useNewUrlParser: true,
	    useUnifiedTopology: true,
	    useCreateIndex: true,
	    useFindAndModify: false,
    });
    mongoose.connection.close(); // close connection
    ```
    - listen for connection
    ```
    mongoose.connection.on('connected', () => {
        console.log(`Mongoose connected to ${connectionString}`);
    });
    ```
## Schema
- schema (Example.js)
    ```
    const mongoose = require('mongoose'); //require mongoose package
    const Schema = mongoose.Schema; //mongoose has many properties on it.  One is a constructor function for Schemas
    ```
    - declare new Schema
        ```
        const exampleSchema = new Schema({
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
        - [validators](https://mongoosejs.com/docs/validation.html)  exists to do things such as set minimum length
    - create a class with specified schema
        ```
        const Example = mongoose.model('Example', exampleSchema)
        ```
    - export the Schema 
        ```
        module.exports = Example
        ```
## Queries
- Querying the Database
    - Queries are typically done in the controller
    - setup
        ```
        require('./db) //file path of database connection file
        const Example = require('./Example.js') // file path of exported class
        ```
    - Methods
        - [Mongoose Queries](https://mongoosejs.com/docs/queries.html)
        - [Query Operators](https://docs.mongodb.com/manual/reference/operator/query/)
        - Find
            ```
            Example.find({},(err,allExamples)=>{
                console.log(allExamples) // an array of all examples
            })
            ```
            ```
            Example.find({stringA: "Example String"},(err, foundExamples)=>{
                console.log(foundExamples) // array of all Examples with stringA value of "Example String"
            })
            ```
        - Create
            ``` 
            const newExample = {
                stringA: 'test'
            }
            Example.create(newExample, (err, createdExample)=>{
                if(err){
                    console.log(err)
                } else{
                    console.log(createdExample)
                }
            })
        - Update
            ```
            Example.findByIdAndUpdate(
                exampleId,
                { stringA: 'search target'},
                { bool: true},
                (err, response)=>{
                    console.log(response)
                }
            )
        - Delete
            - Delete by Id
                ```
                Example.findByIdAndDelete(exampleId, (err, deletedExample) => {
                    console.log(deletedExample);
                });
                ```
            - Delete by contents
                ```
                Example.deleteOne({ stringA: 'test str' }, (err, response) => {
	                console.log(response);
                });
                ```
        - Combining methods

