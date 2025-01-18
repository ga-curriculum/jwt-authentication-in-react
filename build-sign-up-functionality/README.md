<h1>
  <span class="headline">JWT Authentication in React</span>
  <span class="subhead">Build Sign-Up Functionality</span>
</h1>

**Learning objective:** By the end of this lesson, learners will be able to develop and integrate a controlled form in React to register new users, including handling form submissions and making API calls to a back-end for user creation.

## Signing up a new user

Let's start by making sure new users can sign up in our application. This will involve creating a form where users can input their details, and then sending this information to our back-end server.

### Creating our component

Let's create a component for our sign up form:

```bash
mkdir src/components/SignUpForm
touch src/components/SignUpForm/SignUpForm.jsx
```

In our `SignUpForm` component, we will add the form for our new user. A user has two attributes:

- username
- password

This password will be hashed on the back-end, so we handle it as plain text here. Additionally, we will include a password confirmation field to add a layer of client-side validation.

Add the following to `SignUpForm.jsx`:

```jsx
// SignUpForm.jsx

import { useState } from 'react';
import { useNavigate } from 'react-router';

const SignUpForm = () => {
  const navigate = useNavigate();
  const [message, setMessage] = useState('');
  const [formData, setFormData] = useState({
    username: '',
    password: '',
    passwordConf: '',
  });

  const { username, password, passwordConf } = formData;

  const handleChange = (evt) => {
    setMessage('');
    setFormData({ ...formData, [evt.target.name]: evt.target.value });
  };

  const handleSubmit = async (evt) => {
    evt.preventDefault();
    console.log(formData); // this line will print the form data to the console
  };

  const isFormInvalid = () => {
    return !(username && password && password === passwordConf);
  };

  return (
    <main>
      <h1>Sign Up</h1>
      <p>{message}</p>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor='username'>Username:</label>
          <input
            type='text'
            id='name'
            value={username}
            name='username'
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label htmlFor='password'>Password:</label>
          <input
            type='password'
            id='password'
            value={password}
            name='password'
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <label htmlFor='confirm'>Confirm Password:</label>
          <input
            type='password'
            id='confirm'
            value={passwordConf}
            name='passwordConf'
            onChange={handleChange}
            required
          />
        </div>
        <div>
          <button disabled={isFormInvalid()}>Sign Up</button>
          <button onClick={() => navigate('/')}>Cancel</button>
        </div>
      </form>
    </main>
  );
};

export default SignUpForm;
```

There's a lot to unpack in this component, so take a few minutes to read through the code and figure out what's going on. Note these features:

- Standard, controlled form functionality where form data is handled by the state within the component
- Using the `disabled` property. This attribute can be dynamically set based on certain conditions. For instance, you could disable the submission button until the user fills all the fields correctly, and the passwords entered in both password fields match.
- A `message` property in state that will handle future error messages from the server, such as the username already being taken. Server interactions can result in various responses, and handling these appropriately enhances user experience.

### Adding our `SignUpForm` to `App.jsx`

Let's import the `SignUpForm` component into the `App` component and attach it to the `/sign-up` route, replacing our existing heading:

```jsx
// src/App.jsx

import { Routes, Route } from 'react-router'; // Import React Router

import NavBar from './components/NavBar/NavBar';
// Import the SignUpForm component
import SignUpForm from './components/SignUpForm/SignUpForm';

const App = () => {

  return (
    <>
      <NavBar />
      {/* Add the Routes component to wrap our individual routes*/}
      <Routes>
        <Route path='/sign-up' element={<SignUpForm />} />
      </Routes>
    </>
  );
};

export default App;

```

### Adding a link to the `/sign-up` route

To navigate to the `/sign-up` route, we need to add a link to the `NavBar` component. We can use the `Link` component from `react-router` to create a link to the `/sign-up` route:

```jsx
// src/components/NavBar/NavBar.jsx

import { Link } from 'react-router';

const NavBar = () => {
  return (
    <nav>
      <ul>
        <li><Link to='/sign-up'>Sign Up</Link></li>
      </ul>
    </nav>
  );
};

export default NavBar;
```

Now that we have our `SignUpForm` rendering when we navigate to `/sign-up`, we can play around with the form and try to add data.

We have setup our `handleSubmit` function to log our `formData` to the console when the form is submitted. Try it out. If you see your form data in the dev console you are ready to pass the form data to the a new service function that will send the data to the server.

## Pass the sign-up form's data to a service function

We will write the logic for this call in an auth service file that will better organize our logic.

First, we'll create a `services` directory and a nested `authService.js` file:

```bash
mkdir src/services
touch src/services/authService.js
```

Next, we'll define our `signUp` method in the `authService.js`:

```javascript
// src/services/authService.js

// Use the `VITE_BACK_END_SERVER_URL` environment variable to set the base URL.
// Note the `/auth` path added to the server URL that forms the base URL for
// all the requests in this service.
const BASE_URL = `${import.meta.env.VITE_BACK_END_SERVER_URL}/auth`;

const signUp = async (formData) => {
  try {
    const res = await fetch(`${BASE_URL}/sign-up`, {
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

export {
  signUp,
};

```

The logic above makes a `POST` request to our back-end server at `/auth/sign-up`. If we successfully sign up a user, then this function should return the payload from a token. If not, an error is thrown and we will see it in the console.

Let's break down this code specifically:

```javascript
    if (data.token) {
      localStorage.setItem('token', data.token);
      return JSON.parse(atob(data.token.split('.')[1])).payload;
    }
```

If there is a token in the response, then we:

- Store the token (`data.token`) in local storage with the key `token`.
- Return the payload from the token. Let's break down how this happens working from the first operation to the last:
  - `data.token.split('.')[1]` - Split the token into three parts using the `.` character. We're only interested in the second part of the token (the payload) so we select the second element of the array.
  - `atob()` - Decode this part of the token from base64 (an encoded - not encrypted - string) into human readable text.
  - `JSON.parse().payload` - Parse the decoded payload into a JavaScript object and get the specific data we attached in the back-end - the user payload data.

If everything goes well, an object that looks like `{ username: 'user', _id: '6785cc89a998cb8ea1d14725' }` will be returned from this function.

> 🧠 A JWT is composed of three parts: a header, a payload, and a signature. These parts are encoded in Base64Url format and are separated by `.` dots . The header contains the token type and the hashing algorithm used, the payload contains the claims (data about the user and other metadata), and the signature is used to verify the token.

## Using the `signUp()` authentication service

Now that our service function is built, we'll return to the `SignUpFrom` component and modify the `handleSubmit()` function in our `SignUpForm` to use the authService function.

Start with the import:

```jsx
// SignUpForm.jsx

// Existing imports
import { useState } from 'react';
import { useNavigate } from 'react-router';

// Import the signUp() function from the authService
import { signUp } from '../../services/authService';
```

Then use it in the `handleSubmit()` function:

```jsx
  const handleSubmit = async (evt) => {
    evt.preventDefault();
    try {
      const newUser = await signUp(formData);
      console.log(newUser);
    } catch (err) {
      setMessage(err.message);
    }
  };
```

Take note of the `try...catch` block in the `handleSubmit()` function. If an error occurs in the `signUp()` function, the error message will be stored in the `message` state, which is displayed to the user in the form.

This is where we might typically put the new user data into state, but instead, we're going to cover a new way to manage special pieces of state that need to exist throughout our application.
