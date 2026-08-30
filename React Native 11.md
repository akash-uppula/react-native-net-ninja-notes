# 📱 React Native — Video #11 Notes

## Complete React Native Tutorial #11 — Safe Area View

This video is about **`SafeAreaView`** and why it is important when building React Native screens.

The main problem is that some parts of a phone's screen are **not safe for placing content**.

For example:

```text
        ┌──────────────────┐
        │   🔴 Notch       │
        ├──────────────────┤
        │                  │
        │   Your content   │
        │                  │
        │                  │
        ├──────────────────┤
        │   Home indicator │
        └──────────────────┘
```

If you don't account for these areas, your content can overlap with the **notch, status bar, or home indicator**.

---

# 1. What is Safe Area?

A **safe area** is the portion of the screen where you can safely place your UI without it being covered by system elements.

Examples of things that can occupy screen space:

* Status bar
* Notch
* Dynamic Island
* Home indicator
* Device-specific screen edges

So instead of putting content directly against the physical edges, we give it appropriate spacing.

---

# 2. `SafeAreaView`

React Native provides:

```tsx
import { SafeAreaView } from "react-native";
```

Then:

```tsx
<SafeAreaView>
  <Text>My Shelf</Text>
</SafeAreaView>
```

The purpose is to keep your content within the safe area.

---

# 3. Basic Example

Without safe area handling:

```tsx
import {
  StyleSheet,
  Text,
  View,
} from "react-native";

const Home = () => {
  return (
    <View style={styles.container}>
      <Text>My Shelf</Text>
    </View>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

Your content can potentially extend into areas occupied by system UI.

With `SafeAreaView`:

```tsx
import {
  SafeAreaView,
  StyleSheet,
  Text,
} from "react-native";

const Home = () => {
  return (
    <SafeAreaView style={styles.container}>
      <Text>My Shelf</Text>
    </SafeAreaView>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

Now the content is placed within the safe area.

---

# 4. `SafeAreaView` Is a Container

Just like:

```tsx
<View>
```

you can put other components inside:

```tsx
<SafeAreaView>
  <Text>My Shelf</Text>

  <Text>My Books</Text>

  <Pressable>
    <Text>Add Book</Text>
  </Pressable>
</SafeAreaView>
```

Think of it as:

```text
SafeAreaView
      │
      ├── Text
      ├── Text
      └── Pressable
```

---

# 5. Why Not Just Use Padding?

You might think:

```tsx
paddingTop: 50
```

would solve the problem.

But this isn't ideal.

Different devices have different safe-area requirements:

```text
Device A
  ↓
Different top inset

Device B
  ↓
Different top inset

Device C
  ↓
Different bottom inset
```

The safe-area system can calculate the appropriate insets for the device.

---

# 6. Safe Area Is Especially Important on iOS

Historically, safe-area handling became especially important with devices that have:

* Notches
* Home indicators
* Different screen shapes

For example:

```text
iPhone
┌────────────────────┐
│      Notch         │
│────────────────────│
│                    │
│    Safe Content    │
│                    │
│                    │
│                    │
│   Home Indicator   │
└────────────────────┘
```

Your application shouldn't blindly assume that:

```text
top = 0
bottom = 0
```

is safe.

---

# 7. Expo and Safe Areas

In modern Expo/React Native projects, you'll often encounter:

```tsx
react-native-safe-area-context
```

This is a very important package.

You may have already installed it earlier:

```bash
npx expo install react-native-safe-area-context
```

The library provides more flexible safe-area handling than the basic React Native `SafeAreaView`.

Import:

```tsx
import { SafeAreaView } from "react-native-safe-area-context";
```

---

# 8. Which `SafeAreaView` Should We Use?

There are two commonly encountered imports:

### React Native

```tsx
import { SafeAreaView } from "react-native";
```

### `react-native-safe-area-context`

```tsx
import { SafeAreaView } from "react-native-safe-area-context";
```

For an Expo Router application, **`react-native-safe-area-context` is generally the more flexible/current approach**.

This distinction is worth remembering.

---

# 9. `react-native-safe-area-context`

After installing:

```bash
npx expo install react-native-safe-area-context
```

you can write:

```tsx
import { SafeAreaView } from "react-native-safe-area-context";
```

Then:

```tsx
const Home = () => {
  return (
    <SafeAreaView>
      <Text>My Shelf</Text>
    </SafeAreaView>
  );
};
```

---

# 10. `SafeAreaProvider`

The safe-area-context library also provides:

```tsx
SafeAreaProvider
```

Import:

```tsx
import {
  SafeAreaProvider,
} from "react-native-safe-area-context";
```

It acts as the provider for safe-area information.

Conceptually:

```text
SafeAreaProvider
       │
       └── App
            │
            ├── Home
            ├── Books
            └── Profile
```

The child components can then access safe-area information.

---

# 11. Basic Provider Example

```tsx
import {
  SafeAreaProvider,
} from "react-native-safe-area-context";

const App = () => {
  return (
    <SafeAreaProvider>
      {/* App */}
    </SafeAreaProvider>
  );
};

export default App;
```

However, with **Expo Router**, you generally don't need to manually add a provider in every screen. Expo Router's setup commonly works with the safe-area context package.

The important thing for you at this stage is understanding what the provider does.

---

# 12. `useSafeAreaInsets`

The safe-area-context library also provides:

```tsx
useSafeAreaInsets
```

Import:

```tsx
import {
  useSafeAreaInsets,
} from "react-native-safe-area-context";
```

Then:

```tsx
const insets = useSafeAreaInsets();
```

You get values such as:

```text
insets.top
insets.bottom
insets.left
insets.right
```

---

# 13. What Are Insets?

An **inset** is basically the amount of space you need to keep away from the unsafe edge.

For example:

```text
insets.top
     ↓
┌──────────────────┐
│     unsafe       │
├──────────────────┤
│                  │
│     content      │
│                  │
```

And:

```text
insets.bottom
     ↓
│                  │
├──────────────────┤
│  unsafe bottom   │
└──────────────────┘
```

---

# 14. Example Using `useSafeAreaInsets`

```tsx
import {
  StyleSheet,
  Text,
  View,
} from "react-native";

import {
  useSafeAreaInsets,
} from "react-native-safe-area-context";

const Home = () => {
  const insets = useSafeAreaInsets();

  return (
    <View
      style={[
        styles.container,
        {
          paddingTop: insets.top,
          paddingBottom: insets.bottom,
        },
      ]}
    >
      <Text>My Shelf</Text>
    </View>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});
```

Now we're dynamically applying the safe-area spacing.

---

# 15. Why Use `useSafeAreaInsets`?

Sometimes you don't simply want:

```tsx
<SafeAreaView>
```

You may need to apply the inset to a specific component.

For example:

```text
Header
 ↓
needs top inset

Content
 ↓
doesn't need it

Bottom button
 ↓
needs bottom inset
```

Then:

```tsx
const insets = useSafeAreaInsets();
```

gives you the values you need.

---

# 16. Example — Custom Header

Imagine:

```text
┌─────────────────────────┐
│      Status / Notch     │
├─────────────────────────┤
│       My Shelf          │
├─────────────────────────┤
│                         │
│       Content           │
│                         │
└─────────────────────────┘
```

You can use:

```tsx
paddingTop: insets.top
```

for your custom header.

---

# 17. `edges`

`SafeAreaView` can also control **which edges** should receive safe-area padding.

Example:

```tsx
<SafeAreaView
  edges={["top"]}
>
  ...
</SafeAreaView>
```

This means:

> Apply safe-area handling to the top edge.

You can also use:

```tsx
edges={["top", "bottom"]}
```

or:

```tsx
edges={["left", "right"]}
```

This gives you more control.

---

# 18. Example

```tsx
import {
  SafeAreaView,
} from "react-native-safe-area-context";

const Home = () => {
  return (
    <SafeAreaView
      edges={["top"]}
    >
      <Text>My Shelf</Text>
    </SafeAreaView>
  );
};
```

Now only the top safe area is being handled.

---

# 19. Safe Area + Your Existing Themes

You have already learned themed components in Video #6.

You can combine them.

For example:

```tsx
<SafeAreaView style={styles.container}>
  <Text style={styles.title}>
    My Shelf
  </Text>
</SafeAreaView>
```

Your safe-area container can still have:

* Background color
* Padding
* Flexbox styles
* Other styling

Safe area handling doesn't replace styling.

It simply makes sure the content respects the device's safe boundaries.

---

# 20. Safe Area + Tabs

This is especially important because you've just learned Tabs.

Your app:

```text
┌──────────────────────────┐
│                          │
│       My Shelf           │
│                          │
│       Content            │
│                          │
│                          │
├──────────────────────────┤
│ 🏠     📚      👤       │
│ Home   Books   Profile   │
└──────────────────────────┘
```

The bottom of the screen may have a home indicator.

The tab navigator and safe-area handling need to account for these areas.

That's why safe-area support is an important part of building mobile navigation.

---

# 21. Safe Area ≠ StatusBar

Don't confuse:

```text
Safe Area
```

with:

```text
Status Bar
```

### Status Bar

The system area containing things like:

```text
Time
Battery
Wi-Fi
Signal
```

### Safe Area

The area where your app's content can safely be displayed without being obstructed.

They're related, but they're **not the same thing**.

---

# 22. A Simple My Shelf Example

```tsx
import {
  StyleSheet,
  Text,
} from "react-native";

import {
  SafeAreaView,
} from "react-native-safe-area-context";

const Home = () => {
  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.title}>
        My Shelf
      </Text>

      <Text>
        Welcome to your bookshelf.
      </Text>
    </SafeAreaView>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
  },

  title: {
    fontSize: 28,
    fontWeight: "bold",
  },
});
```

This is a nice starting pattern for your screens.

---

# 23. Important Difference: Safe Area vs Padding

Consider:

```tsx
padding: 20
```

This is **your design spacing**.

While:

```tsx
insets.top
```

is **device-specific safe-area spacing**.

They can be combined:

```tsx
paddingTop: insets.top + 20
```

Meaning:

```text
Device safe space
       +
Your design space
       =
Total top spacing
```

---

# 24. When Should You Use It?

Safe-area handling is especially useful for:

### Full-screen screens

```text
Home
Profile
Settings
```

### Custom headers

```text
┌─────────────────┐
│ Custom Header   │
└─────────────────┘
```

### Custom bottom controls

```text
┌─────────────────┐
│                 │
│                 │
├─────────────────┤
│ Custom Button   │
└─────────────────┘
```

### Screens with edge-to-edge UI

Where your content intentionally reaches the screen edges.

---

# ⭐ Video #11 — Must Remember

### 1. Safe area

Protects your UI from device/system areas such as:

```text
Notch
Status bar
Home indicator
Screen edges
```

### 2. Basic import

For the safe-area-context library:

```tsx
import {
  SafeAreaView,
} from "react-native-safe-area-context";
```

### 3. Basic usage

```tsx
<SafeAreaView style={styles.container}>
  <Text>My Shelf</Text>
</SafeAreaView>
```

### 4. Provider

```tsx
import {
  SafeAreaProvider,
} from "react-native-safe-area-context";
```

Provides safe-area information to the component tree.

### 5. Insets

```tsx
import {
  useSafeAreaInsets,
} from "react-native-safe-area-context";
```

Then:

```tsx
const insets = useSafeAreaInsets();
```

You can access:

```tsx
insets.top
insets.bottom
insets.left
insets.right
```

### 6. `edges`

```tsx
<SafeAreaView
  edges={["top", "bottom"]}
>
```

Controls which edges should respect the safe area.

---

# 🧠 The Main Mental Model

Remember this:

```text
             DEVICE SCREEN
┌────────────────────────────┐
│     ❌ Unsafe area          │
│       Notch / Status       │
├────────────────────────────┤
│                            │
│                            │
│       ✅ SAFE AREA          │
│                            │
│       Your content         │
│                            │
├────────────────────────────┤
│     ❌ Home indicator       │
└────────────────────────────┘
```

Safe-area handling essentially tells your app:

> **"Don't put important UI where the device/system UI might cover it."**

---

# 🔥 Video #10 → Video #11

The progression is:

```text
Video #10
Tab Bar Icons
      ↓
Make tabs visually useful
      ↓
Video #11
Safe Area
      ↓
Make screen content fit safely
```

And for your **My Shelf** app:

```text
                    My Shelf
                       │
              ┌────────┴────────┐
              │                 │
           Safe Area           Tabs
              │                 │
              ↓                 ↓
        Screen Content    Home / Books / Profile
              │
              ↓
         Device-safe UI
```

### One thing to practice

Take your existing `Home` screen and change:

```tsx
<View>
```

to:

```tsx
<SafeAreaView>
```

using:

```tsx
import { SafeAreaView } from "react-native-safe-area-context";
```

Then add your existing `My Shelf` content inside it. This will make the concept much clearer before moving to Video #12.
