# 📱 React Native — Video #7 Notes

## Complete React Native Tutorial #7 — Route Groups & Nested Layouts

This video builds on **Video 3 (file-based navigation)** and **Video 4 (`_layout` + Stack)**.

The two main concepts are:

1. **Route Groups**
2. **Nested Layouts**

These become very important when your app starts having many screens.

---

# 1. The Problem

Suppose our app grows:

```text
app/
├── index.tsx
├── about.tsx
├── books.tsx
├── profile.tsx
├── login.tsx
└── register.tsx
```

Everything is inside one folder.

But logically, these screens belong to different sections:

```text
Main App
 ├── Home
 ├── Books
 └── Profile

Authentication
 ├── Login
 └── Register
```

We want to organize the files **without necessarily changing the URLs/routes**.

That's where **route groups** come in.

---

# 2. Route Groups

A route group is a folder whose name is surrounded by parentheses:

```text
(auth)
```

For example:

```text
app/
├── index.tsx
├── (auth)/
│   ├── login.tsx
│   └── register.tsx
└── books.tsx
```

The important thing is:

> The `(auth)` folder is used for organization and navigation structure, but it does **not** become part of the URL.

So:

```text
(auth)/login.tsx
```

becomes:

```text
/login
```

**not:**

```text
/auth/login
```

---

# 3. Why Parentheses?

This:

```text
(auth)
```

is a **route group**.

The parentheses tell Expo Router:

> Group these routes together, but don't include this folder name in the route path.

Example:

```text
app/
└── (auth)/
    ├── login.tsx
    └── register.tsx
```

Routes:

```text
login.tsx
   ↓
/login

register.tsx
   ↓
/register
```

---

# 4. Route Groups Don't Affect the URL

Compare these:

### Normal folder

```text
app/
└── auth/
    └── login.tsx
```

Route:

```text
/auth/login
```

### Route group

```text
app/
└── (auth)/
    └── login.tsx
```

Route:

```text
/login
```

So:

```text
auth/
 ↓
part of route

(auth)/
 ↓
NOT part of route
```

This is one of the most important things to remember from this video.

---

# 5. Why Use Route Groups?

Route groups are useful for **organizing screens that belong together**.

For example:

```text
app/
├── (auth)/
│   ├── login.tsx
│   └── register.tsx
│
└── (app)/
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Now the project structure clearly communicates:

```text
(auth)
   ↓
Authentication screens

(app)
   ↓
Main application screens
```

But the group names aren't added to the URL.

---

# 6. Route Groups Can Have Their Own `_layout`

This is where things become powerful.

You can put a `_layout.tsx` inside a route group.

Example:

```text
app/
├── (auth)/
│   ├── _layout.tsx
│   ├── login.tsx
│   └── register.tsx
│
└── (app)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Now each group can have its **own navigation configuration**.

---

# 7. Nested Layouts

A layout inside another directory is called a **nested layout**.

For example:

```text
app/
├── _layout.tsx
│
└── books/
    ├── _layout.tsx
    ├── index.tsx
    └── [id].tsx
```

Here:

```text
app/_layout.tsx
       ↓
Parent layout

books/_layout.tsx
       ↓
Child/nested layout
```

The `books` screens can have navigation behavior specific to that section.

---

# 8. Think of Layouts Like Parents

Imagine:

```text
app/
│
├── _layout.tsx
│       ↓
│    Parent
│       │
│       ├── index.tsx
│       │
│       └── books/
│             │
│             ├── _layout.tsx
│             │       ↓
│             │     Child
│             │
│             ├── index.tsx
│             └── [id].tsx
```

The parent layout controls the overall application.

The nested layout controls the routes inside `books`.

---

# 9. Example — Books Section

Suppose:

```text
app/
└── books/
    ├── _layout.tsx
    ├── index.tsx
    └── [id].tsx
```

The routes are:

```text
books/index.tsx
       ↓
/books

books/[id].tsx
       ↓
/books/123
```

Now `books/_layout.tsx` can contain:

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

So the books section has its own Stack.

---

# 10. Parent and Child Layouts

Suppose:

```text
app/
├── _layout.tsx
│
└── books/
    ├── _layout.tsx
    ├── index.tsx
    └── [id].tsx
```

Navigation hierarchy:

```text
App Stack
   │
   └── Books Stack
         │
         ├── Books
         └── Book Details
```

This is called **nested navigation**.

---

# 11. Route Groups + Nested Layouts Together

This is where the video becomes particularly useful.

Consider:

```text
app/
├── _layout.tsx
│
├── (auth)/
│   ├── _layout.tsx
│   ├── login.tsx
│   └── register.tsx
│
└── (app)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

We can think of it as:

```text
                    Root Layout
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
           (auth)                 (app)
              │                     │
          Auth Layout            App Layout
              │                     │
        ┌─────┴─────┐       ┌──────┼──────┐
        ↓           ↓       ↓      ↓      ↓
      Login      Register  Home   Books  Profile
```

This is a clean way to structure a larger application.

---

# 12. Root `_layout.tsx`

The root layout is:

```text
app/_layout.tsx
```

It sits at the top of the navigation hierarchy.

For example:

```tsx
import { Stack } from "expo-router";

export default function RootLayout() {
  return <Stack />;
}
```

It can provide navigation configuration that applies to the routes beneath it.

---

# 13. Group `_layout.tsx`

Inside `(auth)`:

```text
app/
└── (auth)/
    ├── _layout.tsx
    ├── login.tsx
    └── register.tsx
```

We can have:

```tsx
import { Stack } from "expo-router";

export default function AuthLayout() {
  return (
    <Stack>
      <Stack.Screen
        name="login"
        options={{
          title: "Login",
        }}
      />

      <Stack.Screen
        name="register"
        options={{
          title: "Register",
        }}
      />
    </Stack>
  );
}
```

Now the authentication screens share this navigation configuration.

---

# 14. Group Names Are Not Routes

This is worth repeating.

Given:

```text
app/
└── (auth)/
    └── login.tsx
```

You navigate with:

```tsx
<Link href="/login">
  Login
</Link>
```

**Not:**

```tsx
<Link href="/auth/login">
```

because `(auth)` is only a route group.

---

# 15. Nested Folders vs Route Groups

### Normal folder

```text
app/
└── books/
    └── details.tsx
```

Route:

```text
/books/details
```

### Route group

```text
app/
└── (books)/
    └── details.tsx
```

Route:

```text
/details
```

The difference:

```text
books
 ↓
URL path

(books)
 ↓
organization only
```

---

# 16. A Realistic My Shelf Structure

Let's improve our **My Shelf** app.

We could organize it like this:

```text
app/
│
├── _layout.tsx
│
├── (auth)/
│   ├── _layout.tsx
│   ├── login.tsx
│   └── register.tsx
│
└── (main)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Conceptually:

```text
                    Root
                     │
          ┌──────────┴──────────┐
          ↓                     ↓
        Auth                   Main
          │                     │
      Auth Stack             Main Stack
          │                     │
     ┌────┴────┐       ┌────────┼────────┐
     ↓         ↓       ↓        ↓        ↓
   Login    Register  Home     Books   Profile
```

---

# 17. Why This Structure Is Useful

Imagine later we add:

```text
books/
├── index.tsx
├── [id].tsx
└── favorites.tsx
```

We can give the Books section its own layout:

```text
(main)/
└── books/
    ├── _layout.tsx
    ├── index.tsx
    ├── [id].tsx
    └── favorites.tsx
```

Now:

```text
Main Layout
     ↓
Books Layout
     ↓
 ┌───┼────────┐
 ↓   ↓        ↓
Books Details Favorites
```

This makes large apps much easier to manage.

---

# 18. The Key Difference: Route vs Layout

A normal screen:

```text
index.tsx
books.tsx
profile.tsx
```

represents something the user **navigates to**.

A `_layout.tsx`:

```text
_layout.tsx
```

defines **how the routes underneath it are organized/navigated**.

So:

```text
Screen
 ↓
Something the user sees

Layout
 ↓
Controls/navigation structure around those screens
```

---

# ⭐ Video #7 — Important Terms

| Concept           | Meaning                                                  |
| ----------------- | -------------------------------------------------------- |
| Route Group       | Folder using `(name)` that doesn't appear in the URL     |
| `(auth)`          | Example of a route group                                 |
| `_layout.tsx`     | Defines layout/navigation for child routes               |
| Nested Layout     | `_layout.tsx` inside a child folder/group                |
| Root Layout       | `app/_layout.tsx`                                        |
| Child Layout      | Layout inside a nested directory                         |
| Nested Navigation | Navigation structure inside another navigation structure |

---

# 🧠 Most Important Mental Model

Remember these three rules:

### Rule 1 — Normal file

```text
app/books.tsx
```

➡️ creates:

```text
/books
```

### Rule 2 — Normal folder

```text
app/books/details.tsx
```

➡️ creates:

```text
/books/details
```

### Rule 3 — Route group

```text
app/(main)/books.tsx
```

➡️ creates:

```text
/books
```

because:

```text
(main)
```

doesn't appear in the route.

---

# 🔥 Route Groups vs Nested Layouts

Don't mix these two concepts.

### Route Group

Main purpose:

> **Organize routes without changing their URL path.**

```text
(auth)
(main)
```

### Nested Layout

Main purpose:

> **Give a group of routes its own navigation/layout configuration.**

```text
books/
└── _layout.tsx
```

They can also be used **together**.

---

# 🎯 Small Practice

For your **My Shelf** app, try creating:

```text
app/
│
├── _layout.tsx
│
├── (auth)/
│   ├── _layout.tsx
│   ├── login.tsx
│   └── register.tsx
│
└── (main)/
    ├── _layout.tsx
    ├── index.tsx
    ├── books.tsx
    └── profile.tsx
```

Don't worry about authentication logic yet.

Just understand the structure:

```text
                 app
                  │
              _layout
                  │
          ┌───────┴───────┐
          ↓               ↓
       (auth)           (main)
          │               │
       _layout         _layout
          │               │
     ┌────┴────┐    ┌─────┼─────┐
     ↓         ↓    ↓     ↓     ↓
   Login   Register Home  Books Profile
```

And remember:

**`(auth)` and `(main)` help organize the app, but they don't become part of the URL.**

---

## 📚 Your course progression

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
```

At this point you're learning the **navigation architecture** of Expo Router, not just individual components. The next lessons can build on this structure rather than starting over each time.
