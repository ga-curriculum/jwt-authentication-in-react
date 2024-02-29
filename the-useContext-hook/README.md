# ![React Authentication JWT - The `useContext` hook](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to tktk

Instead of storing the user in the App.js state and passing it down through every component everywhere, refactor to use the useContext hook and provide the user and their JWT as globally accessible pieces of state.

## The `useContext` hook

Here we will import and implement the `useContext` hook to help make our user available to any components that need it without the need for passing the user as a prop.

### Creating the "context" for our App

We will first import the `createContext` hook in our app component and provide it with a value:

```jsx
import { createContext, useState } from 'react' // add createContext
.
.
.

export const AuthedUserContext = createContext(null); // set the initial value of the context to null
.
.
.
return (
    <AuthedUserContext.Provider value={user}> // wrap all of our components with the context provider and give it the value of the user state
      <NavBar handleLogout={handleLogout} />
      <Routes>
        {
          user ?
          <Route path="/" element={<Dashboard />} />
          :
          <Route path="/" element={<Landing />} />
        }
        
        <Route path='/signup' element={<SignupForm setUser={setUser} />} />
        <Route path="/signin" element={<SigninForm setUser={setUser} />} />
      </Routes>
    </AuthedUserContext.Provider>
  )
}
```

The above code brings in the `createContext` hook to allow us to provide a value for our other components to grab when needed.  We then define the context as `AuthedUserContext` and set that to an initial value of `null`.  Lastly, we will wrap all of the nested components with this `<AuthedUserContext.Provider value={user}>` and set that value to whatever the state of our component is.  This is nice because as the state of our `App` component is updatad so will the value of this context as well.  We can also remove the `user` prop from the `NavBar` and `Dashboard` components.

### Provide context to the components that need it

With our context created we can go about adding it to any components we want to use.  We'll start with the `NavBar` component:

```jsx
// src/components/NavBar/NavBar.jsx
import { useContext } from 'react'; // import useContext to grab the context
import { AuthedUserContext } from '../../App'; // import the AuthedUserContext
import { Link } from 'react-router-dom';

const NavBar = ({ handleLogout }) => { // remove the user prop
  const user = useContext(AuthedUserContext); // set the value of user to the imported useContext hook bringing in the value from AuthedUserContext

```

And also our `Dashboard` component:

```jsx
// src/components/Dashboard/Dashboard.jsx
import { useContext } from 'react';
import { AuthedUserContext } from '../../App';

const Dashboard = () => {
  const user = useContext(AuthedUserContext);

```

In both cases we are bringing in the `useContext` hook to access the values provided from our newly created `AuthedUserContext`.  Since this value is updated whenever our state is updated via a signin or signout the context is also passed to any components that use it.

Give it a try and if your components behave as you had them congrats!!  You are providing the values via `useContext`