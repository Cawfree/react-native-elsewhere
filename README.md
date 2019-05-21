# react-native-elsewhere
Ridiculously simple React Native thread unblocking.

## 🚀 Getting Started
Using [npm](https://www.npmjs.com/package/@cawfree/react-native-elsewhere):
```shell
npm install --save @cawfree/react-native-elsewhere
```
Using [yarn](https://www.npmjs.com/package/@cawfree/react-native-elsewhere):
```shell
yarn add @cawfree/react-native-elsewhere
```
Yeah, no linking. 👍

## ⚙ How does it work?
By delegating stateless JavaScript computation to a [`<WebView />`](https://facebook.github.io/react-native/docs/webview), your app can maintain responsive whilst you crunch through heavy computation in the background.

```javascript
import React from 'react';
import {View} from 'react-native';
import Elsewhere from '@cawfree/react-native-elsewhere';

// XXX: The engine is the worker instance for the data you
//      send to the Elsewhere. You can do whatever stateless
//      operations you want. You can post result objects back
//      to the JavaScript thread by calling postMessage with
//      a result JSON. The data argument is the object that is
//      passed during the JS thread's call to __queueSomeIntensiveTask.
function engine(postMessage, data) {
  // TODO: Do something crazy intense here. Literally whatever you want.
  //       You may find it useful to declare these functions in dedicated
  //       source files, just to emphasise that they operate independently
  //       from the Component.
}

export default class App extends React.Component {
  constructor(nextProps) {
    super(nextProps);
    this.__onPostMessage = this.__onPostMessage.bind(this);
    this.state = {
      postMessage: null,
    };
  }
  // XXX: postMessage is a function by which we can use to pass
  //      message data to the engine.
  __onPostMessage(postMessage) {
    this.setState({
      postMessage,
    });
  }
  // XXX: Note this data is just an example. You can post whatever
  //      JSON object you want; just make sure your worker understands
  //      it and you have sufficient information to understand the 
  //      result which is returned later.
  __queueSomeIntensiveTask(obj = {}) {
    const {
      postMessage,
    } = this.state;
    postMessage({});
  }
  render() {
    return (
      <View
        style={{
          flex: 1,
        }}
      >
        <Elsewhere
          onPostMessage={this.__onPostMessage}
          engine={engine}
          onMessage={(data) => {
            // XXX: Here, data contains any results that were passed
            //      from the engine's call to postMessage({}).
          }}
        />
        {/* your app here */}
      </View>
    );
  }
}
```
Check out the [example](https://github.com/Cawfree/react-native-elsewhere/blob/master/example/App.js) application for a full demonstration.

## ✌️ License
[MIT](https://opensource.org/licenses/MIT)
