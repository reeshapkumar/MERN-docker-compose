# MERN Stack Docker Application 

Creating a MERN (MongoDB, Express.js, React, Node.js) application using Docker Compose involves setting up a full-stack web application where the frontend (React) and backend (Node.js with Express) are containerized along with a MongoDB database. Below are the steps to create a MERN application using Docker Compose, along with code snippets.

**1. Project Structure**
First, create a directory for your project with the following structure:

``java
Copy code
mern-docker-compose/
├── backend/
│   ├── Dockerfile
│   ├── package.json
│   ├── server.js
│   └── .env
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── src/
│   └── public/
├── docker-compose.yml
``

**2. Backend Setup (Node.js + Express)**

**A. Create the Backend Directory Navigate to the backend directory:**

``bash
Copy code
mkdir backend
cd backend
Initialize a new Node.js project:
``

``bash
Copy code
npm init -y
Install required dependencies:
``

``bash
Copy code
npm install express mongoose dotenv cors
``

**Create the server.js file:**

``javascript
Copy code
// backend/server.js
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
require('dotenv').config();
``

``const app = express();
const PORT = process.env.PORT || 5000;
``

// Middleware
``app.use(cors());
app.use(express.json());
``

// MongoDB Connection
``mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('MongoDB connection error:', err));
  ``

// Sample route
``app.get('/', (req, res) => {
  res.send('Welcome to the MERN backend!');
});
``

// Start the server
``app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
``

**Create a .env file to store environment variables:**

``bash
Copy code
backend/.env
MONGO_URI=mongodb://mongo:27017/mydatabase
``

**B. Create the Dockerfile for Backend dockerfile**

``Copy code
backend/Dockerfile
FROM node:14
``

``Create app directory
WORKDIR /usr/src/app
``

``Install app dependencies
COPY package*.json ./
RUN npm install
``

``Bundle app source
COPY . .
``

``Expose the port
EXPOSE 5000
``

``Start the application
CMD ["node", "server.js"]
``

**3. Frontend Setup (React)**

**A. Create the Frontend Directory Navigate to the frontend directory:**

``bash
Copy code
cd ../frontend
Create a new React app (You need to have create-react-app installed):
``

``bash
Copy code
npx create-react-app .
``

**Modify the package.json to include a proxy (for API calls to the backend):**

``json
Copy code
// frontend/package.json
{
  // ... other configurations
  "proxy": "http://backend:5000"
}
Create a sample component to fetch data from the backend:
Create a file App.js in the src directory with the following content:
javascript
Copy code
// frontend/src/App.js
import React, { useEffect, useState } from 'react';
``

``function App() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    fetch('/')
      .then(response => response.text())
      .then(data => setMessage(data));
  }, []);

  return (
    <div>
      <h1>MERN Docker Compose App</h1>
      <p>{message}</p>
    </div>
  );
}

export default App;
``

**B. Create the Dockerfile for Frontend**

``dockerfile
Copy code
frontend/Dockerfile
FROM node:14 as build

WORKDIR /app

COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
``

``Serve the app using a simple server
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
``

**4. Docker Compose Setup**

**A. Create the docker-compose.yml file**

``yaml
Copy code
docker-compose.yml
version: '3.8'

services:
  backend:
    build:
      context: ./backend
    ports:
      - "5000:5000"
    env_file:
      - ./backend/.env
    depends_on:
      - mongo

  frontend:
    build:
      context: ./frontend
    ports:
      - "3000:80"

  mongo:
    image: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
  ``
  
**5. Running the Application**
Navigate to the root of your project directory (where docker-compose.yml is located):

``bash
Copy code
cd mern-docker-compose
Run Docker Compose:
``

``bash
Copy code
docker-compose up --build
Access the application:
``

``Frontend: http://localhost:3000
Backend: http://localhost:5000 (API)
``

**6. Testing the Application**

**Open your browser and navigate to http:**//localhost:3000.
You should see "MERN Docker Compose App" and a message from the backend.

**7. Enhancements**

**Once you have the basic setup running, consider adding:**

CRUD operations for your backend.
State management using Context API or Redux in your React app.
Error handling and user feedback for a better user experience.

**Conclusion**
You now have a fully functioning MERN stack application running in Docker containers using Docker Compose. You can further expand on this setup by implementing additional features, adding unit tests, or integrating a CI/CD pipeline. Let me know if you have any questions or need further assistance!
