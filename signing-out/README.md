# ![React Authentication JWT - Signing Out](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to add a method to their application that successfully implements signing out functionality.

## Signing Out

Our users now need to have the ability to signout of their session and our render components accordingly.  For this process, we really only need to create a method and attach it to a link.

### Making our `signout` service:

In `authService.js`, let's create the method that will allow users to signout:

```jsx
// src/services/authService.js
const signout = () => {
    localStorage.removeItem('token');
}

export {
  signup,
  signin,
  getUser,
  signout
}
```

Note that, with token-based authentication, we didn't even need to make a fetch request to our back-end. This is because token authentication is **stateless**, as Express keeps no records of the tokens it has issued.

### Implementing the `signout` method:

We can now add this method to the rest of our code and test it out.

```jsx
// App.jsx

  const handleLogout = () => {
    authService.signout()
    setUser(null)
  }

  return (
    <>
      <NavBar user={user} handleLogout={handleLogout} />
  .
  .
  .
```

In the above, we call the `signout` method and immediately set the value of the `user` in state to `null`.  We then pass the `handleLogout` function as a prop to be called by our link in the `NavBar`.

```jsx
// src/components/Navbar/Navbar.jsx
const NavBar = ({ user, handleLogout }) => { // grab the handleLogout prop
  return (
    <>
      {user ?
        <nav>
          <ul>
            <li>Welcome, {user.username}</li>
            <li><Link to="/">Home</Link></li>
            <li><Link to="" onClick={handleLogout}>LOG OUT</Link></li> // call handleLogout on the click of the link
          </ul>
        </nav>
      :
        <nav>
          <ul>
            <li><Link to="/signin">Sign In</Link></li>
            <li><Link to="/signup">Sign Up</Link></li>
          </ul>
        </nav>
      }
    </>
  )
}
```
Finally, we add the prop to a click handler.  When the user clicks, they are logged out and the token is removed from `localStorage`.