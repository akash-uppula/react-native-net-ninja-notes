# 📱 React Native — Video #8 Notes

## Complete React Native Tutorial #8 — Pressable Component

This video introduces **`Pressable`**, one of the core React Native components for handling user touches.

Until now, we used things like:

```tsx
<Link href="/books">
  My Books
</Link>
```

Now we're learning how to create **interactive/touchable UI ourselves**.

---

# 1. What is `Pressable`?

`Pressable` is a React Native component that detects different types of user interaction.

Import it using ES6:

```tsx
import { Pressable } from "react-native";
```

Basic example:

```tsx
<Pressable>
  <Text>Press Me</Text>
</Pressable>
```

When the user touches the `Pressable`, React Native can respond to that interaction.

Think:

```text
User touches screen
        ↓
   Pressable
        ↓
   Event happens
        ↓
   Run our function
```

---

# 2. `onPress`

The most commonly used Pressable prop is:

```tsx
onPress
```

Example:

```tsx
import { Pressable, Text } from "react-native";

const Home = () => {
  const handlePress = () => {
    console.log("Button pressed");
  };

  return (
    <Pressable onPress={handlePress}>
      <Text>Press Me</Text>
    </Pressable>
  );
};

export default Home;
```

When the user presses the component:

```text
Press Me
   ↓
onPress
   ↓
handlePress()
   ↓
"Button pressed"
```

---

# 3. Passing a Function to `onPress`

Notice:

```tsx
onPress={handlePress}
```

not:

```tsx
onPress={handlePress()}
```

### Correct

```tsx
<Pressable onPress={handlePress}>
```

We're passing the function.

### Incorrect for this case

```tsx
<Pressable onPress={handlePress()}>
```

That calls the function immediately while rendering instead of waiting for the press.

Remember:

```text
onPress={function}
       ↓
"Run this when pressed"
```

---

# 4. Inline Functions

You can also write:

```tsx
<Pressable
  onPress={() => {
    console.log("Pressed");
  }}
>
  <Text>Press Me</Text>
</Pressable>
```

This is an **arrow function**.

You can use either:

```tsx
onPress={handlePress}
```

or:

```tsx
onPress={() => {
  handlePress();
}}
```

For simple handlers, the first is usually cleaner.

---

# 5. `Pressable` Doesn't Look Like a Button

This is important.

Unlike:

```tsx
<Button title="Press Me" />
```

`Pressable` doesn't automatically give you a standard button appearance.

You decide how it looks.

For example:

```tsx
<Pressable style={styles.button}>
  <Text style={styles.buttonText}>
    Press Me
  </Text>
</Pressable>
```

Styles:

```tsx
const styles = StyleSheet.create({
  button: {
    padding: 15,
    backgroundColor: "blue",
  },

  buttonText: {
    color: "white",
    textAlign: "center",
  },
});
```

So:

```text
Pressable
   ↓
Interaction
+
Your styles
   ↓
Custom button
```

---

# 6. `Pressable` Can Wrap Other Components

You don't have to put only `Text` inside it.

For example:

```tsx
<Pressable onPress={handlePress}>
  <View>
    <Text>My Book</Text>
  </View>
</Pressable>
```

Or:

```tsx
<Pressable onPress={handlePress}>
  <Image source={bookImage} />
</Pressable>
```

So `Pressable` can make other UI elements interactive.

---

# 7. `onPressIn`

`Pressable` gives us more than just `onPress`.

One is:

```tsx
onPressIn
```

It runs **when the user starts pressing**.

```tsx
<Pressable
  onPressIn={() => {
    console.log("Press started");
  }}
>
  <Text>Press Me</Text>
</Pressable>
```

Conceptually:

```text
Finger touches
     ↓
 onPressIn
```

---

# 8. `onPressOut`

Another event is:

```tsx
onPressOut
```

It runs when the press is released.

```tsx
<Pressable
  onPressOut={() => {
    console.log("Press released");
  }}
>
  <Text>Press Me</Text>
</Pressable>
```

Conceptually:

```text
Finger released
      ↓
 onPressOut
```

---

# 9. Press Event Sequence

A simple press generally follows:

```text
Touch starts
     ↓
 onPressIn
     ↓
Touch continues
     ↓
Touch released
     ↓
 onPressOut
     ↓
 onPress
```

So:

```text
onPressIn  → start
onPressOut → release
onPress    → completed press
```

---

# 10. `onLongPress`

Pressable also supports:

```tsx
onLongPress
```

This runs when the user holds the component for a certain amount of time.

Example:

```tsx
<Pressable
  onLongPress={() => {
    console.log("Long pressed");
  }}
>
  <Text>Hold Me</Text>
</Pressable>
```

So now we can distinguish:

```text
Quick press
   ↓
onPress

Long press
   ↓
onLongPress
```

---

# 11. `delayLongPress`

You can control how long the user needs to hold before `onLongPress` fires.

```tsx
<Pressable
  onLongPress={() => {
    console.log("Long pressed");
  }}
  delayLongPress={1000}
>
  <Text>Hold Me</Text>
</Pressable>
```

Here:

```text
1000 ms = 1 second
```

So the user needs to hold for roughly one second before the long-press callback fires.

---

# 12. Pressable `style` Can Be a Function

This is one of the most useful features of `Pressable`.

Instead of:

```tsx
style={styles.button}
```

you can provide a function:

```tsx
style={({ pressed }) => ...}
```

React Native gives you information about whether the component is currently being pressed.

Example:

```tsx
<Pressable
  style={({ pressed }) => [
    styles.button,
    pressed && styles.pressed,
  ]}
>
  <Text>Press Me</Text>
</Pressable>
```

---

# 13. The `pressed` Value

The `pressed` value is a boolean:

```text
true
false
```

When the user is pressing:

```text
pressed = true
```

When they're not:

```text
pressed = false
```

So:

```tsx
pressed && styles.pressed
```

means:

> If `pressed` is true, apply `styles.pressed`.

---

# 14. Creating a Press Effect

For example:

```tsx
import {
  Pressable,
  StyleSheet,
  Text,
} from "react-native";

const MyButton = () => {
  return (
    <Pressable
      style={({ pressed }) => [
        styles.button,
        pressed && styles.pressed,
      ]}
    >
      <Text style={styles.text}>
        Press Me
      </Text>
    </Pressable>
  );
};

export default MyButton;

const styles = StyleSheet.create({
  button: {
    padding: 15,
    backgroundColor: "black",
  },

  pressed: {
    opacity: 0.5,
  },

  text: {
    color: "white",
    textAlign: "center",
  },
});
```

Normal:

```text
┌────────────────┐
│    Press Me    │
└────────────────┘
```

While pressing:

```text
┌────────────────┐
│    Press Me    │  ← opacity changes
└────────────────┘
```

This gives the user visual feedback.

---

# 15. `Pressable` and `Link`

Remember Video #3?

We learned:

```tsx
<Link href="/about">
  About
</Link>
```

We also saw:

```tsx
<Link href="/about" asChild>
  <Pressable>
    <Text>About</Text>
  </Pressable>
</Link>
```

Now you can understand what's happening.

```text
Link
 ↓
Navigation
 ↓
Pressable
 ↓
User interaction
```

`Link` handles **navigation**, while `Pressable` provides the **pressable UI**.

---

# 16. Example With `asChild`

```tsx
import { Link } from "expo-router";
import {
  Pressable,
  Text,
} from "react-native";

const Home = () => {
  return (
    <Link href="/books" asChild>
      <Pressable style={styles.button}>
        <Text style={styles.text}>
          My Books
        </Text>
      </Pressable>
    </Link>
  );
};
```

Now the user sees a custom button, but pressing it navigates to:

```text
/books
```

This is a very useful pattern.

---

# 17. `Pressable` vs `Link`

Don't confuse them.

### `Pressable`

Used for **detecting interaction**.

```tsx
<Pressable onPress={handlePress}>
```

### `Link`

Used for **navigation**.

```tsx
<Link href="/books">
```

Together:

```tsx
<Link href="/books" asChild>
  <Pressable>
    ...
  </Pressable>
</Link>
```

means:

> Make this custom UI pressable and use the press to navigate to `/books`.

---

# 18. Small `My Shelf` Example

Let's use everything we've learned so far.

```tsx
import {
  Pressable,
  StyleSheet,
  Text,
  View,
} from "react-native";

const Home = () => {
  const handlePress = () => {
    console.log("Book pressed");
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>
        My Shelf
      </Text>

      <Pressable
        onPress={handlePress}
        style={({ pressed }) => [
          styles.book,
          pressed && styles.pressed,
        ]}
      >
        <Text style={styles.bookText}>
          The Alchemist
        </Text>
      </Pressable>
    </View>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    padding: 20,
  },

  title: {
    fontSize: 28,
    fontWeight: "bold",
  },

  book: {
    padding: 20,
    marginTop: 20,
    borderWidth: 1,
  },

  pressed: {
    opacity: 0.5,
  },

  bookText: {
    fontSize: 18,
  },
});
```

When the user taps:

```text
┌─────────────────────┐
│   The Alchemist     │
└─────────────────────┘
```

`handlePress()` runs.

---

# 19. One Important Difference From `Button`

React Native also has:

```tsx
<Button
  title="Press Me"
  onPress={handlePress}
/>
```

But `Button` is relatively limited in appearance.

`Pressable` gives you much more control:

```text
Button
 ↓
Predefined button UI

Pressable
 ↓
You design the UI yourself
```

That's why `Pressable` is extremely useful for custom interfaces.

---

# ⭐ Video #8 — Must Remember

### 1. Import

```tsx
import { Pressable } from "react-native";
```

### 2. Basic usage

```tsx
<Pressable>
  <Text>Press Me</Text>
</Pressable>
```

### 3. `onPress`

```tsx
<Pressable onPress={handlePress}>
```

Runs when the press is completed.

### 4. `onPressIn`

```tsx
onPressIn={handlePressIn}
```

Runs when pressing starts.

### 5. `onPressOut`

```tsx
onPressOut={handlePressOut}
```

Runs when the press ends.

### 6. `onLongPress`

```tsx
onLongPress={handleLongPress}
```

Runs after holding the component.

### 7. `delayLongPress`

```tsx
delayLongPress={1000}
```

Controls the long-press delay in milliseconds.

### 8. `pressed`

```tsx
style={({ pressed }) => [
  styles.button,
  pressed && styles.pressed,
]}
```

Allows you to change styling while the component is being pressed.

---

# 🧠 The Main Mental Model

Think of `Pressable` like this:

```text
                 Pressable
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
       onPress    onLongPress   style
          │          │          │
          ↓          ↓          ↓
       Action      Action    Press effect
```

And the basic flow:

```text
User touches
     ↓
onPressIn
     ↓
User releases
     ↓
onPressOut
     ↓
onPress
```

---

# 🎯 Practice — My Shelf

Create a custom book card:

```text
┌─────────────────────────┐
│                         │
│     The Alchemist       │
│                         │
│       Read Book         │
│                         │
└─────────────────────────┘
```

Requirements:

* Use `Pressable`
* Use `onPress`
* Use `StyleSheet`
* Change opacity while pressing
* Print `"Book pressed"` to the console

Then try adding:

```tsx
onLongPress
```

and print:

```text
"Long pressed"
```

### One thing to really understand before Video #9:

```tsx
style={({ pressed }) => [
  styles.button,
  pressed && styles.pressed,
]}
```

This pattern is **very important** because it lets you build polished custom buttons, cards, list items, etc., rather than relying only on the default `Button` component.
