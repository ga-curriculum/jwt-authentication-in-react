<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Conditionally Render Components</span>
</h1>

**Learning objective:** By the end of this lesson, learners will be able to implement a dynamic navigation bar in a React application that adjusts its content based on the user's authentication status.

### Creating our components

We will create a `Dashboard` component to display for users who are logged in. We will also create a `Landing` component for to display for new users or those who are not logged-in.

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
      <p>
        This is the dashboard page where you, and only you, can see a dashboard
        of all of your things.
      </p>
    </main>
  );
};

export default Dashboard;
```

Add the following to your `Landing` component:

```jsx
// src/components/Landing.jsx

const Landing = () => {
  return (
    <main>
      <h1>Hello, you are on the landing page for visitors.</h1>
      <h3>
        If you sign up for a new account, you will have the ability to sign in
        and see your super secret dashboard.
      </h3>
    </main>
  );
};

export default Landing;
```

Finally, let's modify our `App.jsx` to import these components and use React Router to help us with conditionally rendering these components:

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
        { user ? (
          <Route path="/" element={<Dashboard user={user} />} />
        ) : (
          <Route path="/" element={<Landing />} />
        )}
      </Routes>
    </>
  );
};

export default App;
```

You can experiment with displaying the components by changing the initial state of `user` from `null` to something like `{ username: 'Shawn Doe' }` and back to view the different templates.
