---
layout: post
title:  "Firebase + Create React App"
date:   2018-08-01 00:00:00
categories: [firebase]
icon: 🔥
---

![Firebase + Create React App Logo]({{ site.baseurl }}/images/logo-firebase-cra.svg){:class="inline-img"}

Earlier this year, my team and I built and deployed our first production Firebase app. It was a lot of fun and we learned a ton about the platform. For our project, we needed to build a web-based chat client that would allow users to interact with market research bots in realtime.

Firebase was the perfect fit. We used the Realtime Database to synchronize and push data to connected clients, Cloud Functions to process messages on the backend, and Firebase Hosting to serve the app.

On the client-side, we chose [Create React App](https://github.com/facebook/create-react-app) for its simplicity and preconfigured tooling. Combining Firebase with Create React App is pretty straightforward but it can be tricky to get local development working correctly. Here’s how we did it.


## Dev servers

Assuming you plan to use Firebase Functions and Firebase Hosting, you’ll want to run the Create React App development server in parallel with the Firebase emulator. This allows you to emulate your HTTP Firebase Functions and Firebase Hosting environment while you develop your React app.

Here’s how to run the two servers in parallel:

First, install `npm-run-all` as a dev dependency:
```sh
yarn add --dev npm-run-all
```

Now, add these scripts to your `package.json` file:
```js
{
  "scripts:": {
    "dev": "run-p --race dev:firebase dev:react",
    "dev:firebase": "firebase serve -p 4000",
    "dev:react": "react-scripts start"
  }
}
```

Now you can fire up both servers at the same time by running `yarn dev`. Your React app can be viewed at [http://localhost:3000](http://localhost:3000) while your Firebase project is emulated on port 4000.

You might be wondering why this is necessary, but stay with me. In the next section it should start to make sense.

## Implicit initialization

When deploying your app to Firebase Hosting, I highly recommend using [implicit initialization](https://firebase.google.com/docs/web/setup#sdk_imports_and_implicit_initialization). Doing so eliminates the need to manually configure the SDK for each environment (staging, production, etc.)

So instead of doing this for each environment:
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
Just load the auto-configured scripts from the hosting environment:
```xml
<script src="/__/firebase/5.3.0/firebase.js"></script>
<script src="/__/firebase/init.js"></script>
```

Once deployed to Firebase Hosting, your app will be automagically configured for the current project environment.

You might be wondering about that `/__` directory. Since this path only exists in the Firebase Hosting environment, you’ll need to tell the React development server how to handle these requests. Just add this entry to your `package.json` file:

```js
{
  "proxy": {
    "/__": {
      "target": "http://localhost:4000"
    }
  }
}
```

The [proxy](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development) field tells the React development server to proxy any unknown requests to the target URL. With this entry in place, the React development server will proxy any request that contains `/__`to the Firebase Hosting emulator running on port 4000.


## Calling Firebase Functions from your app

At some point you might want to call an HTTP Firebase Function from your React app. Since the function URL is different for each environment, you might be tempted to store the URLs in a config file, but Firebase Hosting provides a more elegant solution.

In your `firebase.json` file, you can configure a rewrite directive that points to the HTTP Firebase function of your choice:

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

Now your app can make a request to `/myFunction` and the Firebase Hosting environment will map the request to the correct Firebase Function.

```js
const myData = await fetch('/myFunction')
```

But wait, for the rewrite to work locally, you’ll need to add another entry to the `proxy` field in your `package.json` file:

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

Now the React development server will know what to do with that request. This approach works fine but if you plan to add more functions in the future, a more scalable solution might look something like this:

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

Now any request that starts with `/functions` will be proxied to the Firebase Hosting emulator.

## Deploying

Coming soon


