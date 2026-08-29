# 📱 React Native — Video #3 Notes

## Complete React Native Tutorial #3 — File-Based Navigation

This video introduces **file-based navigation with Expo Router**, and one of the important things Net Ninja discusses is the **`Link` component**.

---

# 1. What is Navigation?

Navigation means moving from **one screen to another**.

For example:

```text
Home
 ↓
Books
 ↓
Book Details
 ↓
Profile
```

In a mobile app, we need a way to move between these screens.

With **Expo Router**, navigation is based on the files inside the `app` directory.

---

# 2. File-Based Navigation

Suppose we have:

```text
app/
├── index.tsx
├── books.tsx
└── profile.tsx
```

These files represent routes.

Conceptually:

```text
index.tsx
   ↓
/

books.tsx
   ↓
/books

profile.tsx
   ↓
/profile
```

So instead of manually creating a route for every screen, the **file structure defines the routes**.

---

# 3. `index.tsx`

The file:

```text
app/index.tsx
```

represents the **initial/root route**.

For example:

```tsx id="5s6cv8"
export default function Home() {
  return (
    <Text>Home Screen</Text>
  );
}
```

When the app starts, this is the screen associated with:

```text
/
```

---

# 4. Creating Another Screen

Suppose we create:

```text
app/
├── index.tsx
└── about.tsx
```

Then:

```text
index.tsx → /
about.tsx → /about
```

Inside `about.tsx`:

```tsx id="w7k7dr"
import { View, Text } from "react-native";

export default function About() {
  return (
    <View>
      <Text>About Screen</Text>
    </View>
  );
}
```

Now we have two screens.

---

# 5. `Link`

Now comes the important part from this video.

Expo Router provides a component called:

```tsx id="7f4l6a"
Link
```

`Link` allows us to navigate from one route to another.

Import it from:

```tsx id="2j5l4g"
import { Link } from "expo-router";
```

---

# 6. Basic `Link`

Suppose we have:

```text
app/
├── index.tsx
└── about.tsx
```

From `index.tsx`, we can create a link to `about.tsx`:

```tsx id="q2qv6k"
import { View, Text } from "react-native";
import { Link } from "expo-router";

export default function Home() {
  return (
    <View>
      <Text>Home Screen</Text>

      <Link href="/about">
        Go to About
      </Link>
    </View>
  );
}
```

When the user presses:

```text
Go to About
```

they navigate to:

```text
/about
```

---

# 7. `href`

The important prop here is:

```tsx id="0xv5nq"
href
```

Example:

```tsx id="s6f1pw"
<Link href="/about">
  Go to About
</Link>
```

`href` tells `Link`:

> **Where should I navigate?**

So:

```text
Link
 ↓
href
 ↓
/about
```

---

# 8. `Link` is Similar to a Web Link

If you've worked with HTML, you may have seen:

```html id="6s8suo"
<a href="/about">
  About
</a>
```

Expo Router's:

```tsx id="3p0l6f"
<Link href="/about">
  About
</Link>
```

has a similar idea.

But `Link` is designed for **Expo Router navigation**.

---

# 9. Using `Link` with a React Native Component

An important thing you'll encounter is the `asChild` prop.

For example, we may want a `Pressable` or another component to act as the link.

```tsx id="o7k7av"
import { View, Text, Pressable } from "react-native";
import { Link } from "expo-router";

export default function Home() {
  return (
    <View>
      <Link href="/about" asChild>
        <Pressable>
          <Text>Go to About</Text>
        </Pressable>
      </Link>
    </View>
  );
}
```

Here:

```text
Link
 ↓
asChild
 ↓
Pressable
 ↓
Text
```

The `Pressable` becomes the thing the user interacts with.

We'll understand `Pressable` properly later when we reach touchables.

---

# 10. `Link` With Dynamic Routes

Expo Router also supports dynamic routes.

Suppose:

```text
app/
├── index.tsx
└── books/
    └── [id].tsx
```

The `[id]` means:

> This part of the route is dynamic.

So we can have routes such as:

```text
/books/1
/books/2
/books/3
```

The same screen can handle different IDs.

For example:

```text
/books/[id]
```

can represent:

```text
/books/1
/books/25
/books/100
```

This becomes very useful for things like:

* book details
* product details
* user profiles
* article pages

---

# 11. Example for Our "My Shelf" App

Imagine our project:

```text
app/
├── index.tsx
├── books.tsx
└── profile.tsx
```

We could have:

### Home

```tsx id="wqzcbj"
import { View, Text } from "react-native";
import { Link } from "expo-router";

export default function Home() {
  return (
    <View>
      <Text>My Shelf</Text>

      <Link href="/books">
        View My Books
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
   ├── View My Books → /books
   │
   └── My Profile → /profile
```

---

# 12. Important Difference: `Link` vs `href`

This is worth remembering.

```tsx id="w21u0e"
<Link href="/books">
  Books
</Link>
```

Here:

### `Link`

The **navigation component**.

### `href`

The **destination**.

Think:

```text
<Link>
   ↓
"Make this a navigation link"

href="/books"
   ↓
"Go here"
```

---

# 13. File Structure Controls Navigation

This is the biggest concept of this video.

Suppose:

```text
app/
├── index.tsx
├── books.tsx
├── profile.tsx
└── settings.tsx
```

You automatically have routes corresponding to:

```text
/
 /books
 /profile
 /settings
```

Then:

```tsx id="zjzq1y"
<Link href="/settings">
  Settings
</Link>
```

navigates to:

```text
settings.tsx
```

So:

```text
FILE
 ↓
ROUTE
 ↓
LINK
 ↓
SCREEN
```

---

# 14. Folder-Based Routes

You can also organize routes using folders.

Example:

```text
app/
├── index.tsx
└── books/
    ├── index.tsx
    └── details.tsx
```

Conceptually:

```text
books/index.tsx
      ↓
/books

books/details.tsx
      ↓
/books/details
```

This allows us to organize larger applications.

---

# 15. Why File-Based Navigation is Useful

Imagine an application with 30 screens.

Without a clear file-based structure, navigation can become difficult to manage.

With Expo Router:

```text
app/
├── index.tsx
├── login.tsx
├── profile.tsx
├── settings.tsx
├── books/
│   ├── index.tsx
│   └── [id].tsx
└── account/
    ├── index.tsx
    └── security.tsx
```

The folder structure itself gives you a visual understanding of the navigation structure.

---

# ⭐ Video #3 — Important Things to Remember

### 1. File-based navigation

Files inside the routing directory represent routes.

```text
app/about.tsx
      ↓
/about
```

---

### 2. `index.tsx`

Represents the route for its directory.

```text
app/index.tsx
     ↓
/
```

---

### 3. `Link`

Used to navigate between routes.

```tsx id="2jzq7j"
import { Link } from "expo-router";

<Link href="/about">
  About
</Link>
```

---

### 4. `href`

Specifies the destination.

```tsx id="r9x4kp"
href="/about"
```

---

### 5. `asChild`

Allows another component to act as the interactive link:

```tsx id="zhytr8"
<Link href="/about" asChild>
  <Pressable>
    <Text>About</Text>
  </Pressable>
</Link>
```

---

### 6. Dynamic routes

```text
app/books/[id].tsx
```

can represent:

```text
/books/1
/books/2
/books/3
```

---

# 🧠 Small Example

Let's apply Video #3 to our **My Shelf** application.

### Project

```text
app/
├── index.tsx
├── books.tsx
└── profile.tsx
```

### `index.tsx`

```tsx id="h8q4w2"
import { View, Text } from "react-native";
import { Link } from "expo-router";

const Home = () => {
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
};

export default Home;
```

Now we have:

```text
My Shelf
│
├── My Books
│      ↓
│   /books
│
└── My Profile
       ↓
    /profile
```

That's the **core idea of Video #3**.

---

# 🎯 Practice

Add two new screens to your existing project:

```text
app/
├── index.tsx
├── books.tsx
└── profile.tsx
```

Then on your `Home` screen:

```text
My Shelf

[ My Books ]

[ My Profile ]
```

Make both buttons/links navigate to their respective screens using:

```tsx id="n7y7mo"
<Link href="...">
```

Don't worry about styling them yet.

**Next, we'll learn layouts and Stack navigation, where this file-based navigation starts becoming much more powerful.**
