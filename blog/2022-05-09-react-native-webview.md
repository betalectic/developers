
---
slug: react-native-webview
title: # React Native WebView
authors: [rohan]
tags: [react-native]
---

## Definition

**React Native WebView** is the replacement for the built-in WebView on react-native. This offers users to render any web components in a React Native application. A web component can be anything from a whole webpage/application or just a simple HTML file. Using webView we can use the web browser feature in the mobile app.

`react-native-webview` makes it very simple to embed WebViews into your React Native apps. official GitHub link `https://github.com/react-native-webview/react-native-webview`

## Installation

To install `react-native-webview` run the following command inside your project directory:

```
npm i react-native-webview
```
or
```
yarn add react-native-webview
```

## Usage

The simplest way to embed a WebView into your React Native application is to provide a URL as a source to your WebView component:
```js
import React, { Component } from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { WebView } from 'react-native-webview';

// ...
class MyWebComponent extends Component {
  render() {
    return <WebView source={{ uri: 'https://www.google.com/' }} />;
  }
}
```
Please note that `source` takes an object as a value. the value of source can be uri, html, or any path using require.

eg.
```js
  <WebView 
  source={{html: '<h1>Hello World</h1>'}}  
  />
```

```js
  <WebView 
  source={require("./resources/index.html")} 
  />
```
## Use Javascript in Webview

In Webview  we can use some javascript functionalities using `injectedJavaScript` and `injectedJavaScriptBeforeContentLoaded`.
code:
```js

class MyWeb extends Component {
  render() {
  const runFirst = `
      setTimeout(function() { 
          window.alert("Click me!");
          document.getElementById("h1_element").innerHTML = 
          "What is your favourite language?";
          document.getElementById("h2_element").innerHTML =
          "We will see!";
        }, 1000);
      true; // note: this is required, or you'll sometimes get silent failures
    `;

  const runBeforeFirst = `
      window.isNativeApp = true;
      true; // note: this is required, or you'll sometimes get silent failures
  `;

    return (
      <SafeAreaView style={{ flex: 1 }}>
        <WebView 
          source={{ html: customHTML }} 
          onMessage={(event) => {}}
          injectedJavaScript={runFirst}
          injectedJavaScriptBeforeContentLoaded={runBeforeFirst}
        />
      </SafeAreaView>
    );
  }
}
```
If you take a short look at the WebView component, you will notice that three new props have been introduced:  `onMessage`,  `injectedJavaScript`, and  `injectedJavaScriptBeforeContentLoaded`.

The JavaScript code that is provided to the  `injectedJavaScript`  prop is only going to be executed once, after the resource loads for the first time. Even if you refresh the site, the code will not be executed again!

Our script, called  `runFirst` Inside this script, we first trigger a message to render and, afterwards, we change the text of our  `h1`  and  `h2`  elements. All of this is wrapped inside a  `setTimeout`  function in order to run this script after  `1s`.

On the other hand, the script provided in the  `injectedJavaScriptBeforeContentLoaded`  prop will be executed before the page is loaded for the first time. The corresponding script,  `runBeforeFirst`, this script has no effect on the visuals of our app.

The  `onMessage`  prop is required, even though the function inside this prop is empty at the moment. Without this prop, the scripts will not run!

## Props of WebView

- source
- injectedJavaScript
- injectedJavaScriptBeforeContentLoaded
- onError
- onLoad
- onLoadEnd
- onLoadStart
- onMessage
- originWhitelist
- renderError
- style
- userAgent
- geolocationEnabled
- scrollEnabled
- contentInset
- bounces
- allowFileAccess
- nativeConfig

we have already discussed the `source`, `injectJavaScript`, `injectedJavaScriptBeforeContentLoadedprops` so let's discuss other important props.

**onMessage**: This can be used for cross communication from the window objects of the web view to the React Native side. In the js code we can just do **window.postMessage(data).** On the RN side we could listen to these messages **using onMessage(data).**

**onLoad, onLoadStart, onLoadEnd:** These methods are self explanatory. The methods get called when the loading is finished, at the start of the load, and when the loading is either succeeded or failed.


## Conclusion

In this blog we have discussed about `react-native-webview`.I hope this post aids you in better understanding the `react-native-webview`, and will give you brief understanding of using `react-native-webview`. If it has been useful to you, please share and spread the word.

