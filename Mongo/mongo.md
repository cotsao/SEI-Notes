


npm install mongoose
models
- connection file
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
    ```
    - listen for connection
    ```
    mongoose.connection.on('connected', () => {
        console.log(`Mongoose connected to ${connectionString}`);
    });
    ```
- schema (Example.js)
    ```
    const mongoose = require('mongoose'); //require mongoose package
    const Schema = mongoose.Schema; //mongoose has many properties on it.  One is a constructor function for Schemas
    ```
    - declare new Schema
        ```
        const exampleSchema = new Schema({
            stringA: { type:String, required: true}, //required
            stringB: String, // not required,
            arr: [{str: String, num: Number}], // arr w/ object with string/num
            bool: Boolean
            obj: {
                str: String,
                num: Number
            }
        })
        ```
    - create a class with specified schema
        ```
        const Example = mongoose.model('Example', exampleSchema)
        ```
    - export the Schema
        ```
        module.exports = Example
        ```
- Querying the Database
    - Queries are typically done in the controller
    - setup
        ```
        require('./db) //file path of database connection file
        const Example = require('./Example.js') // file path of exported class
        ```
    - Methods
        - [Mongoose Queries](https://mongoosejs.com/docs/queries.html)
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




    

    export schema and import to index.js
    controller runs database(mongo) methods