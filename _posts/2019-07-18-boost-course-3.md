---
layout: post
title:  "[boostcourse] 웹 앱 개발:예약서비스 1/4 - 1"
date:   2019-07-18 19:31:11 +0900
background: '/img/posts/01.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

## JavaScript - FE
---
### 자바스크립트 배열
`Javasciprt`의 배열에는 함수와 객체도 들어갈 수 있다. 또한, `length` 속성이 있어서 길이를 쉽게 알 수 있다.

**유용한 메서드**
- `slice(begin, end)`는 배열의 `begin`부터 `end`까지(미포함)의 **Shallow Copy**하여 새로운 배열을 반환한다.

배열의 순회는 `forEach()` 메서드를 사용하는 것이 편리하다.

**중요한 메서드**
- `map()` 메서드는 배열의 각 요소에 함수를 호출한 결과를 모아 **새로운 배열**을 반환한다.
- `filter()` 메서드는 함수의 테스트를 통과하는 요소를 모아 새로운 배열로 반환한다.
- `reduce()` 메서드는 배열의 각 요소에 **reducer** 함수를 실행하여 **하나의 결과값**을 반환한다.
- `some()` 배열 안의 어떤 요소라도 주어진 판별 함수를 통과하는지 테스트한다.
    - 빈 배열에서 호출하면 무조건 `false`를 반환
- `every()` 메서드는 배열 안의 모든 요소가 주어진 판별 함수를 통과하는지 테스트한다.
    - 빈 배열은 무조건 `true`를 반환

**ES6+**
- Spread Operation
    - `fn(...iterableObj)` 형태로 사용
    - `apply()` 메서드를 대체할 수 있다.
    - `concat()` 메서드를 대체할 수 있다.

### 자바스크립트 객체
순서가 없고 `key-value`의 구조를 지닌 자료구조이다.
> 내부적으로 어떤 순서를 가지고는 있다. 그러나 `index`처럼 사용하면 안 된다.

객체에 프로퍼티를 추가하는 방법은 두 가지가 있다.
- `object["key"] = value`
- `object.key = value`

삭제는 다음과 같다.
- `delete object.key`
- `delete object["key"]`

객체의 탐색은 `Object.keys()` 혹은 `for-in`, `forEach`를 사용한다.
~~~javascript
for (key in obj) {
    console.log(obj[key]);
}

Object.keys(obj).forEach(function(key) {
    console.log(obj[key]);
});
~~~

[데이터](https://gist.github.com/nigayo/ade2c3f74417fc202c8097214c965f27)에서 숫자인 프로퍼티의 객체 키 배열을 리턴
~~~javascript
let search = (data, found = []) => {
  Object.keys(data).forEach((key) => {
    if(typeof data[key] === 'number'){
      found.push(key);
      return found;
    }
    if(typeof data[key] === 'object'){
      search(data[key], found);
    }
  });
  return found;
};

console.log(search(data));
~~~

[데이터](https://gist.github.com/nigayo/a9a118977f82780441db664d6785efe3)에서 type이 sk인 객체의 `name`을 배열로 리턴
~~~javascript
let search = (datas, found = []) => {
    datas.forEach((data) => {
        if (data.type && data.type === 'sk') {
            found.push(data.name);
            return found;
        }
        if (data.childnode) {
            filter(data.childnode, found);
        }
    });
    return found;
}

console.log(search(data));
~~~

## DOM API활용 - FE
---
### DOM Node조작하기
`DOM API`는 라이브러리를 사용하는 것보다 성능 상 이점이 있다.
> 물론 크게 차이가 나진 않는다.

유용한 DOM Element Property
- tagName : 엘리먼트의 태그명 반환
- textContent : 노드의 텍스트 내용을 설정하거나 반환
- nodeType : 노드의 노드 유형을 숫자로 반환

`firstChild`는 공백과 TextNode까지 노드로 인정하고, `fistElementChild`는 아니다.

`HTML`을 문자열로 처리해주는 DOM 속성 / 메소드
- innerText
    - 지정된 노드와 모든 자손의 텍스트 내용을 설정하거나 반환
- innerHTML
    - 지정된 엘리먼트의 내부 html을 설정하거나 반환
- insertAdjacentHTML()
    - HTML로 텍스트를 지정된 위치에 삽입

### DOM APIs 실습
`Array.from()`은 노드리스트를 배열로 바꿔준다.

`closest()`는 부모 노드 방향으로 조건에 만족하는 노드를 반환한다.

`Array-like object`는 배열처럼 보이지만 배열이 아닌 객체이다. 조건은 다음과 같다.
- 배열처럼 인덱스로 접근이 가능하고, `length` 속성이 존재한다.
- 배열의 메서드인 `push`, `forEach`, `indexOf` 메서드가 존재하지 않는다.

대표적인 `Array-like object`로는 **arguments**가 있다.

실제로 배열이 아니기에 배열의 메서드를 사용하기 위해서는
`Array.prototype.slice.call(arguments)`, `Array.from(argument)`를 사용하여 배열로 바꿔 사용한다.

## Ajax - FE
---
### Ajax 응답 처리와 비동기
`JSON.parse(resonseText)`를 사용하면 `JSON` 데이터를 객체로 바꿀 수 있다.

기본저긍로 `XHR` 통신은 다른 도메인 간의 요청을 거부한다.
이러한 **cross-domain issue**를 해결하기 위한 방법 중 하나가 `JSONP`를 사용하는 방식이다.

`JSONP`는 HTTP 문서의 `script` 태그로 다른 도메인을 요청할 시에 **Same-Origin Policy**가
적용되지 않는 방식을 이용하여 동작한다. `script` 태그의 `src` 속성에 `?callback=FUNCTION_NAME`을 붙여 요청한다.
> 비표준이고, 현재 거의 사용하지 않는다고 한다.

## Web Animation - FE
---
### 웹 애니메이션 이해와 setTimeout 활용
단순한 방식으로 동작하는 방식은 CSS3의 `transition`과 `transform` 속성을 사용하여 구현할 수 있다.

`FPS(frame per second)`는 1초당 화면에 표현할 수 있는 정지화면이다.
> 매끄러운 애니메이션은 보통 **60fps**이다.
 
### requestAnimationFrame 활용
`setTimeout`도 역시 최적화된 애니메이션 구현 방법이라고 보긴 어렵다.
그래서 등장한 것이 `requestAnimationFrame`이다.

사용 예시는 다음과 같다.
~~~javascript
var count = 0;
var el=document.querySelector(".outside");
el.style.left = "0px";

function run() {
   if(count > 70) return;
   count = count + 1;
   el.style.left =  parseInt(el.style.left) + count + "px";
   requestAnimationFrame(run);
}

requestAnimationFrame(run);
~~~

### CSS3 transition 활용
CSS3의 `transition`과 `transform`을 이용하면 좋은 성능의 애니메이션 구현을 할 수 있다.
다음과 같이 단축 CSS 문법을 사용할 수 있다.
~~~css
div {
    transition: <property> <duration> <timing-function> <delay>;
}
~~~

`hover` 시에 요소가 좌측 상단에서, 우측 하단으로 움직이는 애니메이션 구현
~~~javascript
var el = document.querySelector(".sample");

el.addEventListener("mouseover", function (e) {
    this.style.left = 70 + "vw";
    this.style.top = 70 + "vh";
});
~~~

Viewport-percentage lengths
- `vw`: 1/100th viewport width
- `vh`: 1/100th viewport height
- `vmin`: 1/100th of the smallest side
- `vmax`: 1/100th of the largest side

**vendor prefix**는 브라우저 별로 상이한 CSS3 속성을 잡아주기 위해 사용하는 접두어이다.
- 사파리와 크롬, 오페라의 경우 `-webkit-`
- 파이어폭스의 경우 `-moz-`
- 인터넷 익스플로러의 경우 `-ms-`

## WEB UI - FE
---
### 서비스 개발을 위한 디렉토리 구성
`css`는 `head` 태그 상단에, `script` 태그는 `body` 태그 최하단에 위치시킨다.

브라우저의 렌더링 순서를 고려할 때, 성능을 최적화하기 위한 목적도 있다.
> 리소스 로드 - DOM Tree 생성(parsing) - 렌더링 트리 생성 - CSS 결정 - 레이아웃 - 페인팅

### DOMContentLoaded 이벤트
- `DOMContentLoaded` 이벤트는 **HTML Parsing**이 완료되기까지의 시간
- `Load` 이벤트는 브라우저에 렌더링이 완료되기까지의 시간

보통 초기작업이 필요한 경우 다음과 같이 코드를 작성한다.
~~~javascript
function init() {
    ...
}

document.addEventListener("DOMContentLoaded", function() {
    init();
});
~~~

### Event delegation
여러 개의 엘리먼트에 비슷한 이벤트 추가하여 처리하려면 각 엘리먼트에 이벤트를 추가하기 보다는 `Event Bubbling`을 이용하는 것이 좋다.
> 애플리케이션의 규모가 커지면 `Event Queue`에 `Callback Function`이 증가하고 메모리 활용면에서 비효율적이기 때문

`Event Bubbling`이란, 특정 엘리먼트에서 이벤트가 발생했을 경우 이를 감싸는 상위 엘리먼트로 올라가며 이벤트리스너가 있는지 찾는 것이다.
> `Event Capturing`은 반대로 동작하는 것이고, 실무에서는 거의 사용하는 경우가 없다고 한다.

`target` 속성은 실제로 이벤트가 발생된 엘리먼트를 알려준다.
> 하위 엘리먼트도 포함하기에 매우 유용한 속성이라고 볼 수 있다.

### HTML templating
반복적인 `HTML`을 `template`로 만들어두고, 서버에서 온 데이터를 결합하여 화면에 추가하는 작업이다.
> 서버와 클라이언트 모두에서 작업 가능하다.

`HTML Template`는 일종의 포멧이라고 볼 수 있다. 다음은 사용 예이다.
~~~javascript
const template = "<h1>{title}</h1>"

const templateParser = (datas) =>
    datas.reduce((acc, cur) => acc + template.replace("{title}", cur.title), "");

let resultHTML = templateParser(datas);
~~~

다음은 `template literal`을 이용한 코드이다.
~~~javascript
let resultHTML = "";
datas.forEach((data) => {
    resultHTML += `
        <h1>${data.title}</h1>
    `;
});

document.querySelector("body").innerHTML = resultHTML;
~~~

## Tab UI 실습 - FE
---
### Tab UI를 만들기 위한 HTML과 CSS 구조전략
`Tab UI` 같은 것들을 `component`라고 한다.

[실습 코드](https://jsfiddle.net/fiddles_mkki/d4eanbuL/8/)

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [MDN web docs](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Loops_and_iteration#for_%EB%AC%B8)
- velog, [JSONP란?](https://velog.io/@yesdoing/JSONP%EB%9E%80-jujowt4jy7)
- WDW, [Viewport units: vw, vh, vmin, vmax](https://web-design-weekly.com/2014/11/18/viewport-units-vw-vh-vmin-vmax/)
- Google Developers, [렌더링 트리 생성, 레이아웃 및 페인트](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=ko)