# 📱 React Native — Video #10 Notes

## Complete React Native Tutorial #10 — Tab Bar Icons

This video builds directly on **Video #9 — Tabs Navigation**.

In Video #9, we created tabs like:

```text
Home
Books
Profile
```

Now we make those tabs look more like a real app by adding **icons to the tab bar**.

For your **My Shelf** project, this is especially useful because we'll eventually have:

```text
🏠 Home   📚 Books   👤 Profile
```

---

# 1. What is a Tab Bar Icon?

A tab bar icon is the small icon displayed above/beside the label of each tab.

For example:

```text
┌───────────────────────────────┐
│                               │
│        Current Screen         │
│                               │
├───────────────────────────────┤
│   🏠          📚          👤   │
│  Home        Books       Profile│
└───────────────────────────────┘
```

Each tab can have its own icon.

---

# 2. `tabBarIcon`

Expo Router's `Tabs.Screen` provides an option called:

```tsx id="1iqv8n"
tabBarIcon
```

Example:

```tsx id="z8c0uy"
<Tabs.Screen
  name="index"
  options={{
    title: "Home",
    tabBarIcon: () => (
      <Text>🏠</Text>
    ),
  }}
/>
```

The important part is:

```tsx id="4k3d6n"
tabBarIcon: () => (...)
```

This tells the tab navigator:

> What should I display as the icon for this tab?

---

# 3. Using Ionicons

Instead of emoji, we can use an icon library.

A common choice with Expo is:

```tsx id="6ufgpl"
Ionicons
```

Import it using ES6:

```tsx id="9bhrh0"
import { Ionicons } from "@expo/vector-icons";
```

Then:

```tsx id="7x8m3k"
<Ionicons
  name="home"
  size={24}
  color="black"
/>
```

---

# 4. Basic Ionicons Example

```tsx id="a8s3y7"
import { Ionicons } from "@expo/vector-icons";

<Ionicons
  name="home"
  size={24}
  color="black"
/>
```

There are three important props here:

```text id="xq8h1j"
name
 ↓
Which icon?

size
 ↓
How big?

color
 ↓
What color?
```

---

# 5. `name`

The `name` property selects the icon.

Example:

```tsx id="d5pm3x"
name="home"
```

Other examples include:

```tsx id="3r4c2q"
name="book"
```

```tsx id="74h5qk"
name="person"
```

```tsx id="tr9vye"
name="settings"
```

So:

```tsx id="j5px7u"
<Ionicons name="book" />
```

displays a book icon.

---

# 6. `size`

You can control the icon size:

```tsx id="p7f8e2"
size={24}
```

For example:

```tsx id="h7yq1e"
<Ionicons
  name="home"
  size={30}
/>
```

Larger value:

```text id="7m4zj8"
size={30}
   ↓
larger icon
```

Smaller value:

```text id="h9p4d2"
size={18}
   ↓
smaller icon
```

---

# 7. `color`

You can specify the icon color:

```tsx id="v2h1c8"
color="blue"
```

Example:

```tsx id="5xg7dk"
<Ionicons
  name="home"
  size={24}
  color="blue"
/>
```

However, for tab icons, we generally **shouldn't hard-code the color**.

Why?

Because the tab navigator knows whether the tab is:

```text id="qz7n2k"
Active
```

or:

```text id="2c3b0p"
Inactive
```

and can provide the appropriate color.

---

# 8. The `color` Provided by Tabs

This is the important part:

```tsx id="s8j4yf"
tabBarIcon: ({ color }) => (
  <Ionicons
    name="home"
    color={color}
  />
)
```

Expo Router gives our function the `color`.

So:

```text id="z2s5pi"
Tabs
 │
 ├── Active tab
 │       ↓
 │    active color
 │
 └── Inactive tab
         ↓
      inactive color
```

Our icon simply uses that color.

---

# 9. `size` Is Also Provided

We can receive both:

```tsx id="efy2t4"
({ color, size })
```

Then:

```tsx id="b6x1mi"
<Ionicons
  name="home"
  size={size}
  color={color}
/>
```

This is better than manually writing:

```tsx id="7zpsa9"
size={24}
color="black"
```

because the tab navigator controls the appropriate values.

---

# 10. Complete Example

```tsx id="8c3w5n"
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

# 11. What Is Happening Here?

Take:

```tsx id="2ey4yn"
tabBarIcon: ({ color, size }) => (
  <Ionicons
    name="book"
    size={size}
    color={color}
  />
)
```

Step by step:

### Step 1

The Tabs navigator calls:

```tsx id="0j75ah"
tabBarIcon
```

### Step 2

It provides:

```tsx id="qjzq1x"
color
size
```

### Step 3

We pass them to Ionicons:

```tsx id="5ymn75"
<Ionicons
  name="book"
  size={size}
  color={color}
/>
```

### Step 4

The book icon appears in the tab bar.

---

# 12. Active and Inactive Icons

Suppose:

```text id="w4cbx8"
Home      Books      Profile
```

and you're currently on Home.

The navigator might provide:

```text id="ddg5r7"
Home
color → active color

Books
color → inactive color

Profile
color → inactive color
```

Because our icons use:

```tsx id="z2o0td"
color={color}
```

the icons automatically follow the tab state.

---

# 13. Setting Active and Inactive Colors

You can configure these at the `<Tabs>` level:

```tsx id="h2f8gx"
<Tabs
  screenOptions={{
    tabBarActiveTintColor: "blue",
    tabBarInactiveTintColor: "gray",
  }}
>
```

Then:

```text id="t0v4n7"
Active tab
   ↓
blue

Inactive tab
   ↓
gray
```

Your icon receives those colors automatically.

---

# 14. `screenOptions`

Instead of configuring common options repeatedly:

```tsx id="r3h2v6"
<Tabs.Screen ... />
<Tabs.Screen ... />
<Tabs.Screen ... />
```

we can put shared options here:

```tsx id="3a1w5m"
<Tabs
  screenOptions={{
    tabBarActiveTintColor: "blue",
    tabBarInactiveTintColor: "gray",
  }}
>
```

Think:

```text id="f5ec4k"
Tabs
 │
 ├── screenOptions
 │      ↓
 │   common settings
 │
 ├── Home
 ├── Books
 └── Profile
```

---

# 15. Different Icons for Different Tabs

For our My Shelf app:

### Home

```tsx id="4y0d7b"
name="home"
```

### Books

```tsx id="j1c8n9"
name="book"
```

### Profile

```tsx id="p0r6k4"
name="person"
```

So:

```text id="9r7p4w"
🏠 Home
📖 Books
👤 Profile
```

---

# 16. Filled vs Outline Icons

Ionicons provides different icon variants.

For example, you might have icons such as:

```text id="4g2x7k"
home
home-outline
```

Similarly:

```text id="qlp3r1"
person
person-outline
```

and:

```text id="d3x8z5"
heart
heart-outline
```

The idea is:

```text id="x9a2k4m"
home
 ↓
filled icon

home-outline
 ↓
outline icon
```

This can be useful for creating different visual states.

---

# 17. Using Active State for Different Icons

The `tabBarIcon` function can also receive:

```tsx id="3x7v0z"
focused
```

Example:

```tsx id="j2a8sf"
tabBarIcon: ({ color, size, focused }) => (
  <Ionicons
    name={focused ? "home" : "home-outline"}
    size={size}
    color={color}
  />
)
```

Now:

```text id="7f9c4n"
Home selected
     ↓
home

Home not selected
     ↓
home-outline
```

This gives the user a clear indication of the active tab.

---

# 18. Understanding `focused`

`focused` is a boolean.

```text id="u0y1mq"
focused === true
```

means:

> This tab is currently selected.

And:

```text id="l4k9p2"
focused === false
```

means:

> This tab isn't selected.

So we can write:

```tsx id="2r3v8f"
focused
  ? "home"
  : "home-outline"
```

---

# 19. Ternary Operator Again

This is the same JavaScript concept you encountered earlier:

```tsx id="7b0c4z"
focused
  ? "home"
  : "home-outline"
```

Meaning:

```text id="6n7w1e"
If focused
    ↓
"home"

Otherwise
    ↓
"home-outline"
```

This is particularly useful for tab icons.

---

# 20. My Shelf — Better Tab Icons

We can now create:

```tsx id="8w0x5c"
import { Tabs } from "expo-router";
import { Ionicons } from "@expo/vector-icons";

const TabsLayout = () => {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: "#2563eb",
        tabBarInactiveTintColor: "#888",
      }}
    >
      <Tabs.Screen
        name="index"
        options={{
          title: "Home",

          tabBarIcon: ({
            color,
            size,
            focused,
          }) => (
            <Ionicons
              name={
                focused
                  ? "home"
                  : "home-outline"
              }
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

          tabBarIcon: ({
            color,
            size,
            focused,
          }) => (
            <Ionicons
              name={
                focused
                  ? "book"
                  : "book-outline"
              }
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

          tabBarIcon: ({
            color,
            size,
            focused,
          }) => (
            <Ionicons
              name={
                focused
                  ? "person"
                  : "person-outline"
              }
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

# 21. Result

Your tab bar can now visually communicate the active screen:

```text id="2e3f9x"
┌─────────────────────────────────┐
│                                 │
│          My Shelf               │
│                                 │
│                                 │
├─────────────────────────────────┤
│      🏠          📖         👤   │
│     Home        Books      Profile│
│      ↑                          │
│    Active                       │
└─────────────────────────────────┘
```

When you select Books:

```text id="8hs7t4"
┌─────────────────────────────────┐
│                                 │
│          My Books               │
│                                 │
│                                 │
├─────────────────────────────────┤
│      🏠          📖         👤   │
│     Home        Books      Profile│
│                   ↑             │
│                 Active          │
└─────────────────────────────────┘
```

---

## 22. `tabBarLabel`

`tabBarLabel` controls the **text displayed underneath the tab icon** in the tab bar.

For example:

```tsx
<Tabs.Screen
  name="books"
  options={{
    tabBarLabel: "Books",
  }}
/>
```

The tab bar will show:

```text
   📚
  Books
```

### `tabBarLabel` vs `title`

These two options are different:

```tsx
<Tabs.Screen
  name="books"
  options={{
    title: "My Books",
    tabBarLabel: "Books",
  }}
/>
```

Here:

* `title` → controls the **screen/header title**
* `tabBarLabel` → controls the **text shown in the tab bar**

So you can have:

```text
Header:
My Books

Tab bar:
📚
Books
```

---

### Example

```tsx
<Tabs.Screen
  name="books"
  options={{
    title: "My Books",
    tabBarLabel: "Books",
    tabBarIcon: ({ color, size }) => (
      <Ionicons
        name="book"
        size={size}
        color={color}
      />
    ),
  }}
/>
```

Result:

```text
┌─────────────────────────┐
│       My Books           │  ← title
│                          │
│       Screen content     │
│                          │
├─────────────────────────┤
│    🏠      📚       👤   │
│   Home    Books   Profile│  ← tabBarLabel
└─────────────────────────┘
```

### If you don't specify `tabBarLabel`

You can usually let the tab navigator derive the label from the route/title:

```tsx
<Tabs.Screen
  name="books"
  options={{
    title: "Books",
  }}
/>
```

But if you specifically want different text in the tab bar, use:

```tsx
tabBarLabel: "Books"
```

---

### Hiding the label

If you want **only icons**:

```tsx
<Tabs
  screenOptions={{
    tabBarShowLabel: false,
  }}
>
```

Result:

```text
┌─────────────────────────┐
│                         │
│   🏠       📚       👤   │
│                         │
└─────────────────────────┘
```

So remember:

```text
title
  ↓
Screen/Header title

tabBarLabel
  ↓
Text under tab icon

tabBarShowLabel
  ↓
Show/hide tab labels
```

---

# 23. `tabBarShowLabel`

If you don't want text labels:

```tsx id="2y7p8n"
<Tabs
  screenOptions={{
    tabBarShowLabel: false,
  }}
>
```

Then you can have:

```text id="5q6r8t"
┌──────────────────────────┐
│                          │
│   🏠       📖       👤   │
│                          │
└──────────────────────────┘
```

instead of:

```text id="0l7c5x"
┌──────────────────────────┐
│                          │
│   🏠       📖       👤   │
│  Home    Books    Profile │
└──────────────────────────┘
```

---

# 24. Important: Don't Hard-Code Icon Colors

Prefer:

```tsx id="m0v8v2"
<Ionicons
  name="book"
  size={size}
  color={color}
/>
```

instead of:

```tsx id="5o8s7z"
<Ionicons
  name="book"
  size={24}
  color="black"
/>
```

Why?

Because:

```text id="d2e6x0"
Tabs
 ↓
knows active/inactive state
 ↓
provides color
 ↓
Ionicons uses that color
```

This also makes your UI easier to adapt to themes.

---

# 25. Combining With Video #5 & #6

This is where your previous lessons connect.

You learned in Video #5:

```tsx id="2z7g0c"
useColorScheme()
```

and in Video #6:

```text id="m5b3d1"
Themed Components
```

Now you can eventually make your tab bar theme-aware too.

Conceptually:

```text id="5t7y9m"
System Theme
     ↓
Light / Dark
     ↓
Theme Colors
     ↓
Tab Bar
     ↓
Icons + Labels
```

So don't treat each video as an isolated topic. The Net Ninja course is gradually building the pieces of a real app.

---

# ⭐ Video #10 — Must Remember

### 1. Import Ionicons

```tsx id="l7b1f4"
import { Ionicons } from "@expo/vector-icons";
```

### 2. `tabBarIcon`

```tsx id="8t2c6j"
tabBarIcon: ({ color, size }) => (
  <Ionicons
    name="home"
    size={size}
    color={color}
  />
)
```

### 3. `color`

Provided by the tab navigator:

```tsx id="p9x3m4"
color={color}
```

Use it instead of hard-coding colors.

### 4. `size`

Provided by the tab navigator:

```tsx id="y6k8r1"
size={size}
```

### 5. `focused`

Tells you whether the tab is currently selected:

```tsx id="q3m7v9"
focused
```

### 6. Active/inactive icons

```tsx id="r8v2k5"
name={
  focused
    ? "home"
    : "home-outline"
}
```

### 7. Active/inactive colors

```tsx id="1w5d8j"
screenOptions={{
  tabBarActiveTintColor: "...",
  tabBarInactiveTintColor: "...",
}}
```

---

# 🧠 Video #9 vs Video #10

### Video #9 — Tabs

You learned **how to create the tab navigation**:

```text id="q5y3x7"
(tabs)/
├── _layout.tsx
├── index.tsx
├── books.tsx
└── profile.tsx
```

and:

```tsx id="7w9z2a"
<Tabs>
```

### Video #10 — Tab Bar Icons

You learned **how to customize the visual appearance of those tabs**:

```tsx id="8k1p4v"
tabBarIcon: ({ color, size }) => (
  <Ionicons
    name="book"
    size={size}
    color={color}
  />
)
```

So:

```text id="v2p9q4"
Video 9
   ↓
Create tabs

Video 10
   ↓
Make tabs look good
```

---

# 🎯 Practice for My Shelf

Your goal after Video #10:

```text id="k4z8q1"
┌─────────────────────────────────┐
│                                 │
│          My Shelf               │
│                                 │
│                                 │
├─────────────────────────────────┤
│     🏠         📚         👤    │
│    Home       Books      Profile │
└─────────────────────────────────┘
```

Use:

```tsx id="z1v6p3"
home / home-outline
```

for Home,

```tsx id="n7x2c8"
book / book-outline
```

for Books, and:

```tsx id="b4m9k2"
person / person-outline
```

for Profile.

And use:

```tsx id="d8q3s5"
focused
```

to switch between the filled and outline versions.

---

## 🔥 Progress So Far

```text id="z6k1p4"
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
 ↓
#10 Tab Bar Icons
```

**Core idea of Video #10:**

> `Tabs` creates the navigation, while `tabBarIcon` lets you control the icon displayed for each tab. `color`, `size`, and `focused` let your icon respond automatically to the tab's state.
