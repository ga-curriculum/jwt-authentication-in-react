# ![React Authentication with JWT - Creating an Authentication Gateway](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to tktk


## Navbar display based on auth status

We want our navigation links to render according to whether a user is logged in or not:

**Logged in**: Display a greeting and a **Log Out** link.
**Not logged in**: Display **Log In** and **Sign up** links.

Thanks to our starter code, we're already passng user to the navbar as state.

A simple way to accomplish what we're aiming for is to use a ternary in our component return. 

In `NavBar.jsx`, add the following code:

```jsx
import { Link } from 'react-router-dom'

const NavBar = ({ user, handleLogout }) => {
  return (
    <>
      {user ?
        <nav>
          <ul>
            <li>Welcome, {user.name}</li>
            {/* <li><Link to="/profiles">Profiles</Link></li> */}
            {/* <li><Link to="" onClick={handleLogout}>LOG OUT</Link></li> */}
          </ul>
        </nav>
      :
        <nav>
          <ul>
            <li><Link to="/login">Log In</Link></li>
            <li><Link to="/signup">Sign Up</Link></li>
          </ul>
        </nav>
      }
    </>
  )
}

export default NavBar

```


