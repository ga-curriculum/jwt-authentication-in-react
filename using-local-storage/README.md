# ![React Authentication JWT - Using `localStorage`](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to store their JWT token in `localStorage` to help persist a session in their React App.

## Using `localStorage`

We have the ability to sign in to our application, but if the page is refreshed we lose the state containing the signed-in user.  We can use `localStorage` in our browser to make our JWT token available to us regardless of page refresh.  Let's add that logic to our functionality.


### Modifying our `signin` service.

We can set the JWT token given to us in the sign in process within our `signin` service.  This will ensure that any time a user signs in to our app, the token is set until we do something to clear the storage.

Modify the `signin` service function to the following:

```jsx
// src/services/authService.js
const signin = async (user) {
  try {
    const res = await fetch(`${BACKEND_URL}/users/signin`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(user),
    })
    const json = await res.json()

    if (json.token) {
      localStorage.setItem('token', json.token); // add this line to store the JWT token in localStorage

      const user = JSON.parse(atob(json.token.split('.')[1]));
      
      return user
    }
    if (json.err) {
      throw new Error(json.err)
    }
  } catch (err) {
    console.log(err)
    throw err
  }
}
```

Add the same logic to our `signup` function as well, so that users authenticated by signing up will also remain signed in.

### Create a method that will check for a token

With our logic to add a token to `localStorage` in place, we can now write and implement logic to check for that token.  First, we'll add the method in the `authService.js` file, making sure to also add it to our list of exports:

```javascript
// src/services/authService.js
.
.
.
const getUser = () =>  {
  
  const token = localStorage.getItem('token');

  if (!token) return null;

  const user = JSON.parse(atob(token.split('.')[1]));

  return user;
}

export {
  signup,
  signin,
  getUser
}
```

The `getUser` function will check `localStorage` for a token.  If one does not exsist, `null` will be returned.  If one does exist and it has a user, the `getUser` function will return that user.

### Add logic to call this service

We can now bring the existing service into our `App.jsx` component to set the default value of our user in state:

`App.jsx`:
```jsx
import * as authService from '../src/services/authService'; // import the authservice

const App = () => {
  const [user, setUser] = useState(authService.getUser()); // using the method from authservice

.
.
.
```

After we sign a user in we can refresh the page and see that our session still persists!

We will also be able to include this token in future `fetch` requests that require an authenticated user.