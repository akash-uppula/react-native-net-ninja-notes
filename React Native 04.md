# 📱 React Native — Video #4 Notes

## Complete React Native Tutorial #4 — Layouts and Stack

I checked the actual Net Ninja video rather than relying only on the title. It's about **10:42 long**, and it follows File-Based Navigation in the course. ([YouTube][1])

This video is important because we're now connecting **file-based routes** with **layouts and Stack navigation**.

---

# 1. What is a Layout?

In Expo Router, a **layout** is a special file that controls how routes inside a directory are organized and displayed.

The special file is:

```text
_layout.tsx
```

For example:

```text
app/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

Here:

```text
_layout.tsx
    ↓
Controls the routes
    ↓
index.tsx
books.tsx
profile.tsx
```

So `_layout.tsx` acts as a **parent layout** for the routes in that folder.

---

# 2. Why do we need `_layout.tsx`?

Imagine our app has:

```text
Home
Books
Profile
```

We want all these screens to belong to the same navigation structure.

Instead of configuring each screen individually, we can configure the navigation in:

```text
app/_layout.tsx
```

This gives us a central place to define the navigation behavior.

---

# 3. `Stack`

The video introduces the **Stack** navigator.

Import it from Expo Router:

```tsx
import { Stack } from "expo-router";
```

Then:

```tsx
export default function Layout() {
  return <Stack />;
}
```

So our `_layout.tsx` can look like:

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return <Stack />;
}
```

---

# 4. What is a Stack?

Think of Stack navigation like a **stack of screens**.

Suppose we're currently on:

```text
Home
```

Then we navigate to:

```text
Books
```

The navigation stack becomes:

```text
Books
Home
```

Then we navigate to:

```text
Book Details
```

Now:

```text
Book Details
Books
Home
```

The newest screen is placed **on top**.

---

# 5. Going Back

When we press the back button, the top screen is removed.

For example:

```text
Book Details
Books
Home
```

Press back:

```text
Books
Home
```

Press back again:

```text
Home
```

So Stack navigation behaves conceptually like:

```text
PUSH
 ↓
Add screen to top

POP
 ↓
Remove top screen
```

This is why it's called a **Stack**.

---

# 6. `_layout.tsx` + `Stack`

Suppose our project is:

```text
app/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

Our `_layout.tsx`:

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return <Stack />;
}
```

Now Expo Router knows that the routes inside this directory should be handled by a Stack navigator.

Conceptually:

```text
_layout.tsx
     │
     └── Stack
          │
          ├── index.tsx
          ├── books.tsx
          └── profile.tsx
```

---

# 7. Stack Headers

One of the useful things about Stack navigation is that it can provide a **header** for the current screen.

For example:

```text
┌──────────────────────────┐
│ Books                 ←  │
├──────────────────────────┤
│                          │
│      Books Screen        │
│                          │
└──────────────────────────┘
```

The header can show the screen title and navigation controls.

---

# 8. Configuring a Screen

You can configure screens inside the Stack.

For example:

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          title: "My Shelf",
        }}
      />

      <Stack.Screen
        name="books"
        options={{
          title: "My Books",
        }}
      />
    </Stack>
  );
}
```

Now:

```text
index.tsx
   ↓
My Shelf

books.tsx
   ↓
My Books
```

The `options` prop controls how the screen is presented.

---

# 9. `Stack.Screen`

This is important:

```tsx
<Stack.Screen />
```

is used to configure an individual screen within the Stack.

For example:

```tsx
<Stack.Screen
  name="books"
  options={{
    title: "My Books",
  }}
/>
```

### `name`

Specifies which route you're configuring.

```tsx
name="books"
```

refers to:

```text
app/books.tsx
```

### `options`

Controls the screen's navigation presentation.

For example:

```tsx
options={{
  title: "My Books",
}}
```

changes the header title.

---

# 10. Hiding the Header

You can also hide the Stack header.

For example:

```tsx
<Stack
  screenOptions={{
    headerShown: false,
  }}
/>
```

Now the navigation header won't be displayed.

This can be useful when you want to create your own custom header.

---

# 11. `screenOptions`

Instead of configuring every screen individually, you can provide common options to the entire Stack.

Example:

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return (
    <Stack
      screenOptions={{
        headerShown: false,
      }}
    />
  );
}
```

This applies:

```text
headerShown: false
```

to the screens in that Stack by default.

Think:

```text
screenOptions
      ↓
Default options
      ↓
All screens in Stack
```

---

# 12. `Stack.Screen` vs `screenOptions`

This distinction is important.

### `screenOptions`

Used for **common/default configuration**.

```tsx
<Stack
  screenOptions={{
    headerShown: false,
  }}
/>
```

### `Stack.Screen`

Used to configure a **specific screen**.

```tsx
<Stack.Screen
  name="books"
  options={{
    title: "My Books",
  }}
/>
```

Think:

```text
screenOptions
     ↓
general/default

Stack.Screen
     ↓
specific screen
```

---

# 13. Connecting This With Video #3

In Video #3 we learned:

```text
File
 ↓
Route
```

For example:

```text
app/books.tsx
      ↓
   /books
```

Now Video #4 adds:

```text
_layout.tsx
      ↓
Navigation structure
      ↓
Stack
      ↓
Routes
```

So the complete picture becomes:

```text
app/
│
├── _layout.tsx
│      ↓
│    Stack
│      ↓
│   Navigation
│
├── index.tsx
│      ↓
│      /
│
├── books.tsx
│      ↓
│    /books
│
└── profile.tsx
       ↓
     /profile
```

---

# 14. Our My Shelf Example

Let's use the application we've been building.

Project:

```text
app/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

### `_layout.tsx`

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          title: "My Shelf",
        }}
      />

      <Stack.Screen
        name="books"
        options={{
          title: "My Books",
        }}
      />

      <Stack.Screen
        name="profile"
        options={{
          title: "My Profile",
        }}
      />
    </Stack>
  );
}
```

Now our navigation structure is:

```text
                 Stack
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
     Home        Books       Profile
```

---

# 15. Home Screen

Our `index.tsx` can use the `Link` we learned in Video #3:

```tsx
import { View, Text } from "react-native";
import { Link } from "expo-router";

export default function Home() {
  return (
    <View>
      <Text>My Shelf</Text>

      <Link href="/books">
        My Books
      </Link>

      <Link href="/profile">
        My Profile
      </Link>
    </View>
  );
}
```

Now:

```text
My Shelf
   │
   ├── My Books
   │       ↓
   │     /books
   │
   └── My Profile
           ↓
         /profile
```

And Stack navigation handles the screen transitions.

---

# 16. Important Mental Model

This is probably the **most important part of this lesson**.

### Video #3:

```text
File
 ↓
Route
```

### Video #4:

```text
_layout.tsx
 ↓
Stack
 ↓
Routes
 ↓
Navigation behavior
```

Together:

```text
app/
│
├── _layout.tsx
│       ↓
│     Stack
│       ↓
│   Navigation
│
├── index.tsx ─────→ /
│
├── books.tsx ─────→ /books
│
└── profile.tsx ───→ /profile
```

---

# ⭐ Important Terms

| Term            | Meaning                                             |
| --------------- | --------------------------------------------------- |
| `_layout.tsx`   | Defines layout/navigation for routes in a directory |
| `Stack`         | Stack-based navigator                               |
| `Stack.Screen`  | Configures an individual screen                     |
| `screenOptions` | Default options for screens                         |
| `options`       | Configuration for a particular screen               |
| `headerShown`   | Controls whether the Stack header is displayed      |
| `title`         | Controls the screen/header title                    |
| `name`          | Identifies the route being configured               |

---

# 🧠 Stack Navigation in Simple Words

Imagine three cards:

```text
       ┌─────────────┐
       │   Details   │ ← Top
       ├─────────────┤
       │    Books    │
       ├─────────────┤
       │    Home     │
       └─────────────┘
```

Navigate forward:

```text
Home
 ↓
Books
 ↓
Details
```

The new screen is **pushed onto the stack**.

Go back:

```text
Details
 ↓
POP
 ↓
Books
```

The top screen is removed.

So:

```text
Navigate forward → PUSH
Go back          → POP
```

You don't necessarily call these operations manually when using `<Link>`; Expo Router manages the navigation behavior for you.

---

# 🎯 Small Practice

Update your **My Shelf** project.

Create:

```text
app/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

Then create this layout:

```tsx
import { Stack } from "expo-router";

export default function Layout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          title: "My Shelf",
        }}
      />

      <Stack.Screen
        name="books"
        options={{
          title: "My Books",
        }}
      />

      <Stack.Screen
        name="profile"
        options={{
          title: "My Profile",
        }}
      />
    </Stack>
  );
}
```

Then use the `Link` from Video #3 to move between the screens.

---

## ⭐ What you should be able to explain after Video #4

If I ask you:

**"What is `_layout.tsx`?"**

You should be able to say:

> It's a special Expo Router file used to define the layout/navigation structure for routes inside that directory.

**"What is `Stack`?"**

> It's a navigation structure where screens are placed on top of one another, like a stack.

**"What is `Stack.Screen`?"**

> It lets us configure an individual screen in the Stack.

**"What is `screenOptions`?"**

> It lets us define default options for the Stack's screens.

And the overall relationship is:

```text
File-based routing
        ↓
   _layout.tsx
        ↓
      Stack
        ↓
   Stack.Screen
        ↓
 Navigation headers/options
```

That is the core of **Video #4**.

[1]: https://www.youtube.com/watch?v=Fo95XFCHEcg&utm_source=chatgpt.com "Complete React Native Tutorial #4 - Layouts and Stack - YouTube"
