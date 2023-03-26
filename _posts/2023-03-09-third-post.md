---
title: "React Native Web 사용기 1"
date: 2023-03-08
categories: jekyll update
---


# React Native Web
hybrid

<br></br>
# React Native 설치

## 0. 기본 환경 셋팅
https://reactnative.dev/docs/environment-setup

<br></br>
### for iOS

#### node & watchman

```bash
brew install node
brew install watchman
```

#### Ruby
ruby 버전이 아래 버전이어야 함
https://reactnative.dev/docs/environment-setup

#### Xcode 설치
1. Mac App Store 에서 xcode 설치
2. XCode > Setting > Locations > command Line Tools 드롭다운에서 최신 버전 한번 클릭 -> 설치


### cocoapod
sudo gem install cocoapods

<br></br>
### for Android

#### node & watchman 설치
```bash
brew install node
brew install watchman
```


#### java development kit
```bash
brew tap homebrew/cask-versions
brew install --cask zulu11
```

#### Android Studio 실행
1. install android studio (https://developer.android.com/studio/index.html)
2. .dmg 파일 실행 아래 item 설치
   1. 사진
3. android studio > more actions > sdk manager > 최신 Android 버전 > Apply
4. configure ANDROID_HOME environment variable (/.zshrc, /.bashrc etc)



<br></br>
## React Native 프로젝트 만들기
<br></br>
```bash
 $ npx react-native init test

               ######                ######
             ###     ####        ####     ###
            ##          ###    ###          ##
            ##             ####             ##
            ##             ####             ##
            ##           ##    ##           ##
            ##         ###      ###         ##
             ##  ########################  ##
          ######    ###            ###    ######
      ###     ##    ##              ##    ##     ###
   ###         ## ###      ####      ### ##         ###
  ##           ####      ########      ####           ##
 ##             ###     ##########     ###             ##
  ##           ####      ########      ####           ##
   ###         ## ###      ####      ### ##         ###
      ###     ##    ##              ##    ##     ###
          ######    ###            ###    ######
             ##  ########################  ##
            ##         ###      ###         ##
            ##           ##    ##           ##
            ##             ####             ##
            ##             ####             ##
            ##          ###    ###          ##
             ###     ####        ####     ###
               ######                ######


                  Welcome to React Native!
                 Learn once, write anywhere

✔ Downloading template
✔ Copying template
✔ Processing template
✔ Installing Bundler
✔ Installing CocoaPods dependencies (this may take a few minutes)


  Run instructions for Android:
    • Have an Android emulator running (quickest way to get started), or a device connected.
    • cd "/Users/user/skateReserve" && npx react-native run-android

  Run instructions for iOS:
    • cd "/Users/user/skateReserve" && npx react-native run-ios
    - or -
    • Open skateReserve/ios/skateReserve.xcworkspace in Xcode or run "xed -b ios"
    • Hit the Run button

  Run instructions for macOS:
    • See https://aka.ms/ReactNativeGuideMacOS for the latest up-to-date instructions.
```

## react-native project 실행하기
### run ios in mac
```bash
• cd "/Users/user/skateReserve" && npx react-native run-ios
- or -
• Open skateReserve/ios/skateReserve.xcworkspace in Xcode or run "xed -b ios"
• Hit the Run button
```

### run android in mac
0. (실제 안드로이드 기기) or (Create Virtual Device... in android studio)
1. npx react-native start
2. npx react-native run-android

### run web in mac
0. cd test


1. npm 패키지 설치
```bash
npm install --save react-dom react-native-web #react-dom 
npm install --save-dev @babel/core babel-loader @babel/preset-react #build를 위한 babel
npm install --save-dev @babel/core babel-loader url-loader webpack webpack-cli webpack-dev-server
npm install --save @babel/preset-react html-webpack-plugin
npm install --save-dev webpack webpack-cli webpack-dev-server html-webpack-plugin #webpack 꾸러미들

```

2. package.json 파일에 아래 내용 추가
```json
"scripts": {
    ...
    "build-react": "webpack --mode production",
    "start-react": "webpack serve --config ./web/webpack.config.js --mode development"
  },
```

3. web 폴더 만들고 webpack.config.js 파일 만들기 (위 package.json 파일에서 ./web/webpack.config.js 로 설정)
```js
const path = require('path');
const webpack = require('webpack');
const HtmlWebpackPlugin = require('html-webpack-plugin');

const appDirectory = path.resolve(__dirname, '../');

const babelLoaderConfiguration = {
  test: /\.js$/,
  use: {
    loader: 'babel-loader',
    options: {
      cacheDirectory: true,
      presets: ['@babel/preset-react'],
      plugins: ['react-native-web']
    }
  }
};

module.exports = {
  entry: [
    path.resolve(appDirectory, 'index.web.js')
  ],
  output: {
    filename: 'bundle.web.js',
    path: path.resolve(appDirectory, 'dist')
  },

  module: {
    rules: [
      babelLoaderConfiguration,
    ]
  },

  plugins:[new HtmlWebpackPlugin({ template: './public/index.html'})],

  resolve: {
    alias: {
      'react-native$': 'react-native-web'
    },
    extensions: [ '.web.js', '.js' ]
  }
}
```

4. public 폴더 만들고 index.html 파일 만들기
```html
   <!DOCTYPE html>
<html>
  <head>
    <title>React Native Web</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

5. 다시 root 디렉토리로 와서 index.web.js 파일 만들기 
```js
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App.web'

ReactDOM.render(<App />, document.getElementById('app'))
```

6. App.web.js 파일 만들기
```js
import React from 'react'

function App() {
  return (
    <>
      <h1> first react-native </h1>
    </>
  )
}

export default App
```

6-1. 아직 react Native 코드로 변환
```js
import React from 'react'
import {View, Text} from 'react-native'

function App() {
  return (
    <View>
	    <Text> first react-native </Text>
    </View>
  )
}

export default App
```

<br></br>
### 에러
___

<br></br>
1. error Your Ruby version is 2.6.3, but your Gemfile specified 2.7.6
 
**원인**<br>
맥은 기본적으로 ruby가 설치되어 있다. 이 버전은 2.6.3이지만 react에 맞지 않는 버전이여서 오류가 발생하였다.

<br>**해결**<br>
ruby 버전 업그레이드
```bash
$ brew update
$ brew install rbenv
$ rbenv install -l
$ rbenv install 2.7.6
$ rbenv global 2.7.6

$ rbenv versions
  system
* 2.7.6 (set by /Users/user/.rbenv/version)
$ ruby --version
ruby 2.6.3p62 (2019-04-16 revision 67580) [universal.x86_64-darwin20]
$ which ruby
/usr/bin/ruby6
```

`rbenv versions` 와 `ruby --version` 버전의 차이가 있다면, 아래 방법으로 해결

```bash
$ rbenv init
# Load rbenv automatically by appending
# the following to ~/.zshrc:

eval "$(rbenv init - zsh)"
```

the following to ~ 에서 ~에 적힌 파일에 `eval ~` 부분 추가 후 재실행

```bash
$ rbenv versions
  system
* 2.7.6 (set by /Users/user/.rbenv/version)
$ ruby -version
ruby 2.7.6p219 (2022-04-12 revision c9c2245c0a) [x86_64-darwin20]

$ which ruby
/Users/user/.rbenv/shims/ruby
```

<br></br>
2. 
error warn Multiple Podfiles were found: ios/Podfile,vendor/bundle/ruby/2.7.0/gems/cocoapods-core-1.12.0/lib/cocoapods-core/Podfile. Choosing ios/Podfile automatically. If you would like to select a different one, you can configure it via "project.ios.sourceDir". You can learn more about it here: https://github.com/react-native-community/cli/blob/master/docs/configuration.md

✖ Installing CocoaPods dependencies (this may take a few minutes)
error Error: Looks like your iOS environment is not properly set. Please go to https://reactnative.dev/docs/next/environment-setup and follow the React Native CLI QuickStart guide for macOS and iOS.
Error: Error: Looks like your iOS environment is not properly set. Please go to https://reactnative.dev/docs/next/environment-setup and follow the React Native CLI QuickStart guide for macOS and iOS.
    at createFromTemplate (/usr/local/lib/node_modules/react-native/node_modules/@react-native-community/cli/build/commands/init/init.js:131:11)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async Object.initialize [as func] (/usr/local/lib/node_modules/react-native/node_modules/@react-native-community/cli/build/commands/init/init.js:184:3)
    at async Command.handleAction (/usr/local/lib/node_modules/react-native/node_modules/@react-native-community/cli/build/index.js:106:9)

**원인**
정확히 모름..


**해결** 
1. uninstall cocoapod<br>
   `sudo gem uninstall cocoapods`
2. XCode > Setting > Locations > command Line Tools 밑에있는 경로 확인<br>
3. `sudo xcode-select --switch '2번경로'` <br>
4. `sudo xcodebuild -license accept`

출처 : https://github.com/CocoaPods/CocoaPods/issues/11641#issuecomment-1317210370
