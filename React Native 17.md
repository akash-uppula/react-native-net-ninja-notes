# 📱 React Native — Video #17 Notes

## Complete React Native Tutorial #17 — Logging Users Out

Video #17 completes the basic authentication flow by adding **logout**.

You already have:

```text id="6j7g2m"
Register
   ↓
Login
   ↓
User authenticated
   ↓
Logout
```

The important concept is that **logout should be handled through `AuthContext`**, just like login and registration.

---

# 1. What Happens When a User Logs Out?

When the user logs out, two things need to happen:

```text id="1i4v4s"
1. Delete Appwrite session
2. Clear user from React state
```

In your `AuthContext`:

```jsx id="y7o4f2"
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
```

---

# 2. `account.deleteSession()`

This is the actual Appwrite logout:

```jsx id="o7f0h3"
await account.deleteSession({
  sessionId: "current",
});
```

`"current"` means:

> Delete the session belonging to the currently authenticated user.

Flow:

```text id="g4v8d1"
User logged in
      ↓
Appwrite session exists
      ↓
logout()
      ↓
deleteSession("current")
      ↓
Session deleted
```

---

# 3. Why `setUser(null)`?

Deleting the Appwrite session is not enough.

Your React application also has:

```jsx id="3up9fy"
const [user, setUser] = useState(null);
```

When logged in:

```text id="zq7t8d"
user = Appwrite user
```

After logout:

```jsx id="2l5v6j"
setUser(null);
```

Now:

```text id="u5x1je"
user = null
```

This tells the React application:

> There is currently no authenticated user.

So logout has two parts:

```text id="z2h5py"
                logout()
                   │
          ┌────────┴────────┐
          ↓                 ↓
   Appwrite session      React state
       deleted             cleared
          ↓                 ↓
     deleteSession()     setUser(null)
```

---

# 4. Get `logout()` From `useAuth()`

On the Profile screen, you can access it:

```jsx id="6e1m8d"
const { logout } = useAuth();
```

Now the screen has access to the logout function.

---

# 5. Create `handleLogout()`

Similar to Login and Register, create a handler:

```jsx id="z3h2y5"
const handleLogout = async () => {
  try {
    await logout();
  } catch (error) {
    console.log("Logout Error:", error);
  }
};
```

Then:

```jsx id="v2k9h4"
<Pressable onPress={handleLogout}>
  <Text>Logout</Text>
</Pressable>
```

---

# 6. Navigate After Logout

After successfully logging out, you usually want to send the user back to Login.

For Expo Router:

```jsx id="8m7n2k"
router.replace("/login");
```

So:

```jsx id="j6f0p9"
const handleLogout = async () => {
  try {
    await logout();

    router.replace("/login");
  } catch (error) {
    console.log("Logout Error:", error);
  }
};
```

The order is important:

```text id="b8j2s1"
logout()
   ↓
Appwrite session deleted
   ↓
user = null
   ↓
router.replace("/login")
```

---

# 7. Why `replace()` Again?

Just like Login:

```jsx id="z6f5v3"
router.replace("/login");
```

is preferable to:

```jsx id="m1x8r4"
router.push("/login");
```

because you don't want the user to simply press Back and return to the authenticated screen.

Conceptually:

```text id="n7r4e2"
Before:

Books → Profile
          ↓
       Logout
          ↓
       Login
```

Using `replace()` means the authenticated route is replaced rather than simply adding Login on top of the stack.

---

# 8. Complete Profile Logout Example

```jsx id="p8c3v7"
import {
  Pressable,
  StyleSheet,
  Text,
  View,
} from "react-native";

import { router } from "expo-router";

import { useAuth } from "../hooks/useAuth";

const Profile = () => {
  const { user, logout } = useAuth();

  const handleLogout = async () => {
    try {
      await logout();

      router.replace("/login");
    } catch (error) {
      console.log("Logout Error:", error);
    }
  };

  return (
    <View style={styles.container}>
      <Text>
        {user?.name}
      </Text>

      <Text>
        {user?.email}
      </Text>

      <Pressable onPress={handleLogout}>
        <Text>Logout</Text>
      </Pressable>
    </View>
  );
};

export default Profile;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },
});
```

---

# 9. `user?.name`

You may remember this from previous videos:

```jsx id="y0m3c6"
user?.name
```

We use optional chaining because:

```jsx id="w7e5j3"
user
```

could be `null`.

Instead of:

```jsx id="t9k2v4"
user.name
```

which could cause an error when `user` is `null`, we use:

```jsx id="6r1c8v"
user?.name
```

---

# 10. Error Handling

Logout is also an asynchronous operation, so use:

```jsx id="1s7h4k"
try {
  await logout();
} catch (error) {
  console.log(error);
}
```

Your AuthContext also throws the error:

```jsx id="p4y6t8"
catch (error) {
  console.log("Logout Error:", error);
  throw error;
}
```

Therefore:

```text id="f8d2m6"
Appwrite
   ↓
Logout fails
   ↓
AuthContext catch
   ↓
throw error
   ↓
Profile catch
```

This follows the same pattern you learned in Video #16.

---

# 11. Your Complete `AuthContext`

At this stage, your authentication context contains:

```jsx id="n6k3q9"
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

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
};

export default AuthProvider;
```

---

# 12. Authentication Functions So Far

Your `AuthContext` now provides four important things:

```text id="z1d7p3"
AuthContext
│
├── user
│
├── login()
│
├── register()
│
└── logout()
```

### `user`

Stores the current authenticated user:

```jsx id="r3w5c1"
user
```

### `login()`

Creates a session:

```jsx id="q4n7v2"
login(email, password)
```

### `register()`

Creates an account and logs the user in:

```jsx id="u5k8j3"
register(name, email, password)
```

### `logout()`

Deletes the current session and clears the user:

```jsx id="a8m2z6"
logout()
```

---

# 13. Complete Authentication Cycle

You now have the complete basic cycle:

```text id="p2k8m4"
              REGISTER
                 ↓
          account.create()
                 ↓
              LOGIN
                 ↓
   createEmailPasswordSession()
                 ↓
              USER
                 ↓
        ┌────────┴────────┐
        ↓                 ↓
      Screens          Profile
                          ↓
                       LOGOUT
                          ↓
                 deleteSession()
                          ↓
                    setUser(null)
                          ↓
                        LOGIN
```

---

# 14. Login → App → Logout

The simplest way to remember Video #17:

```text id="s6f3d9"
LOGIN
  ↓
Session exists
  ↓
user = userData
  ↓
Application
  ↓
Logout button
  ↓
logout()
  ↓
deleteSession("current")
  ↓
setUser(null)
  ↓
Login screen
```

---

# 15. Why `setUser(null)` Is Important

Imagine you only did:

```jsx id="7v3q1m"
await account.deleteSession({
  sessionId: "current",
});
```

Appwrite would know the user logged out.

But your React application might still have:

```text id="k8w2s5"
user = {
  name: "...",
  email: "..."
}
```

That means your UI could still think the user is logged in.

Therefore:

```jsx id="c4j9p6"
setUser(null);
```

keeps your frontend state synchronized with Appwrite.

---

# 16. Video #15 → #16 → #17

These videos form one complete authentication system.

### Video #15 — Login

```text id="x5q8j2"
login()
   ↓
Appwrite
   ↓
setUser()
```

### Video #16 — Errors

```text id="v6r2m8"
Appwrite Error
   ↓
throw error
   ↓
catch()
   ↓
setError()
   ↓
Show message
```

### Video #17 — Logout

```text id="j9c4w1"
logout()
   ↓
deleteSession()
   ↓
setUser(null)
   ↓
Go to Login
```

---

# ⭐ Video #17 — Must Remember

### Get logout

```jsx id="q2m7x5"
const { logout } = useAuth();
```

### Call logout

```jsx id="e5r8n1"
await logout();
```

### Delete Appwrite session

```jsx id="u7k3p9"
await account.deleteSession({
  sessionId: "current",
});
```

### Clear user

```jsx id="h4c8v2"
setUser(null);
```

### Navigate back to Login

```jsx id="w6n1r4"
router.replace("/login");
```

### Handle errors

```jsx id="k9p3s7"
try {
  await logout();
} catch (error) {
  console.log(error);
}
```

---

# 🧠 Final Mental Model

The most important thing from Video #17:

```text id="a5f8j2"
                 LOGOUT
                    │
                    ↓
              logout() 
                    │
             ┌──────┴──────┐
             ↓             ↓
          Appwrite       React
             ↓             ↓
      deleteSession()  setUser(null)
             │             │
             └──────┬──────┘
                    ↓
             User logged out
                    ↓
             router.replace()
                    ↓
                  LOGIN
```

### 🔑 Core lesson

**Logging out isn't just navigating to the Login screen.**

You must first **destroy the Appwrite session** and **clear the user from your React state**:

```jsx
await account.deleteSession({
  sessionId: "current",
});

setUser(null);
```

Then navigate the user back to Login.
