# 📱 React Native — Video #6 Notes

## Complete React Native Tutorial #6 — Themed UI Components

This video builds directly on **Video #5 (Light & Dark Themes)**.

The main idea is:

> Instead of manually checking `useColorScheme()` inside every component, create **reusable themed components** that automatically use the correct colors.

---

# 1. The Problem With Video #5

In Video #5, we learned:

```tsx
const colorScheme = useColorScheme();

const isDark = colorScheme === "dark";
```

Then we might do this everywhere:

```tsx
<Text
  style={{
    color: isDark ? "white" : "black",
  }}
>
  My Shelf
</Text>
```

And for every `View`:

```tsx
<View
  style={{
    backgroundColor: isDark ? "black" : "white",
  }}
>
```

Imagine having **50 Text components**.

We would have to repeatedly write:

```tsx
isDark ? "white" : "black"
```

That's not a good approach.

---

# 2. The Solution — Themed Components

We can create reusable components such as:

```text
ThemedView
ThemedText
```

Then instead of:

```tsx
<Text style={{ color: isDark ? "white" : "black" }}>
  Hello
</Text>
```

we can simply write:

```tsx
<ThemedText>
  Hello
</ThemedText>
```

The component itself handles the theme.

---

# 3. Creating a `ThemedText`

We can create a separate file:

```text
components/
└── ThemedText.tsx
```

Then:

```tsx
import {
  StyleSheet,
  Text,
  TextProps,
  useColorScheme,
} from "react-native";

const ThemedText = (props: TextProps) => {
  const colorScheme = useColorScheme();

  const color =
    colorScheme === "dark"
      ? "white"
      : "black";

  return (
    <Text
      {...props}
      style={[
        styles.text,
        props.style,
        { color },
      ]}
    />
  );
};

export default ThemedText;

const styles = StyleSheet.create({
  text: {
    fontSize: 16,
  },
});
```

Now we have our own reusable component.

---

# 4. Using `ThemedText`

Instead of:

```tsx
import { Text } from "react-native";
```

we can use:

```tsx
import ThemedText from "../components/ThemedText";
```

Then:

```tsx
<ThemedText>
  My Shelf
</ThemedText>
```

The component automatically determines whether the device is using:

```text
light
```

or:

```text
dark
```

and chooses the appropriate text color.

---

# 5. `ThemedView`

We can do the same thing with `View`.

Create:

```text
components/
└── ThemedView.tsx
```

Example:

```tsx
import {
  StyleSheet,
  View,
  ViewProps,
  useColorScheme,
} from "react-native";

const ThemedView = (props: ViewProps) => {
  const colorScheme = useColorScheme();

  const backgroundColor =
    colorScheme === "dark"
      ? "#121212"
      : "#ffffff";

  return (
    <View
      {...props}
      style={[
        styles.container,
        props.style,
        { backgroundColor },
      ]}
    />
  );
};

export default ThemedView;

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

Now we have:

```text
ThemedView
      ↓
Checks system theme
      ↓
 ┌────┴────┐
 ↓         ↓
Light     Dark
 ↓         ↓
White     Dark
background background
```

---

# 6. Why `...props`?

You'll see this:

```tsx
<Text {...props} />
```

or:

```tsx
<View {...props} />
```

The:

```tsx
...props
```

is the **spread operator**.

It passes all the props received by our custom component to the underlying React Native component.

For example:

```tsx
<ThemedText
  style={styles.title}
  numberOfLines={2}
>
  My Book
</ThemedText>
```

Our `ThemedText` receives:

```text
style
numberOfLines
children
```

Then:

```tsx
<Text {...props} />
```

passes those props to the actual `Text`.

---

# 7. What are `TextProps` and `ViewProps`?

React Native provides TypeScript types for component props.

For `Text`:

```tsx
import { TextProps } from "react-native";
```

For `View`:

```tsx
import { ViewProps } from "react-native";
```

Then:

```tsx
const ThemedText = (props: TextProps) => {
```

means:

> `ThemedText` accepts the same type of props that a normal React Native `Text` accepts.

Similarly:

```tsx
const ThemedView = (props: ViewProps) => {
```

means:

> `ThemedView` accepts the same type of props as a normal `View`.

This gives us **TypeScript type safety**.

---

# 8. `children`

When we write:

```tsx
<ThemedText>
  My Shelf
</ThemedText>
```

the text:

```text
My Shelf
```

is passed as a prop called:

```tsx
children
```

Conceptually:

```text
<ThemedText>
    My Shelf
</ThemedText>

        ↓

props.children
```

We don't necessarily need to access `children` ourselves because:

```tsx
<Text {...props} />
```

passes it along automatically.

---

# 9. Passing Styles

We still want to be able to customize our themed components.

For example:

```tsx
<ThemedText
  style={{
    fontSize: 30,
    fontWeight: "bold",
  }}
>
  My Shelf
</ThemedText>
```

Our component should support this.

That's why we use:

```tsx
style={[
  styles.text,
  props.style,
  { color },
]}
```

This combines multiple styles.

---

# 10. Why the Style Array?

Suppose we have:

```tsx
styles.text
```

which contains:

```tsx
{
  fontSize: 16
}
```

And the user passes:

```tsx
style={{
  fontSize: 28,
  fontWeight: "bold"
}}
```

Then:

```tsx
style={[
  styles.text,
  props.style,
]}
```

combines them.

The later style can override earlier values when they conflict.

---

# 11. Theme Colors

Instead of scattering colors throughout the application:

```tsx
"white"
"black"
"#121212"
"#ffffff"
```

we can define a central color object.

For example:

```tsx
const Colors = {
  light: {
    text: "#111111",
    background: "#ffffff",
  },

  dark: {
    text: "#ffffff",
    background: "#111111",
  },
};
```

Now:

```tsx
const theme =
  colorScheme === "dark"
    ? Colors.dark
    : Colors.light;
```

Then:

```tsx
theme.text
```

and:

```tsx
theme.background
```

give us the correct colors.

---

# 12. A Better Structure

A common organization is:

```text
project/
│
├── app/
│
├── components/
│   ├── ThemedText.tsx
│   └── ThemedView.tsx
│
└── constants/
    └── Colors.ts
```

Then:

```text
Colors
   ↓
ThemedView / ThemedText
   ↓
Screens
```

This separates responsibilities.

---

# 13. `Colors.ts`

For example:

```tsx
const Colors = {
  light: {
    text: "#111111",
    background: "#ffffff",
  },

  dark: {
    text: "#ffffff",
    background: "#111111",
  },
};

export default Colors;
```

Notice the ES6 export:

```tsx
export default Colors;
```

And import it with:

```tsx
import Colors from "../constants/Colors";
```

This follows the **`import` / `export` style** we've been using.

---

# 14. Improved `ThemedText`

Now our component can use the centralized colors:

```tsx
import {
  StyleSheet,
  Text,
  TextProps,
  useColorScheme,
} from "react-native";

import Colors from "../constants/Colors";

const ThemedText = (props: TextProps) => {
  const colorScheme = useColorScheme();

  const theme =
    colorScheme === "dark"
      ? Colors.dark
      : Colors.light;

  return (
    <Text
      {...props}
      style={[
        styles.text,
        props.style,
        {
          color: theme.text,
        },
      ]}
    />
  );
};

export default ThemedText;

const styles = StyleSheet.create({
  text: {
    fontSize: 16,
  },
});
```

Now our color management is centralized.

---

# 15. Improved `ThemedView`

```tsx
import {
  StyleSheet,
  View,
  ViewProps,
  useColorScheme,
} from "react-native";

import Colors from "../constants/Colors";

const ThemedView = (props: ViewProps) => {
  const colorScheme = useColorScheme();

  const theme =
    colorScheme === "dark"
      ? Colors.dark
      : Colors.light;

  return (
    <View
      {...props}
      style={[
        styles.container,
        props.style,
        {
          backgroundColor: theme.background,
        },
      ]}
    />
  );
};

export default ThemedView;

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

---

# 16. Using Them in Our App

Now our `Home` screen becomes much cleaner.

```tsx
import ThemedText from "../components/ThemedText";
import ThemedView from "../components/ThemedView";

const Home = () => {
  return (
    <ThemedView>
      <ThemedText>
        My Shelf
      </ThemedText>

      <ThemedText>
        Welcome to my bookshelf.
      </ThemedText>
    </ThemedView>
  );
};

export default Home;
```

Notice something important:

We no longer have:

```tsx
useColorScheme()
```

inside the screen.

We don't have:

```tsx
isDark ? "white" : "black"
```

either.

The themed components handle that for us.

---

# 17. Why This Is Better

### Without themed components

Every screen might contain:

```tsx
const colorScheme = useColorScheme();

const isDark = colorScheme === "dark";
```

and:

```tsx
isDark ? "white" : "black"
```

again and again.

### With themed components

We simply write:

```tsx
<ThemedView>
  <ThemedText>
    My Shelf
  </ThemedText>
</ThemedView>
```

Much cleaner.

---

# 18. The Big Concept

The architecture becomes:

```text
                  Colors
                 /      \
                ↓        ↓
         ThemedText   ThemedView
                \        /
                 ↓      ↓
                    App
                     ↓
                  Screens
```

The screen doesn't need to know exactly how dark mode works.

The themed components handle it.

This is an example of **reusable components + separation of concerns**.

---

# ⭐ Video #6 — Must Remember

### 1. Themed components

Create reusable components such as:

```tsx
<ThemedText>
  Hello
</ThemedText>
```

and:

```tsx
<ThemedView>
  ...
</ThemedView>
```

---

### 2. `useColorScheme()`

The themed component can determine:

```tsx
const colorScheme = useColorScheme();
```

---

### 3. Props forwarding

```tsx
<Text {...props} />
```

passes received props to the underlying component.

---

### 4. `TextProps`

```tsx
import { TextProps } from "react-native";
```

allows our custom text component to accept normal Text props.

---

### 5. `ViewProps`

```tsx
import { ViewProps } from "react-native";
```

allows our custom View component to accept normal View props.

---

### 6. `props.style`

Allow users to customize the component:

```tsx
style={[
  styles.text,
  props.style,
]}
```

---

### 7. Centralized colors

Instead of repeating colors throughout the application:

```tsx
Colors.light
Colors.dark
```

can contain our theme colors.

---

# 🧠 Video #5 vs Video #6

This distinction is **very important**.

### Video #5

We learned **how to detect and respond to the theme**:

```tsx
const colorScheme = useColorScheme();

const isDark = colorScheme === "dark";
```

Then:

```tsx
isDark ? "white" : "black"
```

---

### Video #6

We take that logic and put it into **reusable components**:

```text
useColorScheme()
      ↓
ThemedText
ThemedView
      ↓
Screens
```

So instead of every screen worrying about themes:

```tsx
<ThemedText>
  My Shelf
</ThemedText>
```

does the work.

---

# 🎯 Small My Shelf Example

Your current app can now look like:

```text
components/
├── ThemedText.tsx
└── ThemedView.tsx

constants/
└── Colors.ts

app/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

And your `index.tsx` can be:

```tsx
import ThemedText from "../components/ThemedText";
import ThemedView from "../components/ThemedView";

const Home = () => {
  return (
    <ThemedView>
      <ThemedText
        style={{
          fontSize: 28,
          fontWeight: "bold",
        }}
      >
        My Shelf
      </ThemedText>

      <ThemedText>
        Welcome to my bookshelf.
      </ThemedText>
    </ThemedView>
  );
};

export default Home;
```

Now the same code automatically works in:

```text
☀️ Light Mode
     ↓
White background
Dark text
```

and:

```text
🌙 Dark Mode
     ↓
Dark background
Light text
```

without changing the screen itself.

---

## 🔥 The key takeaway

**Video 5:**

> "How can my app know whether the device is light or dark?"

```tsx
useColorScheme()
```

**Video 6:**

> "How can I avoid repeating that theme logic everywhere?"

```text
Create reusable Themed Components
```

That's the progression you should understand before moving to the next video.
