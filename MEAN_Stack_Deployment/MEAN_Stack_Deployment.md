### **Deploying a MEAN Stack on Localhost**

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
```

```
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

sudo npm install express mongoose

In 'Books' folder, create a folder named apps

mkdir apps && cd apps

Create a file named routes.js

vi routes.js

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

mkdir models && cd models

Create a file named book.js

vi book.js

Copy and paste the code below into 'book.js'

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




