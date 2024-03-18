# ![React Authentication with JWT - Setup](./assets/hero.png)

## Setup

### Running the Express backend

Before we begin to build out our React app we need to make sure the Express app that we will be communicating is up and running.

Open your Terminal application and navigate to your **`~/code/ga/lectures/auth-express-jwt`** directory:

```bash
cd ~/code/ga/lectures/jwt
```

Once there, run your server with `nodemon`:

```bash
nodemon server.js
```

> If your auth-express-jwt backend is not fully built you can pull from the [solution code](https://git.generalassemb.ly/modular-curriculum-all-courses/authenticating-an-express-api-with-jwt-solution).

> Be sure to make the necessary dependency installations and connect to your MongoDB Atlas instance if using the solution code.

### Creating our React frontend

In a seperate terminal window, navigate to your **`~/code/ga/lectures`** directory:

```bash
cd ~/code/ga/lectures
```

Create a new Vite project for your React app:

```bash
npm create vite@latest
```

Follow the prompts to create a new React project:

You'll be prompted to provide a project name. `vite-project` is the default, but when you start typing that default will go away. Choose a name that makes sense for this project; in this case, we'll call the app `react-auth-jwt`. Follow the steps to create the project:

- By default the project will be named `vite-project` let's change it to something that will make sense for this project;`react-auth-jwt`.
- Select a framework. Use the arrow keys to choose the `React` option and hit `Enter`.
- Select a variant. Again, use the arrow keys to choose `JavaScript` and hit `Enter`.

Navigate to your new project directory and install the necessary dependencies:

```bash
cd react-auth-jwt
npm i
```

We will also need to install React Router for this app:

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

### Clear `App.jsx`

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

### Configure React Router
We need to set our app up to be able to use React Router.  Let's change `main.jsx` to the following:

```jsx
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

### Create a `NavBar`
Now let's create our `NavBar` component.  First we need to create the components folder:

```bash
mkdir src/components
```

We will then create the `NavBar` folder in the components folder:

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

### GitHub setup

To add this project to GitHub, initialize a Git repository:

```bash
git init
```

Make a new repository on [GitHub](https://github.com/) named `react-authentication-jwt`. 

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/react-authentication-jwt.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.