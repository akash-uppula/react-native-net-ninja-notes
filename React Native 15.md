# 📱 React Native — Video #15 Notes

## Complete React Native Tutorial #15 — Logging Users In

Video #15 connects the forms from **Video #13** with the authentication system created in **Video #14**.

The two important operations are:

```text
Login
  ↓
login(email, password)

Register
  ↓
register(name, email, password)
```

Both functions are provided by `AuthContext`.

---

# 1. Our Authentication Architecture

Your current setup is:

```text
                  AuthContext
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
        login()     register()    logout()
          │            │
          ↓            ↓
       Appwrite     Appwrite
          │            │
          ↓            ↓
       Session      Account
          │
          ↓
         user
```

The screens don't need to directly handle Appwrite.

Instead:

```text
Login Screen
     ↓
useAuth()
     ↓
login()
     ↓
AuthContext
     ↓
Appwrite
```

and:

```text
Register Screen
     ↓
useAuth()
     ↓
register()
     ↓
AuthContext
     ↓
Appwrite
```

---

# 2. Login Function in `AuthContext`

Your current `login()` function is:

```jsx
const login = async (email, password) => {
  try {
    await account.createEmailPasswordSession({
      email,
      password,
    });

    const userData = await account.get();

    setUser(userData);

    return userData;
  } catch (error) {
    console.log("Login Error:", error);
    throw error;
  }
};
```

Let's understand it step by step.

---

# 3. `account.createEmailPasswordSession()`

```jsx
await account.createEmailPasswordSession({
  email,
  password,
});
```

This sends the user's credentials to Appwrite.

```text
email + password
       ↓
     login()
       ↓
Appwrite authentication
       ↓
   Session created
```

If the credentials are incorrect, Appwrite throws an error.

---

# 4. Get the Logged-In User

After creating the session:

```jsx
const userData = await account.get();
```

This gets the current Appwrite user.

Then:

```jsx
setUser(userData);
```

stores that user in the AuthContext.

So the complete process is:

```text
Email + Password
       ↓
createEmailPasswordSession()
       ↓
Session created
       ↓
account.get()
       ↓
User data
       ↓
setUser(userData)
```

---

# 5. Return the User

Your function also does:

```jsx
return userData;
```

This is useful because the screen that called `login()` can receive the result.

For example:

```jsx
const userData = await login(email, password);

console.log(userData);
```

---

# 6. Why `throw error`?

Your `catch` contains:

```jsx
catch (error) {
  console.log("Login Error:", error);
  throw error;
}
```

The first part:

```jsx
console.log("Login Error:", error);
```

logs the error.

But if you only did that, the calling screen wouldn't know that login failed.

That's why you also do:

```jsx
throw error;
```

This sends the error back to the Login screen.

So:

```text
Appwrite
   ↓
Error
   ↓
catch()
   ↓
throw error
   ↓
Login screen
```

---

# 7. Register Function

Your `register()` function is:

```jsx
const register = async (name, email, password) => {
  try {
    await account.create({
      userId: ID.unique(),
      email,
      password,
      name,
    });

    const userData = await login(email, password);

    return userData;
  } catch (error) {
    console.log("Register Error:", error);
    throw error;
  }
};
```

This handles **account creation + automatic login**.

---

# 8. `account.create()`

The first step is:

```jsx
await account.create({
  userId: ID.unique(),
  email,
  password,
  name,
});
```

This creates a new Appwrite account.

The information being sent is:

```text
name
email
password
userId
```

---

# 9. `ID.unique()`

You imported:

```jsx
import { account, ID } from "../lib/appwrite";
```

Then:

```jsx
userId: ID.unique(),
```

generates a unique ID for the new user.

Conceptually:

```text
ID.unique()
     ↓
Unique User ID
     ↓
Appwrite account
```

You don't manually need to create something like:

```text
user001
user002
user003
```

---

# 10. Automatically Login After Registration

This is an important part of your implementation:

```jsx
const userData = await login(email, password);
```

After creating the account, you're immediately calling the existing `login()` function.

So registration becomes:

```text
User enters:
Name
Email
Password
     ↓
register()
     ↓
account.create()
     ↓
Account created
     ↓
login(email, password)
     ↓
Session created
     ↓
account.get()
     ↓
setUser()
     ↓
Logged in
```

This avoids duplicating the login logic.

---

# 11. Why Reuse `login()`?

You could technically create a session directly inside `register()`.

But then you'd duplicate:

```jsx
account.createEmailPasswordSession(...)
```

Instead, your approach is cleaner:

```jsx
register()
   ↓
account.create()
   ↓
login()
   ↓
account.createEmailPasswordSession()
```

One function is responsible for login.

---

# 12. Login Screen

Your Login screen needs:

```jsx
const { login } = useAuth();
```

Then your state:

```jsx
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
```

---

# 13. `handleLogin()`

Create:

```jsx
const handleLogin = async () => {
  try {
    await login(email, password);

    router.replace("/books");
  } catch (error) {
    console.log("Login Error:", error);
  }
};
```

The important part:

```jsx
await login(email, password);
```

means:

> Wait until authentication finishes.

Only after successful login:

```jsx
router.replace("/books");
```

runs.

---

# 14. Login Flow

```text
User enters email
        ↓
User enters password
        ↓
Press Login
        ↓
handleLogin()
        ↓
login(email, password)
        ↓
AuthContext
        ↓
Appwrite
        ↓
Success?
     ↙       ↘
   Yes        No
    ↓          ↓
setUser()    catch()
    ↓
router.replace()
    ↓
Books
```

---

# 15. Register Screen

The Register screen works almost exactly the same way.

State:

```jsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
```

Get the function:

```jsx
const { register } = useAuth();
```

Then:

```jsx
const handleRegister = async () => {
  try {
    await register(name, email, password);

    router.replace("/books");
  } catch (error) {
    console.log("Register Error:", error);
  }
};
```

---

# 16. Register Flow

```text
Name
Email
Password
   ↓
Register button
   ↓
handleRegister()
   ↓
register()
   ↓
account.create()
   ↓
Account created
   ↓
login()
   ↓
Session created
   ↓
setUser()
   ↓
router.replace("/books")
```

---

# 17. Login vs Register

| Login                                  | Register                          |
| -------------------------------------- | --------------------------------- |
| Existing user                          | New user                          |
| `login()`                              | `register()`                      |
| Email + password                       | Name + email + password           |
| Creates session                        | Creates account + session         |
| `account.createEmailPasswordSession()` | `account.create()` then `login()` |

---

# 18. Complete `AuthContext.jsx`

For your notes, keep this version as the current implementation:

```jsx
import { createContext, useState } from "react";

import { account, ID } from "../lib/appwrite";

export const AuthContext = createContext(null);

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const login = async (email, password) => {
    try {
      await account.createEmailPasswordSession({
        email,
        password,
      });

      const userData = await account.get();

      setUser(userData);

      return userData;
    } catch (error) {
      console.log("Login Error:", error);
      throw error;
    }
  };

  const register = async (name, email, password) => {
    try {
      await account.create({
        userId: ID.unique(),
        email,
        password,
        name,
      });

      const userData = await login(email, password);

      return userData;
    } catch (error) {
      console.log("Register Error:", error);
      throw error;
    }
  };

  const logout = async () => {
    try {
      await account.deleteSession({
        sessionId: "current",
      });

      setUser(null);
    } catch (error) {
      console.log("Logout Error:", error);
      throw error;
    }
  };

  const value = {
    user,
    login,
    register,
    logout,
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
};

export default AuthProvider;
```

---

# 19. What `value` Provides

This part:

```jsx
const value = {
  user,
  login,
  register,
  logout,
};
```

makes all four available to components inside `AuthProvider`.

Therefore:

```text
AuthContext
    │
    ├── user
    ├── login()
    ├── register()
    └── logout()
```

A Login screen can use:

```jsx
const { login } = useAuth();
```

A Register screen:

```jsx
const { register } = useAuth();
```

A Profile screen:

```jsx
const { user, logout } = useAuth();
```

---

# 20. Keep Appwrite Logic Inside AuthContext

This is an important architectural decision.

### ❌ Don't do this in Login:

```jsx
await account.createEmailPasswordSession({
  email,
  password,
});
```

### ✅ Do this:

```jsx
await login(email, password);
```

And let AuthContext handle:

```jsx
account.createEmailPasswordSession();
account.get();
account.create();
account.deleteSession();
```

This keeps the screens simple.

---

# 21. Screen vs Context Responsibilities

### Login Screen

```text
UI
 ↓
Get email/password
 ↓
Call login()
 ↓
Navigate
```

### Register Screen

```text
UI
 ↓
Get name/email/password
 ↓
Call register()
 ↓
Navigate
```

### AuthContext

```text
Authentication logic
 ↓
Call Appwrite
 ↓
Update user state
```

### Appwrite

```text
Backend
 ↓
Accounts
 ↓
Sessions
```

---

# 22. Keep Keyboard Dismissal

From Video #13, you can continue using:

```jsx
import { Keyboard, TouchableWithoutFeedback } from "react-native";
```

Then:

```jsx
<TouchableWithoutFeedback onPress={Keyboard.dismiss}>
  <View>{/* Login/Register form */}</View>
</TouchableWithoutFeedback>
```

This lets the user tap outside the input to close the keyboard.

---

# 23. Loading State

Because both login and registration are asynchronous, you can eventually add:

```jsx
const [loading, setLoading] = useState(false);
```

For Login:

```jsx
const handleLogin = async () => {
  try {
    setLoading(true);

    await login(email, password);

    router.replace("/books");
  } catch (error) {
    console.log(error);
  } finally {
    setLoading(false);
  }
};
```

This is useful to prevent multiple requests while Appwrite is processing.

---

# ⭐ Video #15 — Must Remember

### Login

```jsx
const { login } = useAuth();

await login(email, password);
```

### Register

```jsx
const { register } = useAuth();

await register(name, email, password);
```

### Appwrite Login

```jsx
await account.createEmailPasswordSession({
  email,
  password,
});
```

### Get current user

```jsx
const userData = await account.get();
```

### Store user

```jsx
setUser(userData);
```

### Create account

```jsx
await account.create({
  userId: ID.unique(),
  email,
  password,
  name,
});
```

### Automatic login after registration

```jsx
const userData = await login(email, password);
```

### Navigate after successful authentication

```jsx
router.replace("/books");
```

### Pass errors back to the screen

```jsx
throw error;
```

---

# 🧠 Final Mental Model

The most important thing from **Video #15** is understanding the complete connection:

```text
                 LOGIN
                   │
        email + password
                   ↓
             handleLogin()
                   ↓
              useAuth()
                   ↓
               login()
                   ↓
             AuthContext
                   ↓
        createEmailPasswordSession()
                   ↓
               Appwrite
                   ↓
             account.get()
                   ↓
              setUser()
                   ↓
            router.replace()
                   ↓
                 HOME
```

And registration:

```text
                REGISTER
                   │
          name + email + password
                   ↓
            handleRegister()
                   ↓
              useAuth()
                   ↓
             register()
                   ↓
           account.create()
                   ↓
           Account created
                   ↓
        login(email, password)
                   ↓
       createEmailPasswordSession()
                   ↓
             account.get()
                   ↓
              setUser()
                   ↓
            router.replace()
                   ↓
                 HOME
```

### 🔑 The key idea

**The form collects the data → AuthContext handles authentication → Appwrite handles the backend → `user` stores the authenticated user → Router takes the user into the app.**

This is the foundation for the authentication system you'll continue building in the next videos.
