# ![React Authentication JWT - Signing In An Existing User](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a form that will log an existing user in.

## Signing In An Existing User

We have established a pattern for creating a user from our React app to our Express app.  We can now take some of the principles established to help us log the user into our React app.

### Creating our Signin Form

Let's create, import, and render the Signin form we will be using for this process:

```bash
mkdir src/components/SigninForm
touch src/components/SigninForm/SigninForm.jsx
```

Next, we'll add the code to our `SigninForm`.  You will recognize a lot of the same patterns used in the `SignupForm`:

```jsx
// SigninForm
import { useState } from 'react'
import { Link, useNavigate } from 'react-router-dom'
import * as authService from '../../services/authService' // import the authservice

const SigninForm = (props) => {
  const navigate = useNavigate(); // added this for navigation purposes
  const [message, setMessage] = useState(['']);
  const [formData, setFormData] = useState({
    username: '',
    password: '',
  });

  const updateMessage = (msg) => {
    setMessage(msg)
  };

  const handleChange = (e) => {
    updateMessage('')
    setFormData({ ...formData, [e.target.name]: e.target.value })
  }

  const handleSubmit = async (e) => {
    e.preventDefault()
    try {
      const user = await authService.signin(formData) // getting the user
      
      props.setUser(user);
      navigate('/')
    } catch (err) {
      updateMessage(err.message)
    }
  }

  return (
    <main>
      <h1>Log In</h1>
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
          />
        </div>
        <div>
          <button>Log In</button>
          <Link to='/'><button>Cancel</button></Link>
        </div>
      </form>
    </main>
  )
}

export default SigninForm

```

### Adding the `Signin` Service

Our next step is to navigate to the `authService.js` file and add the `signin` functionality:

```javascript
// authService.js

async function signin(user) {
  try {
    const res = await fetch(`${BACKEND_URL}/users/signin`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(user),
    })
    const json = await res.json()

    if (json.token) {
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

One thing to note with the above code is the `JSON.parse(atob(json.token.split('.')[1]));` line.  This line is essentially taking the JWT token that is sent back to us from our Express server and splitting it.  We are then storing the resulting object in a variable named `user` and returning it to what has called it.


### Importing and rendering our `Signin` form.

With all of the pieces in place we can render and test our form.  Let's do that in `App.jsx`:

```jsx
// App.jsx
.
.
import SigninForm from './components/SigninForm/SigninForm'

...

 return (
    <>
      <NavBar user={user} handleLogout={handleLogout} />
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

We can now try to signin an existing user from our database.  If everything works, we should be able to see the "Dashboard" page after a successful signin.  However, if we refresh the page our state is reverted to `null`.  We can fix tha with the use of `localStorage`.