# Using IPFS with React Native
A boilertemplate for using IPFS with React Native

### Test Environment
```
node: v10.4.1
yarn: 1.12.3
react: 16.6.3
react-native: 0.57.8
IPFS HTTP Client: 29.0.0

Operating System
iOS Simuator: iPhone X iOS 12.1
```


## Setup
1. `git clone git@github.com:anakornk/ipfsWithReactNative.git`
2. `yarn install`
3. 
```
ipfs daemon
echo "test" > testFile
ipfs add testFile
```
4. `react-native run-ios` or `react-native run-android`

## Step By Step Guide
1. Create a new React Native Project
```
react-native init ipfsWithReactNative
```
2. Install Node core modules for React Native
```
yarn add node-libs-react-native
yarn add vm-browserify
```
3. In the project root directory, create `metro.config.js`
```
const nodeLibs = require('node-libs-react-native');
nodeLibs.vm  = require.resolve('vm-browserify');

module.exports = {
  resolver: {
    extraNodeModules: nodeLibs
  },
};
```
4. In the project root directory, create `globals.js`  
```
import url from "url";
global.URL = class URL {
  constructor(inputUrl) {
    return url.parse(inputUrl);
  }
};

if (typeof btoa === 'undefined') {
  global.btoa = function (str) {
    return new Buffer(str, 'binary').toString('base64');
  };
}

if (typeof atob === 'undefined') {
  global.atob = function (b64Encoded) {
    return new Buffer(b64Encoded, 'base64').toString('binary');
  };
}

if (typeof global.self === "undefined") global.self = global;
```
5. At the top of `index.js`, import these modules
```
import 'node-libs-react-native/globals';
import './globals';
```
6. Install and Link `react-native-randombytes`
```
yarn add react-native-randombytes
react-native link
```
7. Install IPFS HTTP Client
```
yarn add ipfs-http-client
```
8. Test IPFS

In App.js, import ipfs client.  
```
ipfsClient
```
Add the following two methods inside your App React component. 
```
  constructor(props) {
    super(props);
    this.ipfs = ipfsClient('localhost', '5001', { protocol: 'http' }) 
    this.state = {
      fileInText: 'empty'
    }
  }
  
  componentDidMount() {
    var that = this;
    this.ipfs.cat('/ipfs/QmeomffUNfmQy76CQGy9NdmqEnnHU9soCexBnGU3ezPHVH', function (err, file) {
      if (err) {
        throw err
      }
      let text = file.toString('utf8')
      that.setState({
        fileInText: text
      })
      console.log(text)
    })
  }
```
Add the following code in your render method to display the text content the file stored at '/ipfs/QmeomffUNfmQy76CQGy9NdmqEnnHU9soCexBnGU3ezPHVH'
```
<Text>{this.state.fileInText}</Text>
```

9. Run
```
ipfs daemon
echo "test" > testFile
ipfs add testFile
react-native run-ios
```
The content of the testFile should be displayed.

## References
1. https://gist.github.com/dougbacelar/29e60920d8fa1982535247563eb63766
2. https://github.com/parshap/node-libs-react-native/issues/6
3. https://github.com/facebook/react-native/issues/16434

## Extra
1. The above code uses websocket provider, if you want to use http provider, you will need to edit a node_modules file since there's currently a problem with XMLHttpRequest. For more information, please see the link below:  
https://github.com/souldreamer/xhr2-cookies/issues/7
2. `vm-browserify` is not a RN-compatible polyfill for vm, but works for now. For more information, please see the link below:
https://github.com/parshap/node-libs-react-native/issues/6