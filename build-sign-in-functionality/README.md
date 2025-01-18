<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Build Sign-In Functionality</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create and implement a form in a React application that allows existing users to sign in by submitting their credentials and handling the response from a back-end.

## Signing in an existing user

We have established a pattern for creating a user from our React app to our back-end app. We can now take some of the principles established to help us log the user into our React app.

### Creating our sign in form

Let's create, import, and render the sign in form we will be using for this process:

```bash
mkdir src/components/SignInForm
touch src/components/SignInForm/SignInForm.jsx
```

Next, we'll add the code to our `SignInForm`. Notice how similar it is to the `SignUpForm` as you go through it:

```jsx
// src/components/SignInForm/SignInForm.jsx

import { useState, useContext } from 'react';
import { useNavigate } from 'react-router';

import { signIn } from '../../services/authService';

import { UserContext } from '../../contexts/UserContext';

const SignInForm = () => {
  const navigate = useNavigate();
  const { setUser } = useContext(UserContext);
  const [message, setMessage] = useState('');
  const [formData, setFormData] = useState({
    username: '',
    password: '',
  });

  const handleChange = (evt) => {
    setMessage('');
    setFormData({ ...formData, [evt.target.name]: evt.target.value });
  };

  const handleSubmit = async (evt) => {
    evt.preventDefault();
    try {
      // This function doesn't exist yet, but we'll create it soon.
      // It will cause an error right now
      const signedInUser = await signIn(formData);

      setUser(signedInUser);
      navigate('/');
    } catch (err) {
      setMessage(err.message);
    }
  };

  return (
    <main>
      <h1>Sign In</h1>
      <p>{message}</p>
      <form autoComplete='off' onSubmit={handleSubmit}>
        <div>
          <label htmlFor='email'>Username:</label>
          <input
            type='text'
            autoComplete='off'
            id='username'
            value={formData.username}
            name='username'
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label htmlFor='password'>Password:</label>
          <input
            type='password'
            autoComplete='off'
            id='password'
            value={formData.password}
            name='password'
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <button>Sign In</button>
          <button onClick={() => navigate('/')}>Cancel</button>
        </div>
      </form>
    </main>
  );
};

export default SignInForm;

```

### Import and render the `SignInForm` component

With the `SignInForm` component created, we can now import and render it in our `App` component when the user navigates to the `/sign-in` route:

```jsx
// App.jsx

import { Routes, Route } from 'react-router';

import NavBar from './components/NavBar/NavBar';
import SignUpForm from './components/SignUpForm/SignUpForm';
import SignInForm from './components/SignInForm/SignInForm';

const App = () => {

  return (
    <>
      <NavBar />
      <Routes>
        <Route path='/sign-up' element={<SignUpForm />} />
        <Route path="/sign-in" element={<SignInForm />} />
      </Routes>
    </>
  );
};

export default App;
```

Finally, add a new link in our the nav bar so users can navigate to the sign-in form:

```jsx
// src/components/NavBar/NavBar.jsx

  return (
    <nav>
      {user ? (
        <ul>
          <li>Welcome, {user.username}</li>
          <li><Link to="/" onClick={handleSignOut}>Sign Out</Link></li>
        </ul>
      ) : (
        <ul>
          {/* The new link! */}
          <li><Link to="/sign-in">Sign In</Link></li>
          <li><Link to="/sign-up">Sign Up</Link></li>
        </ul>
      )}
    </nav>
  );
```

Remember, our app will not work until we stub up and export the `signIn()` service function. We'll do that next.

## Add the `signIn()` service function

Our next step is to navigate to the `authService.js` service file and add a new `signIn()` function. This function will send the user's credentials to the back-end for verification and handle the response.

This function will be extremely similar to the `signUp()` function we created previously.

```javascript
// src/services/authService.js

const signIn = async (formData) => {
  try {
    const res = await fetch(`${BASE_URL}/sign-in`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData),
    });

    const data = await res.json();

    if (data.err) {
      throw new Error(data.err);
    }

    if (data.token) {
      localStorage.setItem('token', data.token);
      return JSON.parse(atob(data.token.split('.')[1])).payload;
    }

    throw new Error('Invalid response from server');
  } catch (err) {
    console.log(err);
    throw new Error(err);
  }
};
```

> 🚀 The `signUp()` and `signIn()` functions are extremely similar. It is so similar that you could likely refactor these two functions into a single function on your own!

### Test it out!

We can now try to sign in an existing user from our database. If everything works, you should be able to see your username in the nav after signing in!

With this functionality in place, we can now sign up, sign in, and sign out users. We have the basic functionality of a user authentication system in place!
