# Steps-for-setting-up-a-Server-App-Mongoose-Express-NodeJs-
A complete backend project using MongoDB, Mongoose, Express, and Node.js to build a robust API server. 

This repository guides you through creating a server architecture for a Full Stack MERN project, including database setup, API routes, and handling HTTP requests.

It includes:

• 🌐 MongoDB configuration with Mongoose

• 🛠️ Creating data models and managing schemas

• 🚀 Setting up Express for API routes

• 🔄 Integration of CRUD operations (Create, Read, Update, Delete)

With this server, you'll be ready to connect the frontend of your MERN application for a full solution. 💻🔗


### A) Server side (Backend): Specifically, on the terminal allocated for the backend (server-side)

1. **Create the Server Folder:**

* Create a new project directory:

  ```
  mkdir server
  ```

2. **Navigate to the `server` folder:**

   ```
   cd server
   ```
3. **Initialize a Node.js Project:**

   Initialize your Node.js project by running the following command:

   ```
   npm init -y
   ```

   After running this command, a `package.json` file will be automatically generated.
   Don't forget to add `"type": "module" `, add this for abling the nodemon to run your server `dev`: `npx nodemon` for enabling the use ` import` and ` export` of and  in your `package.json` file.
   
   //* Correct file for `package.json` 
   ```
     {
       "name": "server",
       "version": "1.0.0",
       "description": "",
       "main": "server.js",
       "scripts": {
         "test": "echo \"Error: no test specified\" && exit 1",
         "start": "node server.js",
         "dev": "npx nodemon"
      },
       "keywords": [],
       "author": "",
       "license": "ISC",
       "type": "module",
      "dependencies": {
        "cors": "^2.8.5",
        "dotenv": "^16.4.7",
        "express": "^4.21.2",
        "mongoose": "^8.13.0",
        "nodemon": "^3.1.9"
     }
   }

   ```


4. **Install Required Dependencies**:

   Install the necessary packages for the backend, including Express, Mongoose, CORS, and dotenv:

```
npm install express mongoose cors dotenv
```

   After running this command, a `package-lock.json`  file and a `node_modules`  folder will be automatically generated.

5. **Create A Modularization Structure:**
   Create the following folders inside the server folder manually or Git Bash commands :

   ```
   mkdir config controllers models routes
   ```
6. **Create The followings files:**

   Create the files  and with Git Bash commands:

   ```
   1. touch .env 
   2. touch .gitignore
   3. touch server.js
   ```
7. **Set Up for** `.env` **file**:

   ```
   * Define the PORT for the server:
   PORT = 8000
   * PASTE THE MONGODB CONNECTION STRING LINK HERE
   MONGO_URI = <your-mongodb-connection-string>  //* Paste here your mongo connection string from YOUR MONGO DB ATLAS ACCOUNT
   * Specify The DB NAME
   DB_NAME = database_name

   ```
8. **Set Up for `.gitignore` file:**

   ```
   node_modules
   .gitignore
   ```
9. **DB Connect with your server application:**
   Inside your `config` folder, create a new file called  `mongoose.config.js` and paste the following code:

   ```
   //* Import the `connect` method from mongoose library to establish a connection with MongoDB
   import { connect } from 'mongoose';
   //* Import the dotenv package to load environment variables from the .env file
   import dotenv from 'dotenv';

   //* Load environment variables
   dotenv.config();

   //* Extract MongoDB URI and database name from environment variables
   const MONGO_URI = process.env.MONGO_URI; // MongoDB URI
   const DB_NAME = process.env.DB_NAME; // Database name

   /**
    * Establish a connection to MongoDB database
    * This function connects to the the datyabase specified by the MONGO_URI and DB environment variable
    * 
    **/ 

   async function dbConnect() {
       try {
           await connect(MONGO_URI, {
               dbName: DB_NAME,
           });
           console.log(`Pinged your deployment. You successfully connected to MongoDB! in Database: ${DB_NAME}`);
       } catch (error) {
           console.log(error);
           throw error;
       }
   }
   export default dbConnect;
   ```

   10. Set Up for `models` folder:
       Create a new file in your `models` folder, naming the file according to your context, project, or assignment.

```
Example: product.model.js, car.model.js, user.model.js and task.model.js, etc...
```

Here is an example of models:

```
//* task.model.js:

import {model, Schema} from 'mongoose';

//* Define the Schema for a Task
const TaskSchema = new Schema(
    {
        //* Task Title
        title: {
            type: String, // * Data type for the task title
            //* Validation for task title
            required: [true, "Title of Task is required"],
            minLength: [3, "Title of Task must be at least 3 characters long!"],
            maxLength: [120, "Title of Task must be at most 120 characters long!"],
        },
        //* Task Description
        description: {
            type: String, // * Data type for the task description
            //* Validation for task description
            required: [true, "Description of Task is required"],
            minLength: [3, "Description of Task must be at least 3 characters long!"],
            maxLength: [500, "Description of Task must be at most 500 characters long!"],
        },
        //* Task Status
        status: {
            type: String, // * Data type for the task status
            enum: ['Not Started', 'In Progress', 'Completed'], // * Valid values for task status
            required: [true, "Status of Task is required"],
            default: 'Not Started', // * Default value for task status
        },
        //* Due Date
        dueDate: {
            type: Date, // * Data type for the task due date
            //* Validation for task due date
            required: [true, "Due Date of Task is required"],
            min: [Date.now(), "Due Date must be a future date"],
        },
    },
    {
        timestamps: true, //* Automatically add createdAt and updatedAt fields
    }
)

//* Create and export the Task model using the defined schema
const Task = model('Task', TaskSchema);

//* Export the Task model
export default Task;
```

11. Set Up for `controllers` folder:
    Inside your controllers folder, create a new file , naming the file according to your context, project, or assignment.

    ```
    Example: product.controller.js, user.controller.js, car.controller.js, task.controller.js, etc...
    ```

**Note: It is important to remember that in your controller, you should define the required functionalities for your application, such as the essential CRUD operations (Create, Read One, Read All, Update, and Delete).**
Here is an example of controller:

```
//* Import the Task model
import Task from "../models/task.model.js";

const taskController = {
    //? CRUD Features
    //! 1. Create a Task feature
    createTask: async (req, res) => {
        try {
            const newTask = await Task.create(req.body);

            //* Update User for updating a Task
            await User.findByIdAndUpdate(newTask.assignedTo, {
                $push: { tasks: newTask._id }
            });

            res.status(201).json({
                success: true,
                message: "✅ Task created successfully ✅",
                data: newTask
            });
        } catch (error) {
            console.log(error);
            res.status(400).json({
                success: false,
                message: "❌ Task creation failed ❌",
                error: error,
            });
        }
    },

    //! 2. Read All Tasks feature:
    getAllTasks: async (req, res) => {
        try {
            const allTasks = await Task.find().populate("assignedTo", "firstName");
            res.status(200).json({
                success: true,
                message: "✅ All tasks retrieved successfully ✅",
                allTaskData: allTasks
            });
        } catch (error) {
            console.log(error);
            res.status(400).json({
                success: false,
                message: "❌ All Tasks not found ❌",
                error: error,
            });
        }
    },

    //! 3. Read One Task feature
    getOneTask: async (req, res) => {
        try {
            const foundTask = await Task.findById(req.params.id);
            res.status(200).json({
                success: true,
                message: "✅ Task found successfully ✅",
                data: foundTask
            });
        } catch (error) {
            console.log(error);
            res.status(400).json({
                success: false,
                message: "❌ Task not found ❌",
                error: error,
            });
        }
    },

    //! 4. Update a Task feature
    updateOneTask: async (req, res) => {
        const options = {
            new: true, //* Return the updated document
            runValidators: true //* Ensure the update follows schema rules
        };
        try {
            const updatedTask = await Task.findByIdAndUpdate(req.params.id, req.body, options);
            if (!updatedTask) {
                return res.status(404).json({
                    success: false,
                    message: "❌ Task not found ❌"
                });
            }

            res.status(200).json({
                success: true,
                message: "✅ Task updated successfully ✅",
                data: updatedTask
            });
        } catch (error) {
            console.log(error);
            res.status(400).json({
                success: false,
                message: "❌ Task update failed ❌",
                error: error,
            });
        }
    },

    //* 5. Delete a Task feature
    deleteOneTask: async (req, res) => {
        try {
            const deletedTask = await Task.findByIdAndDelete(req.params.id);
            if (!deletedTask) {
                return res.status(404).json({
                    success: false,
                    message: "❌ Task not found ❌"
                });
            }
            res.status(200).json({
                success: true,
                message: "✅ Task deleted successfully ✅",
                data: deletedTask
            });
        } catch (error) {
            console.log(error);
            res.status(400).json({
                success: false,
                message: "❌ Task deletion failed ❌",
               error: error
            });
        }
    }
};

export default taskController;

```

12. **Set Uo for `routes` folder:**
    Inside your `routes` folder, create a new file , naming the file according to your context, project, or assignment.
    `Example: product.routes.js, user.routes.js, car.routes.js and task.routes.js, etc...`
    **Note: You should understand when to create a route for each feature defined in the controller.**
    Here is an example for routes:

    ```
    //* task.routes.js:

    //* Import the Router module from Express to create modular and mountable route handlers
    import { Router } from 'express';

    //* Import all features from the task controller to the routes
    import taskController from "../controllers/task.controller.js";

    //* Create a new instance from the Router class called `router`
    const router = Router();
    //? CRUD Features Routes
    //! 1. Create a Task feature Route
    router.route("/tasks/create")
            .post(taskController.createTask)
      //! 2. Read All Tasks feature Route
    //* a) Read All Tasks Route
    router.route("/tasks")
        .get(taskController.getAllTasks)

    //! 3. Read One Task feature Route
    router.route("/tasks/:id/details")
        .get(taskController.getOneTask)

     //! 4. Update One Task feature Route
    taskRr            .put(taskController.updateOneTask)

    //! 5. Delte One Task feature Route
    router.route("/tasks/:id/destroy")
        .delete(taskController.deleteOneTask)

    //* Export the router in the main file for the server application called 'server.js'
    export default router;

    //* Explanation:
    /tasks/create: Route for creating a new task.
    /tasks: Route for getting all tasks.
    /tasks/:id/details: Route for getting a specific task by ID.
    /tasks/:id/edit: Route for updating a specific task by ID.
    /tasks/:id/destroy: Route for deleting a specific task by ID.
    ```

    14. Set Up for `server.js` file:
        The `server.js` file sets up the Express server, connects to MongoDB, configures middleware, defines routes, and starts the server.

```
//* server.js

//* Import necessary modules
import express from 'express';
import cors from 'cors';
import dotenv from 'dotenv';


//* Import the router from the routes folder
import router from './routes/task.routes.js';
import dbConnect from './config/mongoose.config.js';


//* Create an instance of express application
const app = express();


//* Configure the middlewares
app.use(express.json(), cors());


//* Load the server port from environment variables
dotenv.config();


//* Retrives the server port from .env
const PORT = process.env.PORT;


//* Connect to the MONGODB database
dbConnect();

//* Add main routes for app running
app.use("/api", router);

//* Start the server
app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`); // Log server URL to the console
  });
  
```

15. **Running the server:**
    If you already installed `nodemon` globally on your desktop, youu can run directly with the Git Bash Command:
    ```
    nodemon server.js
    ```

**Summary: This is what the back-end structure of your MERN project will look like once you have followed the entire process correctly.**

```
my-project-name or my-assignment-name/
├── server/
│   ├── config/
│   │ ├── mongoose.config.js/
│   ├── controllers/
│   │ ├── task.controller.js/
│   ├── models/
│   │ ├── task.model.js/
│   ├── node_modules/
│   ├── routes/
│   │ ├── task.routes.js/
│   ├── .env/
│   ├── .gitignore/
│   ├── package-lock.json/
│   ├── package.json/
│   └── server.js
└── 
```
