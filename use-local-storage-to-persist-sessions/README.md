<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Use <code>localStorage</code> to Persist Sessions</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to store a JWT token in `localStorage` to persist user sessions in their React application.

## Using `localStorage`

Users can now sign up to use our application, but if they refresh the page, the app will forget who they are.

We've already put a new user's token in local storage in our `signUp()` service function - as shown below:

```javascript
    if (data.token) {
      localStorage.setItem('token', data.token);
      return JSON.parse(atob(data.token.split('.')[1])).payload;
    }
```

However, we never read from local storage to get any token data after that.

Let's change that so the user can refresh the page and stay signed in!

## Get the initial `user` state from a token in `localStorage`

Let's implement logic to check for a token in `localStorage`, and if it exists, we will extract the user data from it. We'll do this in the same component that sets the initial user state in the `src/contexts/UserContext.jsx` file.

```jsx
// src/contexts/UserContext.jsx

import { createContext, useState } from 'react';

const UserContext = createContext();

// Add the new getUserFromToken function
const getUserFromToken = () => {
  const token = localStorage.getItem('token');

  if (!token) return null;

  return JSON.parse(atob(token.split('.')[1])).payload;
};

function UserProvider({ children }) {
  // call getUserFromToken() to get our initial user state
  const [user, setUser] = useState(getUserFromToken());

  const value = { user, setUser };

  return (
    <UserContext.Provider value={value}>
      {children}
    </UserContext.Provider>
  );
};

export { UserProvider, UserContext };
```

In the new `getUserFromToken()` function, we get the token from `localStorage`.

Recall the code we used to put the token into `localStorage` - specifically the value `'token'`:

```javascript
localStorage.setItem('token', data.token);
```

We use the same value (`'token'` in `localStorage.getItem('token')`) to retrieve the item from local storage.

If the token does not exist, we return `null`. If the token does exist, we use the same code we used to get the user data from the token previously in the `signUp()` service function:

```javascript
JSON.parse(atob(data.token.split('.')[1])).payload;
```

This allows us to get the user data we attached to the `payload` object on the back-end when it created the token. React will use data as the initial state for our user.

> 🚀 In a more complex app with more extensive interactions with tokens, we would want to house all the code that works with tokens in a single file to be better organized and reused more easily.
>
> We won't reuse this code in this app, and some of the benefits of stronger organization would be offset by slightly increased complexity. However, it's a good idea to keep this in mind as you build anything more complex than this example.

### Test it out

If you've previously signed up as a user, you should now be able to refresh the page in the browser and see that you're automatically signed in!
