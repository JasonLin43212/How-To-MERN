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
