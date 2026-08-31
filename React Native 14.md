# 📱 React Native — Video #14 Notes

## Complete React Native Tutorial #14 — Making an Auth Context

This video is where the authentication setup starts becoming **shared across the entire application**.

The main concepts are:

```text
Context API
useContext
AuthContext
AuthProvider
Custom Hook
Authentication state
```

The goal is to avoid passing the logged-in user through every component manually.

---

# 1. The Problem: Where Do We Store the User?

Suppose your My Shelf app has:

```text
Login
Signup
Home
Books
Profile
```

After login, you need to know:

> Who is currently logged in?

For example:

```text
user
├── $id
├── name
└── email
```

If every screen needs this information, you don't want to pass it manually:

```text
App
 ↓
Home
 ↓
Books
 ↓
Profile
 ↓
User
```

This is where **Context** helps.

---

# 2. What Is Context?

React Context allows you to make data available to many components without manually passing it through props.

Without Context:

```text
App
 ↓
Home
 ↓
Profile
 ↓
User
```

With Context:

```text
             AuthContext
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     Home      Books     Profile
       │         │         │
       └──────── User ─────┘
```

Any component inside the provider can access the authentication data.

---

# 3. `createContext`

React provides:

```tsx
createContext
```

Import it:

```tsx
import { createContext } from "react";
```

Then:

```tsx
const AuthContext = createContext();
```

This creates the context.

Think of it as creating a **shared data channel**:

```text
AuthContext
     ↓
Shared authentication data
```

---

# 4. `AuthContext`

For our application:

```tsx
const AuthContext = createContext();
```

The context will eventually contain things such as:

```text
user
login()
logout()
signup()
```

So conceptually:

```text
AuthContext
│
├── user
├── login
├── signup
└── logout
```

---

# 5. What Is a Provider?

A Context by itself doesn't automatically give data to your components.

You need a:

```tsx
Provider
```

The provider supplies the data.

For example:

```tsx
<AuthContext.Provider value={...}>
  ...
</AuthContext.Provider>
```

Think of it like:

```text
AuthProvider
      │
      ├── Home
      ├── Books
      └── Profile
```

Everything inside the provider can access the context.

---

# 6. Create `AuthProvider`

Instead of writing the provider directly everywhere, create a component:

```tsx
const AuthProvider = ({ children }) => {
  return (
    <AuthContext.Provider>
      {children}
    </AuthContext.Provider>
  );
};
```

The important part is:

```tsx
{children}
```

---

# 7. What Is `children`?

When you write:

```tsx
<AuthProvider>
  <Home />
</AuthProvider>
```

React passes:

```tsx
<Home />
```

as:

```tsx
children
```

So:

```text
<AuthProvider>
      │
      └── children
             ↓
           <Home />
```

This allows the provider to wrap your application.

---

# 8. Supplying a `value`

The provider uses:

```tsx
value
```

to make data available.

Example:

```tsx
<AuthContext.Provider
  value={{ user }}
>
  {children}
</AuthContext.Provider>
```

Now components inside the provider can access:

```tsx
user
```

---

# 9. Add User State

The currently logged-in user should be stored in React state.

Import:

```tsx
import { createContext, useState } from "react";
```

Then:

```tsx
const [user, setUser] = useState(null);
```

Initially:

```text
user = null
```

meaning:

```text
Nobody is logged in
```

After login:

```text
user = logged-in user
```

---

# 10. Complete Basic Auth Context

A basic version looks like:

```tsx
import {
  createContext,
  useState,
} from "react";

const AuthContext = createContext();

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  return (
    <AuthContext.Provider
      value={{ user, setUser }}
    >
      {children}
    </AuthContext.Provider>
  );
};

export {
  AuthContext,
  AuthProvider,
};
```

This is the basic structure you need to understand.

---

# 11. Why Put `user` in Context?

Suppose the user logs in.

```text
Login
 ↓
setUser(user)
 ↓
AuthContext
 ↓
Entire application knows the user
```

Then Profile can access:

```text
Current user
```

and Books can access:

```text
Current user
```

without receiving it through props.

---

# 12. `useContext`

React provides another hook:

```tsx
useContext
```

Import:

```tsx
import { useContext } from "react";
```

Then:

```tsx
const auth = useContext(AuthContext);
```

Now you can access the context value.

For example:

```tsx
const { user } = useContext(AuthContext);
```

---

# 13. Example in a Screen

Suppose your context provides:

```tsx
value={{ user }}
```

Then your Profile screen can do:

```tsx
import { useContext } from "react";

import {
  AuthContext,
} from "../context/AuthContext";

const Profile = () => {
  const { user } = useContext(AuthContext);

  return (
    <Text>
      {user?.name}
    </Text>
  );
};
```

Now Profile doesn't need:

```tsx
<Profile user={user} />
```

The context provides it.

---

# 14. Optional Chaining `?.`

You may see:

```tsx
user?.name
```

This is JavaScript's **optional chaining**.

Why use it?

Initially:

```tsx
user === null
```

If you wrote:

```tsx
user.name
```

you would get an error because `null` doesn't have a `name`.

With:

```tsx
user?.name
```

JavaScript safely checks whether `user` exists.

```text
user exists?
    │
 ┌──┴──┐
Yes    No
 ↓      ↓
name   undefined
```

---

# 15. The Provider Must Wrap the Screens

Creating the context isn't enough.

Your screens must be **inside** the provider.

Conceptually:

```tsx
<AuthProvider>
  <App />
</AuthProvider>
```

Then:

```text
AuthProvider
     │
     └── App
          │
          ├── Login
          ├── Home
          ├── Books
          └── Profile
```

All of these can access the AuthContext.

---

# 16. With Expo Router

Since your project uses Expo Router, the provider will normally be placed high enough in the component tree so that the routes can access it.

For example, conceptually:

```text
app/
├── _layout.tsx
├── login.tsx
├── signup.tsx
└── (tabs)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

The authentication provider should sit above the screens that need authentication information.

---

# 17. Auth Context + Appwrite

This is where Video #12 connects directly with Video #14.

Previously:

```text
Appwrite
   ↓
Backend authentication
```

Now:

```text
Appwrite
   ↓
AuthContext
   ↓
React Native screens
```

So:

```text
              Appwrite
                 │
                 ↓
          Authentication
                 │
                 ↓
            AuthContext
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     Home      Books     Profile
```

The Context becomes the bridge between authentication and your UI.

---

# 18. `login()` Function

Instead of allowing every screen to directly communicate with Appwrite, you can eventually put authentication logic inside the AuthProvider.

For example:

```tsx
const login = async (
  email,
  password
) => {
  // Appwrite login
};
```

Then expose it:

```tsx
value={{
  user,
  login,
}}
```

Now the Login screen can simply call:

```tsx
login(email, password);
```

instead of knowing all the Appwrite implementation details.

---

# 19. `signup()` Function

Similarly:

```tsx
const signup = async (
  email,
  password,
  name
) => {
  // Appwrite signup
};
```

Then:

```tsx
value={{
  user,
  login,
  signup,
}}
```

The signup screen can use:

```text
signup()
```

without needing to know how Appwrite works internally.

---

# 20. `logout()` Function

You can also keep logout inside the AuthContext:

```tsx
const logout = async () => {
  // Appwrite logout
};
```

Then expose:

```tsx
value={{
  user,
  login,
  signup,
  logout,
}}
```

Your Profile screen can simply call:

```tsx
logout();
```

---

# 21. Why This Architecture Is Useful

Without AuthContext:

```text
Login screen
    ↓
Appwrite

Profile screen
    ↓
Appwrite

Home screen
    ↓
Appwrite

Books screen
    ↓
Appwrite
```

Lots of repeated authentication logic.

With AuthContext:

```text
                  AuthProvider
                       │
              ┌────────┴────────┐
              │                 │
           Appwrite          user state
              │                 │
              └────────┬────────┘
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      Login         Profile         Home
```

Much cleaner.

---

# 22. Context Does NOT Mean Database

This is an important distinction.

Context:

```text
React Context
```

is **not your backend database**.

Context stores data in the running React application.

Appwrite stores your backend data.

Think:

```text
React Context
     ↓
Current app state

Appwrite
     ↓
Backend / persistent data
```

For example:

```text
Appwrite
   ↓
User account exists

AuthContext
   ↓
Current app knows
which user is logged in
```

---

# 23. Context Does NOT Automatically Persist Data

If you do:

```tsx
const [user, setUser] = useState(null);
```

and the application reloads, React state can be reset.

So Context alone doesn't mean:

> "The user will remain logged in forever."

Later, you'll need to check Appwrite's current session/account and restore the user state.

Conceptually:

```text
App starts
   ↓
Check Appwrite session
   ↓
Is user logged in?
   ↓
Yes → setUser(user)
No  → user = null
```

This is an important part of authentication architecture.

---

# 24. Custom `useAuth` Hook

Once you have Context, you'll often create a custom hook.

Instead of writing this everywhere:

```tsx
const {
  user,
  login,
  logout,
} = useContext(AuthContext);
```

you can create:

```tsx
const useAuth = () => {
  return useContext(AuthContext);
};
```

Then screens can simply use:

```tsx
const {
  user,
  login,
  logout,
} = useAuth();
```

This makes your code cleaner.

---

# 25. `useAuth` Mental Model

Think:

```text
useAuth()
   ↓
useContext(AuthContext)
   ↓
AuthContext value
   ↓
user / login / signup / logout
```

So:

```tsx
const { user } = useAuth();
```

is essentially a convenient way of accessing the authentication context.

---

# 26. Example Folder Structure

For your My Shelf project, a clean structure could eventually look like:

```text
project/
│
├── app/
│   ├── _layout.tsx
│   ├── login.tsx
│   ├── signup.tsx
│   │
│   └── (tabs)/
│       ├── _layout.tsx
│       ├── index.tsx
│       ├── books.tsx
│       └── profile.tsx
│
├── context/
│   └── AuthContext.tsx
│
├── hooks/
│   └── useAuth.ts
│
└── lib/
    └── appwrite.ts
```

This is a good architecture to keep in mind as the project grows.

---

# 27. How Everything Connects

You now have:

```text
lib/appwrite.ts
       │
       ↓
   Appwrite Client
       │
       ↓
context/AuthContext.tsx
       │
       ├── user
       ├── login()
       ├── signup()
       └── logout()
       │
       ↓
hooks/useAuth.ts
       │
       ↓
React Native Screens
```

This separation is powerful because each part has a clear responsibility.

---

# ⭐ Video #14 — Must Remember

### 1. Create Context

```tsx
import { createContext } from "react";

const AuthContext = createContext();
```

### 2. Create Provider

```tsx
const AuthProvider = ({ children }) => {
  return (
    <AuthContext.Provider value={{}}>
      {children}
    </AuthContext.Provider>
  );
};
```

### 3. User state

```tsx
const [user, setUser] = useState(null);
```

### 4. Provide the user

```tsx
<AuthContext.Provider
  value={{ user, setUser }}
>
```

### 5. Consume Context

```tsx
import { useContext } from "react";

const { user } = useContext(AuthContext);
```

### 6. Custom hook

Eventually:

```tsx
const useAuth = () => {
  return useContext(AuthContext);
};
```

Then:

```tsx
const { user } = useAuth();
```

---

# 🧠 The Most Important Mental Model

Remember the difference:

```text
                 APPWRITE
                    │
                    │ Backend
                    ↓
              User Account
                    │
                    ↓
              AUTH CONTEXT
                    │
             Current user
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
      Home        Books       Profile
```

**Appwrite** manages the backend authentication.

**AuthContext** makes the current authentication state available throughout your React Native application.

**`useAuth()`** gives your components a convenient way to access that shared authentication state.

And this sets up the next important step: **connecting the Login/Signup forms from Video #13 to the AuthContext and Appwrite authentication.**
