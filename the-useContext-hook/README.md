# ![React Authentication JWT - The `useContext` hook](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to manage app-level users through React's `useContext` hook.

Instead of storing the user in the `App` state and passing it down through every component everywhere, we can refactor to use the [useContext](https://react.dev/reference/react/useContext) hook and provide the user as a globally accessible piece of state.

## Managing state with `useContext` and `createContext`

To enable a more efficient and global approach to state management, React offers the `useContext` hook alongside the `createContext` hook. These tools allow us to create globally accessible pieces of state across multiple components without the cumbersome task of passing props down several layers. This is particularly useful for applications like ours that require widespread access to certain data, such as user authentication status.

For our setup, we will utilize both `createContext` and `useContext` to access and manage this user data across our components. This approach eliminates the need to directly pass the user state as props from one component to another.

### Creating the "context" for our app

To begin, we'll import the `createContext` and `useState` hooks in our `App` component and set up a context to store the user state:

```jsx
import { createContext, useState } from 'react' // add createContext
.
.
.
export const AuthedUserContext = createContext(null); // set the initial value of the context to null

const App = () => {
  const [user, setUser] = useState(authService.getUser());

    const handleSignout = () => {
    authService.signout();
    setUser(null);
  };

  return (
    <AuthedUserContext.Provider value={user}>
      <NavBar handleSignout={handleSignout} />
      <Routes>
        {user ? (
          <Route path="/" element={<Dashboard />} />
        ) : (
          <Route path="/" element={<Landing />} />
        )}
        <Route path="/signup" element={<SignupForm setUser={setUser} />} />
        <Route path="/signin" element={<SigninForm setUser={setUser} />} />
      </Routes>
    </AuthedUserContext.Provider>
  );
};

export default App;
```

The above code brings in the `createContext` hook to allow us to provide a value for our other components to grab when needed. We then define the context as `AuthedUserContext` and set that to an initial value of `null`. Lastly, we will wrap all of the nested components with this `<AuthedUserContext.Provider value={user}>` and set that value to whatever the state of our component is. This is nice because as the state of our `App` component is updated, so will the value of this context. We can also remove the `user` prop from the `NavBar` and `Dashboard` components.

### Provide context to the components that need it

With our context created we can go about adding it to any components we want to use. We'll start with the `NavBar` component:

```jsx
// src/components/NavBar/NavBar.jsx
import { useContext } from 'react'; // import useContext to grab the context
import { AuthedUserContext } from '../../App'; // import the AuthedUserContext
import { Link } from 'react-router-dom';

const NavBar = ({ handleSignout }) => { // remove the user prop
  const user = useContext(AuthedUserContext); // set the value of user to the imported useContext hook bringing in the value from AuthedUserContext

```

And also our `Dashboard` component:

```jsx
// src/components/Dashboard/Dashboard.jsx
import { useContext } from 'react';
import { AuthedUserContext } from '../../App';

const Dashboard = () => { // remove the user prop
  const user = useContext(AuthedUserContext);

```

In both cases we are bringing in the `useContext` hook to access the values provided from our newly created `AuthedUserContext`. Since this value is updated whenever our state is updated via a signin or signout, the context is also passed to any components that use it.

Give it a try and if your components behave the same way as before, then congrats!! You are providing the values via `useContext`, and any future components wanting access to the `user` will be able to do the same.

Congrats! You have a great start to a fully authenticated application in React! 