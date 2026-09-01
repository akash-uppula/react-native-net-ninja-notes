# 📱 React Native — Video #20 Notes

## Complete React Native Tutorial #20 — Activity Indicators

Video #20 introduces the **ActivityIndicator** component.

The main purpose is to show the user that something is **currently loading or processing**.

This is especially useful for the authentication system we've built.

---

# 1. What Is `ActivityIndicator`?

`ActivityIndicator` is React Native's built-in loading spinner.

Import it:

```jsx
import { ActivityIndicator } from "react-native";
```

Then:

```jsx
<ActivityIndicator />
```

will display a spinner.

It communicates:

> **"Please wait, something is happening."**

---

# 2. Why Do We Need It?

Consider Login:

```text
User presses Login
       ↓
Appwrite request
       ↓
        ???
       ↓
Login successful
```

The Appwrite request may take a moment.

Without an indicator:

```text
User presses Login
       ↓
Nothing appears to happen
       ↓
User may press Login again
       ↓
Multiple requests
```

With an indicator:

```text
User presses Login
       ↓
   ⏳ Loading...
       ↓
Appwrite request
       ↓
Login successful
```

---

# 3. Basic Example

```jsx
import {
  ActivityIndicator,
  View,
} from "react-native";

const Loading = () => {
  return (
    <View>
      <ActivityIndicator />
    </View>
  );
};

export default Loading;
```

---

# 4. `size`

You can control the size:

```jsx
<ActivityIndicator size="small" />
```

or:

```jsx
<ActivityIndicator size="large" />
```

So:

```text
small → smaller spinner
large → larger spinner
```

You can also use a numeric size:

```jsx
<ActivityIndicator size={50} />
```

---

# 5. `color`

You can change its color:

```jsx
<ActivityIndicator color="blue" />
```

For your themed application, you can eventually use one of your theme colors:

```jsx
<ActivityIndicator color={colors.text} />
```

---

# 6. Activity Indicator With State

The important part is combining it with `useState`.

For example:

```jsx
const [loading, setLoading] = useState(false);
```

Initially:

```text
loading = false
```

When the user starts an operation:

```jsx
setLoading(true);
```

When it finishes:

```jsx
setLoading(false);
```

---

# 7. Login Example

Your Login screen can use:

```jsx
const [loading, setLoading] = useState(false);
```

Then:

```jsx
const handleLogin = async () => {
  try {
    setLoading(true);
    setError("");

    await login(email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  } finally {
    setLoading(false);
  }
};
```

The important part is:

```jsx
finally {
  setLoading(false);
}
```

---

# 8. Why Use `finally`?

Remember:

```jsx
try {
  // code
} catch (error) {
  // error
}
```

`finally` runs whether the operation succeeds **or fails**.

So:

```jsx
try {
  setLoading(true);

  await login(email, password);
} catch (error) {
  setError(error.message);
} finally {
  setLoading(false);
}
```

Flow:

```text
             setLoading(true)
                    ↓
                 Request
                ↙       ↘
            SUCCESS     ERROR
               ↓          ↓
               └────┬─────┘
                    ↓
             finally
                    ↓
           setLoading(false)
```

This prevents the spinner from getting stuck.

---

# 9. Show Spinner Instead of Button Text

You can conditionally render:

```jsx
<Pressable onPress={handleLogin}>
  {loading ? (
    <ActivityIndicator />
  ) : (
    <Text>Login</Text>
  )}
</Pressable>
```

When:

```text
loading = false
```

you see:

```text
Login
```

When:

```text
loading = true
```

you see:

```text
⏳
```

---

# 10. Why This Is Better

It gives the user immediate feedback:

```text
Before:

[ Login ]

After pressing:

[ ⏳ ]
```

The user knows the request is being processed.

---

# 11. Disable the Button While Loading

You should also prevent repeated submissions.

`Pressable` has a `disabled` prop:

```jsx
<Pressable
  onPress={handleLogin}
  disabled={loading}
>
  {loading ? (
    <ActivityIndicator />
  ) : (
    <Text>Login</Text>
  )}
</Pressable>
```

Now:

```text
loading = false
→ button works

loading = true
→ button disabled
```

This is especially useful for authentication requests.

---

# 12. Complete Login Example

Putting the pieces together:

```jsx
import {
  ActivityIndicator,
  Pressable,
  StyleSheet,
  Text,
  TextInput,
  View,
} from "react-native";

import { useState } from "react";
import { router } from "expo-router";

import { useAuth } from "../hooks/useAuth";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  const { login } = useAuth();

  const handleLogin = async () => {
    try {
      setLoading(true);
      setError("");

      await login(email, password);

      router.replace("/books");
    } catch (error) {
      setError(error.message);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.input}
        placeholder="Email"
        value={email}
        onChangeText={setEmail}
      />

      <TextInput
        style={styles.input}
        placeholder="Password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
      />

      {error && (
        <Text style={styles.error}>
          {error}
        </Text>
      )}

      <Pressable
        onPress={handleLogin}
        disabled={loading}
        style={styles.button}
      >
        {loading ? (
          <ActivityIndicator />
        ) : (
          <Text>Login</Text>
        )}
      </Pressable>
    </View>
  );
};

export default Login;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },

  input: {
    borderWidth: 1,
    padding: 12,
    marginBottom: 15,
  },

  error: {
    marginBottom: 15,
  },

  button: {
    padding: 15,
    alignItems: "center",
  },
});
```

---

# 13. ActivityIndicator for Initial Auth

This is particularly relevant to **Video #18**.

We already have:

```jsx
const [loading, setLoading] = useState(true);
```

inside `AuthContext`.

Previously, in the protected layout we had:

```jsx
if (loading) {
  return null;
}
```

Instead of rendering nothing, we can display a spinner.

For example:

```jsx
if (loading) {
  return <ActivityIndicator size="large" />;
}
```

Now the user sees:

```text
       ⏳
   Checking...
```

instead of a blank screen.

---

# 14. Center the Loading Indicator

A common pattern is:

```jsx
if (loading) {
  return (
    <View style={styles.loading}>
      <ActivityIndicator size="large" />
    </View>
  );
}
```

with:

```jsx
const styles = StyleSheet.create({
  loading: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
});
```

This centers the spinner:

```text
┌─────────────────────┐
│                     │
│                     │
│         ⏳          │
│                     │
│                     │
└─────────────────────┘
```

---

# 15. Important: Two Different Loading States

At this point in your project, you should understand that we have **two different kinds of loading**.

### Authentication initialization

From Video #18:

```jsx
const [loading, setLoading] = useState(true);
```

This asks:

> **"Is there already a logged-in user?"**

It happens when the app starts.

### Form submission

From Video #20:

```jsx
const [loading, setLoading] = useState(false);
```

This asks:

> **"Is my Login/Register request currently running?"**

It happens when the user submits a form.

Don't confuse the two.

---

# 16. Initial Auth Loading

```text
App starts
   ↓
account.get()
   ↓
⏳ ActivityIndicator
   ↓
Authentication determined
   ↓
App / Login
```

---

# 17. Login Loading

```text
Press Login
   ↓
loading = true
   ↓
⏳ ActivityIndicator
   ↓
Appwrite login
   ↓
loading = false
   ↓
Navigate
```

---

# 18. Register Loading

The exact same pattern applies to registration:

```jsx
const [loading, setLoading] = useState(false);
```

Then:

```jsx
const handleRegister = async () => {
  try {
    setLoading(true);
    setError("");

    await register(name, email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  } finally {
    setLoading(false);
  }
};
```

And:

```jsx
<Pressable
  onPress={handleRegister}
  disabled={loading}
>
  {loading ? (
    <ActivityIndicator />
  ) : (
    <Text>Register</Text>
  )}
</Pressable>
```

---

# 19. The `finally` Pattern

This is a very useful pattern to remember:

```jsx
try {
  setLoading(true);

  await someAsyncFunction();
} catch (error) {
  // handle error
} finally {
  setLoading(false);
}
```

Think:

```text
START
  ↓
loading = true
  ↓
Async operation
  ↓
 ┌───────────┐
 ↓           ↓
Success     Error
 └─────┬─────┘
       ↓
   finally
       ↓
loading = false
```

---

# 20. Don't Do This

Avoid:

```jsx
try {
  setLoading(true);

  await login(email, password);

  setLoading(false);
} catch (error) {
  setError(error.message);
}
```

Why?

If `login()` throws an error:

```text
setLoading(true)
      ↓
login()
      ↓
ERROR
      ↓
catch()
```

The:

```jsx
setLoading(false);
```

inside the `try` never executes.

The spinner could remain visible forever.

Instead:

```jsx
finally {
  setLoading(false);
}
```

---

# ⭐ Video #20 — Must Remember

### 1. Import

```jsx
import { ActivityIndicator } from "react-native";
```

### 2. Basic spinner

```jsx
<ActivityIndicator />
```

### 3. Size

```jsx
<ActivityIndicator size="large" />
```

### 4. Color

```jsx
<ActivityIndicator color="blue" />
```

### 5. Loading state

```jsx
const [loading, setLoading] = useState(false);
```

### 6. Start loading

```jsx
setLoading(true);
```

### 7. Stop loading

```jsx
setLoading(false);
```

### 8. Always stop after async operation

```jsx
finally {
  setLoading(false);
}
```

### 9. Conditional rendering

```jsx
{loading ? (
  <ActivityIndicator />
) : (
  <Text>Login</Text>
)}
```

### 10. Prevent duplicate submissions

```jsx
disabled={loading}
```

---

# 🧠 Final Mental Model

Video #20 connects directly with your authentication work:

```text
                  USER ACTION
                      ↓
                Press Login
                      ↓
               setLoading(true)
                      ↓
               ⏳ Show spinner
                      ↓
                Appwrite request
                   ↙       ↘
              SUCCESS       ERROR
                 ↓            ↓
              Login       setError()
                 ↓            ↓
                 └─────┬──────┘
                       ↓
                finally
                       ↓
              setLoading(false)
                       ↓
                 Stop spinner
```

### 🔑 Core lesson

**`ActivityIndicator` gives visual feedback while an asynchronous operation is happening.**

The most important pattern is:

```jsx
try {
  setLoading(true);

  await someAsyncFunction();
} catch (error) {
  // handle error
} finally {
  setLoading(false);
}
```

And for a button:

```jsx
<Pressable disabled={loading}>
  {loading ? (
    <ActivityIndicator />
  ) : (
    <Text>Login</Text>
  )}
</Pressable>
```

This pattern will be useful far beyond authentication—API requests, fetching books, saving data, uploading images, and many other React Native operations.
