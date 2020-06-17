# A Guide to Writing A MERN Application (Kinda)
The purpose of this document is mainly a reference for me in the future if I want to write and deploy a website. This guide will mostly cover MongoDB, Express.js, Redux, and Node. The only thing it will not cover is how to create a React front end application. You will need to first install Node and the node package manager.

## Setting Up the Back End
1. First, create the directory for your project.
2. `cd` into that directory and run `npm init` to create a `package.json`.
3. Let's install some packages: `npm i express mongoose concurrently`. This will install express, which is used for the back end, mongoose, which is used to interact with MongoDB, and concurrently, which allows you to run the client and server at the same time.
4. We will install nodemon, which allows for the page to automatically reload when we make changes to our code:
`npm i -D nodemon`. The `-D` means that this package is only for development, not production.
5. Finally, go to the `package.json` and add the following to the `"scripts"` object:
```
"start": "node server.js"
"server": "nodemon server.js"
```
To run these scripts, you can do `npm run start` or `npm run server`.

## Mongoose and Express
### Setting Up Server.js
Create a file called `server.js` and import the following things:
```javascript
const express = require("express");
const mongoose = require("mongoose");
const path = require("path");
require('dotenv').config();
```
`require` is just another way of saying that you are importing the package. The `require('dotenv').config()` is used to read from your environment variables.

Finally, to set up express and add middleware for the body parser:
```javascript
const app = express();
app.use(express.json());
```

### Mongo Models
Here is a [cheat sheet](http://weblab.mit.edu/public/databases-cheatsheet.pdf) for MongoDB related things.

The first thing we want to do is create a model for our database. We want to put our models in a `models` folder. This is an example with `Item.js`:
```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

// Create a Schema
const ItemSchema = new Schema({
    name: {
        type: String,
        required: true,
    },
    date: {
        type: Date,
        default: Date.now,
    }
});

// Exports the model
module.exports = Item = mongoose.model('item', ItemSchema);
```

### Express GET, POST, DELETE Requests Using the Model
To set up the routes for the back end, we first create a directory called `routes/api`. Here is an example with `items.js`:
```javascript
const express = require("express");
const router = express.Router();

// Item Model
const Item = require("../../models/Item");

// The slash represents api/items already
router.get("/", (req, res) => {
    Item.find()
        .sort({ date: -1 }) // Sorts items by date in descending order
        .then(items => res.json(items))
});


router.post("/", (req, res) => {
    const newItem = new Item({
        name: req.body.name,
    });

    newItem.save().then(item => res.json(item))
});


router.delete("/:id", (req, res) => {
    Item.findById(req.params.id) // Fetches the id from the url
        .then(item => item.remove().then(() => res.json({success: true})))
        .catch(err => res.status(404).json({success: false}));
});

module.exports = router;
```

### Connecting Server to Routes
Now we want our server to execute the functions above whenever a request is sent to the correct urls. To do this, we go back to `server.js` and add:
```javascript
app.use('/api/items', require('./routes/api/items.js'));
```
This connects the url `/api/items` to the router. Finally, we want to listen to a port or any other port you may have put in your `.env` file:
```javascript
const port = process.env.PORT || 5000;
app.listen(port, () => console.log(`Server started on port ${port}`));
```

### Connect to Mongo
In the `.env` file, we add in
```
REACT_APP_MONGO_URI=<YOUR_MONGO_URI>
```
Then, to connect to MongoDB in `server.js`, we do this:
```javascript
const options = {
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useCreateIndex: true,
    dbName: <INSERT_DB_NAME_HERE>,
}
// Connect to Mongo
mongoose
    .connect(process.env.REACT_APP_MONGO_URL, options)
    .then(() => console.log("MongoDB Connected..."))
    .catch(err => console.log(err));
```
Congratulations, you finished setting up the back end for development.

## Setting Up The Front End
Create a `client` folder and go into that directory. Now, you can create your front end by running `create-react-app .`.

Go into the `package.json` in the `client` directory and add in the following key-value pair:
`"proxy": "http://localhost:5000"`

This will make it so that when you send a get request to `/api/items`, it will automatically add the proxy to the beginning.

Now, add the following to the `"scripts"` section of the `package.json` file for the server. This will allow you to run both the server and client with one command.
```javascript
"client": "npm start --prefix client"
"dev": "concurrently \"npm run server\" \"npm run client\""
"client-install": "npm install --prefix client"
```
Now, you can create your React application within the client folder with any kind of structure that you want.

### Setting Up Redux
#### Action Types
The first thing you want to do is to think about what kind of state you will have and what kind of actions can operate on that state. Create a folder called `actions` within the `src` folder of your `client`. Add a file called `types.js`. This file will just export action names like so:
```javascript
export const ADD_ITEM = "ADD_ITEM";
export const GET_ITEMS = "GET_ITEMS";
```
#### Creating the Actions
