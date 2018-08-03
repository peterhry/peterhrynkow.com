---
layout: post
title:  "Firebase + Create React App"
date:   2018-08-01 00:00:00
categories: [firebase]
icon: 🔥
---

Earlier this year, my team and I built and deployed our first production Firebase app. It was a lot of fun and we learned a ton about the platform. Firebase is a powerful service that includes almost everything you need to build a robust, scalable, realtime web app.

For our project, we needed to build a web-based chat client that would allow users to interact with market research bots. Firebase was the perfect fit. We used the Realtime Database to synchronize and push data to connected clients, Cloud Functions to process messages on the backend, and Firebase Hosting to serve the app.

On the client-side, we chose [Create React App](https://github.com/facebook/create-react-app) for its simplicity and preconfigured tooling. Combining Firebase with Create React App is pretty straightforward but we discovered a few problems along the way. This post covers some of those problems and how we managed to solve them. Hopefully it will help you with your next Firebase project.


## Local development

Assuming you plan to use Firebase Functions and Firebase Hosting, you’ll want to run the Create React App development server in parallel with the Firebase emulator. This allows you to emulate your HTTP Firebase Functions and Firebase Hosting environment while you develop your React app.

Here’s how to run the two scripts in parallel:

First, install `npm-run-all` as a dev dependency:
```sh
yarn add --dev npm-run-all
```

Now, add these fields to your `package.json` file:
```js
{
  "scripts:": {
    "dev": "run-p --race dev:firebase dev:react",
    "dev:firebase": "firebase serve -p 4000",
    "dev:react": "react-scripts start"
  }
  "proxy": {
    "/__": {
      "target": "http://localhost:4000"
    }
  }
}
```

Now you can fire up both servers by running `yarn dev` and your React app can be viewed at [http://localhost:3000](http://localhost:3000). The `proxy` field tells the React development server to [proxy any unknown requests](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development) to the Firebase Hosting emulator during development. More on that in just a bit.

## Implicit Initialization

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

Once deployed to Firebase Hosting, your app will be automatically configured for the current project environment.

You might be wondering about that `/__` directory. Since this path only exists in the Firebase Hosting environment, we need to tell the React development server how to handle these requests. This is where the `proxy` field comes in. The React development server will now proxy any request that contains `/__` to the Firebase Hosting emulator running on port 4000.

## Deploying

Coming soon

## Calling HTTP Firebase Functions from your app

Coming soon
