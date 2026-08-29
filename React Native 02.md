# 📱 React Native — Video #2

## Complete React Native Tutorial #2 — Text, View & Image Components

This video introduces three fundamental React Native components:

```text
View
Text
Image
```

These are the basic building blocks we'll use to create our screens.

---

# 1. React Native Components

In React Native, we build the UI by combining **components**.

Example:

```tsx
import { View, Text } from "react-native";

export default function App() {
  return (
    <View>
      <Text>Hello React Native</Text>
    </View>
  );
}
```

The structure is:

```text
App
└── View
    └── Text
```

A component can contain other components.

This is called **nesting components**.

---

# 2. `View`

## What is `View`?

`View` is a **container** used to hold and organize other components.

Think of it roughly like a `<div>` in web development.

```tsx
import { View } from "react-native";

export default function App() {
  return (
    <View>
      ...
    </View>
  );
}
```

We use `View` to:

* group components
* create sections
* organize our UI
* create layouts
* apply styles

---

## Example

```tsx
import { View, Text } from "react-native";

export default function App() {
  return (
    <View>
      <Text>Hello</Text>
      <Text>Welcome</Text>
    </View>
  );
}
```

Structure:

```text
View
├── Text
└── Text
```

The `View` is the **parent**.

The `Text` components are its **children**.

---

# 3. Nested `View`s

A `View` can contain another `View`.

```tsx
import { View, Text } from "react-native";

export default function App() {
  return (
    <View>
      <View>
        <Text>First section</Text>
      </View>

      <View>
        <Text>Second section</Text>
      </View>
    </View>
  );
}
```

Structure:

```text
Outer View
│
├── Inner View
│   └── Text
│
└── Inner View
    └── Text
```

You'll use this kind of nesting constantly when building real applications.

---

# 4. `Text`

`Text` is used to **display text**.

Import:

```tsx
import { Text } from "react-native";
```

Use:

```tsx
<Text>Hello World</Text>
```

Example:

```tsx
import { View, Text } from "react-native";

export default function App() {
  return (
    <View>
      <Text>Hello</Text>
      <Text>I am learning React Native</Text>
    </View>
  );
}
```

---

# 5. ⚠️ Text Must Be Inside `<Text>`

This is one of the most important rules.

### ❌ Wrong

```tsx
<View>
  Hello World
</View>
```

### ✅ Correct

```tsx
<View>
  <Text>Hello World</Text>
</View>
```

In React Native, raw text cannot be directly placed inside a `View`.

So remember:

> **If you want to display text → use `<Text>`.**

---

# 6. Nested `Text`

A `Text` component can contain another `Text`.

```tsx
<Text>
  Hello <Text>World</Text>
</Text>
```

This is useful when different parts of the same sentence need different styles.

Example:

```tsx
<Text>
  Hello{" "}
  <Text style={{ fontWeight: "bold" }}>
    Sai
  </Text>
</Text>
```

Here:

```text
Text
├── "Hello"
└── Text
    └── "Sai"
```

---

# 7. Styling Text

We can pass a `style` prop to `Text`.

```tsx
<Text
  style={{
    fontSize: 24,
    fontWeight: "bold",
  }}
>
  Hello
</Text>
```

Here:

```text
style
├── fontSize
└── fontWeight
```

We'll learn styling properly later.

For now, understand:

> `style` controls how the component looks and behaves.

---

# 8. `Image`

The `Image` component is used to display pictures.

Import:

```tsx
import { Image } from "react-native";
```

Basic syntax:

```tsx
<Image source={...} />
```

---

# 9. Local Images

Suppose our project looks like this:

```text
project/
│
├── app/
│   └── index.tsx
│
└── assets/
    └── profile.png
```

We can import the image using ES6 module syntax:

```tsx
import profileImage from "../assets/profile.png";
```

Then:

```tsx
<Image source={profileImage} />
```

Complete example:

```tsx
import { Image } from "react-native";
import profileImage from "../assets/profile.png";

export default function App() {
  return (
    <Image source={profileImage} />
  );
}
```

### ⭐ Our convention

Since you want to learn using **ES6 modules**, we'll use:

```tsx
import profileImage from "../assets/profile.png";
```

instead of:

```tsx
require("../assets/profile.png")
```

in our main examples.

---

# 10. Remote Images

Images can also come from the internet.

For example:

```tsx
import { Image } from "react-native";

export default function App() {
  return (
    <Image
      source={{
        uri: "https://example.com/image.png",
      }}
    />
  );
}
```

Notice the difference.

### Local image

```tsx
import profileImage from "../assets/profile.png";

<Image source={profileImage} />
```

### Remote image

```tsx
<Image
  source={{
    uri: "https://example.com/image.png",
  }}
/>
```

---

# 11. Image Size

We can control the image size using `style`.

```tsx
<Image
  source={profileImage}
  style={{
    width: 150,
    height: 150,
  }}
/>
```

For example:

```text
width  → 150
height → 150
```

So the image will be displayed at:

```text
150 × 150
```

---

# 12. `ImageBackground`

React Native also provides:

```tsx
ImageBackground
```

It allows us to use an image as a background and put other components on top.

Import:

```tsx
import {
  ImageBackground,
  Text,
} from "react-native";
```

Example:

```tsx
import {
  ImageBackground,
  Text,
} from "react-native";

import backgroundImage from "../assets/background.png";

export default function App() {
  return (
    <ImageBackground
      source={backgroundImage}
      style={{
        width: "100%",
        height: "100%",
      }}
    >
      <Text>Welcome!</Text>
    </ImageBackground>
  );
}
```

Conceptually:

```text
ImageBackground
│
├── Background image
│
└── Text
```

The text appears over the image.

---

# 13. Props

This video also introduces an important React concept:

## Props

Props are values that we pass to components to **customize them**.

Example:

```tsx
<Text style={{ fontSize: 20 }}>
  Hello
</Text>
```

Here:

```text
style
```

is a prop.

Another example:

```tsx
<Image source={profileImage} />
```

Here:

```text
source
```

is a prop.

Think of it as:

```text
Component
    ↓
   Props
    ↓
Customize component
```

---

# 14. JSX and `{}`

You'll frequently see:

```tsx
<Text>{name}</Text>
```

The `{}` allows us to put a JavaScript expression inside JSX.

Example:

```tsx
const name = "Sai";

export default function App() {
  return (
    <Text>{name}</Text>
  );
}
```

The screen displays:

```text
Sai
```

---

# 15. Why `style={{ }}`?

You'll see this a lot:

```tsx
<View
  style={{
    padding: 20,
    backgroundColor: "white",
  }}
>
```

It looks like there are two sets of curly braces.

There are actually two different things.

### First `{}`

```tsx
style={...}
```

means:

> We're passing a JavaScript value to the `style` prop.

### Second `{}`

```tsx
{
  padding: 20,
  backgroundColor: "white",
}
```

is the JavaScript object.

Therefore:

```tsx
style={{
  padding: 20,
}}
```

means:

```text
style =
  JavaScript object
```

This syntax will become very familiar once we start learning styles.

---

# 16. Complete Example

Let's combine `View`, `Text`, and `Image`.

```tsx
import { View, Text, Image } from "react-native";

import profileImage from "../assets/profile.png";

export default function App() {
  return (
    <View>
      <Text
        style={{
          fontSize: 24,
          fontWeight: "bold",
        }}
      >
        My Profile
      </Text>

      <Image
        source={profileImage}
        style={{
          width: 150,
          height: 150,
        }}
      />

      <Text>
        Welcome to my React Native app!
      </Text>
    </View>
  );
}
```

Component tree:

```text
App
│
└── View
    │
    ├── Text
    │   └── "My Profile"
    │
    ├── Image
    │
    └── Text
        └── "Welcome..."
```

---

# ⭐ Things You Must Remember

## `View`

```tsx
<View>
  ...
</View>
```

**Container / layout component**

---

## `Text`

```tsx
<Text>
  Hello
</Text>
```

**Displays text**

---

## `Image`

```tsx
<Image
  source={profileImage}
/>
```

**Displays an image**

---

## Local image

```tsx
import profileImage from "../assets/profile.png";

<Image source={profileImage} />
```

---

## Remote image

```tsx
<Image
  source={{
    uri: "https://example.com/image.png",
  }}
/>
```

---

## Text inside View

```tsx
// ❌ Wrong
<View>
  Hello
</View>
```

```tsx
// ✅ Correct
<View>
  <Text>Hello</Text>
</View>
```

---

## Components can be nested

```text
View
├── Text
├── Image
└── View
    └── Text
```

---

# 🧠 The Main Idea of Video #2

Don't just memorize the three components.

Understand their **roles**:

```text
             SCREEN
                │
              View
                │
       ┌────────┼────────┐
       ↓        ↓        ↓
      View     Text     Image
   container   text     picture
```

We're going to build increasingly complicated screens by **combining these components**.

---

# 🎯 Practice

Create a simple profile screen:

```text
       My Profile

      [YOUR IMAGE]

      Hello, I am Sai

  I am learning React Native
```

Requirements:

* `View` for the container
* `Text` for the title
* `Image` for your picture
* another `Text` for the introduction
* import the local image using ES6 `import`
* give the image `width` and `height`
* make the title larger using `fontSize`

Don't worry about making it look perfect yet. **The next lessons will build the styling/layout skills needed for that.**
