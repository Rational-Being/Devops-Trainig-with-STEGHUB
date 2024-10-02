![03 node server](https://github.com/user-attachments/assets/1367fe32-336c-4c1a-8869-b90432dcba02)### **Deploying a MEAN Stack on VPS**

---

#### **Introduction**

##### **Overview of the MEAN Stack and Its Components**

The MEAN stack is a powerful and flexible web development framework comprised of four primary technologies:
- **MongoDB**: A NoSQL database system that stores data in JSON-like format, making it easy to handle complex, hierarchical data structures.
- **Express.js**: A web application framework for Node.js that provides a minimal, flexible, and robust set of features to build single-page, multi-page, or hybrid web applications.
- **Angular**: A TypeScript-based front-end web framework for building dynamic and robust web applications. It handles the client-side part of your application.
- **Node.js**: A JavaScript runtime environment that executes JavaScript code outside of a browser, allowing you to build scalable network applications.

By combining these components, the MEAN stack provides a full-stack JavaScript solution for building fast, scalable, and efficient web applications.

##### **Why Use the MEAN Stack for Development**

- **Single Language for Full-Stack**: You can use JavaScript across the entire stack (front-end, back-end, and database), which simplifies development and allows for a more unified codebase.
- **Highly Scalable**: MEAN is perfect for building cloud-native applications that scale easily with demand, thanks to the non-blocking nature of Node.js and the flexible schema of MongoDB.
- **Community Support**: The components of the MEAN stack, particularly Angular and Node.js, are widely used, resulting in a large support community, plenty of resources, and libraries to help you develop efficiently.
- **RESTful APIs**: MEAN is great for developing RESTful APIs, allowing seamless communication between the front-end and back-end.

---

#### **Pre-requisites**

###### **While the Prerequisite of this training is to to use AWS as instructed by my traingin guide, I currently do not have access to my AWS Account right now, so I will use a VPS but I will be sure to replicate tis project on an AWS Environemnt Later**

##### **Operating System Requirements**

- The MEAN stack can be installed on Windows, macOS, and Linux operating systems. For this documentation, I’ll outline steps for Ubuntu Linux, but the process is similar across all major platforms.
  
##### **Required Software Installations**

Here’s a breakdown of the essential software and tools you'll need before starting:

1. **Node.js**:
   - This is the backbone of your back-end application. It needs to be installed to run JavaScript code on the server. You will also get `npm` (Node Package Manager) along with it, which is essential for managing dependencies.
  
2. **MongoDB**:
   - MongoDB serves as the database for your application. It stores your application data in a JSON-like structure and allows for flexible and scalable data modeling.

3. **Angular CLI**:
   - The Angular Command Line Interface helps to scaffold and build your Angular applications. It simplifies the management of your front-end codebase.

4. **Git** (optional but recommended):
   - Git is a version control system that is helpful for managing and tracking changes in your project.

5. **Postman** (optional but recommended):
   - Postman is an API development tool that you can use to test your back-end API endpoints (GET, POST, PUT, DELETE) with ease.

---

Next, we will move into setting up the environment, including installation of the required components and configuring MongoDB. Let me know if you want to proceed with the next section.


# In this assignment I will be implementing a simple Book Register web form using MEAN stack

## Step 1: Install NodeJs

Update ubuntu
```
sudo apt update
```

Upgrade ubuntu

```
sudo apt upgrade
```

Add certificates

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

Install NodeJS

```
sudo apt install -y nodejs
```

## Step 2: Install MongoDB

**In this Learning, I will use Mongodb for adding book records to MongoDB that contain book name, isbn number, author, and number of pages**
```

sudo apt-get install -y gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
      --dearmor



echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

  Install MongoDB

```
      sudo apt-get install -y mongodb-org

```
  Start The server

 ```
     sudo service mongodb start
```

  Verify that the service is up and running
```
      sudo systemctl status mongodb
```

Install [npm](https://www.npmjs.com) - Node package manager.

```
      sudo apt install -y npm
```

Install 'body-parser package

We need 'body-parser' package to help us process JSON files passed in requests to the server.
```
      sudo npm install body-parser
```

  Create a folder named 'Books'

```
      mkdir Books && cd Books
```

In the Books directory, Initialize npm project

```
      npm init
```
![01 npm init](https://github.com/user-attachments/assets/02536326-84f1-4ba1-ad0d-651731394764)


Add a file to it named server.js

```
      vi server.js
```

Copy and paste the web server code below into the server.js file.
```
      const express = require('express');
      const bodyParser = require('body-parser');
      const mongoose = require('mongoose');
      const path = require('path');

      const app = express();
      const PORT = process.env.PORT || 3300;

      // MongoDB connection
      mongoose.connect('mongodb://localhost:27017/test', {
        useNewUrlParser: true,
          useUnifiedTopology: true,
          })
          .then(() => console.log('MongoDB connected'))
          .catch(err => console.error('MongoDB connection error:', err));

          app.use(express.static(path.join(__dirname, 'public')));
          app.use(bodyParser.json());

          require('./apps/routes')(app);

          app.listen(PORT, () => {
            console.log(`Server up: http://localhost:${PORT}`);
            });

```
### Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

```
sudo npm install express mongoose
```
![02 express mongoose](https://github.com/user-attachments/assets/773d9588-b40a-4825-a18d-affe362c73bc)


In 'Books' folder, create a folder named apps
```
mkdir apps && cd apps
```
Create a file named routes.js
```
vi routes.js
```
Copy and paste the code below into routes.js

```
const Book = require('./models/book');
const path = require('path');

module.exports = function(app) {
  app.get('/book', async (req, res) => {
    try {
      const books = await Book.find();
      res.json(books);
    } catch (err) {
      res.status(500).json({ message: 'Error fetching books', error: err.message });
    }
  });

  app.post('/book', async (req, res) => {
    try {
      const book = new Book({
        name: req.body.name,
        isbn: req.body.isbn,
        author: req.body.author,
        pages: req.body.pages
      });
      const savedBook = await book.save();
      res.status(201).json({
        message: 'Successfully added book',
        book: savedBook
      });
    } catch (err) {
      res.status(400).json({ message: 'Error adding book', error: err.message });
    }
  });

  app.delete('/book/:isbn', async (req, res) => {
    try {
      const result = await Book.findOneAndDelete({ isbn: req.params.isbn });
      if (!result) {
        return res.status(404).json({ message: 'Book not found' });
      }
      res.json({
        message: 'Successfully deleted the book',
        book: result
      });
    } catch (err) {
      res.status(500).json({ message: 'Error deleting book', error: err.message });
    }
  });

  app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, '../public', 'index.html'));
  });
};

```


In the 'apps' folder, create a folder named models
```
mkdir models && cd models
```
Create a file named book.js
```
vi book.js
```
Copy and paste the code below into 'book.js'
```
const mongoose = require('mongoose');

const bookSchema = new mongoose.Schema({
  name: { type: String, required: true },
    isbn: { type: String, required: true, unique: true, index: true },
      author: { type: String, required: true },
        pages: { type: Number, required: true, min: 1 }
        }, {
          timestamps: true
          });

  module.exports = mongoose.model('Book', bookSchema);
```


### Step 4 - Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to 'Books'
```
cd ../..
```
Create a folder named public
```
mkdir public && cd public
```
Add a file named script.js
```
vi script.js
```
Copy and paste the Code below (controller configuration defined) into the script.js file.

```
angular.module('myApp', [])
  .controller('myCtrl', function($scope, $http) {
    function fetchBooks() {
      $http.get('/book')
        .then(response => {
          $scope.books = response.data;
        })
        .catch(error => {
          console.error('Error fetching books:', error);
        });
    }

    fetchBooks();

    $scope.del_book = function(book) {
      $http.delete(`/book/${book.isbn}`)
        .then(() => {
          fetchBooks();
        })
        .catch(error => {
          console.error('Error deleting book:', error);
        });
    };

    $scope.add_book = function() {
      const newBook = {
        name: $scope.Name,
        isbn: $scope.Isbn,
        author: $scope.Author,
        pages: $scope.Pages
      };

      $http.post('/book', newBook)
        .then(() => {
          fetchBooks();
          // Clear form fields
          $scope.Name = $scope.Isbn = $scope.Author = $scope.Pages = '';
        })
        .catch(error => {
          console.error('Error adding book:', error);
        });
    };
  });
```

In 'public' folder, create a file named index.html
```
vi index.html
```

Copy and paste the code below into index.html file.

```
<!DOCTYPE html>
<html ng-app="myApp" ng-controller="myCtrl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Book Management</title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
  <script src="script.js"></script>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    table { border-collapse: collapse; width: 100%; }
    th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
    th { background-color: #f2f2f2; }
    input[type="text"], input[type="number"] { width: 100%; padding: 5px; }
    button { margin-top: 10px; padding: 5px 10px; }
  </style>
</head>
<body>
  <h1>Book Management</h1>
  
  <h2>Add New Book</h2>
  <form ng-submit="add_book()">
    <table>
      <tr>
        <td>Name:</td>
        <td><input type="text" ng-model="Name" required></td>
      </tr>
      <tr>
        <td>ISBN:</td>
        <td><input type="text" ng-model="Isbn" required></td>
      </tr>
      <tr>
        <td>Author:</td>
        <td><input type="text" ng-model="Author" required></td>
      </tr>
      <tr>
        <td>Pages:</td>
        <td><input type="number" ng-model="Pages" required></td>
      </tr>
    </table>
    <button type="submit">Add Book</button>
  </form>

  <h2>Book List</h2>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>ISBN</th>
        <th>Author</th>
        <th>Pages</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      <tr ng-repeat="book in books">
        <td>{{book.name}}</td>
        <td>{{book.isbn}}</td>
        <td>{{book.author}}</td>
        <td>{{book.pages}}</td>
        <td><button ng-click="del_book(book)">Delete</button></td>
      </tr>
    </tbody>
  </table>
</body>
</html>
```

Change the directory back up to 'Books'
```
cd ..
```
Start the server by running this command:
```
node server.js
```

![03 node server](https://github.com/user-attachments/assets/1be6ea8a-3b57-4810-9eb4-51f032b65376)

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.
```
curl -s http://localhost:3300
```
It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

For this - you need to open TCP port 3300 on your VPS

![4 welcome to express](https://github.com/user-attachments/assets/28eb1a8d-15c0-409c-b15c-0bef310a8bc4)







