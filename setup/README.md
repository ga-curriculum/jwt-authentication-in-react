<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Setup</span>
</h1>

## Set up the Express API

The app we're building in this lecture will require a back-end to handle requests from your React app. If you already have the Express API with JWT Authentication template set up, skip to the **Use an existing Express API with JWT Authentication template** section below. Otherwise, start from here and follow the steps to set up your development environment.

### Get the Express API with JWT Authentication template

Open your Terminal application and navigate to your `~/code/ga/lectures/` directory:

```bash
cd ~/code/ga/lectures/
```

Clone the Express API with JWT Authentication template from the [solution code repo](https://github.com/ga-curriculum/express-api-jwt-auth-template):

```bash
git clone https://github.com/ga-curriculum/express-api-jwt-auth-template.git
```

Move into the directory you just cloned the template repo into:

```bash
cd express-api-jwt-auth-template
```

Install the dependencies:

```bash
npm i
```

Open the project in VS Code:

```bash
code .
```

Open the built-in terminal in VS Code. You'll need to set up a `.env` file that includes values for the `MONGODB_URI` and `JWT_SECRET` before the app can function. Start by creating the `.env` file:

```bash
touch .env
```

Add your MongoDB URI to the `.env` file. It will look similar to the following but with your username, password, and database name:

```plaintext
MONGODB_URI=mongodb+srv://<username>:<password>@sei-w0kys.azure.mongodb.net/express-api-jwt-auth?retryWrites=true
```

> ⚠️ If you're unsure where to obtain your MongoDB URI, please refer to the MongoDB Atlas Setup Lab.

On the next line in the `.env` file, add a `JWT_SECRET`:

```plaintext
JWT_SECRET=your_secure_random_string_here
```

> ⚠️ Replace `your_secure_random_string_here` with a secure random string of your choice.

You're done! If everything went well, you should be able to run the server with this command:

```bash
npm run dev
```

You should see a message that the Express app is ready and that you've connected to a MongoDB database. Troubleshoot any errors before continuing.

Skip to the **Create the React front-end** section below.

### Use an existing Express API with JWT Authentication template

Move into the `~/code/ga/lectures/express-api-jwt-auth-template` directory:

```bash
cd ~/code/ga/lectures/express-api-jwt-auth-template
```

Open the project in VS Code:

```bash
code .
```

Open the integrated terminal in VS Code and run the server:

```bash
npm run dev
```

You should see a message that the Express app is ready and that you've connected to a MongoDB database. Troubleshoot any errors before continuing.

## Set up the React front-end

Return to your terminal window, and navigate to your `~/code/ga/lectures` directory:

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
npm i react-router
```

Open the project directory in VS Code:

```bash
code .
```

### Configure ESLint

Before we begin, we need to adjust the ESLint configuration. Add the indicated rules to the `rules` object in your `eslint.config.js` file:

```javascript
    rules: {
      ...js.configs.recommended.rules,
      ...react.configs.recommended.rules,
      ...react.configs['jsx-runtime'].rules,
      ...reactHooks.configs.recommended.rules,
      'react/jsx-no-target-blank': 'off',
      'react-refresh/only-export-components': [
        'warn',
        { allowConstantExport: true },
      ],
      'react/prop-types': 'off', // add this line
      'react/no-unescaped-entities': 'off', // add this line
    },
```

The first addition prevents warnings if you don't declare types for your props (which we're not), and the second prevents warnings if you use contractions within JSX.

### Add environment variables

We want the back-end and front-end to be as loosely coupled as possible. This means instead of hard-coding our `localhost:3000` address into the codebase, we want to use an environment variable that can change depending on context.

That way, when we deploy an app, we only need to tell the React app where the back-end is located using an environment variable.

So, we need to create a `.env` file that will contain our URL:

```bash
touch .env
```

Then add the following line to the `.env` file:

```plaintext
VITE_BACK_END_SERVER_URL=http://localhost:3000
```

If this variable name seems awkward, it's because [Vite's environment variables](https://vitejs.dev/guide/env-and-mode) must start with `VITE_` for the client application to read it.

Also, add `.env` to the list in your `.gitignore` file so that our local version doesn't travel with the final deployed app.

### Add routing with React Router

To enable navigation and routing in your React application, you need to integrate React Router. This setup allows your application to handle different URLs and display the appropriate content without reloading the page. Let's change `main.jsx` to the following:

```jsx
// src/main.jsx

import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter } from 'react-router' // add import for BrowserRouter
import './index.css'
import App from './App.jsx'

// Wrap the App component with the BrowserRouter component to enable
// enable route handling throughout your application.
createRoot(document.getElementById('root')).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>,
)
```

### Create a `NavBar`

Now let's create our `NavBar` component. First, we'll need to create a `components` directory with a `NavBar` directory inside it and a `NavBar.jsx` component file inside it:

```bash
mkdir src/components
mkdir src/components/NavBar
touch src/components/NavBar/NavBar.jsx
```

Add the following to the new `NavBar` component:

```jsx
// src/components/NavBar/NavBar.jsx

const NavBar = () => {
  return (
    <nav>
      <p>Navbar contents will go here.</p>
    </nav>
  );
};

export default NavBar;

```

### Clear `App.jsx`

Open the `App.jsx` file in the `src` directory and replace the contents of it with the following:

```jsx
// src/App.jsx

import NavBar from './components/NavBar/NavBar';

const App = () => {
  
  return (
    <>
      <NavBar />
      <h1>Hello, friend!</h1>
    </>
  );
};

export default App;

```

Note that we're importing and using the `NavBar` component that we just made in the `App` component.

### Start the development server

To start the development server and view our app in the browser, we'll use the following command:

```bash
npm run dev
```

If you're not running any other React apps, you should see that `Vite` is available on port number 5173. Open your browser and navigate to [`http://localhost:5173/`](http://localhost:5173). You should see the text **Navbar contents will go here** and a **Hello, friend!** heading on the page.

### GitHub setup

To add this project to GitHub, initialize a Git repository:

```bash
git init
git add .
git commit -m 'init commit'
```

Make a new repository on [GitHub](https://github.com/) named `react-jwt-auth-template`.

Link your local project to your remote GitHub repo:

```bash
git remote add origin https://github.com/<github-username>/react-jwt-auth-template.git
git push origin main
```

> 🚨 Do not copy the above command. It will not work. Your GitHub username will replace `<github-username>` (including the `<` and `>`) in the URL above.
