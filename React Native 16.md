# 📱 React Native — Video #16 Notes

## Complete React Native Tutorial #16 — Showing Error Messages

Video #16 builds on the Login/Signup authentication from **Video #15**.

The main idea is:

> Instead of only logging errors with `console.log()`, show a useful error message to the user.

The flow becomes:

```text
User submits form
       ↓
AuthContext / Appwrite
       ↓
Success? ─────────── No
  ↓                    ↓
Continue             throw error
                       ↓
                    catch(error)
                       ↓
                  setErrorMessage()
                       ↓
                   Show on UI
```

---

# 1. Why Show Errors?

Previously, you had:

```jsx
catch (error) {
  console.log("Login Error:", error);
}
```

The developer can see the error in the console, but the user cannot.

For example, Appwrite may return an error when:

* Email/password is incorrect
* Email is already registered
* Password doesn't satisfy requirements
* Network request fails
* User doesn't exist

Instead of:

```text
Nothing happens 😕
```

we want:

```text
Invalid credentials
```

or another appropriate message.

---

# 2. Create an Error State

Inside the Login screen:

```jsx
const [error, setError] = useState("");
```

Initially:

```text
error = ""
```

which means there is no error.

When something goes wrong:

```jsx
setError(error.message);
```

Now the error message is stored in React state.

---

# 3. Catch the Error

Your login function can be:

```jsx
const handleLogin = async () => {
  try {
    await login(email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

The important part is:

```jsx
catch (error) {
  setError(error.message);
}
```

---

# 4. What Is `error.message`?

An error object can contain information such as:

```text
error
├── message
├── code
├── type
└── ...
```

The useful human-readable part is commonly:

```jsx
error.message
```

So:

```jsx
setError(error.message);
```

means:

> Take the error message returned by Appwrite and store it in our `error` state.

---

# 5. Display the Error

Once we have:

```jsx
const [error, setError] = useState("");
```

we can display it:

```jsx
{error && (
  <Text>
    {error}
  </Text>
)}
```

This uses JavaScript's **short-circuit rendering**.

---

# 6. How `error &&` Works

Consider:

```jsx
{error && <Text>{error}</Text>}
```

If:

```text
error = ""
```

then:

```text
Nothing is displayed
```

If:

```text
error = "Invalid credentials"
```

then:

```text
<Text>
  Invalid credentials
</Text>
```

So:

```text
error exists?
    │
 ┌──┴──┐
No    Yes
 ↓      ↓
Hide   Show
       message
```

---

# 7. Clear Previous Errors

Suppose the user first enters the wrong password:

```text
Invalid credentials
```

Then they correct the password.

Before trying again, clear the previous error:

```jsx
setError("");
```

So:

```jsx
const handleLogin = async () => {
  try {
    setError("");

    await login(email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

This prevents an old error from remaining on the screen while the next request is running.

---

# 8. Complete Login Error Flow

```jsx
const handleLogin = async () => {
  try {
    setError("");

    await login(email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

Flow:

```text
Press Login
     ↓
Clear old error
     ↓
login()
     ↓
Appwrite
     ↓
 ┌───────────────┐
 ↓               ↓
Success         Error
 ↓               ↓
Navigate       catch()
                 ↓
           error.message
                 ↓
             setError()
                 ↓
             Display
```

---

# 9. Show the Error Near the Form

For example:

```jsx
<View>
  <TextInput
    placeholder="Email"
    value={email}
    onChangeText={setEmail}
  />

  <TextInput
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

  <Pressable onPress={handleLogin}>
    <Text>Login</Text>
  </Pressable>
</View>
```

Usually, showing the error close to the form is better UX than only logging it.

---

# 10. Add an Error Style

You can style it:

```jsx
const styles = StyleSheet.create({
  error: {
    marginBottom: 10,
    fontSize: 14,
  },
});
```

For your actual project, you can later make it match your theme.

---

# 11. Same Concept for Registration

Registration also needs an error state:

```jsx
const [error, setError] = useState("");
```

Then:

```jsx
const handleRegister = async () => {
  try {
    setError("");

    await register(
      name,
      email,
      password
    );

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

So if the email is already registered:

```text
Register
   ↓
Appwrite
   ↓
❌ Email already exists
   ↓
catch(error)
   ↓
setError(error.message)
   ↓
Show message
```

---

# 12. Login and Register Together

Your screens now follow the same pattern.

### Login

```jsx
const [error, setError] = useState("");

const handleLogin = async () => {
  try {
    setError("");

    await login(email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

### Register

```jsx
const [error, setError] = useState("");

const handleRegister = async () => {
  try {
    setError("");

    await register(name, email, password);

    router.replace("/books");
  } catch (error) {
    setError(error.message);
  }
};
```

---

# 13. Why the Error Is Thrown From `AuthContext`

Remember your AuthContext:

```jsx
catch (error) {
  console.log("Login Error:", error);
  throw error;
}
```

The important part is:

```jsx
throw error;
```

It sends the error back to the screen.

Therefore:

```text
AuthContext
    ↓
catch(error)
    ↓
throw error
    ↓
Login screen
    ↓
catch(error)
    ↓
setError(error.message)
```

This is why the Login screen can handle the error.

---

# 14. Don't Put UI Error State in AuthContext

Your AuthContext should mainly handle:

```text
user
login()
register()
logout()
```

The Login screen can handle:

```text
error message
loading state
input validation
navigation
```

So:

```text
AuthContext
     │
     ├── user
     ├── login()
     ├── register()
     └── logout()
     
Login Screen
     │
     ├── email
     ├── password
     ├── error
     ├── loading
     └── navigation
```

This keeps responsibilities clean.

---

# 15. Error vs Error Message

You should distinguish between:

```jsx
error
```

and:

```jsx
error.message
```

The entire error might be:

```text
{
  message: "Invalid credentials",
  code: 401,
  type: "...",
  ...
}
```

But the UI usually needs:

```jsx
error.message
```

which might display:

```text
Invalid credentials
```

---

# 16. A Small Complete Example

```jsx
import {
  Keyboard,
  Pressable,
  StyleSheet,
  Text,
  TextInput,
  TouchableWithoutFeedback,
  View,
} from "react-native";

import { useState } from "react";

import { router } from "expo-router";

import { useAuth } from "../hooks/useAuth";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");

  const { login } = useAuth();

  const handleLogin = async () => {
    try {
      setError("");

      await login(email, password);

      router.replace("/books");
    } catch (error) {
      setError(error.message);
    }
  };

  return (
    <TouchableWithoutFeedback
      onPress={Keyboard.dismiss}
    >
      <View style={styles.container}>
        <Text style={styles.title}>
          Login
        </Text>

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
          style={styles.button}
        >
          <Text>Login</Text>
        </Pressable>
      </View>
    </TouchableWithoutFeedback>
  );
};

export default Login;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },

  title: {
    fontSize: 28,
    fontWeight: "bold",
    marginBottom: 20,
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

# 17. Important Pattern to Remember

This pattern will appear **very frequently** in React Native:

```jsx
const [error, setError] = useState("");
```

Then:

```jsx
try {
  setError("");

  // async operation
} catch (error) {
  setError(error.message);
}
```

Then:

```jsx
{error && (
  <Text>{error}</Text>
)}
```

Think:

```text
State
  ↓
try
  ↓
Operation
  ↓
catch
  ↓
setError()
  ↓
UI automatically updates
```

---

# ⭐ Video #16 — Must Remember

### 1. Create error state

```jsx
const [error, setError] = useState("");
```

### 2. Clear old error

```jsx
setError("");
```

### 3. Catch the error

```jsx
catch (error) {
  setError(error.message);
}
```

### 4. Display conditionally

```jsx
{error && (
  <Text>{error}</Text>
)}
```

### 5. AuthContext passes the error back

```jsx
catch (error) {
  console.log("Login Error:", error);
  throw error;
}
```

### 6. Same pattern for registration

```jsx
try {
  setError("");

  await register(
    name,
    email,
    password
  );
} catch (error) {
  setError(error.message);
}
```

---

# 🧠 Final Mental Model

Your authentication system now works like this:

```text
                    LOGIN / REGISTER
                           │
                           ↓
                       Form data
                           │
                           ↓
                    handleLogin()
                  handleRegister()
                           │
                           ↓
                       useAuth()
                           │
                           ↓
                      AuthContext
                           │
                           ↓
                        Appwrite
                       ↙        ↘
                  SUCCESS       ERROR
                     ↓             ↓
                 setUser()    throw error
                     ↓             ↓
                 Navigate       catch(error)
                                   ↓
                              setError()
                                   ↓
                              Show <Text>
```

### 🔑 Core lesson from Video #16

**Errors should travel from Appwrite → AuthContext → Screen → React state → UI.**

The AuthContext handles **authentication**, while the screen decides **how to present the error to the user**.
