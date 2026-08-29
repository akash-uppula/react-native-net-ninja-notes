# React Native — Video #1 Notes

## Complete React Native Tutorial #1 — Introduction & Setup (Expo)

---

## 1. What is React Native?

**React Native** is a framework for building mobile applications using **React + JavaScript/TypeScript**.

It allows us to create applications for platforms such as:

* Android
* iOS

Instead of HTML elements like:

```tsx
<div>
  <p>Hello</p>
</div>
```

React Native uses its own components:

```tsx
import { View, Text } from "react-native";

export default function App() {
  return (
    <View>
      <Text>Hello</Text>
    </View>
  );
}
```

### Important

React Native is **not React for the web**.

You will work with components such as:

```text
View
Text
Image
ScrollView
TextInput
Pressable
FlatList
```

and many others.

---

# 2. What is Expo?

**Expo** is a set of tools and services that makes React Native development easier.

It helps us with things like:

* creating projects
* running the application
* testing on devices
* development tools
* accessing native functionality
* building applications

For this course, we are using **Expo** to set up and run our React Native application.

---

# 3. Node.js

Before starting React Native development, install **Node.js**.

Website:

`nodejs.org`

Node.js provides the environment required to run many of the development tools we'll use.

After installing Node.js, we get access to commands such as:

```bash
npm
```

and:

```bash
npx
```

---

# 4. npm

`npm` stands for:

> **Node Package Manager**

It is used to install and manage packages in JavaScript projects.

For example:

```bash
npm install package-name
```

You'll use npm frequently in React Native projects.

---

# 5. npx

`npx` allows us to execute packages/commands.

For example:

```bash
npx create-expo-app
```

We're going to use `npx` to create our Expo application.

### Remember

```text
Node.js
   ↓
npm / npx
   ↓
JavaScript & React Native tools
```

---

# 6. VS Code

We use **Visual Studio Code** as our code editor.

We'll use VS Code for:

* writing React Native code
* creating files
* editing files
* running terminal commands
* installing extensions
* using code snippets

---

# 7. Creating an Expo Project

The command used to create the project is:

```bash
npx create-expo-app@latest --template blank ./
```

Let's understand it piece by piece.

### `npx`

Runs a package command.

### `create-expo-app`

Expo's project creation tool.

### `@latest`

Uses the latest available version.

### `--template blank`

Tells Expo to use the **blank template**.

### `./`

Means:

> Create the project in the current directory.

So the command is basically:

```text
npx
 ↓
create-expo-app
 ↓
latest version
 ↓
blank template
 ↓
current directory
```

---

# 8. Starting the Expo Application

Once the project is created, run:

```bash
npx expo start
```

This starts the **Expo development server**.

You'll then be able to run your application using the available development options, such as:

* Android emulator
* iOS simulator
* physical device
* web

The basic development flow is:

```text
Write React Native code
        ↓
Expo development server
        ↓
Run application
        ↓
Android / iOS / Web
```

---

# 9. Project Structure

When you create an Expo project, several files and folders are created.

You'll see things such as:

```text
project/
│
├── assets/
├── node_modules/
├── package.json
├── app.json
└── ...
```

Don't worry about understanding every file yet.

We'll learn them as we progress through the course.

---

# 10. `package.json`

`package.json` is one of the most important files in a JavaScript/React Native project.

It contains information about the project, including:

* project metadata
* dependencies
* scripts
* configuration

For example:

```json
{
  "name": "my-app",
  "version": "1.0.0"
}
```

It also contains the project's dependencies.

---

# 11. Expo Router

The tutorial introduces **Expo Router**.

Expo Router provides **file-based navigation** for Expo applications.

The basic idea is:

> Your files and folders represent your application's routes.

For example:

```text
app/
│
├── index.tsx
├── profile.tsx
└── settings.tsx
```

Conceptually:

```text
index.tsx
    ↓
    /

profile.tsx
    ↓
    /profile

settings.tsx
    ↓
    /settings
```

We'll learn this properly when we reach the navigation videos.

---

# 12. Installing Expo Router

The tutorial uses:

```bash
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

These packages are used to set up Expo Router and related functionality.

### Important

Don't memorize this entire command.

For now, understand:

```text
Expo
 ↓
Expo Router
 ↓
File-based navigation
```

---

# 13. Entry Point

The tutorial modifies `package.json`.

The important part is:

```json
{
  "main": "expo-router/entry"
}
```

### What is an entry point?

The entry point tells the application:

> **Where should the application start?**

With Expo Router:

```text
package.json
     ↓
"main"
     ↓
expo-router/entry
     ↓
Expo Router
     ↓
Application routes
```

So:

```json
"main": "expo-router/entry"
```

tells Expo to use the Expo Router entry point.

---

# 14. `app.json`

Expo applications also have configuration in:

```text
app.json
```

The tutorial introduces:

```json
{
  "expo": {
    "scheme": "your-app-scheme",
    "experiments": {
      "typedRoutes": true
    }
  }
}
```

There are two important things here.

---

# 15. `scheme`

```json
"scheme": "your-app-scheme"
```

A **URL scheme** is related to deep linking.

For example, suppose:

```json
"scheme": "myapp"
```

Then you can conceptually have a URL such as:

```text
myapp://profile
```

This can allow an external link to open your application and navigate to a particular screen.

Deep linking can be useful for:

* notifications
* emails
* authentication
* external links
* opening specific screens

You don't need to master deep linking yet. Just understand what a **scheme** is for.

---

# 16. Typed Routes

The tutorial also shows:

```json
"experiments": {
  "typedRoutes": true
}
```

This enables **typed routes**.

Since we're using TypeScript, this allows route information to be better understood by TypeScript.

For example, when working with navigation, TypeScript can help catch incorrect routes.

We'll understand the benefit of this much better when we learn Expo Router.

---

# 17. VS Code Snippets

The tutorial also mentions snippets such as:

```text
rnfes
```

Depending on the React Native snippet extension you're using, this can generate a React Native functional component structure.

For example, something like:

```tsx
import { StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <View>
      <Text>Hello</Text>
    </View>
  );
}

const styles = StyleSheet.create({});
```

### Important

`rnfes` is only a **shortcut**.

You should understand the code it generates instead of relying on the shortcut.

---

# ⭐ Commands to Remember

### Create an Expo project

```bash
npx create-expo-app@latest --template blank ./
```

### Start the project

```bash
npx expo start
```

### Install Expo Router packages

```bash
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

---

# ⭐ Important Concepts

Make sure you understand these words:

| Term           | Meaning                                                   |
| -------------- | --------------------------------------------------------- |
| React Native   | Framework for building native mobile apps with React      |
| Expo           | Tools/platform that simplifies React Native development   |
| Node.js        | Runtime/tooling environment used by our development tools |
| npm            | Node Package Manager                                      |
| npx            | Executes package commands                                 |
| VS Code        | Code editor                                               |
| Expo Router    | File-based navigation                                     |
| `package.json` | Project/package configuration                             |
| `app.json`     | Expo app configuration                                    |
| `main`         | Application entry point                                   |
| `scheme`       | Used for deep linking                                     |
| typed routes   | TypeScript support for routes                             |

---

# 🧠 The Main Picture

You should be able to explain the setup like this:

```text
Install Node.js
      ↓
Get npm / npx
      ↓
Create Expo project
      ↓
npx create-expo-app
      ↓
Open project in VS Code
      ↓
npx expo start
      ↓
Expo development server
      ↓
Run React Native application
```

And if using Expo Router:

```text
Expo project
      ↓
Expo Router
      ↓
package.json
      ↓
"main": "expo-router/entry"
      ↓
app.json
      ↓
scheme + typed routes
      ↓
File-based navigation
```

---

# 🎯 What You Should Know Before Video #2

You **do not need to memorize** every configuration line from this video.

You should be comfortable explaining:

**1. What is React Native?**

A framework for building mobile apps using React.

**2. What is Expo?**

A toolset/platform that makes React Native development easier.

**3. Why Node.js?**

Our development tooling relies on the Node.js ecosystem.

**4. What does this do?**

```bash
npx create-expo-app@latest
```

Creates an Expo React Native project.

**5. What does this do?**

```bash
npx expo start
```

Starts the Expo development server.

**6. What is Expo Router?**

File-based navigation for Expo applications.

**7. What is `package.json`?**

A central project/package configuration file.

**8. What is `main`?**

It specifies the application's entry point.

---

## 🔥 One thing I want you to remember

Don't try to memorize commands like:

```bash
npx expo install ...
```

by heart.

At this stage, focus on understanding the **relationship**:

```text
Node.js
  ↓
npm / npx
  ↓
Expo
  ↓
React Native
  ↓
Components
  ↓
Application
```

We'll learn the actual React Native code starting with **Video #2: `Text`, `View` & `Image` Components**.
