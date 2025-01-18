<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Conditionally Render in a Route</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to write routes that conditionally adjust what they render based on the user's authentication status.

## Building a landing page and a dashboard

Let's implement a new piece of functionality in our application: a landing page for visitors and a dashboard that we'll show to authenticated users. We'll build these as two different components and then conditionally render one when the user navigates to the `/` route depending on whether the user is authenticated.

### Creating our components

Let's start with the less complex component - the `Landing` component. This component will be displayed to users who are not authenticated. Start by creating a new directory for this component along with a new file:

```bash
mkdir src/components/Landing
touch src/components/Landing/Landing.jsx
```

Let's add some content to our `Landing` component:

```jsx
// src/components/Landing.jsx

const Landing = () => {
  return (
    <main>
      <h1>Hello, you are on the landing page for visitors.</h1>
      <p>Sign up now, or sign in to see your super secret dashboard!</p>
    </main>
  );
};

export default Landing;

```

Next, create the `Dashboard` component:

```bash
mkdir src/components/Dashboard
touch src/components/Dashboard/Dashboard.jsx
```

This component will eventually show a list of all the users that have created accounts in our application. For now though, let's display a message that welcomes the user by their username. This means we'll need to import the `UserContext` and use the `useContext()` hook to access the `user` context.

```jsx
// src/components/Dashboard/Dashboard.jsx

import { useContext } from 'react';

import { UserContext } from '../../contexts/UserContext';

const Dashboard = () => {
  const { user } = useContext(UserContext);

  return (
    <main>
      <h1>Welcome, {user.username}</h1>
      <p>
        This is the dashboard page where you can see a list of all the users.
      </p>
    </main>
  );
};

export default Dashboard;

```

## Add a new route

Finally, let's modify our `App` component. Start by importing these new components, along with the `UserContext` and `useContext()` hook to access the `user` context.

```jsx
// src/App.jsx

// Import useContext
import { useContext } from 'react';
import { Routes, Route } from 'react-router';

import NavBar from './components/NavBar/NavBar';
import SignUpForm from './components/SignUpForm/SignUpForm';
import SignInForm from './components/SignInForm/SignInForm';
// Import the Landing and Dashboard components
import Landing from './components/Landing/Landing';
import Dashboard from './components/Dashboard/Dashboard';

// Import the UserContext
import { UserContext } from './contexts/UserContext';

const App = () => {
  // Get the user
  const { user } = useContext(UserContext);

  // Return statement code here
};
```

With all the pieces in place, we can finally build the route! We'll use a ternary with the `user` context to conditionally render the `Dashboard` or `Landing` component within the `element` property of a `Route` component. This will allow us to render the appropriate component based on the user's authentication status.

```jsx
// src/App.jsx

const App = () => {
  const { user } = useContext(UserContext);

  return (
    <>
      <NavBar />
      <Routes>
        {/* Add the new `/` route! */}
        <Route path='/' element={user ? <Dashboard /> : <Landing /> } />
        <Route path='/sign-up' element={<SignUpForm />} />
        <Route path="/sign-in" element={<SignInForm />} />
      </Routes>
    </>
  );
};
```

## Link to the new route

Finally, let's make sure our users can get here easily by adding a couple new links to the `NavBar` component.

```jsx
// src/components/NavBar/NavBar.jsx

  return (
    <nav>
      {user ? (
        <ul>
          <li>Welcome, {user.username}</li>
          {/* The new link */}
          <li><Link to="/">Dashboard</Link></li>
          <li><Link to="/" onClick={handleSignOut}>Sign Out</Link></li>
        </ul>
      ) : (
        <ul>
          {/* Another new link */}
          <li><Link to="/">Home</Link></li>
          <li><Link to="/sign-in">Sign In</Link></li>
          <li><Link to="/sign-up">Sign Up</Link></li>
        </ul>
      )}
    </nav>
  );
```

Remember, users can get to this route regardless of whether they're signed in or not.

### Test it out!

Open your browser and navigate to `http://localhost:3000`. If you're signed in, you should see the `Dashboard` component. If you're signed out, you should see the `Landing` component.

> 🧠 Regardless of whether the user is signed in or not, they can navigate to the `/` route.
