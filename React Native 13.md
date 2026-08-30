# 📱 React Native — Video #13 Notes

## Complete React Native Tutorial #13 — Login & Signup Forms

> **Note:** Video #12 comes before this in the playlist. Since you asked specifically for **Video #13**, these notes focus on the Login & Signup Forms video.

The main goal of this video is to build **forms** where users can enter information such as:

```text
Email
Password
Name
```

and then submit that information.

---

# 1. What is a Form?

A form is a collection of input fields that allows the user to enter data.

For example, a login form:

```text
┌─────────────────────────┐
│         Login           │
│                         │
│  Email                  │
│  ┌───────────────────┐  │
│  │                   │  │
│  └───────────────────┘  │
│                         │
│  Password               │
│  ┌───────────────────┐  │
│  │                   │  │
│  └───────────────────┘  │
│                         │
│      [ Login ]          │
│                         │
└─────────────────────────┘
```

---

# 2. `TextInput`

The main component used for entering text is:

```tsx
TextInput
```

Import it using ES6:

```tsx
import { TextInput } from "react-native";
```

Basic example:

```tsx
<TextInput />
```

---

# 3. Basic `TextInput`

You can give it a placeholder:

```tsx
<TextInput
  placeholder="Enter your email"
/>
```

The placeholder is displayed when the input is empty.

Example:

```text
┌─────────────────────────┐
│ Enter your email        │
└─────────────────────────┘
```

Once the user starts typing, the placeholder disappears.

---

# 4. Styling `TextInput`

Just like `View` and `Text`, you can style `TextInput`.

```tsx
<TextInput
  style={styles.input}
  placeholder="Enter your email"
/>
```

Then:

```tsx
const styles = StyleSheet.create({
  input: {
    borderWidth: 1,
    padding: 10,
    marginBottom: 15,
  },
});
```

---

# 5. `TextInput` Doesn't Store Your Data Automatically

This is an important React concept.

If the user types:

```text
akash@gmail.com
```

you normally need to keep that value in **state** if your component needs to use it.

That's where:

```tsx
useState
```

comes in.

---

# 6. `useState`

Import:

```tsx
import { useState } from "react";
```

Create state:

```tsx
const [email, setEmail] = useState("");
```

This gives you:

```text
email
   ↓
current value

setEmail
   ↓
function used to update value
```

---

# 7. Connecting `TextInput` to State

Use:

```tsx
value={email}
```

and:

```tsx
onChangeText={setEmail}
```

Example:

```tsx
<TextInput
  value={email}
  onChangeText={setEmail}
  placeholder="Enter your email"
/>
```

Now the flow is:

```text
User types
    ↓
onChangeText
    ↓
setEmail()
    ↓
email state updates
    ↓
TextInput displays new value
```

---

# 8. `onChangeText`

`onChangeText` is specifically useful for `TextInput`.

Example:

```tsx
<TextInput
  onChangeText={(text) => {
    console.log(text);
  }}
/>
```

Every time the user changes the text, the callback runs.

For example:

```text
User types: A
→ "A"

User types: k
→ "Ak"

User types: a
→ "Aka"

User types: s
→ "Akas"
```

---

# 9. Controlled Input

When you use:

```tsx
value={email}
onChangeText={setEmail}
```

the `TextInput` becomes a **controlled input**.

Example:

```tsx
const [email, setEmail] = useState("");

<TextInput
  value={email}
  onChangeText={setEmail}
/>
```

React state is now controlling the input's value.

Think:

```text
TextInput
   ↕
React State
```

---

# 10. Login Form Example

A simple login form:

```tsx
import {
  StyleSheet,
  Text,
  TextInput,
  View,
} from "react-native";

import { useState } from "react";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  return (
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
      />
    </View>
  );
};

export default Login;

const styles = StyleSheet.create({
  container: {
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
});
```

---

# 11. Password Input

You generally don't want the password to be displayed as normal text.

Use:

```tsx
secureTextEntry
```

Example:

```tsx
<TextInput
  style={styles.input}
  placeholder="Password"
  secureTextEntry
/>
```

Instead of:

```text
password123
```

the user sees something like:

```text
•••••••••••
```

---

# 12. `secureTextEntry`

Remember:

```tsx
secureTextEntry
```

is a boolean prop.

You can write:

```tsx
secureTextEntry={true}
```

or simply:

```tsx
secureTextEntry
```

because JSX allows shorthand boolean props.

---

# 13. `keyboardType`

You can control the keyboard displayed for an input.

For email:

```tsx
keyboardType="email-address"
```

Example:

```tsx
<TextInput
  placeholder="Email"
  keyboardType="email-address"
/>
```

This helps provide a keyboard appropriate for entering an email address.

---

# 14. Common `keyboardType` Values

Some useful values include:

```text
default
email-address
numeric
phone-pad
```

For example:

```tsx
keyboardType="numeric"
```

for numbers.

---

# 15. `autoCapitalize`

You can control automatic capitalization.

For example:

```tsx
autoCapitalize="none"
```

This is useful for email addresses and passwords.

Example:

```tsx
<TextInput
  placeholder="Email"
  autoCapitalize="none"
/>
```

You don't want the keyboard automatically changing:

```text
akash@gmail.com
```

into something unexpected.

---

# 16. Login Button

You can use the `Button` component:

```tsx
import { Button } from "react-native";
```

Then:

```tsx
<Button
  title="Login"
  onPress={() => {
    console.log(email);
    console.log(password);
  }}
/>
```

When the user presses Login, you can access the values stored in state.

---

# 17. `onPress`

You already learned `onPress` in Video #8.

The same concept applies here:

```tsx
<Button
  title="Login"
  onPress={handleLogin}
/>
```

with:

```tsx
const handleLogin = () => {
  console.log(email);
  console.log(password);
};
```

So the concepts connect:

```text
Video #8
Pressable / onPress
       ↓
Video #13
Submit the form
```

---

# 18. Using `Pressable` Instead of `Button`

Since you already learned `Pressable`, you can also create a custom login button:

```tsx
<Pressable
  onPress={handleLogin}
  style={styles.button}
>
  <Text style={styles.buttonText}>
    Login
  </Text>
</Pressable>
```

This gives you much more control over the appearance.

---

# 19. Complete Login Form

Here's a clean version combining the concepts:

```tsx
import {
  Pressable,
  StyleSheet,
  Text,
  TextInput,
  View,
} from "react-native";

import { useState } from "react";

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = () => {
    console.log("Email:", email);
    console.log("Password:", password);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>
        Login
      </Text>

      <TextInput
        style={styles.input}
        placeholder="Email"
        value={email}
        onChangeText={setEmail}
        keyboardType="email-address"
        autoCapitalize="none"
      />

      <TextInput
        style={styles.input}
        placeholder="Password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
        autoCapitalize="none"
      />

      <Pressable
        onPress={handleLogin}
        style={({ pressed }) => [
          styles.button,
          pressed && styles.pressed,
        ]}
      >
        <Text style={styles.buttonText}>
          Login
        </Text>
      </Pressable>
    </View>
  );
};

export default Login;

const styles = StyleSheet.create({
  container: {
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

  button: {
    padding: 15,
    alignItems: "center",
    backgroundColor: "black",
  },

  pressed: {
    opacity: 0.5,
  },

  buttonText: {
    color: "white",
    fontWeight: "bold",
  },
});
```

---

# 20. Signup Form

A signup form usually needs more information.

For example:

```text
Name
Email
Password
Confirm Password
```

So we could have:

```tsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
const [confirmPassword, setConfirmPassword] =
  useState("");
```

Each input gets its own state.

---

# 21. Signup Example

```tsx
<TextInput
  style={styles.input}
  placeholder="Name"
  value={name}
  onChangeText={setName}
/>

<TextInput
  style={styles.input}
  placeholder="Email"
  value={email}
  onChangeText={setEmail}
  keyboardType="email-address"
  autoCapitalize="none"
/>

<TextInput
  style={styles.input}
  placeholder="Password"
  value={password}
  onChangeText={setPassword}
  secureTextEntry
/>

<TextInput
  style={styles.input}
  placeholder="Confirm Password"
  value={confirmPassword}
  onChangeText={setConfirmPassword}
  secureTextEntry
/>
```

---

# 22. Form Data Flow

This is the most important concept.

For an email input:

```text
User types
    ↓
TextInput
    ↓
onChangeText
    ↓
setEmail()
    ↓
email state
```

For password:

```text
User types
    ↓
TextInput
    ↓
onChangeText
    ↓
setPassword()
    ↓
password state
```

Then when Login is pressed:

```text
Login button
     ↓
handleLogin()
     ↓
email + password
     ↓
send to authentication/API
```

---

# 23. `TextInput` Props to Remember

You don't need to memorize every prop, but these are important:

| Prop              | Purpose                 |
| ----------------- | ----------------------- |
| `value`           | Current input value     |
| `onChangeText`    | Runs when text changes  |
| `placeholder`     | Hint shown when empty   |
| `secureTextEntry` | Hides password text     |
| `keyboardType`    | Controls keyboard type  |
| `autoCapitalize`  | Controls capitalization |
| `style`           | Styles the input        |

---

# 24. `value` + `onChangeText`

This combination is especially important:

```tsx
<TextInput
  value={email}
  onChangeText={setEmail}
/>
```

Remember it as:

```text
value
  ↓
"What is currently inside?"

onChangeText
  ↓
"What should happen when it changes?"
```

---

# 25. Why Use State?

Without state:

```tsx
<TextInput />
```

you can display an input.

But when you need to actually **use the entered information**, you need to capture it.

For example:

```tsx
const [email, setEmail] = useState("");
```

Now you can do:

```tsx
console.log(email);
```

or eventually:

```text
email
 ↓
API
 ↓
Authentication server
```

---

# 26. Forms + Navigation

You already learned Expo Router.

So after successful login, you could eventually navigate:

```text
Login
  ↓
Authentication successful
  ↓
Home
```

For example, using the router:

```tsx
import { router } from "expo-router";
```

Then:

```tsx
router.replace("/(tabs)");
```

The actual authentication logic will come later; for now, understand how the form data can trigger navigation.

---

# 27. `TouchableWithoutFeedback` — Close the Keyboard

When a user taps a `TextInput`, the keyboard opens:

```text
┌─────────────────────────┐
│ Email                   │
│ ┌─────────────────────┐ │
│ │ akash@gmail.com     │ │
│ └─────────────────────┘ │
│                         │
│        Content          │
├─────────────────────────┤
│      KEYBOARD           │
│  Q W E R T Y U I O P   │
└─────────────────────────┘
```

Sometimes you want the keyboard to disappear when the user taps somewhere outside the input.

For this, we can use:

```tsx
TouchableWithoutFeedback
```

---

### 1. Import it

Using ES6 `import`:

```tsx
import {
  Keyboard,
  TouchableWithoutFeedback,
} from "react-native";
```

`Keyboard` is used to control the keyboard, while `TouchableWithoutFeedback` detects the user's tap.

---

### 2. `Keyboard.dismiss()`

React Native provides:

```tsx
Keyboard.dismiss();
```

This closes the currently open keyboard.

Example:

```tsx
onPress={() => Keyboard.dismiss()}
```

---

### 3. Combine Them

Wrap your screen with:

```tsx
<TouchableWithoutFeedback
  onPress={Keyboard.dismiss}
>
  <View>
    {/* content */}
  </View>
</TouchableWithoutFeedback>
```

Now, when the user taps outside the `TextInput`:

```text
User taps screen
      ↓
TouchableWithoutFeedback
      ↓
onPress
      ↓
Keyboard.dismiss()
      ↓
Keyboard closes
```

---

## 4. Complete Login Example

```tsx
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

const Login = () => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = () => {
    console.log(email);
    console.log(password);
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
          keyboardType="email-address"
          autoCapitalize="none"
        />

        <TextInput
          style={styles.input}
          placeholder="Password"
          value={password}
          onChangeText={setPassword}
          secureTextEntry
          autoCapitalize="none"
        />

        <Pressable
          onPress={handleLogin}
          style={styles.button}
        >
          <Text style={styles.buttonText}>
            Login
          </Text>
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

  button: {
    padding: 15,
    alignItems: "center",
  },

  buttonText: {
    fontWeight: "bold",
  },
});
```

---

## 5. Why `TouchableWithoutFeedback`?

The name can be confusing.

It means:

> Make something respond to a touch **without providing visual feedback**.

Unlike a `Pressable`, it doesn't automatically change appearance when pressed.

Here, we're mainly interested in detecting the tap:

```tsx
<TouchableWithoutFeedback
  onPress={Keyboard.dismiss}
>
```

We don't need a visible button because the **whole surrounding area** can respond to the touch.

---

## 6. Important: Don't Wrap the `TextInput` Separately

You generally want to wrap the **whole screen/content**:

```tsx
<TouchableWithoutFeedback
  onPress={Keyboard.dismiss}
>
  <View>
    <TextInput />
    <TextInput />
    <Pressable />
  </View>
</TouchableWithoutFeedback>
```

Not:

```tsx
<TextInput>
  ...
</TextInput>
```

The idea is:

```text
TouchableWithoutFeedback
          │
          ↓
     Entire screen
       │   │   │
       ↓   ↓   ↓
     Input Input Button
```

So tapping outside the inputs can dismiss the keyboard.

---

# ⭐ Add This to Your Video #13 Must Remember

### Close keyboard when tapping outside

Import:

```tsx
import {
  Keyboard,
  TouchableWithoutFeedback,
} from "react-native";
```

Wrap your screen:

```tsx
<TouchableWithoutFeedback
  onPress={Keyboard.dismiss}
>
  <View>
    {/* content */}
  </View>
</TouchableWithoutFeedback>
```

The key line is:

```tsx
Keyboard.dismiss
```

which closes the keyboard.

### Mental model

```text
TouchableWithoutFeedback
          ↓
      onPress
          ↓
   Keyboard.dismiss()
          ↓
    Keyboard closes
```

So your **Video #13 core concepts** now include:

```text
TextInput
   ↓
useState
   ↓
value + onChangeText
   ↓
secureTextEntry
   ↓
keyboardType
   ↓
autoCapitalize
   ↓
Pressable / Button
   ↓
TouchableWithoutFeedback
   ↓
Keyboard.dismiss()
```

This is a very useful pattern to remember for almost every React Native form.


---

# ⭐ Video #13 — Must Remember

### `TextInput`

```tsx
import { TextInput } from "react-native";
```

### Basic input

```tsx
<TextInput
  placeholder="Email"
/>
```

### State

```tsx
const [email, setEmail] = useState("");
```

### Controlled input

```tsx
<TextInput
  value={email}
  onChangeText={setEmail}
/>
```

### Password

```tsx
<TextInput
  secureTextEntry
/>
```

### Email keyboard

```tsx
keyboardType="email-address"
```

### Disable capitalization

```tsx
autoCapitalize="none"
```

### Submit

```tsx
<Pressable onPress={handleLogin}>
```

---

# 🧠 The Most Important Concept

Don't just memorize `TextInput`.

Understand this cycle:

```text
                  USER
                    │
                    ↓
              Types something
                    │
                    ↓
               TextInput
                    │
                    ↓
              onChangeText
                    │
                    ↓
              setEmail(...)
                    │
                    ↓
                useState
                    │
                    ↓
             email variable
                    │
                    ↓
              handleLogin()
                    │
                    ↓
              Login / API
```

That's the foundation of almost every form you'll build in React Native.

---

# 🎯 My Shelf Login Screen

For your **My Shelf** app, you can now create:

```text
┌────────────────────────────┐
│                            │
│          My Shelf          │
│                            │
│          Login             │
│                            │
│  Email                     │
│  ┌──────────────────────┐  │
│  │                      │  │
│  └──────────────────────┘  │
│                            │
│  Password                  │
│  ┌──────────────────────┐  │
│  │ •••••••••••          │  │
│  └──────────────────────┘  │
│                            │
│       [ Login ]            │
│                            │
│     Create account         │
│                            │
└────────────────────────────┘
```

This combines several things you've already learned:

```text
Text
 ↓
TextInput
 ↓
useState
 ↓
Pressable
 ↓
onPress
 ↓
Expo Router
```

So **Video #13 is where your React Native UI starts becoming interactive and data-driven**, rather than just displaying static content.
