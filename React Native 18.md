# 📱 React Native — Video #18 Notes

## Complete React Native Tutorial #18 — Initial Auth State

Video #18 solves an important problem in the authentication system we built in Videos **14–17**:

> **How do we know if the user is already logged in when the app starts?**

So far, we have:

```text
Register → Login → user → Logout
```

But when the app starts again, our React state initially does:

```jsx
const [user, setUser] = useState(null);
```

So React initially thinks:

```text
user = null
```

even if Appwrite already has a valid session.

Video #18 introduces **initial authentication state** to solve this.

---

# 1. The Problem

Imagine this:

```text
User logs in
     ↓
Appwrite session created
     ↓
user = userData
     ↓
User closes app
     ↓
Opens app again
```

Our React state starts again with:

```jsx
const [user, setUser] = useState(null);
```

So:

```text
React:
"user = null"

Appwrite:
"Actually, this user still has a valid session."
```

There is a mismatch.

We need to check Appwrite when the application starts.

---

# 2. What Is Initial Auth State?

**Initial auth state** means:

> When the application first starts, check whether a user is already authenticated.

The flow becomes:

```text
App starts
   ↓
Check Appwrite
   ↓
Is there an active session?
   ↓
 ┌───────────────┐
 ↓               ↓
YES              NO
 ↓                ↓
account.get()    user = null
 ↓
setUser()
```

---

# 3. Use `useEffect`

We need something that runs when the AuthProvider starts.

Import:

```jsx
import {
  createContext,
  useEffect,
  useState,
} from "react";
```

Then:

```jsx
useEffect(() => {
  // Check authentication
}, []);
```

The empty dependency array:

```jsx
[]
```

means the effect runs when the component initially mounts.

---

# 4. Create an `init()` Function

A clean way to organize the authentication check is:

```jsx
const init = async () => {
  // Check current Appwrite user
};
```

Then:

```jsx
useEffect(() => {
  init();
}, []);
```

So:

```text
AuthProvider starts
       ↓
useEffect()
       ↓
init()
       ↓
Check Appwrite
```

---

# 5. Use `account.get()`

Inside `init()`:

```jsx
const init = async () => {
  try {
    const userData = await account.get();

    setUser(userData);
  } catch (error) {
    setUser(null);
  }
};
```

The important part is:

```jsx
const userData = await account.get();
```

`account.get()` asks Appwrite:

> Who is the currently authenticated user?

If a valid session exists, Appwrite returns the user.

---

# 6. If the User Is Logged In

Suppose the user already has an Appwrite session.

Then:

```jsx
const userData = await account.get();
```

returns the user.

Then:

```jsx
setUser(userData);
```

updates our React state.

Flow:

```text
App starts
   ↓
init()
   ↓
account.get()
   ↓
Valid session
   ↓
User data returned
   ↓
setUser(userData)
   ↓
user is logged in
```

---

# 7. If the User Is NOT Logged In

If there is no active Appwrite session:

```jsx
await account.get();
```

will fail.

Therefore:

```jsx
catch (error) {
  setUser(null);
}
```

keeps the user unauthenticated.

Flow:

```text
App starts
   ↓
init()
   ↓
account.get()
   ↓
No session
   ↓
Error
   ↓
catch()
   ↓
setUser(null)
```

---

# 8. Why `try...catch`?

Because `account.get()` can fail when there isn't an authenticated session.

So don't do:

```jsx
const init = async () => {
  const userData = await account.get();

  setUser(userData);
};
```

without handling the error.

Instead:

```jsx
const init = async () => {
  try {
    const userData = await account.get();

    setUser(userData);
  } catch (error) {
    setUser(null);
  }
};
```

---

# 9. Important Difference Between `login()` and `init()`

You now have two places using:

```jsx
account.get();
```

but they serve different purposes.

### `login()`

Used when the user actively logs in:

```text
User enters credentials
        ↓
login()
        ↓
createEmailPasswordSession()
        ↓
account.get()
        ↓
setUser()
```

### `init()`

Used when the app starts:

```text
App starts
    ↓
init()
    ↓
account.get()
    ↓
setUser()
```

So:

```text
login() → establish authentication

init() → restore authentication
```

---

# 10. Authentication Restoration

This is often called **restoring the authentication state**.

The user doesn't have to log in every time they open the app.

For example:

```text
Day 1
User logs in
   ↓
Appwrite session

Close app

Day 2
Open app
   ↓
init()
   ↓
account.get()
   ↓
Existing session
   ↓
setUser()
   ↓
User remains logged in
```

---

# 11. Update Your `AuthContext`

Your current AuthContext can now be extended like this:

```jsx
import {
  createContext,
  useEffect,
  useState,
} from "react";

import { account, ID } from "../lib/appwrite";

export const AuthContext = createContext(null);

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const init = async () => {
    try {
      const userData = await account.get();

      setUser(userData);
    } catch (error) {
      setUser(null);
    }
  };

  useEffect(() => {
    init();
  }, []);

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

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
};

export default AuthProvider;
```

---

# 12. Should `init()` Be Inside `value`?

Usually, **no**.

You don't need to expose:

```jsx
init
```

to your screens.

It's an internal authentication initialization function.

So keep:

```jsx
const init = async () => {
  ...
};
```

but don't add:

```jsx
init,
```

to:

```jsx
const value = {
  user,
  login,
  register,
  logout,
};
```

The screens don't need to manually initialize authentication.

---

# 13. `useEffect` Dependency Array

This:

```jsx
useEffect(() => {
  init();
}, []);
```

is intended to run once when `AuthProvider` mounts.

The:

```jsx
[]
```

is the dependency array.

Think:

```text
AuthProvider mounts
        ↓
useEffect()
        ↓
init()
```

---

# 14. The New Problem: Loading

There is one subtle problem now.

At startup:

```jsx
const [user, setUser] = useState(null);
```

Initially, React doesn't know whether:

```text
user is actually logged out
```

or:

```text
Appwrite check is still running
```

For a brief moment:

```text
user = null
```

while:

```text
account.get()
```

is still running.

This can cause incorrect navigation.

For example:

```text
App starts
   ↓
user = null
   ↓
App thinks user is logged out
   ↓
Shows Login
   ↓
account.get() finishes
   ↓
User actually WAS logged in
```

This is called an **authentication loading state** problem.

---

# 15. Add `loading`

A better implementation uses:

```jsx
const [loading, setLoading] = useState(true);
```

Why `true`?

Because when the app starts:

```text
Authentication check = in progress
```

Then:

```jsx
const init = async () => {
  try {
    const userData = await account.get();

    setUser(userData);
  } catch (error) {
    setUser(null);
  } finally {
    setLoading(false);
  }
};
```

Now:

```text
App starts
   ↓
loading = true
   ↓
Check Appwrite
   ↓
Finished
   ↓
loading = false
```

---

# 16. Authentication State Now Has 3 States

This is an important concept.

Instead of thinking only:

```text
Logged in
Logged out
```

you should think:

```text
1. Checking
2. Logged in
3. Logged out
```

Represented by:

```text
loading = true
      ↓
   Checking
```

Then:

```text
loading = false
user exists
      ↓
 Logged in
```

or:

```text
loading = false
user = null
      ↓
 Logged out
```

---

# 17. Authentication State Diagram

```text
                 APP START
                     │
                     ↓
              loading = true
                     │
                     ↓
              account.get()
                 ↙       ↘
            SUCCESS      ERROR
               ↓           ↓
          setUser()    setUser(null)
               ↓           ↓
               └─────┬─────┘
                     ↓
              loading = false
                     │
              ┌──────┴──────┐
              ↓             ↓
         user exists     user = null
              ↓             ↓
          LOGGED IN     LOGGED OUT
```

---

# 18. Add `loading` to Context

If your screens or root layout need to know whether authentication is still being checked, add it to:

```jsx
const value = {
  user,
  loading,
  login,
  register,
  logout,
};
```

Then a component can do:

```jsx
const { user, loading } = useAuth();
```

---

# 19. Why Loading Matters With Expo Router

Eventually, you'll want your routing logic to behave something like:

```text
             App starts
                 ↓
             loading?
              ↙    ↘
            YES     NO
             ↓       ↓
          Wait     user?
                    ↙  ↘
                  YES   NO
                   ↓     ↓
                  App   Login
```

You should **not redirect based on `user === null` while the initial Appwrite check is still running**.

That's why the loading state is important.

---

# 20. `init()` Is Not the Same as `login()`

Keep this distinction very clear:

### `login()`

```jsx
login(email, password)
```

The user explicitly asks to log in.

It:

```text
Creates session
     ↓
Gets user
     ↓
Sets user
```

### `init()`

```jsx
init()
```

The app starts.

It:

```text
Checks existing session
     ↓
Gets user if available
     ↓
Sets user
```

---

# 21. Authentication Lifecycle

You now have the complete lifecycle:

```text
                 APP START
                    ↓
                  init()
                    ↓
              Check session
                ↙       ↘
             exists     none
                ↓         ↓
             setUser    user=null
                ↓         ↓
               APP      LOGIN
                │
                ↓
             Logout
                ↓
       deleteSession()
                ↓
          setUser(null)
                ↓
              LOGIN
```

---

# 22. Video #14 → #18

Here's how all the authentication videos connect.

### Video #14 — Auth Context

Created:

```text
AuthContext
AuthProvider
useAuth
user
```

### Video #15 — Logging In

Added:

```text
login()
```

and Appwrite sessions.

### Video #16 — Error Messages

Added:

```text
try/catch
error.message
setError()
```

### Video #17 — Logging Out

Added:

```text
logout()
deleteSession()
setUser(null)
```

### Video #18 — Initial Auth State

Added:

```text
init()
useEffect()
account.get()
loading
```

So your authentication system is becoming:

```text
                  AuthContext
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      user           loading        methods
                                      │
                         ┌────────────┼────────────┐
                         ↓            ↓            ↓
                       login()     register()    logout()
                         │            │            │
                         └────────────┴────────────┘
                                      ↓
                                   Appwrite
```

---

# ⭐ Video #18 — Must Remember

### 1. Import `useEffect`

```jsx
import {
  createContext,
  useEffect,
  useState,
} from "react";
```

### 2. Create `init()`

```jsx
const init = async () => {
  try {
    const userData = await account.get();

    setUser(userData);
  } catch (error) {
    setUser(null);
  }
};
```

### 3. Run it when AuthProvider starts

```jsx
useEffect(() => {
  init();
}, []);
```

### 4. Check current Appwrite user

```jsx
const userData = await account.get();
```

### 5. Restore the user

```jsx
setUser(userData);
```

### 6. Handle no session

```jsx
catch (error) {
  setUser(null);
}
```

### 7. Add loading

```jsx
const [loading, setLoading] = useState(true);
```

### 8. Finish loading check

```jsx
finally {
  setLoading(false);
}
```

### 9. Expose loading if needed

```jsx
const value = {
  user,
  loading,
  login,
  register,
  logout,
};
```

---

# 🧠 Final Mental Model

Before Video #18:

```text
App starts
   ↓
user = null
```

After Video #18:

```text
App starts
   ↓
loading = true
   ↓
init()
   ↓
account.get()
   ↓
"Do I already have a logged-in user?"
        ↙             ↘
      YES              NO
       ↓                ↓
  setUser(user)    setUser(null)
       ↓                ↓
       └───────┬────────┘
               ↓
         loading = false
               ↓
       ┌───────┴───────┐
       ↓               ↓
   Logged in       Logged out
```

### 🔑 Core lesson

**`login()` creates a new session. `logout()` destroys the session. `init()` checks for an existing session when the app starts.**

And the `loading` state tells your application:

> **"Don't decide whether the user is logged in or logged out until Appwrite has finished checking."**
