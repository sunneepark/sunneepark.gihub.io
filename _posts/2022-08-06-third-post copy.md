---
title: "반응형 웹사이트 만들기"
date: 2022-08-06
categories: jekyll update
---

# 반응형 웹사이트
: 웹사이트로 같은 정보를 제공하나, desktop, mobile 등의 기기에 따라 다르게 반응하는 웹사이트

- library install


- App.js
```js
import { BrowserView, MobileView } from 'react-device-detect'

function App() {
  return (
    <div className='App'>
      <BrowserView>
        데스크탑 브라우저!
      </BrowserView>
      <MobileView>
        모바일 브라우저!
      </MobileView>
    </div>

  );
}
```

참고) 데스크탑에서 휴대폰 ui 확인하는 법 <br>
검사 -> 개발자 도구 -> toggle device toolbar(핸드폰 모양으로 되어 있는 것)