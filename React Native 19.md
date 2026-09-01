# 📱 React Native — Video #19 Notes

## Complete React Native Tutorial #19 — Protecting Routes

Video #19 builds directly on the authentication system from Videos **14–18**.

The goal is:

> **Prevent unauthenticated users from accessing protected screens.**

For your Shelfie app, for example:

```text
Not logged in
     ↓
Can access → Login / Register
Cannot access → Books / Profile / other app screens
```

---

# 1. What Is Route Protection?

Suppose your app has:

```text
app/
├── login.jsx
├── register.jsx
├── books/
├── profile/
└── _layout.jsx
```

A user could potentially manually navigate to:

```text
/books
```

even without logging in.

That's a problem.

We want:

```text
                User
                 ↓
           Is user logged in?
              ↙       ↘
            YES        NO
             ↓          ↓
       Protected     Login
        screens
```

This is called **protecting routes**.

---

# 2. We Already Have `user`

From Video #18, our `AuthContext` has:

```jsx
const [user, setUser] = useState(null);
```

So:

```text
user exists → authenticated
user = null → unauthenticated
```

But remember Video #18:

```jsx
const [loading, setLoading] = useState(true);
```

Therefore we actually have **three authentication states**:

```text
loading = true
    ↓
Still checking

loading = false + user exists
    ↓
Logged in

loading = false + user is null
    ↓
Logged out
```

---

# 3. Don't Protect Routes While Loading

This is extremely important.

When the app first opens:

```jsx
user = null
loading = true
```

We **don't yet know** whether the user is logged in.

If we immediately do:

```jsx
if (!user) {
  router.replace("/login");
}
```

we could incorrectly send an already-authenticated user to Login before `account.get()` finishes.

So:

```text
loading = true
     ↓
WAIT
     ↓
Authentication check finishes
     ↓
Then decide where user should go
```

---

# 4. Route Groups

Expo Router gives us a useful way to organize protected and public routes.

You can use **route groups** with parentheses:

```text
app/
│
├── (auth)/
│   ├── login.jsx
│   └── register.jsx
│
├── (app)/
│   ├── books/
│   └── profile/
│
└── _layout.jsx
```

The parentheses mean the group is used for organization and navigation structure, but the group name doesn't become part of the URL.

For example:

```text
(app)/profile.jsx
```

still corresponds to:

```text
/profile
```

not:

```text
/(app)/profile
```

---

# 5. Public vs Protected Routes

Think of your groups like this:

### `(auth)`

Public routes:

```text
(auth)
├── login
└── register
```

These should be accessible when the user is **not logged in**.

### `(app)`

Protected routes:

```text
(app)
├── books
├── profile
└── ...
```

These should only be accessible when the user **is logged in**.

---

# 6. Nested `_layout.jsx`

Each route group can have its own layout.

For example:

```text
app/
│
├── (auth)/
│   ├── _layout.jsx
│   ├── login.jsx
│   └── register.jsx
│
├── (app)/
│   ├── _layout.jsx
│   ├── books.jsx
│   └── profile.jsx
│
└── _layout.jsx
```

This allows the layouts to control what happens inside each group.

---

# 7. Protect the `(app)` Group

Inside:

```text
app/(app)/_layout.jsx
```

we can check authentication.

The basic idea is:

```jsx
const { user, loading } = useAuth();
```

Then:

```text
loading?
   ↓
Wait

not loading?
   ↓
user?
 ↙   ↘
YES   NO
 ↓     ↓
Allow  Login
```

---

# 8. Use `Redirect`

Expo Router provides:

```jsx
import { Redirect, Stack } from "expo-router";
```

`Redirect` allows you to redirect the user to another route.

For example:

```jsx
<Redirect href="/login" />
```

means:

> Navigate the user to `/login`.

---

# 9. Protected Layout

A basic protected layout can look like:

```jsx
import { Redirect, Stack } from "expo-router";

import { useAuth } from "../../hooks/useAuth";

const AppLayout = () => {
  const { user, loading } = useAuth();

  if (loading) {
    return null;
  }

  if (!user) {
    return <Redirect href="/login" />;
  }

  return <Stack />;
};

export default AppLayout;
```

---

# 10. Understand This Code

First:

```jsx
const { user, loading } = useAuth();
```

We get authentication information from our AuthContext.

Then:

```jsx
if (loading) {
  return null;
}
```

This means:

> Don't render the protected routes while authentication is still being checked.

Then:

```jsx
if (!user) {
  return <Redirect href="/login" />;
}
```

This means:

> If there is no authenticated user, redirect to Login.

Finally:

```jsx
return <Stack />;
```

means:

> The user is authenticated, so allow the protected routes to render.

---

# 11. Why `return null`?

This:

```jsx
return null;
```

means:

> Render nothing for the moment.

We're waiting for:

```jsx
account.get()
```

from our `init()` function.

Once the authentication check finishes:

```text
loading = false
```

React renders the correct screen.

---

# 12. Protected Route Flow

The complete process is:

```text
App starts
    ↓
AuthProvider
    ↓
init()
    ↓
account.get()
    ↓
loading = false
    ↓
App Layout
    ↓
Protected Layout
    ↓
Is user available?
       ↙       ↘
     YES        NO
      ↓          ↓
   <Stack />   <Redirect />
                 ↓
               Login
```

---

# 13. What Happens If User Is Logged In?

Suppose:

```jsx
user = {
  name: "Akash",
  email: "..."
}
```

and:

```jsx
loading = false
```

Then:

```jsx
if (loading) {
  return null;
}

if (!user) {
  return <Redirect href="/login" />;
}
```

Neither condition is true.

So:

```jsx
return <Stack />;
```

runs.

The user gets access to:

```text
Books
Profile
Other protected screens
```

---

# 14. What Happens If User Is Logged Out?

Suppose:

```jsx
user = null;
loading = false;
```

Then:

```jsx
if (!user) {
  return <Redirect href="/login" />;
}
```

runs.

The user is sent to:

```text
/login
```

They cannot access the protected screens through normal navigation.

---

# 15. Logout Works With Route Protection

This is where Video #17 and Video #19 connect.

When the user presses Logout:

```jsx
await logout();
```

Your AuthContext does:

```jsx
await account.deleteSession({
  sessionId: "current",
});

setUser(null);
```

Now:

```text
user = null
```

The protected layout sees:

```jsx
if (!user) {
  return <Redirect href="/login" />;
}
```

and redirects the user.

So route protection gives you an additional layer of security for the UI flow.

---

# 16. You Don't Have to Manually Navigate Everywhere

Without route protection, you might write:

```jsx
if (!user) {
  router.replace("/login");
}
```

on lots of individual screens.

That's not ideal.

Instead, put the authentication check in the layout:

```text
(app)
   ↓
(app)/_layout.jsx
   ↓
Authentication check
   ↓
All routes inside the group are controlled
```

So:

```text
(app)
├── books
├── profile
├── settings
└── ...
```

all benefit from the same authentication logic.

---

# 17. Why Layouts Are Perfect for This

A layout wraps its child routes.

For example:

```text
(app)/_layout.jsx
        │
        ├── books.jsx
        ├── profile.jsx
        └── settings.jsx
```

The layout can decide:

```text
Should these routes be rendered?
```

That's why authentication checks fit naturally inside layouts.

---

# 18. Public Authentication Routes

You can have:

```text
(auth)
├── login.jsx
└── register.jsx
```

These shouldn't require authentication.

So your structure becomes:

```text
app/
│
├── (auth)
│   ├── login.jsx
│   └── register.jsx
│
└── (app)
    ├── books.jsx
    └── profile.jsx
```

Conceptually:

```text
             Authentication
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
       (auth)               (app)
          ↓                   ↓
   Login/Register       Protected screens
```

---

# 19. Root Layout vs Group Layout

You may now have multiple layouts.

### Root layout

```text
app/_layout.jsx
```

Usually handles things such as:

* `Stack`
* global providers
* `AuthProvider`
* general navigation configuration

### Auth layout

```text
app/(auth)/_layout.jsx
```

Controls authentication-related routes.

### App layout

```text
app/(app)/_layout.jsx
```

Controls protected routes.

Think:

```text
app/_layout.jsx
       ↓
   AuthProvider
       ↓
 ┌─────┴─────┐
 ↓           ↓
(auth)      (app)
             ↓
        Authentication
           check
```

---

# 20. Important: AuthProvider Must Wrap the Routes

Your `AuthProvider` needs to be above the layouts that use:

```jsx
useAuth()
```

For example:

```jsx
import { Stack } from "expo-router";

import AuthProvider from "../contexts/AuthContext";

const RootLayout = () => {
  return (
    <AuthProvider>
      <Stack />
    </AuthProvider>
  );
};

export default RootLayout;
```

Then the nested layouts can access the context.

The important relationship is:

```text
AuthProvider
     ↓
   Stack
     ↓
Nested layouts
     ↓
useAuth()
```

---

# 21. `Redirect` vs `router.replace()`

You'll see both approaches.

### `router.replace()`

```jsx
router.replace("/login");
```

This is an imperative navigation command.

You call it inside a function/effect.

### `<Redirect />`

```jsx
<Redirect href="/login" />
```

This is declarative.

You're effectively saying:

> If this condition is true, this route should redirect.

For route protection, `<Redirect />` fits very naturally.

---

# 22. Don't Forget the Loading Check

This is one of the most important parts of Video #19.

❌ Avoid:

```jsx
if (!user) {
  return <Redirect href="/login" />;
}
```

without considering the initial loading state.

Instead:

```jsx
if (loading) {
  return null;
}

if (!user) {
  return <Redirect href="/login" />;
}
```

Because:

```text
user = null
```

doesn't necessarily mean:

> User is logged out.

It might mean:

> We haven't finished checking yet.

---

# 23. Three Authentication States

Keep this mental model:

### State 1 — Checking

```jsx
loading === true
```

```text
"Wait, I don't know yet."
```

### State 2 — Authenticated

```jsx
loading === false
user !== null
```

```text
"User is logged in."
```

### State 3 — Unauthenticated

```jsx
loading === false
user === null
```

```text
"User is logged out."
```

This is the foundation of your protected routing.

---

# 24. Your Auth System So Far

After Videos 14–19:

```text
                 AuthContext
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
        user        loading     methods
                                  │
                    ┌─────────────┼─────────────┐
                    ↓             ↓             ↓
                  login()      register()     logout()
                    │             │             │
                    └─────────────┴─────────────┘
                                  ↓
                               Appwrite
```

And routing:

```text
                    App
                     ↓
                AuthProvider
                     ↓
                  init()
                     ↓
             Check Appwrite
                     ↓
              loading = false
                     ↓
             Protected Layout
                 ↙       ↘
               user    no user
                ↓          ↓
              Stack     Redirect
                           ↓
                         Login
```

---

# ⭐ Video #19 — Must Remember

### 1. Route groups

```text
(auth)
(app)
```

Use groups to separate public and protected routes.

### 2. Get authentication state

```jsx
const { user, loading } = useAuth();
```

### 3. Wait for initial auth check

```jsx
if (loading) {
  return null;
}
```

### 4. Redirect unauthenticated users

```jsx
if (!user) {
  return <Redirect href="/login" />;
}
```

### 5. Allow authenticated users

```jsx
return <Stack />;
```

### 6. Protect the whole group

Put the check inside:

```text
app/(app)/_layout.jsx
```

rather than repeating it on every protected screen.

---

# 🧠 Final Mental Model

The key lesson of Video #19 is:

```text
                   APP
                    ↓
              AuthProvider
                    ↓
                  init()
                    ↓
             Check Appwrite
                    ↓
               loading?
              ↙        ↘
            YES         NO
             ↓           ↓
           WAIT        user?
                       ↙   ↘
                     YES    NO
                      ↓      ↓
                   <Stack> <Redirect>
                              ↓
                            Login
```

### 🔑 Remember this pattern:

```jsx
const { user, loading } = useAuth();

if (loading) {
  return null;
}

if (!user) {
  return <Redirect href="/login" />;
}

return <Stack />;
```

That is the core idea behind **protecting routes with authentication state**.
