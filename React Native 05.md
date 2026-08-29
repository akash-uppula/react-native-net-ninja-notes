# 📱 React Native — Video #5 Notes

## Complete React Native Tutorial #5 — Light and Dark Themes

This video introduces **light and dark themes** in React Native/Expo and shows how the app can adapt its appearance based on the device's color scheme. ([YouTube][1])

The key idea is:

```text
Device Theme
     ↓
Light / Dark
     ↓
Our App
     ↓
Different colors/styles
```

---

# 1. What is a Theme?

A **theme** is a collection of visual settings used throughout an application.

For example, a light theme might use:

```text
Background → White
Text       → Black
```

while a dark theme might use:

```text
Background → Black
Text       → White
```

Instead of manually changing every component, we can create styles for different themes.

---

# 2. Light Theme

A light theme normally has:

```text
Light background
Dark text
```

Example:

```tsx
<View
  style={{
    backgroundColor: "white",
  }}
>
  <Text
    style={{
      color: "black",
    }}
  >
    My Shelf
  </Text>
</View>
```

---

# 3. Dark Theme

A dark theme could use:

```text
Dark background
Light text
```

Example:

```tsx
<View
  style={{
    backgroundColor: "black",
  }}
>
  <Text
    style={{
      color: "white",
    }}
  >
    My Shelf
  </Text>
</View>
```

So the **same screen** can have two different appearances.

---

# 4. Detecting the Device's Color Scheme

React Native provides:

```tsx
useColorScheme
```

Import it from React Native:

```tsx
import {
  useColorScheme,
} from "react-native";
```

Then:

```tsx
const colorScheme = useColorScheme();
```

The hook tells us the current preferred color scheme.

Conceptually:

```text
useColorScheme()
       ↓
 ┌─────┴─────┐
 ↓           ↓
light       dark
```

---

# 5. Using `useColorScheme`

Example:

```tsx
import {
  View,
  Text,
  useColorScheme,
} from "react-native";

export default function Home() {
  const colorScheme = useColorScheme();

  return (
    <View>
      <Text>
        Current theme: {colorScheme}
      </Text>
    </View>
  );
}
```

Depending on the device settings, `colorScheme` can indicate:

```text
"light"
```

or:

```text
"dark"
```

React Native's `useColorScheme` hook subscribes to the device's appearance preference, so it can update when the system theme changes.

---

# 6. Conditional Styling

Now we can use the value to choose different styles.

```tsx
import {
  View,
  Text,
  useColorScheme,
} from "react-native";

export default function Home() {
  const colorScheme = useColorScheme();

  const isDark = colorScheme === "dark";

  return (
    <View
      style={{
        backgroundColor: isDark ? "black" : "white",
      }}
    >
      <Text
        style={{
          color: isDark ? "white" : "black",
        }}
      >
        My Shelf
      </Text>
    </View>
  );
}
```

The important part is:

```tsx
const isDark = colorScheme === "dark";
```

Then:

```tsx
backgroundColor: isDark ? "black" : "white"
```

and:

```tsx
color: isDark ? "white" : "black"
```

---

# 7. The Ternary Operator

You'll see this pattern frequently:

```tsx
isDark ? "black" : "white"
```

This is a **ternary operator**.

It means:

```text
condition
   ?
value if true
   :
value if false
```

So:

```tsx
isDark ? "black" : "white"
```

means:

> If `isDark` is true → use `"black"`; otherwise → use `"white"`.

Another example:

```tsx
const title = isDark ? "Dark Mode" : "Light Mode";
```

---

# 8. `StyleSheet`

Instead of putting all our styles directly inside the JSX:

```tsx
<View
  style={{
    backgroundColor: "white",
  }}
>
```

we can use `StyleSheet`.

Import:

```tsx
import {
  StyleSheet,
} from "react-native";
```

Then:

```tsx
const styles = StyleSheet.create({
  container: {
    padding: 20,
  },

  title: {
    fontSize: 24,
  },
});
```

And use:

```tsx
<View style={styles.container}>
  <Text style={styles.title}>
    My Shelf
  </Text>
</View>
```

This keeps our component cleaner.

---

# 9. Theme Styles

We can create different styles depending on the theme.

For example:

```tsx
import {
  StyleSheet,
  Text,
  View,
  useColorScheme,
} from "react-native";

export default function Home() {
  const colorScheme = useColorScheme();

  const isDark = colorScheme === "dark";

  return (
    <View
      style={[
        styles.container,
        isDark && styles.darkContainer,
      ]}
    >
      <Text
        style={[
          styles.title,
          isDark && styles.darkText,
        ]}
      >
        My Shelf
      </Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    backgroundColor: "white",
  },

  darkContainer: {
    backgroundColor: "black",
  },

  title: {
    color: "black",
    fontSize: 24,
  },

  darkText: {
    color: "white",
  },
});
```

Here we're using an important React Native feature:

```tsx
style={[
  styles.container,
  isDark && styles.darkContainer,
]}
```

---

# 10. Multiple Styles

React Native allows the `style` prop to accept an array.

For example:

```tsx
style={[
  styles.container,
  styles.padding,
]}
```

Both styles are applied.

You can also conditionally add a style:

```tsx
style={[
  styles.container,
  isDark && styles.darkContainer,
]}
```

If:

```tsx
isDark === true
```

then:

```text
container
+
darkContainer
```

are applied.

If:

```tsx
isDark === false
```

then only:

```text
container
```

is applied.

---

# 11. A Better Theme Structure

Instead of repeatedly writing:

```tsx
isDark ? "black" : "white"
```

everywhere, we can create theme objects.

For example:

```tsx
const colors = {
  light: {
    background: "white",
    text: "black",
  },

  dark: {
    background: "black",
    text: "white",
  },
};
```

Then select the appropriate theme:

```tsx
const theme =
  colorScheme === "dark"
    ? colors.dark
    : colors.light;
```

Now:

```tsx
<View
  style={{
    backgroundColor: theme.background,
  }}
>
  <Text
    style={{
      color: theme.text,
    }}
  >
    My Shelf
  </Text>
</View>
```

This approach becomes much more useful when an application gets larger.

---

# 12. Our `My Shelf` Example

Let's apply what we've learned to our project.

```tsx
import {
  StyleSheet,
  Text,
  View,
  useColorScheme,
} from "react-native";

const Home = () => {
  const colorScheme = useColorScheme();

  const isDark = colorScheme === "dark";

  return (
    <View
      style={[
        styles.container,
        isDark && styles.darkContainer,
      ]}
    >
      <Text
        style={[
          styles.title,
          isDark && styles.darkText,
        ]}
      >
        My Shelf
      </Text>

      <Text
        style={[
          styles.description,
          isDark && styles.darkText,
        ]}
      >
        Welcome to my bookshelf.
      </Text>
    </View>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: "white",
  },

  darkContainer: {
    backgroundColor: "black",
  },

  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "black",
  },

  description: {
    fontSize: 16,
    color: "black",
  },

  darkText: {
    color: "white",
  },
});
```

---

# 13. What happens here?

First:

```tsx
const colorScheme = useColorScheme();
```

gets the device's current appearance.

Then:

```tsx
const isDark = colorScheme === "dark";
```

checks whether we're in dark mode.

Then:

```tsx
style={[
  styles.container,
  isDark && styles.darkContainer,
]}
```

adds the dark background if necessary.

And:

```tsx
style={[
  styles.title,
  isDark && styles.darkText,
]}
```

changes the text color.

So:

```text
                  Device
                    │
             useColorScheme()
                    │
             ┌──────┴──────┐
             ↓             ↓
           light          dark
             │             │
             ↓             ↓
       Light styles    Dark styles
```

---

# 14. Important: System Theme vs App Theme

There's an important distinction.

### System/device theme

The user's phone itself is set to:

```text
Light
```

or:

```text
Dark
```

`useColorScheme()` lets us respond to that preference.

### App-controlled theme

An app can also provide its own setting:

```text
Appearance
 ├── Light
 ├── Dark
 └── System
```

That is a more advanced setup because we need to store the user's choice and make it available throughout the application.

For now, the important concept from this lesson is **responding to the device's color scheme**.

---

# ⭐ Important Concepts From Video #5

### `useColorScheme`

```tsx
import { useColorScheme } from "react-native";

const colorScheme = useColorScheme();
```

Gets the current system color scheme.

---

### Check for dark mode

```tsx
const isDark = colorScheme === "dark";
```

---

### Ternary

```tsx
isDark ? "black" : "white"
```

Means:

```text
if dark → black
otherwise → white
```

---

### Multiple styles

```tsx
style={[
  styles.container,
  isDark && styles.darkContainer,
]}
```

---

### `StyleSheet`

```tsx
const styles = StyleSheet.create({
  container: {
    padding: 20,
  },
});
```

Used to organize styles.

---

# 🧠 The Main Mental Model

Don't memorize the entire example.

Understand this:

```text
useColorScheme()
       ↓
  "light" / "dark"
       ↓
   Check theme
       ↓
Choose appropriate styles
       ↓
Render UI
```

For example:

```tsx
const isDark = colorScheme === "dark";
```

then:

```tsx
backgroundColor: isDark ? "black" : "white"
```

---

# 🎯 Small Practice

Modify your **My Shelf** app so that:

### Light mode

```text
┌──────────────────────┐
│                      │
│      My Shelf        │
│                      │
│  Welcome to my app   │
│                      │
└──────────────────────┘
```

has:

* white background
* black text

### Dark mode

```text
┌──────────────────────┐
│                      │
│      My Shelf        │
│                      │
│  Welcome to my app   │
│                      │
└──────────────────────┘
```

has:

* black/dark background
* white text

Use:

```tsx
useColorScheme()
```

and:

```tsx
StyleSheet.create()
```

Don't add a manual theme switch yet. **First understand how the app responds automatically to the device's light/dark setting.**

---

## 🔥 What you've learned so far

```text
Video 1
  ↓
Expo + Project Setup

Video 2
  ↓
View + Text + Image

Video 3
  ↓
File-Based Navigation + Link

Video 4
  ↓
_layout.tsx + Stack

Video 5
  ↓
Light/Dark Theme + useColorScheme
```

You're now moving from simply **creating screens** to making those screens behave like a real application.

[1]: https://www.youtube.com/watch?v=42JKBeRTTpk&utm_source=chatgpt.com "Complete React Native Tutorial #5 - Light and Dark Themes - YouTube"
