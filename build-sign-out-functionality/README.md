<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Build Sign-Out Functionality</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement sign-out functionality in an React application, allowing users to log off.

## Sign out

Now that our users can sign up and stay signed in, there's no way for them to sign out. Let's fix that next.

This is simpler than the other functionality we've implemented so far, as we only need to remove the token from `localStorage` and clear the `user` state to sign a user out.

We don't even need to tell the back-end the user is signing out since it's not maintaining any state regarding the currently signed-in users.

### Add the sign out UI

We'll start by adding a **Sign Out** link to the `NavBar` component. This link will eventually call a function to sign the user out.

```jsx
// src/components/Navbar/Navbar.jsx

  return (
    <nav>
      {user ? (
        <ul>
          <li>Welcome, {user.username}</li>
          {/* Add the Sign Out link */}
          <li><Link to="/">Sign Out</Link></li>
        </ul>
      ) : (
        <ul>
          <li><Link to="/sign-up">Sign Up</Link></li>
        </ul>
      )}
    </nav>
  );
```

When the user clicks this link, we want to take them to the home page and sign them out. We'll need a function to accomplish this.

Remember, because we're using context to manage our user state, we can update the user state inside this component. We'll just need to get the `setUser()` function from the `UserContext` first so it's available in this component.

```jsx
// src/components/Navbar/Navbar.jsx

const NavBar = () => {
  // Get the setUser function from the UserContext
  const { user, setUser } = useContext(UserContext);

  // Add the handleSignOut function
  const handleSignOut = () => {
    // Clear the token from localStorage
    localStorage.removeItem('token');
    // Clear the user state
    setUser(null);
  };

  return (
    <nav>
      {user ? (
        <ul>
          <li>Welcome, {user.username}</li>
          {/* Call the handleSignOut function on a click */}
          <li><Link to="/" onClick={handleSignOut}>Sign Out</Link></li>
        </ul>
      ) : (
        <ul>
          <li><Link to="/sign-up">Sign Up</Link></li>
        </ul>
      )}
    </nav>
  );
};
```

See the comments in the code above for an overview of the changes made to the `NavBar` component to implement this functionality.

### Test it out!

Signed-in users should now be able to sign out by clicking the **Sign Out** link in the `NavBar` component.
