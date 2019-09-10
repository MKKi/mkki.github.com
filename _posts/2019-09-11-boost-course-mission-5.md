---
layout: post
title:  "[boostcourse] 예약 시스템: 상세페이지 - 2"
date:   2019-09-11 01:34:11 +0900
background: '/img/posts/02.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

---
이번 포스팅은 다섯 번째 미션인 예약 기능 구현의 **프론트엔드** 관련 내용이다.

지난 미션과 마찬가지로 [기획서](https://docs.google.com/presentation/d/1i2IC1yIH5ACFCvCH4EMVv_3Zw2oltRvHK94amyNEKbs/edit#slide=id.p11)의
15-19페이지에 해당하는 내용을 구현해야 했고, 세부 루브릭은 다음과 같다.

### Object-Oriented Javascript
[[boostcourse] 웹 앱 개발: 예약서비스 3/4 - 1](https://mkki.github.io/boostcourse/2019/08/30/boost-course-6.html)에서
학습했던 내용을 바탕으로 기존의 객체리터럴 패턴으로 작성된 코드를 `ES5` 프로토타입 기반 객체지향 코드로 리팩토링했다.

`ES6`의 `class` 키워드를 사용하면 더 편하게 구현할 수 있었으나,
프로토타입 기반으로 프로젝트를 진행해본 경험이 없었기에 시간이 더 들더라도 진행하기로 했다.

복잡한 로직일 수록 `DOM` 조작이 많아져서 프로퍼티가 많이 늘어났는데, 전역 변수를 사용하는 느낌이 들어 보기 좋지 않았다.
그래도 객체리터럴 패턴으로 작성했을 때 보다는 코드가 많이 깔끔해진 것 같아 뿌듯했다.

![props](https://user-images.githubusercontent.com/28993371/64627131-0dfe5f00-d42a-11e9-9955-e7691dcfe2c1.PNG)
> 동적으로 추가되는 프로퍼티들을 볼 수 있다. 역할과 기능에 따라 더 쪼개는 코딩을 해야겠다.

### Promise
비동기 요청을 보내기 위해 `Fetch API`나 `Axios` 같은 라이브러리를 사용할 수도 있었지만,
기존에 사용하던 `XMLHttpRequest`에 `promise`를 입혀 구현해보기로 했다.

```javascript
function Ajax() {}

Ajax.prototype.promiseAjax = function(method, url, payload) {
return new Promise((resolve, reject) => {
  const xhr = new XMLHttpRequest();
  xhr.open(method, url);
  xhr.setRequestHeader("Content-type", "application/json");
  xhr.send(JSON.stringify(payload));

  xhr.onreadystatechange = function() {
    if (xhr.readyState !== XMLHttpRequest.DONE) return;

    if (xhr.status >= 200 && xhr.status < 400) {
      resolve(xhr.response);
    } else {
      reject(new Error(xhr.status));
    }
  };
});
};
```

루브릭에 에러 핸들링과 분기 처리에 대한 내용이 없어 `async/await`는 사용하지 않았다.

과거 토이 프로젝트에서 `Axios`를 사용했던 경험이 있는데, 라이브러리와 새로운 문법을 적용하면 훨씬 코드가 간결해지다는 것을 다시금 느꼈다.

### Obfuscate & Minify
난독화 라이브러리로 **uglify-js**가 가장 많이 사용된다고 하는데,
이번 과정에는 별도의 빌드 툴이나 패키지 매니저를 사용하지 않았다.

대안으로 [javascript-obfuscator](https://obfuscator.io/)라는 오픈소스 라이브러리로
Validation 코드만 변환하여 제출하였다. **보안**과 관련된 루브릭이 적었던 덕분에 리뷰어님께 좋은 평가를 받았다.

![review-result](https://user-images.githubusercontent.com/28993371/64628542-8a923d00-d42c-11e9-87d2-2d5aaf1b288d.PNG)
> 네, 자랑 맞습니다.

### Encoding
![review-result-2](https://user-images.githubusercontent.com/28993371/64628989-58cda600-d42d-11e9-8acd-4d82d4bd6686.PNG)

리뷰어님께 코드 내 한글 사용을 지양하라는 조언을 받았다.
코드를 수정하긴 했는데,다음 코드처럼 `enum`을 구현해 적용하면 가독성이 좋아질 것 같다.

```javascript
const priceType = Object.freeze({
  adult: "%uC131%uC778"
});
```

찾아보니 여러 인코딩/디코딩 내장 메서드가 존재했다.
- `escape`, `unescape`는 단순히 한글만을 위해 사용하면 좋을 것이다.
- `encodeURI`, `decodeURI`는 일부 특수문자를 제외한 문자를 다루기에, URL의 **파라미터**에 사용한다. 
- `encodeURIComponent`, `decodeURIComponent`는 일부 특수문자를 허용하기에, **URL**을 파라미터로 전송할 때 사용한다.

### Secure Coding
![review-result-3](https://user-images.githubusercontent.com/28993371/64628990-59663c80-d42d-11e9-872b-e0393c2195d3.PNG)

세 번째 미션에서 리뷰어님이 던져주신 프론트엔드 보안 기법인 난독화를 적용해보았으나,
**Brute Force Attack**에 취약점이 존재한다고 조언해주셨다.

**무차별 대입 공격(brute force attack)**은 특정한 암호를 풀기 위해 가능한 모든 값을 대입하는 것을 의미한다.

**사전 공격(Dictionary attack)**은 사전에 있는 단어를 입력하여 암호를 알아내거나 해독하는 컴퓨터 공격법이다. 암호를 알아내기 위한 공격은 사전의 단어를 순차적으로 입력하는 것이다.
> **Brute Force Attack**의 일종이라고 한다.

API 설계 상 어쩔 수 없는 부분이라지만, **Brute Force Attack**의 대응 방안으로 다음을 생각해봤다.
- 파라미터를 노출시키지 않는다.
- 인증 로직을 추가한다.
- 로깅과 모니터링을 통해 **Blocking**한다.
- 요청 실패 시 횟수를 제한한다.
- `Capcha`를 도입한다.

위와 같은 부분들은 **로그인, 회원가입**을 구현할 때 더욱 신경써야할 부분인 것 같다.

### Web Accessibility
![review-result-4](https://user-images.githubusercontent.com/28993371/64628988-58cda600-d42d-11e9-8d21-09959213ba78.PNG)

**Modal Window**에서 웹 접근성 관련해서 조언을 받았다.

이번 과정에서 `HTML Layout`과 `CSS Styling`이 기본적으로 제공되었고, 이를 최대한 활용하라는 내용이 있었다.
또한, 웹 접근성에 대해 무지했기에 신경쓰지 못했던 부분인 것 같다.

곧 시작할 기업 프로젝트를 위해 [웹 UI 개발](https://www.edwith.org/boostcourse-ui) 강좌를 수강하고 있는데,
웹 접근성에 대해 학습하고 수정해 봐야겠다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [Global Objects](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects)
- Wikipedia, [Dictionary Attack](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%A0%84_%EA%B3%B5%EA%B2%A9)
- Wikipedia, [Brute Force Attack](https://ko.wikipedia.org/wiki/%EB%AC%B4%EC%B0%A8%EB%B3%84_%EB%8C%80%EC%9E%85_%EA%B3%B5%EA%B2%A9)