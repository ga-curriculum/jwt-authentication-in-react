# ![React Authentication with JWT - Creating an Authentication Gateway](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to add a navbar component to the React app which will act as an authentication gateway for users to interact with.


## Navbar display based on auth status

We want our navigation links to render according to whether a user is logged in or not:

**Logged in**: Display a greeting and a **Log Out** link.
**Not logged in**: Display **Log In** and **Sign up** links.

Thanks to our starter code, we're already passing user to the navbar as state.

A simple way to accomplish what we're aiming for is to use a ternary in our component return. 

In `NavBar.jsx`, add the following code:

```jsx
import { Link } from 'react-router-dom'

const NavBar = ({ user }) => {
  return (
    <>
      {user ?
        <nav>
          <ul>
            <li>Welcome, { user.username }</li>
            <li><Link to='/'>Dashboard</Link></li>
            <li><Link to=''>LOG OUT</Link></li>
          </ul>
        </nav>
      :
        <nav>
          <ul>
            <li><Link to='/login'>Log In</Link></li>
            <li><Link to='/signup'>Sign Up</Link></li>
          </ul>
        </nav>
      }
    </>
  )
}

export default NavBar
```

Based on the logic we have added to the NavBar, if a user exists in state they will see their name with a link to the "Dashboard".  If a user is not in state they will see links to signup or login.

We can extend this further to displaying a component based on the user state with React Router.

### Create our Components

We will create a `Dashboard` component to display for users that are logged in.  We will also create a `Landing` component for to display for non-logged in users.

In our components directory we will create both folders and files:

```bash
mkdir src/components/Dashboard src/components/Landing
touch src/components/Dashboard/Dashboard.jsx src/components/Landing/Landing.jsx
```

Add the following to your `Dashboard` component:
```jsx
// src/components/Dashboard.jsx
const Dashboard = ({ user }) => {
  return (
    <main>
      <h1>Welcome, {user.username}</h1>
      <p>This is the dashboard page where you, and only you, can see a dashboard of all of your things.</p>
    </main>
  )
}

export default Dashboard
```

Add the following to your `Landing` component:
```jsx
// src/components/Landing.jsx
const Landing = () => {
  return (
    <main>
      <h1>Hello, you are on the landing page for visitors.</h1>
      <h3>If you create a sign up, you will have the ability to log in and see your super secret dashboard.</h3>
    </main>
  )
}

export default Landing
```

Lastly, let's modify our `App.jsx` to import these components and use React Router to help us with conditionally rendering these components:

```jsx
// App.jsx
import { useState } from 'react';
import { Routes, Route } from 'react-router-dom';
import NavBar from './components/NavBar/NavBar';
import Landing from './components/Landing/Landing';
import Dashboard from './components/Dashboard/Dashboard';

const App = () => {
  const [user, setUser] = useState(null);

  return (
    <>
      <NavBar user={user} />
      <Routes>
        {
          user ?
            <Route path='/' element={<Dashboard user={user} />} />
            :
            <Route path='/' element={<Landing />} />
        }
      </Routes>
    </>
  )
}

export default App
```

You can experiment with displaying the components by changing the initial state of `user` from `null` to something like `{ username: 'Shawn Doe' }` and back to view the different templates.
