# ![React Authentication JWT - Signing In An Existing User](./assets/hero.png)

**Learning objective:** By the end of this lesson, learners will be able to create and implement a form in a React application that allows existing users to log in by submitting their credentials and handling the response from an Express backend.

## Signing in an existing user

We have established a pattern for creating a user from our React app to our Express app. We can now take some of the principles established to help us log the user into our React app.

### Creating our sign in form

Let's create, import, and render the Sign In form we will be using for this process:

```bash
mkdir src/components/SigninForm
touch src/components/SigninForm/SigninForm.jsx
```

Next, we'll add the code to our `SigninForm`. You will recognize a lot of the same patterns used in the `SignupForm`:

```jsx
// SigninForm

import { useState } from 'react';
import { Link, useNavigate } from 'react-router-dom';
import * as authService from '../../services/authService'; // import the authservice

const SigninForm = (props) => {
  const navigate = useNavigate(); // added this for navigation purposes
  const [message, setMessage] = useState(['']);
  const [formData, setFormData] = useState({
    username: '',
    password: '',
  });

  const updateMessage = (msg) => {
    setMessage(msg);
  };

  const handleChange = (e) => {
    updateMessage('');
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const user = await authService.signin(formData); // TODO build signin service function

      props.setUser(user);
      navigate('/');
    } catch (err) {
      updateMessage(err.message);
    }
  };

  return (
    <main>
      <h1>Log In</h1>
      <p>{message}</p>
      <form autoComplete="off" onSubmit={handleSubmit}>
        <div>
          <label htmlFor="email">Username:</label>
          <input
            type="text"
            autoComplete="off"
            id="username"
            value={formData.username}
            name="username"
            onChange={handleChange}
          />
        </div>
        <div>
          <label htmlFor="password">Password:</label>
          <input
            type="password"
            autoComplete="off"
            id="password"
            value={formData.password}
            name="password"
            onChange={handleChange}
          />
        </div>
        <div>
          <button>Log In</button>
          <Link to="/">
            <button>Cancel</button>
          </Link>
        </div>
      </form>
    </main>
  );
};

export default SigninForm;
```

## Adding the `Signin` Service

Our next step is to navigate to the `authService.js` file and add the `signin` functionality:

```javascript
// authService.js

const signin = async (user) => {
  try {
    const res = await fetch(`${BACKEND_URL}/users/signin`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(user),
    });
    const json = await res.json();

    if (json.error) {
      throw new Error(json.error);
    }

    if (json.token) {
      const user = JSON.parse(atob(json.token.split('.')[1]));
      return user;
    }
  } catch (err) {
    console.log(err);
    throw err;
  }
};

export { signup, signin };
```

One thing to note with the above code is the `JSON.parse(atob(json.token.split('.')[1]));` line.

This part of the code splits the JWT string into its **_three components_** using the dot as a delimiter and selects the second element (index 1), which is the payload containing the user data.

> A JWT is composed of three parts: a header, a payload, and a signature. These parts are encoded in Base64Url format and are separated by `.` dots . The header contains the token type and the hashing algorithm used, the payload contains the claims (data about the user and other metadata), and the signature is used to verify the token.

### Importing and rendering our `Signin` form.

With all of the pieces in place we can render and test our signin form. Let's do that in `App.jsx`:

```jsx
// App.jsx
.
.
import SigninForm from './components/SigninForm/SigninForm'

...

 return (
    <>
      <NavBar user={user} handleSignout={handleSignout} />
      <Routes>
        {
          user ?
          <Route path='/' element={<Dashboard user={user} />} />
          :
          <Route path='/' element={<Landing />} />
        }

        <Route path='/signup' element={<SignupForm setUser={setUser} />} />
        <Route path='/signin' element={<SigninForm setUser={setUser} />} />
      </Routes>
    </>
  )
```

We can now try to sign in an existing user from our database. If everything works, we should be able to see the "Dashboard" page after a successful sign in. However, if we refresh the page our state is reverted to `null`.

Our goal is to ultimately store the authentication token in a place our React app will always be able to find it, even after a refresh.

We'll do that with the help of `localStorage`.
