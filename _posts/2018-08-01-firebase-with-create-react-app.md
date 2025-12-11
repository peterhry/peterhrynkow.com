---
layout: post
title:  "Firebase + Create React App"
date:   2018-08-01 00:00:00
categories: [firebase]
icon: 🔥
---

Earlier this year my team built and shipped its first production Firebase app. We needed a web-based chat client where users could interact with market research bots in real time. Firebase was the perfect fit: the Realtime Database kept clients in sync, Cloud Functions processed messages, and Firebase Hosting served the app.

On the client side we chose [Create React App](https://github.com/facebook/create-react-app) for its simple, batteries-included tooling. Combining the two is straightforward, but getting local development right can be tricky. Here’s the recipe that worked for us.

### Dev servers

If you plan to use Firebase Functions and Firebase Hosting, run the Create React App dev server **alongside** the Firebase emulator. That way you can emulate HTTP functions and hosting while your React app runs in dev mode.

Install `npm-run-all` to orchestrate both servers:
```sh
yarn add --dev npm-run-all
```

Add these scripts to `package.json`:
```js
{
  "scripts": {
    "dev": "run-p --race dev:firebase dev:react",
    "dev:firebase": "firebase serve -p 4000",
    "dev:react": "react-scripts start"
  }
}
```

Run `yarn dev` to start both. React stays on [http://localhost:3000](http://localhost:3000) while the Firebase project is emulated on port 4000. The `--race` flag ensures either process exiting will stop the other so you don’t end up with orphaned servers.

### Implicit initialization

When deploying to Firebase Hosting, use [implicit initialization](https://firebase.google.com/docs/web/setup#sdk_imports_and_implicit_initialization). It removes the need to juggle environment-specific configs.

Instead of manually configuring each environment:
```xml
<script src="https://www.gstatic.com/firebasejs/5.3.0/firebase.js"></script>
<script>
  firebase.initializeApp({
    apiKey: "<API_KEY>",
    authDomain: "<PROJECT_ID>.firebaseapp.com",
    databaseURL: "https://<DATABASE_NAME>.firebaseio.com",
    projectId: "<PROJECT_ID>",
    storageBucket: "<BUCKET>.appspot.com",
    messagingSenderId: "<SENDER_ID>",
  })
</script>
```

Just load the auto-configured scripts from Firebase Hosting:
```xml
<script src="/__/firebase/5.3.0/firebase.js"></script>
<script src="/__/firebase/init.js"></script>
```

Once deployed, the app is automatically configured for its host project.

You might be wondering about that `/__` directory. Because it only exists in the Firebase Hosting environment, tell the React dev server how to proxy those requests locally by adding this to `package.json`:

```js
{
  "proxy": "http://localhost:4000"
}
```

The [proxy](https://create-react-app.dev/docs/proxying-api-requests-in-development/) setting sends unknown requests to the target URL. Any request containing `/__` now reaches the Firebase Hosting emulator running on port 4000.

### Calling Firebase Functions from your app

If you call an HTTP Firebase Function from React, you might be tempted to store environment-specific URLs. A cleaner option is to configure a rewrite in `firebase.json` that exposes the function behind a friendly path:

```js
{
  "hosting": {
    "public": "build",
    "ignore": ["firebase.json", "**/.*", "**/node_modules/**"],
    "rewrites": [
      {
        "source": "/myFunction", // Some URL to expose
        "function": "myFunction" // Your HTTP function
      },
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```

Now your app can call `/myFunction` and Firebase Hosting will route it to the correct backend.

```js
const myData = await fetch('/myFunction')
```

To make that rewrite work locally, expand the proxy entry in `package.json`:

```js
  "proxy": {
    "/__": {
      "target": "http://localhost:4000"
    }
    "/myFunction": {
      "target": "http://localhost:4000"
    }
  }
```

This approach works, but if you plan to add more functions, a scalable version might look like this:

```js
  "proxy": {
    "/__": {
      "target": "http://localhost:4000"
    }
    "/functions": {
      "target": "http://localhost:4000"
    }
  }
```

Any request starting with `/functions` now reaches the emulator, keeping local development predictable while matching production routing.
