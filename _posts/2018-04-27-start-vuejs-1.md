---
layout: post
title:  "[Vue.js Quick Start] Vue.js 기초와 디렉티브"
date:   2018-04-27 09:27:11 +0900
background: '/img/posts/05.jpg'
categories: vue.js
---

## Hello Vue.js!
---
1. `{% raw %}{{}}{% endraw %}` 템플릿 표현식을 사용해 선언적으로 HTML DOM에 데이터를 **렌더링**한다.
콧수염 표현식(Mustache Expression), 보간법(Interpolation)이라고 한다.
이 코드만으로도 모든 작업은 반응형으로 이루어진다.
> 렌더링이란? 브라우저의 렌더링 엔진이 HTML 요소를 화면에 표시하는 작업
 
2. `MVVM` Model-View-ViewModel 구조
- `Model` 데이터를 갖고 있는 객체이다. 도메인 특화 데이터를 다룬다.
- `View` HTML 요소이다. 유저 인터페이스 역할을 한다.
- `ViewModel` View의 실제 논리 및 데이터 흐름을 담당한다. 뷰모델 객체는 `상태(State: 데이터)`와 `연산(Operation: 메서드)`을 가지고 있다.

3. 가상 DOM(Virtual DOM) 이란?
![browser-work-flow](/img/browser-work-flow.png)
> 브라우저의 Workflow 

- **가상 DOM**이란, DOM 차원에서의 더블 버퍼링과 다름 없다. 뷰의 변화가 잦은 SPA(Single Page Application)에서는 DOM 조작이 많이 발생한다.
이는 `Render Tree`, `레이아웃` 재생성과 `페인팅` 과정이 반복되어 브라우저의 잦은 연산을 초래하고 전체적인 프로세스를 비효율적으로 만든다.

- 가상 DOM을 적용하였을 때, 뷰의 변화가 생기면 가상 DOM에 **우선적**으로 적용하고 최종적인 결과를 실제 DOM에 전달해준다.
이로써 브라우저 내에서 발생하는 연산의 양을 줄이고, 성능을 개선할 수 있는 것이다.

- 이는 수동으로도 가능한 부분이지만, 가상 DOM을 사용하면 DOM Fragment 관리를 자동화하고 추상화 할 수 있다.

## 디렉티브
---
### 단방향 디렉티브
- 뷰(HTML 요소)에서 값을 변경하더라도 모델 객체의 값이 변하지 않는다.
> 모델에서 뷰로의 바인딩을 지원한다.
- `v-text`, `{{}}` innerText 속성에 연결된다. 태그 문자열을 HTML 인코딩하여 나타내기에 화면에는 태그 문자열이 그대로 나타난다.
- `v-html` innerHTML 속성에 연결된다. 태그 문자열을 파싱하여 화면에 나타낸다. HTML 인코딩과 디코딩을 수행하지 않기에, XSS 공격에 취약하다.
> v-text 사용이 권장된다.

- `v-bind` 디렉티브는 요소의 컨텐츠 영역을 설정하는 것이 아닌, **HTML 요소 객체들의 속성**들을 바인딩하기 위해 사용된다.
> `v-bind:src`를 `:src`로 줄여서 표현 가능

### 양방향 디렉티브
- `v-model` 양방향 데이터 바인딩이다. **HTML 요소 값**을 다룬다. 텍스트 박스 외에도 여러 가지 입력 폼 필드에서도 사용할 수 있다. 모델에서 뷰, 뷰에서 모델로의 바인딩이 가능하다.
- 여러 개의 아이템을 선택할 수 있는 `checkbox`나 `selectmultibox`의 경우 모델 객체의 배열 객체와 연결
- `radio`나 `select`인 경우는 모델 객체의 단일 값과 바인딩 된다.

### 조건 디렉티브
- `v-if`와 `v-show`는 비슷한 기능을 수행하지만, 렌더링 여부에 차이가 있다.
- `v-if`는 조건에 부합하지 않으면 렌더링하지 않지만, `v-show`는 일단 렌더링한 후 display 스타일 속성으로 화면을 보여줄지 판단한다.
- `v-else-if`, `v-else` 역시 다른 언어에서의 기능과 같다.

### 반복 렌더링 디렉티브
- `v-for`는 다른 언어에서의 for 문과 유사하다. 원본 데이터가 배열, 유사 배열인 경우 다음과 같은 형태이다.
~~~
    <table>
        <tr v-for="content in contents">
            <td>...</td>
        </tr>
    </table>
~~~

- 원본 데이터가 객체인 경우 다음과 같은 형태이다.
~~~
    <table>
        <tr v-for="(val, key) in contents" v-bind:value="key">
            {% raw %}{{val}}{% endraw %}
        </tr>
    </table>
~~~

- 인덱스 번호를 표현해야 한다면
~~~
    <table>
        <tr v-for="(val, key, index) in contents" v-bind:value="key">
            {% raw %}{{index}}{% endraw %}
            {% raw %}{{val}}{% endraw %}
        </tr>
    </table>
~~~

- `v-for` 디렉티브와 `v-if` 디렉티브는 함께 사용할 수 있다. 주의할 점은 `v-for` 디렉티브가 **먼저** 실행된다는 것이다.

- 여러 요소를 한 번에 반복 렌더링 하기 위해서 `<template>`를 사용한다. 렌더링 내용에는 **포함되지 않고**, 단지 요소들을 그룹으로 묶어주기 위한 용도로만 사용된다.

### 기타 디렉티브
- `v-pre`는 HTML 요소에 대한 컴파일을 수행하지 않는다. 다음 예에서 출력 값은 문자열 그대로인 `{{message}}`이다.
~~~
    <span v-pre>{% raw %}{{message}}{% endraw %}</span>
~~~

- `v-once`는 HTML 요소를 단 한 번만 렌더링하도록 설정한다. 그렇기에 Vue 인스턴스의 데이터를 변경해도 다시 렌더링을 수행하지 않는다.
이는 초기값이 주어지면 변경되지 않는 UI를 만들 때 유용하다.

- `v-for` 디렉티브를 이용해 많은 데이터를 출력할 때, Vue 인스턴스가 el 옵션의 템플릿을 컴파일 할 때 발생하는 시간 때문에 **interpolation 문자열**이 그대로 표시될 때가 있다.
`v-cloak`은 화면 초기에 컴파일되지 않은 템플릿은 나타나지 않도록 할 수 있다.

## 계산형 속성
- 연산 로직이 필요한 경우, Vue 객체를 만들 때 `computed`라는 속성과 함께 함수를 등록해 두면 마치 속성처럼 사용할 수 있다.
~~~ javascript
    ...
    <span>{% raw %}{{sum}}{% endraw %}<span>
    ...
    var sample = new Vue({
        el: "#examle",
        data: { num: 0 },
        compute {
            sum: function(){
                var n = Number(this.num);
                if(Number.isNaN(n) || n < 1) return 0;
                return ((n + 1) * n) / 2;
            }
        }
    })
~~~

- 주의해야 될 점은 다음과 같다.
1. 함수 안에서의 `this`는 Vue 객체 자신을 참조한다. 만약 함수 내에서 다른 콜백 함수를 호출하면 this가 다른 값으로 바인딩 될 수 있다.
2. HTML 요소 내부에서는 모두 문자열로 다루어지기에, 숫자 값을 다루기 위해 명시적으로 형 변환을 해주어야 한다.

## References
---
1. Velopert님의 블로그, [https://velopert.com/3236](https://velopert.com/3236)
2. Vue.js Developers, [https://medium.com/js-dojo/whats-new-in-vue-js-2-0-virtual-dom-dc4b5b827f40](https://medium.com/js-dojo/whats-new-in-vue-js-2-0-virtual-dom-dc4b5b827f40)
3. 원형섭님 저서, Vue.js Quick Start

