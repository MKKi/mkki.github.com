---
layout: post
title:  "[boostcourse] 예약 시스템: 상세페이지 - 2"
date:   2019-08-27 03:22:11 +0900
background: '/img/posts/01.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

---
이번 포스팅은 세 번째 미션인 상세페이지 구현의 **프론트엔드** 관련 내용이다.

지난 미션과 마찬가지로 [기획서](https://docs.google.com/presentation/d/1i2IC1yIH5ACFCvCH4EMVv_3Zw2oltRvHK94amyNEKbs/edit#slide=id.p11)의
9-14페이지에 해당하는 내용을 구현해야 했고, 세부 루브릭은 다음과 같다.

![mission-3-1](https://user-images.githubusercontent.com/28993371/63650415-a2f42d80-c785-11e9-8996-5d899747ec9a.JPG)
![mission-3-2](https://user-images.githubusercontent.com/28993371/63650417-a2f42d80-c785-11e9-87c7-df2aeeb913bc.JPG)

### Context Path Issue
첫 제출 당시, 로컬에서 정상적으로 동작하던 애플리케이션이 리뷰 당시 동작하지 않아 **Fail** 되었다.

발생한 에러는 자바스크립트 개발 중 가장 많이 겪는다는 **Uncaught TypeError: Cannot read property**였다.

데이터를 받아 조작하는 로직에서 발생한 에러여서, 처음에는 구현한 로직이 잘못되었다고 생각했는데 아니었다.
원인은 `URL`이 **절대 경로**로 지정되어 생긴 것이었다.

저번 미션부터 IDE를 `eclipse`에서 `IntelliJ`로 바꾸어 진행하였는데, 이 둘은 **Default Context Path**가 다르다고 한다.

로컬에서는 **Context Path**가 `/`로 설정되어 정상적으로 동작했던 것이고, 제출한 `eclipse` 프로젝트에서는 `/(PROJECT_NAME)`으로 설정되어 데이터를 정상적으로 받지 못한 것이다.

설정과 무관하게 `URL`을 **상대 경로**로 바꾸어주면 정상적으로 동작하지만,
`<a href="/">`를 사용하기 위해 **Context Path**를 `/`로 설정하여 다시 제출하였다.

### Infinite Slider UI
첫 미션때 완벽하게 구현하지 못했던 `Infinite Slider`를 구현했다.

[링크](https://poiemaweb.com/fastcampus-exercise/carousel-slider-ui)의 도움을 많이 받았다.
> 라기에는 너무나 똑같은 코드

핵심은 첫 번째와 마지막 요소를 복사해두고, 
현재 진행하고 있는 요소의 인덱스가 처음이거나 마지막일 경우 `animation` 없이 이동시키는 것이다.

다음 프로젝트에 `prototype` 기반의 리팩토링이 루브릭에 있어서, 조금 더 구조적으로 코드를 바꿀 수 있을 것 같다.

### Secure Coding
루브릭에는 없는 내용이었지만, `user_email`을 일부만 노출시키도록 프론트엔드 단에서 구현해보았다.

클라이언트에서 하는 보안 처리가 좋지 않은 방법이라는 것을 인지하고 있었기에 프로젝트를 제출하면서 질문사항으로 남겼다.

`코드 경량화(minify)`, `난독화(obfuscation)`를 사용하여 취약점에 대한 예방조치를 할 수는 있으나,
**반드시 서버 사이드에서의 조치**가 필요하다는 답변을 받았다.
> `Max-length Restriction`, `sanitization` 등

더불어 `Ajax`를 엔터프라이즈로 처음 사용한 **구글맵**과,
금융권에서 개발자도구 사용을 막는데 사용되는 **AhnLab Safe Transaction**의 예시를 들어주셨다.

실제로 구글맵에서 개발자 도구를 사용하여 응답 결과를 보려고 하는 경우 **난독화된 코드**를 볼 수 있다.
![google-map-obfuscation](https://user-images.githubusercontent.com/28993371/63682796-b231b480-c833-11e9-9c7a-c87fb11c5bb3.PNG)

이번 에이스 과정에서는 크게 고려되지 않지만, 실 서비스를 운영하는 단계에서는 매우 중요한 이슈라고 생각된다.

### UX
---
리뷰어님께서 `UX`도 고려해보면 좋겠다고 조언해주셨다.

#### Smooth Scroll
이전 미션에서 페이지를 최상단으로 이동시키는 기능을 구현하는 루브릭이 있었다.

`<a href="#top">`으로 간단하게 구현할 수 있는 기능이다.
여기에 `UX` 관점에서 부드러운 스크롤업이 된다면 더 좋을 것 같다는 것에 동감했다.

다음과 같이 구현 가능하지만, `ScrollToOptions` 파라미터는 크로스브라우징 이슈가 존재한다.

````javascript
window.scroll({
  top: 0,
  left: 0,
  behavior: 'smooth'
});
````

재귀적으로 구현한 코드는 다음과 같다.

```javascript
const button = document.querySelector('.top');
const height = window.pageYOffset + button.getBoundingClientRect().top
const smoothScroll = (h) => {
    let i = h || 0;
    if (i > 0) {
        setTimeout(() => {
          window.scrollTo(0, i);
          smoothScroll(i - 20);
        }, 10);
    }
}

button.addEventListener('click', () => smoothScroll(height));
```

#### 데이터 보존
뒤로가기 버튼을 구현하기 위해 `window.history.back()`을 사용했다.

만약, **Infinite Scroll**이 구현된 페이지에서 사용자가 스크롤을 여러 번 내리고 어떤 페이지로 이동했다고 가정했을 때,
`window.history.back()` 메서드 호출만으로 이전 페이지로 이동한다면 기존 스크롤이 유지되지 않을 것이다.

해결책은 어떤것이 있을지 생각해봤다.

##### 1. History API
`SPA`는 단일 페이지라 주소가 바뀌지 않는 단점이 있었고, `#!(HashBang)`을 사용하여 하위 주소를 표현했었다.
그러나 `#!`이 포함된 주소는 의미가 없고, 서버가 제대로 된 주소로 처리하지 않는 문제가 있었다.

그래서 등장한 것이 `History API`이다. 기존의 `window.history` 객체를 활용하기에, 이에 해당하는 메서드를 모두 사용할 수 있다.

먼저 `pushState` 메서드를 사용하여 원하는 데이터를 저장한다.

```javascript
document.addEventListener("DOMContentLoaded", function() {
    sendAjax("/items", function() {
        const data = JSON.parse(this.responseText());
        
        history.pushState({list: data}, "items", "/items");
    });
});
```

브라우저에서 뒤로가기 버튼을 누르거나, `history.back()`과 같은 메서드를 호출하면 `popstate` 이벤트가 발생된다. 

```javascript
document.addEventListener("popstate", function(e) {
    const data = e.state;
    // 필요한 로직 구현
});
```

그러나 프로젝트가 **Multi-Page Application** 구조라 사용하기 힘들다고 판단했다.

##### 2. Cookie
클라이언트에 4kb 이하의 데이터를 저장할 때 사용되는 기술이다.

모든 요청마다 쿠키가 함께 전송되어 성능이 저하되는 원인이 될 수 있고, 취약점도 많다.
> **MDN**에서도 쿠키보다 `Web Storage API`의 사용을 권장하고 있다.

##### 3. Web Storage API
브라우저에 `key-value` 쌍을 저장할 수 있게 해주는 기술로 두 가지 메커니즘이 존재한다.
- `sessionStorage`는 페이지 리로드, 복구를 포함하여 페이지 세션이 유지되는 동안에만 사용할 수 있다.
- `localStorage`도 데이터를 유지하는 것을 제외하고는 똑같다.
> 페이지 세션은 브라우저가 닫히면 종료된다.

쿠키는 매 요청마다 서버로 전송된다고 했는데, 쿠키 데이터 중 서버로 전송될 필요가 없는 데이터를 저장하는 용도로 사용하면 될 것 같다.

결국, 뒤로가기 이슈를 해결하고 `UX` 개선하기 위해서 **Web Storage API**를 사용하기로 결정했다.

기존 코드에서는 더보기 버튼을 누르면 서버 API에서 전달받은 데이터를 템플릿하는 작업만 수행했기에,
추가로 `data` 프로퍼티에 추가하는 작업을 수행하고 `sessionStorage`에 저장하는 기능을 구현하면 되겠다고 생각했다.

다음 미션 루브릭에 객체지향 코드로 리팩토링하는 항목이 있어 구현은 다음으로 미루었다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- Poiema Web, [16. Carousel slider UI](https://poiemaweb.com/fastcampus-exercise/carousel-slider-ui)
- MDN, [Window.scrollTo()](https://developer.mozilla.org/ko/docs/Web/API/Window/scrollTo)
- Stack Overflow, [How to window.scrollTo() with a smooth effect](https://stackoverflow.com/questions/42261524/how-to-window-scrollto-with-a-smooth-effect)
- MDN, [Manipulating the browser history](https://developer.mozilla.org/ko/docs/Web/API/History_API)
- Outsider's Dev Story, [Ajax를 사용할 때 웹브라우저 "뒤로 가기"의 구현](https://blog.outsider.ne.kr/1276)
- MDN, [Using the Web Storage API](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)