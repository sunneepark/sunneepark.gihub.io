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

#### Xcode
1. Mac App Store 에서 xcode 설치
2. XCode > Preferences... > Components
3. 

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
3. android studio > more actions > sdk manager



## 1. React Native 설치

react native 설치

<br></br>
### 에러
___

error Your Ruby version is 2.6.3, but your Gemfile specified 2.7.6

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

