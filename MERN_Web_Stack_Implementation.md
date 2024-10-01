### **Introduction to the MERN Stack**

The **MERN Stack** is a popular full-stack development framework that is used to build modern web applications. It is a combination of four powerful technologies that together create a seamless, end-to-end development experience for both frontend and backend.

#### **What is MERN?**
- **MongoDB**: A NoSQL database that stores data in JSON-like documents. It is highly scalable and flexible, making it ideal for applications that handle large amounts of data and require fast read/write operations.
  
- **Express.js**: A lightweight backend web application framework for **Node.js**. It simplifies the process of building robust APIs by providing a set of tools and features for handling HTTP requests, middleware, routing, and more.

- **React**: A popular JavaScript library for building dynamic, responsive, and interactive user interfaces. React follows a component-based architecture, making it easy to create reusable UI components that update efficiently.

- **Node.js**: A runtime environment that allows developers to execute JavaScript code on the server side. It is event-driven and non-blocking, making it well-suited for building fast, scalable, and data-intensive applications.

#### **Benefits of Using MERN for Full-Stack Development**

1. **End-to-End JavaScript**: One of the biggest advantages of MERN is that it uses JavaScript throughout the stack — for the frontend, backend, and database queries (with MongoDB). This reduces the complexity of switching between languages and makes development more consistent and streamlined.

2. **Component-Based Development**: React’s component-driven architecture encourages modularity and code reuse. Components can be used across the application, enhancing development speed and maintainability.

3. **Fast and Scalable**: Node.js is designed for high-performance, asynchronous programming, making it efficient for handling multiple simultaneous connections. MongoDB's flexibility allows horizontal scaling, which is crucial for large-scale applications.

4. **Rich Ecosystem**: All the MERN technologies are open-source and backed by strong community support. Developers have access to extensive libraries, frameworks, and tools that speed up the development process.

5. **MVC Architecture**: The MERN stack naturally supports the **Model-View-Controller (MVC)** architecture, with MongoDB acting as the **Model**, Express as the **Controller**, and React as the **View**, leading to a clear separation of concerns.

6. **Ease of Development and Debugging**: Using a single language across the entire stack makes it easier to understand, debug, and maintain the application code. Development tools like **MongoDB Compass** for database inspection, **Express middleware**, and **React Developer Tools** also enhance productivity.

7. **Full Stack Flexibility**: With MERN, developers have the flexibility to use React for highly dynamic and interactive frontends, while leveraging Express and Node.js to create RESTful APIs or full-scale backend logic, all powered by MongoDB for data storage.

MERN is an ideal choice for building single-page applications (SPAs), social media platforms, e-commerce websites, and any project requiring fast development and robust, scalable architecture.


### **Prerequisites**
- Node.js and npm installed
  - Ensure you have Node.js and npm installed by running:
    ```bash
    node -v
    npm -v
    ```
- MongoDB setup (local or cloud-based)
  - MongoDB Atlas (cloud) or MongoDB Community Edition (local)
 
### **Project Directory Setup**
- Create the project root folder:
  ```bash
  mkdir todo
  cd todo
  ```

### **Setting Up the Backend (Node.js & Express)**
1. **Initialize a Node.js project**:
   ```bash
   npm init -y
   ```



2. **Install Express.js and other dependencies**:
   ```bash
   npm install express mongoose dotenv
   ```

3. **Create Server File**:
   - Create `index.js` to define routes and middleware
   ```
   vim index.js
  ```

   Type the following code into the file:
   ```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
  ```

##Finally, Start the server
```
node index.js
```

**Making sure no other service is running on port 5000 then visit the following endpoint**
```
http://<PublicIP-or-PublicDNS>:5000
```

##**Define API Endpoints**:
Now, let's define the routes for GET, POST, PUT, and DELETE operations. These routes will interact with a MongoDB collection through a Mongoose model.

```
mkdir routes
cd routes
vim api.js
```

Then, save the below code in api.js

```
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```


### **Setting Up MongoDB and Models**
1. **Install MongoDB** which is a Node.js package that makes working with mongodb easier.
```
npm install mongoose
```

Change directory into the newly created 'models' folder with `cd models`.

Inside the models folder, create a file and name it `todo.js`

```
mkdir models && cd models && touch todo.js
```

Then save the following inside todo.js

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

Then update `api.js` in routes directory to make use of te new model

```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```

### Setting up MongoDB Databse
**Since I am using a VPS and not AWS Console, the steps taken her deffers from the guide**

Visit https://www.mongodb.com/docs/manual/installation/ and follow the installation tutorials for your platform

Finally, start mongodb using the following command

```
sudo systemctl start mongod 
```

Navigate to your todo directory and create a `.env`

```
touch .env
vi .env
```

Add the connection string to access the database in it, just as below:
```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```

Then, update your index.js with the following:
```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());

app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

###Finally, Start your server
```
node index.js
```


##Testing Backend Code without Frontend using POSTMAN 

Make the following request, with the default postman configuration:








### **Setting Up the Frontend (React)**
1. **Create React App**:
   ```bash
   npx create-react-app client
   ```

#### Running a React App

Before testing the react app, there are some dependencies that need to be installed.

Install concurrently. It is used to run more than one command simultaneously from the same terminal window.
  ```
    npm install concurrently --save-dev
    ```

Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.

    ```
        npm install nodemon --save-dev
    ```

In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
```
    "scripts": {
    "start": "node index.js",
    "start-watch": "nodemon index.js",
    "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
    },
```

2. **Install Axios** for API requests:
   ```bash
   npm install axios
   ```


## Configure Proxy in package.json

    Change directory to 'client'

    cd client
    
    Open the package.json file

        vi package.json

Add the key value pair in the package.json file "proxy": "http://localhost:5000". The purpose of this is to be able to visit the the apllication just by using the domain withput specifying the path `/api/todos`

**Finally, in your todo folder**
Run your app
```
npm run dev
```


## **React Components**:
   - Build components that you like e.g. Input.js ListTodo.js Todo.js
   - Fetch data from the backend API and display it
In my case, I have done the following:

Here’s a list of your commands with notes explaining what each does in a my react setup :

```bash
cd todo
```
- **Navigates back to the `todo` directory.**

```bash
cd client/src
```
- **Navigates to the `src` folder inside the `client` directory, typically where React components are located.**

```bash
mkdir components
```
- **Creates a new `components` folder to store React components.**

```bash
cd components
```
- **Navigates into the newly created `components` folder.**

```bash
vim input.js
```
- **Opens `input.js` in `vim` to write a React component for input functionality.**

```bash
cd ../..
```
- **Moves back two levels up to the `client` folder.**

```bash
npm install axios
```
- **Installs `axios`, a library used for making HTTP requests from the React frontend to the Node.js backend.**

```bash
cd src/components
```
- **Navigates to the `components` folder inside `src` again.**

```bash
vi ListTodo.js
```
- **Opens `ListTodo.js` in `vi`, a React component that probably lists todo items.**

```bash
vi Todo.js
```
- **Opens `Todo.js` in `vi`, which likely represents the structure of individual todo items.**

```bash
cd ..
```
- **Moves up one directory level.**

```bash
vi App.js
```
- **Opens the main React component, `App.js`, for editing.**

```bash
ls
```
- **Lists the contents of the current directory.**

```bash
cp App.js App.js.bak
```
- **Creates a backup of `App.js` before making further changes.**

```bash
vi App.js
```
- **Opens `App.js` for further editing.**

```bash
vi App.css
```
- **Opens `App.css`, which handles styling for the React app.**

```bash
cp App.css App.css.bak
```
- **Creates a backup of `App.css` before making changes.**

```bash
vi App.css
```
- **Opens `App.css` for further editing.**

```bash
vim index.css
```
- **Opens `index.css`, which provides global styles for the React application.**

```bash
cp index.css index.css.bak
```
- **Creates a backup of `index.css` before making any edits.**

```bash
vim index.css
```
- **Opens `index.css` again for further editing.**

```bash
cd ../..
```
- **Moves two levels up to the root of the `client` folder.**

```bash
npm run dev
```
- **Runs the React development server, typically using a script defined in `package.json`.**

```bash
cd client/src/components
```
- **Navigates back to the `components` directory to continue working on React components.**

```bash
vim Todo.js
```
- **Opens `Todo.js` for further editing.**

```bash
ls
```
- **Lists the contents of the current directory.**

```bash
mv input.js Input.js
```
- **Renames `input.js` to `Input.js` to follow React's convention of capitalizing component filenames.**


**Finally, Success**
