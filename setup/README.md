# ![React Authentication with JWT - Setup](./assets/hero.png)

## Setup

Open your Terminal application and navigate to your **`~/code/ga/lectures`** directory:

```bash
cd ~/code/ga/lectures
```

Create a new Vite project for your React app:

```bash
npm create vite@latest
```

Follow the prompts to create a new React project:

You'll be prompted to provide a project name. `vite-project` is the default, but when you start typing that default will go away. Choose a name that makes sense for this project; in this case, we'll call the app `react-auth-jwt`. Follow the steps to create the project:

- By default the project will be named `vite-project` let's change it to something that will make sense for this project;`react-auth-jwt`.
- Select a framework. Use the arrow keys to choose the `React` option and hit `Enter`.
- Select a variant. Again, use the arrow keys to choose `JavaScript` and hit `Enter`.

Navigate to your new project directory and install the necessary dependencies:

```bash
cd react-auth-jwt
npm i
```

Open the project's folder in your code editor:

```bash
code .
```

### Clear `App.jsx`

Open the `App.jsx` file in the `src` directory and replace the contents of it with the following:

```jsx
// src/App.jsx

import { useState } from 'react'
import { Routes, Route } from 'react-router-dom'
import NavBar from './components/NavBar/NavBar'
import Signup from './pages/Signup/Signup'
import Login from './pages/Login/Login'
import Landing from './pages/Landing/Landing'

const App = () => {
  const [user, setUser] = useState(null)

  return (
    <>
      <NavBar user={user} />
      <Routes>
        <Route path="/" element={<Landing user={user} />} />
        <Route
          path="/signup"
          element={<Signup />}
        />
        <Route
          path="/login"
          element={<Login />}
        />
      </Routes>
    </>
  )
}

export default App
```

Start the dev server by running `npm run dev` in your terminal and the go to `localhost:5173` in your browser.

Now let's get the file and folder structure set up for our project.

- Create a new folder called `components` in the `src` directory.
- Create a new folder called `pages` in the `src` directory.
- Create a new folder called `services` in the `src` directory.
- Inside of `services` create three new files:
  - authService.js
  - profileService.js
  - tokenService.js

- Create a `.env` file in the root of the project and add the following:

```bash
VITE_BACK_END_SERVER_URL='http://localhost:3000'
```

### Create our components

Since the focus of this lecture is on authentication, we're going to copy some starter code for our components and pages. 

- Create three new folders inside the `components` directory:
  - `LoginForm`
  - `NavBar`
  - `SignupForm`
- Inside the `LoginFrom` folder, create a file called `LoginForm.jsx` and add the following code to it:

```jsx
import { useState } from 'react'
import { Link } from 'react-router-dom'
import styles from './LoginForm.module.css'

const LoginForm = props => {
  const [formData, setFormData] = useState({
    email: '',
    pw: '',
  })

  const handleChange = e => {
    props.updateMessage('')
    setFormData({ ...formData, [e.target.name]: e.target.value })
  }

  const handleSubmit = async evt => {
    evt.preventDefault()
  }

  return (
    <form
      autoComplete="off"
      onSubmit={handleSubmit}
      className={styles.container}
    >
      <div className={styles.inputContainer}>
        <label htmlFor="email" className={styles.label}>Email</label>
        <input
          type="text"
          autoComplete="off"
          id="email"
          value={formData.email}
          name="email"
          onChange={handleChange}
        />
      </div>
      <div className={styles.inputContainer}>
        <label htmlFor="password" className={styles.label}>Password</label>
        <input
          type="password"
          autoComplete="off"
          id="password"
          value={formData.pw}
          name="pw"
          onChange={handleChange}
        />
      </div>
      <div>
        <button className={styles.button}>Log In</button>
        <Link to="/">
          <button>Cancel</button>
        </Link>
      </div>
    </form>
  )
}

export default LoginForm
```

- Inside the `LoginFrom` folder, create a second file called `LoginForm.module.css` and add the following code to it:

```css
.container {
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
  margin-bottom: 50px;
}

.label,
.button {
  margin-right: 0.5rem;
}

.inputContainer {
  margin-bottom: 1rem;
}
```

- Inside the `SignupFrom` folder, create a file called `SignupForm.jsx` and add the following code to it:
```jsx
import { useState } from 'react'
import { Link, useNavigate } from 'react-router-dom'
import styles from './SignupForm.module.css'
import * as authService from '../../services/authService'

const SignupForm = props => {
  const navigate = useNavigate()
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    password: '',
    passwordConf: '',
  })

  const handleChange = e => {
    props.updateMessage('')
    setFormData({
      ...formData,
      [e.target.name]: e.target.value,
    })
  }

  const handleSubmit = async e => {
    e.preventDefault()
    try {
      await authService.signup(formData)
      navigate('/')
    } catch (err) {
      props.updateMessage(err.message)
    }
  }

  const { name, email, password, passwordConf } = formData

  const isFormInvalid = () => {
    return !(name && email && password && password === passwordConf)
  }

  return (
    <form
      autoComplete="off"
      onSubmit={handleSubmit}
      className={styles.container}
    >
      <div className={styles.inputContainer}>
        <label htmlFor="name" className={styles.label}>Name</label>
        <input
          type="text"
          autoComplete="off"
          id="name"
          value={name}
          name="name"
          onChange={handleChange}
        />
      </div>
      <div className={styles.inputContainer}>
        <label htmlFor="email" className={styles.label}>Email</label>
        <input
          type="text"
          autoComplete="off"
          id="email"
          value={email}
          name="email"
          onChange={handleChange}
        />
      </div>
      <div className={styles.inputContainer}>
        <label htmlFor="password" className={styles.label}>Password</label>
        <input
          type="password"
          autoComplete="off"
          id="password"
          value={password}
          name="password"
          onChange={handleChange}
        />
      </div>
      <div className={styles.inputContainer}>
        <label htmlFor="confirm" className={styles.label}>
          Confirm Password
        </label>
        <input
          type="password"
          autoComplete="off"
          id="confirm"
          value={passwordConf}
          name="passwordConf"
          onChange={handleChange}
        />
      </div>
      <div className={styles.inputContainer}>
        <button disabled={isFormInvalid()} className={styles.button}>
          Sign Up
        </button>
        <Link to="/">
          <button>Cancel</button>
        </Link>
      </div>
    </form>
  )
}

export default SignupForm
```

- Inside the `SignupFrom` folder, create a second file called `SignupForm.module.css` and add the following code to it:

```css
.container {
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
  margin-bottom: 50px;
}

.label,
.button {
  margin-right: .5rem;
}

.inputContainer {
  margin-bottom: 1rem;
}
```

- Inside the `NavBar` folder, create a file called `NavBar.jsx`.


### Create our pages

- Create four new folders inside the `pages` directory:
  - `Landing`
  - `Login`
  - `Profiles`
  - `Signup`
- Inside the `Landing` folder, create a file called `Landing.jsx` and add the following code to it:

```jsx
import styles from './Landing.module.css'

const Landing = ({ user }) => {
  return (
    <main className={styles.container}>
      <h1>hello, {user ? user.name : 'friend'}</h1>
    </main>
  )
}

export default Landing
```

- Still inside the `Landing` folder, create a file called `Landing.module.css` and add the following code to it:

```css
.container {
  display: flex;
  align-items: center;
  flex-direction: column;
}
```

- Create a new file inside the `Login` folder called `Login.jsx`, and add this code:

```jsx
import { useState } from 'react'
import LoginForm from '../../components/LoginForm/LoginForm'
import styles from './Login.module.css'

const LoginPage = props => {
  const [message, setMessage] = useState([''])

  const updateMessage = msg => {
    setMessage(msg)
  }

  return (
    <main className={styles.container}>
      <h1>Log In</h1>
      <p>{message}</p>
      <LoginForm
        {...props}
        updateMessage={updateMessage}
      />
    </main>
  )
}

export default LoginPage
```

- Also create a css module in the same folder: `Login.module.css`, and add this:

```css
.container {
  display: flex;
  align-items: center;
  flex-direction: column;
}
```

- Create a new file in the `Signup` folder called `Signup.jsx` with this code:

```jsx
import { useState } from 'react'
import SignupForm from '../../components/SignupForm/SignupForm'
import styles from './Signup.module.css'

const Signup = props => {
  const [message, setMessage] = useState([''])

  const updateMessage = msg => {
    setMessage(msg)
  }

  return (
    <main className={styles.container}>
      <h1>Sign Up</h1>
      <p>{message}</p>
      <SignupForm {...props} updateMessage={updateMessage} />
    </main>
  )
}

export default Signup
```

- Then create a css module for that page: `Signup.module.css`. Add this css:

```css
.container {
  display: flex;
  align-items: center;
  flex-direction: column;
}
```


### Adding our auth service

We're going to provide the contents of `authService.js` here, but we'll cover the other two services in the lesson. Add this to the file:


```js
const BASE_URL = `${process.env.REACT_APP_BACK_END_SERVER_URL}/api/auth`

async function signup(user) {
  try {
    const res = await fetch(`${BASE_URL}/signup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(user),
    })
    const json = await res.json()
    if (json.token) {
      return json.token
    }
    if (json.err) {
      throw new Error(json.err)
    }
  } catch (err) {
    console.log(err)
    throw err
  }
}

export { signup, }

```

