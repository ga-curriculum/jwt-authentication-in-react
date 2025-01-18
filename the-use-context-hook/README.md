<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">The <code>useContext()</code> Hook</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to manage app-level users through React's `createContext()` API and `useContext()` hook.

## Context

In React, context provides a way to pass data through the component tree without having to pass props down manually at every level. Context is designed to share data with a group of React components, such as the current authenticated user or the application's theme.

This process starts by creating context with the [`createContext()` API](https://react.dev/reference/react/createContext). The context we create will hold the `user` state, which will be accessible to any component that needs it.

This means that instead of storing and managing the `user` state in the `App` component and passing it down through every component in our app, we can use the [useContext()](https://react.dev/reference/react/useContext) hook to access the `user` state anywhere in the app.

## Create the user context for our app

We'll build a component that will manage the user state and provide it to the rest of the application.

We'll create our user context in an entirely new directory called `contexts`:

```bash
mkdir src/contexts
touch src/contexts/UserContext.jsx
```

The `UserContext.jsx` file will export a component called `UserProvider` that will manage the `user` state and provide that state to any component inside of it. We won't set up state yet, just write the basic structure of the component:

```jsx
// src/contexts/UserContext.jsx

import { createContext } from 'react';

const UserContext = createContext();

function UserProvider({ children }) {

  return (
    <UserContext.Provider>
      {children}
    </UserContext.Provider>
  );
};

export { UserProvider };

```

`UserContext` is created using the `createContext()` API. We'll use this context to provide the `user` state to the rest of the application. It also holds a `Provider` component.

The `UserContext.Provider` component will be responsible for keeping track of the context data given to components that consume the `UserContext`. Currently, the `Provider` component is empty, but we'll add data soon.

Note that we are destructuring the `children` prop in the `UserProvider` component. This is a special prop that React provides to components that have children. It allows the component to display whatever we pass between its opening and closing tags.

Think of it as a placeholder for the content that React will render inside the component. In this case, the `children` prop will be the components that need access to the `user` state we eventually add to this component.

### Wrap the `UserProvider` component around the `App` component

Import the `UserProvider` component and wrap it around the `App` component in the `main.jsx` file:

```jsx
// src/main.jsx

import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter } from 'react-router';

// Import the UserProvider component
import { UserProvider } from './contexts/UserContext.jsx';

import App from './App.jsx';

import './index.css';

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <BrowserRouter>
      {/* Wrap the UserProvider around the App */}
      <UserProvider>
        <App />
      </UserProvider>
    </BrowserRouter>
  </StrictMode>,
);
```

Now, the `UserProvider` component can provide context to the `App` component and its children.

But what context will it provide? Let's answer that next!

## Add state to the `UserProvider` component

```jsx
// src/contexts/UserContext.jsx

import { createContext, useState } from 'react';

const UserContext = createContext();

function UserProvider({ children }) {
  // Create state just like you normally would in any other component
  const [user, setUser] = useState(null);

  // This is the user state and the setUser function that will update it!
  // This variable name isn't special; it's just convention to use `value`.
  const value = { user, setUser };

  return (
    <UserContext.Provider value={value}>
      {/* The data we pass to the value prop above is now available to */}
      {/* all the children of the UserProvider component. */}
      {children}
    </UserContext.Provider>
  );
};

// When components need to use the value of the user context, they will need
// access to the UserContext object to know which context to access.
// Therefore, we export it here.
export { UserProvider, UserContext };

```

Reference the comments above for details on the changes we've made and why.

That's part one of setting up context for our user data. Next, we'll look at how to use this context in our components.

### Provide context to the components that need it

With our context created, we can add it to any components we want to use. We'll start with the `SignUpForm` component, where we'll use it to set the user state when the user signs up:

```jsx
// src/components/SignUpForm/SignUpForm.jsx

// Import the useContext hook
import { useState, useContext } from 'react';
import { useNavigate } from 'react-router';

import { signUp } from '../../services/authService';

// Import the UserContext object
import { UserContext } from '../../contexts/UserContext';

const SignUpForm = () => {
  const navigate = useNavigate();
  // Pass the UserContext object to the useContext hook to access:
  // - The user state (which we're not using here).
  // - The setUser function to update the user state (which we are using).
  //
  // Destructure the object returned by the useContext hook for easy access
  // to the data we added to the context with familiar names.
  const { setUser } = useContext(UserContext);
  const [message, setMessage] = useState('');
  const [formData, setFormData] = useState({
    username: '',
    password: '',
    passwordConf: '',
  });

  // formData destructuring and handleChange function.

  const handleSubmit = async (evt) => {
    evt.preventDefault();
    try {
      const newUser = await signUp(formData);
      // Call the setUser function to update the user state, just like normal.
      setUser(newUser);
      // Take the user to the (non-existent) home page after they sign up.
      // We'll get to this shortly!
      navigate('/');
    } catch (err) {
      setMessage(err.message);
    }
  };

  // isFormInvalid function and return statement.
};

```

To access the `setUser()` function from the `UserContext` object, we had to:

- Import the `useContext()` hook from React.
- Import the `UserContext` object from the `UserContext` component file.
- Pass the `UserContext` object to the `useContext(())` hook.
- Destructure the object returned by the `useContext()` hook to get the `setUser()` function. We also optionally have access to the `user` state, but we're not using it here.

> 💡 The output of this work intentionally gives us familiar tools we already know how to use to manipulate state!

## Update the `NavBar` component to use `UserContext`

Let's access the `user` state in the `NavBar` component using the `useContext` hook.

Note that the steps to get access to the `user` state and `setUser` setter are the same as in the `SignUpForm` component:

```jsx
// src/components/NavBar/NavBar.jsx

// Import the useContext hook
import { useContext } from 'react';
import { Link } from 'react-router';

// Import the UserContext object
import { UserContext } from '../../contexts/UserContext';

const NavBar = () => {
  // Pass the UserContext object to the useContext hook to access:
  // - The user state (which we use here).
  // - The setUser function to update the user state (which we aren't using).
  //
  // Destructure the object returned by the useContext hook for easy access
  // to the data we added to the context with familiar names.
  const { user } = useContext(UserContext);

  return (
    <nav>
      {user ? (
        <ul>
          <li>Welcome, {user.username}</li>
        </ul>
      ) : (
        <ul>
          <li><Link to='/sign-up'>Sign Up</Link></li>
        </ul>
      )}
    </nav>
  );
};

export default NavBar;

```

To enhance user experience, it's common to alter a navbar's content based on whether the user is logged in or not. Above, we're conditionally rendering the `Welcome, {user.username}` message if there is a `user` in state.

If the `user` state is falsy, we render a link to the sign-up page.

## Test it out

Refresh the app, and try signing up a new user. If everything is working correctly, the content shown in the `NavBar` component should change based on whether a user is signed in or not.

## Wow, `useContext()` is awesome! I'm never going to pass props down again!

Hold on there! While `useContext()` is a powerful tool, it's not always the best choice. It's best to only utilize `useContext()` when you have a notable value you need to share across many components, like the current user.

The `useContext()` hook can make your code harder to read and understand if you overly use it. It's best to use it sparingly. Passing props down is the primary mechanism for sharing data between components.
