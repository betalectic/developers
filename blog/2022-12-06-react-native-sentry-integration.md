---
slug: react-native-sentry-integration
title: React Native Sentry Integration
authors: [mahesh]
tags: [React-Native]
---

## Steps to Integrate Sentry into the react-native application

### Sentry Integration in Turbo Repo

1. Go to your `apps` directory which contains all of your apps such as web, mobile, or any other kind of applications you've added to the `apps` directory.
2. As you can see, there is a folder called `apps/mobile` which is a react-native application
3. I switched to the `apps/mobile` folder in where I was trying to install the `@sentry/react-native` SDK into my react native application.
4. For that I follow the below steps: (**macOS - MacBook Air**)

   - 4.1. I opened the terminal in my mac (You can use any other terminals you want or go with the `vscode` terminal)
   - 4.2. Type the `cd apps/mobile` command in the terminal and click on the `enter/return` button
   - 4.3. Type the `yarn add @sentry/react-native` command in the terminal and click on the `enter/return` button
     - 4.3.1. If you are using the `npm` package installer, use the `npm install --save @sentry/react-native` command (but in this repo, it won't work because it is a turbo repo and uses the `yarn`)
     - For more info, check the Sentry official documentation: "https://docs.sentry.io/platforms/react-native/"
   - 4.4. After the installation was successful, I put the below code in `apps/mobile/mobile-screens/index.js`

   ```js
   import {AppRegistry} from 'react-native';
   import App from './mobile-screens';
   import {name as appName} from './app.json';
   import {Provider} from 'react-redux';
   import {mobileStore} from 'store';
   import React from 'react'
   import Config from 'react-native-config';

   // Sentry Code: import & initiate sentry in the App
   import * as Sentry from "@sentry/react-native";
   Sentry.init({
     dsn: Config.SENTRY_DSN, // put SENTRY_DSN env variable in .env, otherwise it fails
   });
   // above App component
   const App = () => {
     ....other code
   }

   // Sentry Code: wrap the App component with a sentry `wrap` HOC function
   export default Sentry.wrap(App);
   ```

   - 4.5. Make changes in `apps/mobile/index.js` with the following code

   ```js
   import { AppRegistry } from "react-native";
   import App from "./mobile-screens";
   import { name as appName } from "./app.json";
   import { Provider } from "react-redux";
   import { mobileStore } from "store";
   import React from "react";
   // Sentry Code: Import sentry
   import * as Sentry from "@sentry/react-native";

   const MobileApp = () => {
     return (
       // Sentry Code: Wrap the App with "Sentry.TouchEventBoundary"
       <Sentry.TouchEventBoundary>
         <Provider store={mobileStore}>
           <App />
         </Provider>
       </Sentry.TouchEventBoundary>
     );
   };

   AppRegistry.registerComponent(appName, () => MobileApp);
   ```

### How to use?

- Refer this documentation to know more: "https://docs.sentry.io/platforms/react-native/usage/"
- To test whether the sentry is working or not in the app, I have added the below code in the `apps/mobile/mobile-screens/index.js->init()` function

```js
const init = async () => {
  try {
    // error will come because of the comment in the below code
    // let tokenFromStorage = await AsyncStorage.getItem('@access_token');

    if (tokenFromStorage !== null) {
      dispatch(setTokens(JSON.parse(tokenFromStorage)));
      let userProfile = await getUser();
      if (userProfile) {
        dispatch(setUser(userProfile));
      }
    }
  } catch (error) {
    // this will execute and will be shown in the Sentry dashboard
    Sentry.captureException(error);
  }
};
```