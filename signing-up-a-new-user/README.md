# ![React Authentication with JWT - Signing Up A New User](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to use a controlled form t create a user by making an API call to their Express backend.

## Signing Up A New User

Now that we have logic in place to show certain components based on whether or not a user is authenticad in our app, we need to allow creation of a user.

### Creating our component

Let's create a component for our sign up form:

```bash
mkdir src/components/SignupForm
touch src/components/SignupForm/SignupForm.jsx
```

In our `SignupForm` component, we will add the form for our new user.  A user has two attributes:

- username
- password

Since our passwords are going to be hashed on the backend, we will only worry about plain text passwords in our front end. We will also add a passwordConfirm field to have a little frontend validation making sure the user matches the password they have created.

Add the following to `SignupForm.jsx`:

```jsx
// SignupForm.jsx
import { useState } from 'react'
import { Link, useNavigate } from 'react-router-dom'

const SignupForm = (props) => {
  const navigate = useNavigate()
  const [message, setMessage] = useState(['']);
  const [formData, setFormData] = useState({
    username: '',
    password: '',
    passwordConf: '',
  });

  const updateMessage = (msg) => {
    setMessage(msg);
  }

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  }

  const handleSubmit = async (e) => {
    e.preventDefault();
    updateMessage('');
    console.log(formData); // this line will print the form data to the console
  }

  const { username, password, passwordConf } = formData

  const isFormInvalid = () => {
    return !(username && password && password === passwordConf)
  }

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
          />
        </div>
        <div>
          <button disabled={isFormInvalid()}>Sign Up</button>
          <Link to='/'><button>Cancel</button></Link>
        </div>
      </form>
    </main>
  )
}

export default SignupForm
```

There's a lot to unpack in this component, so take a few minutes to read through the code and figure out what's going on. A few features include:

- Using the `disabled` property to prevent a user from submitting the form until the validation function confirms the request will have a chance of succeeding
- A `message` property in state that will handle future error messages from the server, such as the username already being taken
- Standard, controlled form functionality managing the state of the sign up form


### Adding our `SignupForm` to `App.jsx`:

Let's import our form to the `App` component and attach it to the "/signup" route:

```jsx
// App.jsx
import { useState } from 'react'
import { Routes, Route } from 'react-router-dom'
import NavBar from './components/NavBar/NavBar'
import SignupForm from './components/SignupForm/SignupForm' // import the SignupForm

.
.
.

return (
  <>
    <NavBar user={user} />
    <Routes>
      {
        user ?
          <Route path="/" element={<Dashboard user={user} />} />
        :
          <Route path="/" element={<Landing />} />
      }
        
      <Route path='/signup' element={<SignupForm />} /> // importing the signup form here
    </Routes>
  </>
)
```

Now that we have our `SignupForm` rendering when we navigate to `/signup`, we can play around with the form and try to add data.  

We have setup our `handleSubmit` function to log our `formData` to the console.  Try it out.  If you see your form data in the developer console you are ready to connect it to the state of the `App` component.

### Connecting our form to `App` state:

We want to make sure that our form submission modifies the state of our app component and mimics the process of signing in. Let's begin that process by passing `setUser` as a prop to our `SignupForm` component:

```jsx
// App.jsx
<Route path='/signup' element={<SignupForm setUser={setUser} />} />
```

We can now take that prop and use it to set the `user` state of our app in the `handleSubmit` function of our `SignupForm` component:

```jsx
// SignupForm.jsx
const SignupForm = (props) => {
.
.
.

  const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      props.setUser(formData) // this will modify the state in the App component
      navigate('/') // upon redirect you will see the "Dashboard" page 
    } catch (err) {
      updateMessage(err.message)
    }
  }
}
```

With this logic in place you should be able to fill the form out, submit, and be redirected to the "Dashboard" page.  If you refresh the page, the state will be reset to `null` and you will see the "Landing" page. The behavior lets you know that your form is one step closer to signing up a new user.

### Connecting our form to an AuthService

Now that our form is functioning, it is now time to connect the form to a fetch request that will sign a user up via our Express backend.  

We will write the logic for this call in an `authService` that will better organize our logic.

First, we'll create our `services` directory and nested `authService.js` file:

```bash
mkdir src/services
touch src/services/authService.js
```

Next, we'll define our `signin` method in the `authService.js`:

```javascript
// src/services/authService.js
const BACKEND_URL = "http://localhost:3000" // this is our Express API url

const signup = async (formData) => {
  try {
    const res = await fetch(`${BACKEND_URL}/users/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData),
    })
    const json = await res.json()
    if (json.err) {
      throw new Error(json.err)
    }
    return json
  } catch (err) {
    console.log(err)
    throw err
  }
}

export default {
  signup
}
```
The logic above makes a `POST` call to our Express server at `/users/signup`.  If we successfully sign up a user, that user object is returned to us.  If not, an error is thrown and we will see it in the console.

### Refactoring our BACKEND_URL

With a SPA like React, we want the backend and frontend to be as loosely coupled as possible. This means, instead of hard-coding our `localhost:3000` address into the codebase, we want to use an environment variable that can change depending on context.

When we deploy the app, all we'll need is to tell the React app where the backend is located, using an environment variable.

So, we need to create a `.env` file that will contain our url:

```bash
touch .env
```

`.env`:
```
VITE_EXPRESS_BACKEND_URL='http://localhost:3000'
```

If this variable name seems awkward, it's because [Vite's environment variables](https://vitejs.dev/guide/env-and-mode) are required to start with `VITE_` in order to be readable from the client application.

Be sure to also add `.env` to the list in your `.gitignore` file, so that our local version doesn't travel with the final, deployed app.

Now, we can refactor our authService to access this variable for use in all fetch calls:

```jsx
const BACKEND_URL = import.meta.env.VITE_EXPRESS_BACKEND_URL 
```

Now that our service is set up for sign in, we will modify the `handleSubmit` function in our `SignupForm` to use the authService function, and set the state to the returned user:

```jsx
// SignupForm.jsx
import * as authService from '../../services/authService'

.
.
.

const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      const newUserResponse = await authService.signup(formData)
      props.setUser(newUserResponse.user);
      navigate('/')
    } catch (err) {
      updateMessage(err.message)
    }
  }
```

## Cross Origin Resource Sharing

If you just tried out the code we added, you were likely disappointed by a "Failed to Fetch" message. 

The browser dev console would also have told you about a CORS policy that blocked the request.

By default, most servers (like Express) don't like getting requests from another origin, as they consider it a security risk. Who knows where this request is coming from?

So, we have to modify our Express back-end to allow for Cross Origin Resource Sharing requests, or CORS requests. Fortunately, the `cors` middleware library makes this incredibly simple.

First, install the `cors` library in your express project:

```bash
npm i cors
```

Then, in `server.js`, add the following to your middleware stack:

```js
const cors = require('cors');
.
.
.
app.use(cors())
```

You can read further in the [express documentation](https://expressjs.com/en/resources/middleware/cors.html) on how to configure CORS policy more securely, such as specifying the origin of your React app and only allowing requests from that address.

NOW, feel free to test the form by signing up a new user: it should work this time!

And with that, you have created a `SignupForm` that creates a brand new user from your server.

Congrats!!