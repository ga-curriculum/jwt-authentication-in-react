# ![React Authentication JWT - Signing Out](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement log out functionality by clearing a JWT from local storage.

## Signing out

It's essential for users to have the option to log out of their session. To implement this, we need to create a method that handles the sign-out process. This method will then be linked to a sign-out button in our user interface. To effectively "sign out" all we need to do is delete the token so that it is not available in the browser for future requests to the server. Without a valid token, there is no access.

## Creating a `signout` service:

In `authService.js`, let's create the method that will allow users to sign out:

```jsx
// src/services/authService.js

const signout = () => {
  localStorage.removeItem('token');
};

export { signup, signin, getUser, signout };
```

> Note that, with token-based authentication, we didn't even need to make a fetch request to our back-end. This is because token authentication is **stateless**, as Express keeps no records of the tokens it has issued.

### Implementing the `signout` method:

We can now add this method to the rest of our code and test it out.

```jsx
// App.jsx

const handleSignout = () => {
  authService.signout()
  setUser(null)
}

return (
  <>
    <NavBar user={user} handleSignout={handleSignout} /> // pass the logout functionality to the navbar
.
.
.
```

In the above code, we pass the `handleSignout` function as a prop to be called by our "Sign Out" link in the `NavBar`.

When called, the `handleSignout` function uses the `signout` method to clear the token from local storage and then immediately resets the value of `user` in state to `null`.

```jsx
// src/components/Navbar/Navbar.jsx

const NavBar = ({ user, handleSignout }) => {
  return (
    <>
      { user ? (
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="" onClick={handleSignout}>Sign Out</Link></li>
          </ul>
        </nav>
      ) : (
        <nav>
          <ul>
            <li><Link to="/signin">Sign In</Link></li>
            <li><Link to="/signup">Sign Up</Link></li>
          </ul>
        </nav>
      )}
    </>
  )
}

```

Finally, we add the prop to a click handler. When the user clicks, they are logged out and the token is removed from `localStorage`.
