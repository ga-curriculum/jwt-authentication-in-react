# ![JWT Authentication in React - Setup](./assets/hero.png)

## Setup

### Running the Express backend

Before diving into our React app development, you'll need to ensure that the Express backend server is operational. This backend will handle requests from your React app. Follow these steps to set up the server:

Open your Terminal application and navigate to your **`~/code/ga/lectures/express-api-jwt-auth-template`** directory:

```bash
cd ~/code/ga/lectures/express-api-jwt-auth-template
```

Once there, run your server with `nodemon`:

```bash
nodemon server.js
```

> Note: If your `express-api-jwt-auth-template` backend is incomplete, you can obtain a fully implemented version from the [solution code repo](https://git.generalassemb.ly/modular-curriculum-all-courses/express-api-jwt-auth-template). Remember to install all necessary dependencies with `npm i` and establish a connection to your MongoDB Atlas by adding a connection string in a `.env` file.

### Create your server .env

To configure your server using the provided starter code, you'll need to set up a `.env` file that includes both the `MONGODB_URI` and `JWT_SECRET`:

1. **Establish a MongoDB Connection:**
   - Sign up or log into MongoDB Atlas and create a new database cluster. 
   - Generate a connection string for your MongoDB Atlas database, which will be used as your `MONGODB_URI`.

2. **Create JWT_SECRET:**
   - The `JWT_SECRET` is a secret key used for signing your JWT tokens. Choose a secure and random string.

3. **Set Up Your `.env` File:**
   - In the root directory of your server application, create a `.env` file.
   - Add your MongoDB connection string and JWT secret to this file as follows:

```plaintext
MONGODB_URI=your_mongodb_atlas_connection_string_here
JWT_SECRET=your_secure_random_string_here
```

Start the server and you are ready to start on the React front-end!

```bash
nodemon start
```


## Creating our React frontend

In a separate terminal window, navigate to your **`~/code/ga/lectures`** directory:

```bash
cd ~/code/ga/lectures
```

Create a new Vite project for your React app:

```bash
npm create vite@latest
```

You'll be prompted to choose a project name. Let's name it `react-jwt-auth-template`. 

- Select a framework. Use the arrow keys to choose the `React` option and hit `Enter`.

- Select a variant. Again, use the arrow keys to choose `JavaScript` and hit `Enter`.

Navigate to your new project directory and install the necessary dependencies:

```bash
cd react-jwt-auth-template
npm i
```

We will also need to install React Router for this application:

```bash
npm i react-router-dom
```

Open the project's folder in your code editor:

```bash
code .
```

### Configuring ESLint

Before we begin, we need to adjust the ESLint configuration. Add the following rules to the `.eslintrc.cjs` file:

```js
rules: {
  'react-refresh/only-export-components': [
    'warn',
    { allowConstantExport: true },
  ],
  'react/prop-types': 'off', // add this line
  'react/no-unescaped-entities': 'off' // add this line
},
```

The first addition prevents warnings if you're not declaring types for your props (which we're not), and the second prevents warnings if you're using contractions within JSX.

## Configure React Router

To enable navigation and routing in your React application, you need to integrate React Router. This setup allows your application to handle different URLs and display the appropriate content without reloading the page. Let's change `main.jsx` to the following:

```jsx
// src/main.jsx

import { BrowserRouter } from 'react-router-dom'; // import BrowserRouter
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.jsx';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>,
)
```

Start by importing BrowserRouter from 'react-router-dom', which is the router component responsible for managing the history API used in our web app.

This code sets up the React Router wrapped around your App component, enabling route handling throughout your application.

## Clear `App.jsx`

Open the `App.jsx` file in the `src` directory and replace the contents of it with the following:

```jsx
// src/App.jsx

import { useState } from 'react';
import { Routes, Route } from 'react-router-dom';
import NavBar from './components/NavBar/NavBar';

const App = () => {
  const [user, setUser] = useState(null)

  return (
    <>
      <NavBar user={user} />
      <h1>Hello world!</h1>
    </>
  )
}

export default App
```

We'll be starting this build with a `NavBar` component and a `user` set in state.


### Create a `NavBar`

Now let's create our `NavBar` component. First we need to create the components folder:

```bash
mkdir src/components
```

We will then create the `NavBar` folder:

```bash
mkdir src/components/NavBar
```

Finally, we will create our `NavBar` component:

```bash
touch src/components/NavBar/NavBar.jsx
```

Add the following to your `NavBar` component:

```jsx
const NavBar = ({ user }) => {
  return (
    <>
      <p>Hello Navbar</p>
    </>
  )
}
export default NavBar
```

### Running the development server

To start the development server and view our app in the browser, we'll use the following command: 

```bash
npm run dev
```

You should see that `Vite` is available on port number 5173: 

```plaintext
localhost:5173
```

### GitHub setup

To add this project to GitHub, initialize a Git repository:

```bash
git init
git add .
git commit -m "init commit"
```

Make a new repository on [GitHub](https://github.com/) named react-jwt-auth-template. 

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/react-jwt-auth-template.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.
