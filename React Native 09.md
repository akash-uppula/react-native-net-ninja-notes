# 📱 React Native — Video #9 Notes

## Complete React Native Tutorial #9 — Tabs Navigation

This video introduces **Tabs Navigation** with Expo Router.

You already learned:

* **Video 3:** File-based navigation + `Link`
* **Video 4:** `Stack` navigation
* **Video 7:** Route groups + nested layouts
* **Video 8:** `Pressable`

Now we're adding another major navigation pattern:

> **Tabs let users switch between the main sections of an app using a tab bar.**

---

# 1. What is Tab Navigation?

Think of apps like:

```text
┌─────────────────────────────┐
│                             │
│        Current Screen       │
│                             │
│                             │
├─────────────────────────────┤
│  🏠 Home   📚 Books  👤 Me  │
└─────────────────────────────┘
```

Each tab represents a different section.

For our **My Shelf** app:

```text
Home
Books
Profile
```

could each be a separate tab.

---

# 2. `Tabs` From Expo Router

With Expo Router, tabs are configured using:

```tsx
import { Tabs } from "expo-router";
```

Then:

```tsx
<Tabs>
  ...
</Tabs>
```

This creates the tab navigator.

---

# 3. Folder Structure

Tabs are created using the file structure.

A common structure is:

```text
app/
├── _layout.tsx
│
└── (tabs)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Notice:

```text
(tabs)
```

is a **route group**.

You learned about this in Video #7.

---

# 4. Why `(tabs)`?

The parentheses mean:

```text
(tabs)
```

is a route group.

Therefore, it doesn't become part of the URL.

For example:

```text
(tabs)/books.tsx
```

still represents:

```text
/books
```

not:

```text
/tabs/books
```

So we're combining concepts from Video #7:

```text
Route Group
     +
Tabs Layout
```

---

# 5. The Tabs `_layout.tsx`

Inside:

```text
app/(tabs)/_layout.tsx
```

we configure the tabs.

Example:

```tsx
import { Tabs } from "expo-router";

export default function TabsLayout() {
  return (
    <Tabs>
      <Tabs.Screen
        name="index"
        options={{
          title: "Home",
        }}
      />

      <Tabs.Screen
        name="books"
        options={{
          title: "Books",
        }}
      />

      <Tabs.Screen
        name="profile"
        options={{
          title: "Profile",
        }}
      />
    </Tabs>
  );
}
```

That's the basic setup.

---

# 6. How Expo Router Connects Files to Tabs

This is extremely important.

Suppose we have:

```text
(tabs)/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

Then:

```text
index.tsx
    ↓
Home tab

books.tsx
    ↓
Books tab

profile.tsx
    ↓
Profile tab
```

The:

```tsx
<Tabs.Screen name="books" />
```

refers to:

```text
books.tsx
```

The file name and `name` are connected.

---

# 7. The `index.tsx` Tab

Remember from file-based routing:

```text
index.tsx
```

represents the default route for its folder.

Therefore:

```text
(tabs)/index.tsx
```

becomes the default/home tab.

Example:

```tsx
const Home = () => {
  return (
    <View>
      <Text>My Shelf</Text>
    </View>
  );
};

export default Home;
```

---

# 8. Adding Icons

Tabs usually have icons.

Expo Router allows us to configure:

```tsx
tabBarIcon
```

For example:

```tsx
<Tabs.Screen
  name="index"
  options={{
    title: "Home",
    tabBarIcon: ({ color }) => (
      <Text style={{ color }}>
        🏠
      </Text>
    ),
  }}
/>
```

However, in a real application, you'll generally use an icon library rather than emoji.

---

# 9. Using Icons

Expo projects commonly use:

```tsx
import { Ionicons } from "@expo/vector-icons";
```

Then:

```tsx
tabBarIcon: ({ color, size }) => (
  <Ionicons
    name="home"
    size={size}
    color={color}
  />
)
```

So:

```text
tabBarIcon
     ↓
Ionicons
     ↓
Home icon
```

---

# 10. Complete Tabs Example

```tsx
import { Tabs } from "expo-router";
import { Ionicons } from "@expo/vector-icons";

export default function TabsLayout() {
  return (
    <Tabs>
      <Tabs.Screen
        name="index"
        options={{
          title: "Home",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="home"
              size={size}
              color={color}
            />
          ),
        }}
      />

      <Tabs.Screen
        name="books"
        options={{
          title: "Books",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="book"
              size={size}
              color={color}
            />
          ),
        }}
      />

      <Tabs.Screen
        name="profile"
        options={{
          title: "Profile",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="person"
              size={size}
              color={color}
            />
          ),
        }}
      />
    </Tabs>
  );
}
```

---

# 11. What Are `color` and `size`?

Notice:

```tsx
({ color, size })
```

Expo Router provides these values to the `tabBarIcon` function.

So:

```tsx
tabBarIcon: ({ color, size }) => (
```

means:

> Give me the color and size that the tab navigator wants me to use for this icon.

Then:

```tsx
<Ionicons
  name="home"
  size={size}
  color={color}
/>
```

uses those values.

This is useful because the tab navigator can automatically provide different colors for:

```text
Selected tab
     ↓
Active color

Unselected tab
     ↓
Inactive color
```

---

# 12. `tabBarActiveTintColor`

You can customize the active tab color:

```tsx
<Tabs
  screenOptions={{
    tabBarActiveTintColor: "blue",
  }}
>
```

For example:

```tsx
export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: "blue",
      }}
    >
      ...
    </Tabs>
  );
}
```

Now the currently selected tab uses the specified active color.

---

# 13. `tabBarInactiveTintColor`

You can also control inactive tabs:

```tsx
<Tabs
  screenOptions={{
    tabBarActiveTintColor: "blue",
    tabBarInactiveTintColor: "gray",
  }}
>
```

So:

```text
Selected
   ↓
blue

Not selected
   ↓
gray
```

---

# 14. `screenOptions`

Instead of repeating options for every tab:

```tsx
<Tabs.Screen ... />
<Tabs.Screen ... />
<Tabs.Screen ... />
```

you can put common configuration on:

```tsx
<Tabs
  screenOptions={{
    ...
  }}
>
```

For example:

```tsx
<Tabs
  screenOptions={{
    headerShown: false,
    tabBarActiveTintColor: "blue",
  }}
>
```

These options can apply to the tab screens unless overridden.

---

# 15. `headerShown`

By default, a tab screen can have a header.

You can hide it:

```tsx
screenOptions={{
  headerShown: false,
}}
```

Example:

```tsx
<Tabs
  screenOptions={{
    headerShown: false,
  }}
>
```

This is useful if your screen already has its own header/UI.

---

# 16. Tabs + Stack Navigation

This is where navigation architecture gets interesting.

A real app often has:

```text
              Root
               │
              Tabs
       ┌───────┼────────┐
       ↓       ↓        ↓
     Home     Books    Profile
                │
                ↓
              Stack
                │
        ┌───────┴───────┐
        ↓               ↓
     Book List      Book Details
```

So tabs and stacks can work together.

---

# 17. Example: Books Tab With Nested Screens

Suppose:

```text
app/
└── (tabs)/
    ├── _layout.tsx
    ├── index.tsx
    │
    └── books/
        ├── _layout.tsx
        ├── index.tsx
        └── [id].tsx
```

Now:

```text
Books tab
    ↓
Books Stack
    ↓
 ┌───────────────┐
 ↓               ↓
Book List     Book Details
```

The user can stay within the Books section while navigating between:

```text
/books
```

and:

```text
/books/123
```

---

# 18. Nested Stack Inside a Tab

The Books layout could be:

```tsx
import { Stack } from "expo-router";

export default function BooksLayout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          title: "My Books",
        }}
      />

      <Stack.Screen
        name="[id]"
        options={{
          title: "Book Details",
        }}
      />
    </Stack>
  );
}
```

This combines what we learned in **Video #4 + Video #7** with today's Tabs.

---

# 19. Tab Navigation vs Stack Navigation

This distinction is important.

### Stack

Used for moving **deeper** into a section.

```text
Books
  ↓
Book Details
  ↓
Reviews
```

Like:

```text
A → B → C
```

### Tabs

Used for switching between **major sections**.

```text
Home
Books
Profile
```

Like:

```text
A ↔ B ↔ C
```

So:

```text
Tabs
 ↓
Main sections

Stack
 ↓
Details/deeper screens
```

---

# 20. Tabs and Route Groups

You learned route groups in Video #7.

Now combine them:

```text
app/
├── _layout.tsx
│
└── (tabs)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Here:

```text
(tabs)
```

is the **route group**.

And:

```text
(tabs)/_layout.tsx
```

contains:

```tsx
<Tabs>
```

So:

```text
Route Group
     ↓
(tabs)
     ↓
Tabs Layout
     ↓
Tabs.Screen
     ↓
Individual tab screens
```

---

# 21. Our My Shelf App

Let's create the structure:

```text
app/
├── _layout.tsx
│
└── (tabs)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

### `index.tsx`

```tsx
import {
  StyleSheet,
  Text,
  View,
} from "react-native";

const Home = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>
        My Shelf
      </Text>
    </View>
  );
};

export default Home;

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },

  title: {
    fontSize: 28,
    fontWeight: "bold",
  },
});
```

### `books.tsx`

```tsx
import {
  Text,
  View,
} from "react-native";

const Books = () => {
  return (
    <View>
      <Text>My Books</Text>
    </View>
  );
};

export default Books;
```

### `profile.tsx`

```tsx
import {
  Text,
  View,
} from "react-native";

const Profile = () => {
  return (
    <View>
      <Text>My Profile</Text>
    </View>
  );
};

export default Profile;
```

---

# 22. Tabs Layout for My Shelf

`app/(tabs)/_layout.tsx`:

```tsx
import { Tabs } from "expo-router";
import { Ionicons } from "@expo/vector-icons";

const TabsLayout = () => {
  return (
    <Tabs>
      <Tabs.Screen
        name="index"
        options={{
          title: "Home",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="home"
              size={size}
              color={color}
            />
          ),
        }}
      />

      <Tabs.Screen
        name="books"
        options={{
          title: "Books",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="book"
              size={size}
              color={color}
            />
          ),
        }}
      />

      <Tabs.Screen
        name="profile"
        options={{
          title: "Profile",
          tabBarIcon: ({ color, size }) => (
            <Ionicons
              name="person"
              size={size}
              color={color}
            />
          ),
        }}
      />
    </Tabs>
  );
};

export default TabsLayout;
```

---

# 23. Result

You now get something like:

```text
┌─────────────────────────────┐
│                             │
│         My Shelf            │
│                             │
│                             │
│                             │
├─────────────────────────────┤
│ 🏠        📖         👤     │
│ Home      Books      Profile │
└─────────────────────────────┘
```

Tap:

```text
Books
```

and Expo Router loads:

```text
(tabs)/books.tsx
```

Tap:

```text
Profile
```

and it loads:

```text
(tabs)/profile.tsx
```

---

# 24. Important: You Don't Need `<Link>` for Tabs

In Video #3 we learned:

```tsx
<Link href="/books">
```

For tabs, you normally **don't need to manually create links**.

The tab bar itself handles navigation.

```text
User taps Books tab
       ↓
Tabs navigator
       ↓
books.tsx
```

So the tab bar acts as the navigation UI.

---

# 25. `Tabs.Screen`

The basic syntax is:

```tsx
<Tabs.Screen
  name="books"
  options={{
    title: "Books",
  }}
/>
```

`name` corresponds to the route/file:

```text
books.tsx
```

while `options` controls how it appears in the tab navigator.

---

# 26. Hiding a Screen From the Tab Bar

Sometimes a screen should exist inside the tabs group but **shouldn't appear as a tab**.

You can hide its tab button:

```tsx
<Tabs.Screen
  name="settings"
  options={{
    href: null,
  }}
/>
```

This is useful for routes that are part of the group but aren't intended to be directly accessible from the tab bar.

---

# 27. Important `href: null`

This:

```tsx
href: null
```

means the route isn't shown as a tab button.

It does **not** necessarily mean the route doesn't exist.

That's useful when organizing more complex navigation.

---

# 28. Tab Bar Styling

You can customize the tab bar using `tabBarStyle`.

Example:

```tsx
<Tabs
  screenOptions={{
    tabBarStyle: {
      height: 60,
    },
  }}
>
```

You can also change:

```text
tabBarLabelStyle
tabBarIcon
tabBarActiveTintColor
tabBarInactiveTintColor
tabBarStyle
```

But don't try to memorize every option now.

The important thing is understanding:

```text
screenOptions
     ↓
Common tab configuration
```

---

# ⭐ Video #9 — Must Remember

### 1. Import

```tsx
import { Tabs } from "expo-router";
```

### 2. Tabs layout

```tsx
<Tabs>
  ...
</Tabs>
```

### 3. Configure a tab

```tsx
<Tabs.Screen
  name="books"
  options={{
    title: "Books",
  }}
/>
```

### 4. File-based tabs

```text
(tabs)/
├── index.tsx
├── books.tsx
└── profile.tsx
```

becomes:

```text
Home
Books
Profile
```

### 5. Icons

```tsx
tabBarIcon: ({ color, size }) => (
  <Ionicons
    name="book"
    size={size}
    color={color}
  />
)
```

### 6. Active/inactive colors

```tsx
screenOptions={{
  tabBarActiveTintColor: "...",
  tabBarInactiveTintColor: "...",
}}
```

### 7. Hide headers

```tsx
screenOptions={{
  headerShown: false,
}}
```

---

# 🧠 Stack vs Tabs — Remember This

This is probably the **most important concept from Video #9**.

### Stack

```text
Home
 ↓
Details
 ↓
More Details
```

Think:

> **Going deeper**

### Tabs

```text
Home  ↔  Books  ↔  Profile
```

Think:

> **Switching between major sections**

A real application can use both:

```text
                 App
                  │
                 Tabs
          ┌───────┼────────┐
          ↓       ↓        ↓
        Home     Books   Profile
                   │
                  Stack
               ┌───┴────┐
               ↓        ↓
             List     Details
```

---

# 🎯 Your My Shelf Navigation

After Video #9, your app architecture can look like:

```text
app/
│
├── _layout.tsx
│
└── (tabs)/
    │
    ├── _layout.tsx       ← Tabs
    │
    ├── index.tsx         ← Home
    │
    ├── books/
    │   ├── _layout.tsx   ← Stack
    │   ├── index.tsx     ← Book List
    │   └── [id].tsx      ← Book Details
    │
    └── profile.tsx       ← Profile
```

Which gives us:

```text
                 My Shelf
                    │
                  Tabs
        ┌───────────┼───────────┐
        ↓           ↓           ↓
      Home        Books       Profile
                    │
                  Stack
               ┌────┴────┐
               ↓         ↓
          Book List   Book Details
```

That's a **very realistic navigation structure** for a React Native app.

---

## 🔥 Progress So Far

```text
#1  Expo + Setup
        ↓
#2  View + Text + Image
        ↓
#3  File-Based Navigation + Link
        ↓
#4  Layouts + Stack
        ↓
#5  Light/Dark Themes
        ↓
#6  Themed UI Components
        ↓
#7  Route Groups + Nested Layouts
        ↓
#8  Pressable
        ↓
#9  Tabs Navigation
```

The big progression is now:

**Screens → Routes → Stack → Groups → Themes → Reusable Components → Tabs**

So don't just memorize the `Tabs` syntax. Make sure you understand **why `(tabs)/_layout.tsx` exists and how Expo Router maps each file to a tab**.
