---
layout: post
title:  "[Vue.js Quick Start] Vue.js 이벤트 처리"
date:   2018-04-29 22:31:11 +0900
background: '/img/posts/01.jpg'
categories: vue.js
---

## 이벤트 처리
---
### 인라인 이벤트 처리 및 이벤트 핸들러
- 이벤트 처리는 `v-on` 디렉티브를 이용해서 처리할 수 있다.
- 다음 예에서와 같이 실행할 코드를 직접 연결할 수도 있고, 인라인 처리가 힘들면 `methods` 옵션에 메서드를 구현해놓고
참조 시킬 수도 있다.
~~~
...
<button id="examle" v-on:click="value += temp">
<button id="examle2" v-on:click="method1">
...

var vm = new Vue({
    el: "#example2",
    ...
    methods: {
        method1 = function(e){
            ...
        }
    }
})
~~~
> `v-on:click`은 `@click`으로 대체 가능하다.

### 이벤트 객체
- 이벤트를 처리하는 함수는 첫 번째 파라미터로 이벤트 객체를 전달 받는다.
- 이 이벤트 객체는 W3C 표준 [HTML DOM Event 모델](https://www.w3schools.com/jsref/dom_obj_event.asp)을 그대로 따르면서 추가적인 속성을 제공한다.

### 기본 이벤트
- `<a>` 요소처럼 개발자가 이벤트를 연결하지 않았음에도 어떤 기능을 실행하도록 정의되어 있는 이벤트를 `기본 이벤트(Default Event)`라고 한다. 대표적인 예는 다음과 같다.
    - `<a>` 요소를 클릭했을 때 href 특성의 경로로 페이지를 이동시킨다.
    - 브라우저 화면을 마우스 오른쪽 클릭했을 때, `내장 컨텍스트 메뉴(ContextMenu)`가 나타난다.
    - `<form>` 요소 내부의 submit 버튼을 클릭했을 때, <form> 요소의 `action` 특성에 지정된 경로로 `method` 특성에 지정된 방식으로 전송한다.
    - `<input type="text" ... />` 요소에 키보드를 누르면 입력한 문자가 텍스트로 나타난다.

- 이러한 기본 이벤트의 실행 중지하기 위해 이벤트 객체의 `preventDefault()` 메서드를 호출하거나, `이벤트 수식어(Event Modifier)`를 사용할 수 있다.
~~~
    <div id="examle" v-on:contextmenu.prevent="...">
~~~

### 이벤트 전파와 버블링
- HTML 문서의 이벤트 처리는 3단계를 거친다. 
1. `이벤트 포착 단계(CAPTURING_PHASE)` : 문서 내의 요소에서 이벤트가 발생했을 때, HTML 문서 밖에서부터 이벤트를 발생시킨 HTML 요소까지 포착해 들어가는 단계
2. `이벤트 발생(RAISING_PHASE: AT_TARGET)` : 이벤트를 발생시킨 요소에 다다르면 요소의 이벤트에 연결된 함수를 직접 호출시키는 단계
3. `이벤트 버블링(BUBBLING_PHASE)` : 이벤트가 발생한 요소로부터 상위 요소로 거슬러 올라가면서 동일한 이벤트를 호출시키는 단계

- 이벤트 버블링을 막기 위해서는 이벤트 객체의 `stopPropagation()`를 호출해주면 된다. 이 역시 수식어로 대체할 수 있다. 
    - `.stop` : 이벤트 전파를 중단시킨다.
    - `.capture` : CAPTURING_PHASE 단계에서만 이벤트가 발생한다.
    - `.self` : RAISING_PHASE 단계일 때만 이벤트가 발생한다.

### 이벤트 수식어
1. `once` 수식어
    - 한 번만 이벤트를 발생시킬 수 있다.
2. 키보드 수식어
    - `.keyup`를 사용하여 키보드의 키를 누를 때마다 고유의 `키코드(KeyCode)` 값을 가질 때만 이벤트를 발생시킬 수 있다.
    - 키코드 값을 일일이 기억하기 어렵기 때문에, Vue.js에서는 [키보드 수식어의 별칭](https://vuejs.org/v2/guide/events.html#Key-Modifiers)을 제공한다.
3. 마우스 버튼 수식어
    - [마우스 버튼 수식어](https://vuejs.org/v2/guide/events.html#Mouse-Button-Modifiers)도 제공된다.
    
## References
---
1. 원형섭님 저서, Vue.js Quick Start
2. Vue.js doc., [https://kr.vuejs.org/v2/guide](https://kr.vuejs.org/v2/guide)
