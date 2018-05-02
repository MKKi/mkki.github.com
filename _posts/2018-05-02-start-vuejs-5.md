---
layout: post
title:  "[Vue.js Quick Start] Vue.js 컴포넌트 기초"
date:   2018-05-02 00:43:11 +0900
background: '/img/posts/03.jpg'
categories: vue.js
---

## 컴포넌트
---
### 컴포넌트 조합
- Vue.js는 컴포넌트를 조합해 전체 애플리케이션을 작성한다. 컴포넌트들은 `부모-자식` 관계로 트리 구조를 형성한다.
> 웹에서의 컴포넌트란 HTML 요소를 확장하여 재사용 가능한 코드를 캡슐화하는데 도움을 주는 UI로 볼 수 있다.

- 컴포넌트들은 `속성(Props)`을 통해서 자식 컴포넌틀 정보를 전달할 수 있다.
주로 부모에서 자식으로의 단방향이다. 양방향은 애플리케이션의 복잡도가 높아지고 유지 보수가 어려워 권장되지 않는다.

- 자식 컴포넌트는 부모 컴포넌트로 이벤트를 발신할 수 있다. 자식 컴포넌트에서 사용자 정의 이벤트를 정의하고 발생시키면,
부모 컴포넌트에서 이벤트 핸들러 메서드를 호출하도록 작성한다.

- Vue 인스턴스 옵션을 컴포넌트 수준에서도 사용할 수 있다. 다만 `data` 옵션은 각 컴포넌트의 **로컬 상태(Local State)**를
관리하기 위한 용도로만 사용한다.
> 하나의 컴포넌트를 애플리케이션에서 여러 번 사용할 경우 모두 다른 상태 정보를 가져야 하기에 `data` 옵션은 함수로 작성하고
내부에서 객체를 리턴하도록 작성되야 한다.

### 컴포넌트의 작성
- 컴포넌트를 작성하는 메서드는 다음과 같다.
~~~
    Vue.component(tagname, options)
~~~
> **options**는 컴포넌트에서 렌더링할 template 등을 지정한다.

- `Vue.config.devtools = true;`는 크롬 확장 프로그램인 Vue devtools를 사용하기 위함이다.
> Windows 환경에서 live-server 설치 시 `npm --no-optional`을 추가해줘야 애러가 발생하지 않았다.

### DOM 템플릿 구문 작성 시 주의 사항
- HTML 요소들은 자식 요소로 포함시킬 수 있는 요소들이 정해져 있는 경우가 있고, 구문 분석 수행 시 오류가 발생할 수 있다.
~~~
    <select>
        <option-component></option-component>
    </select>
~~~

- 브라우저는 태그들을 구분 분석한 후 Vue 컴포넌트를 렌더링하기에 발생하는 오류이고, 이는 `is` 특성을 이용해 해결할 수 있다.
~~~
    <select>
        <option is="option-component"></option>
    </select>
~~~

- `<script type="text/x-template">` 태그 안에서 코드가 작성되거나, `.vue` 확장자를 사용하는 단일 파일 컴포넌트를
작성하는 경우에는 `is` 특성을 사용하지 않아도 된다.

- 또 한 가지 주의할 점은 `루트 요소(Root Element)`는 하나여야 한다는 것이다. 여러 요소를 작성해야 한다면 `<div>` 등을 이용해
감싸주어야 한다.

### 컴포넌트에서의 data 옵션
- `data` 옵션에 함수가 주어지기 때문에, 매 호출 마다 새로운 객체가 만들어진다.
~~~
    <template>
        <div>{{example}}</div>
    <template>
    ...
    Vue.component('example-component', {
        template: '#exampleComponent',
        data: function() {
            return {
                example: 0
            }
        }
     }
~~~

### props를 이용한 정보 전달
- Vue 컴포넌트들이 부모-자식 관계로 형성되었을 때, 각 컴포넌트 내부의 데이터는 **캡슐화**되어 다른 컴포넌트나 앱에서 접근할 수 없다.
- Vue 컴포넌트를 정의할 때 `props` 라는 옵션을 작성하고 props 속성명을 배열로 나열하면 된다. 
- 컴포넌트 작성 시 속성명을 카멜 표기법을 사용했다면, 태그에서 속성명을 케밥 표기법을 사용해야 한다.
~~~
    <template>
        <div>{{exampleMsg}}</div>
    <template>
    
    <script>
    Vue.component('example-component', {
        template: '#exampleComponent',
        props: [ 'exampleMsg' ]
    }
    </script>
    
    <body>
        <div id="app">
            <example-component example-msg="hello"></example-component">
        </div>
    </body>
~~~

- 속성에 대한 엄격한 유효성 검사가 필요하다면 배열이 아닌 객체 형태를 사용할 수 있다.
- 리터럴이 아닌 자바스크립트 구문으로 속성값을 전달하기 위해서는 `v-bind` 디렉티브를 사용하면 된다.
- 속성으로 전달할 값이 배열이나 객체인 경우, 기본값을 부여하려면 반드시 **함수**를 사용해야 한다.
> `data` 옵션에 함수의 리턴 값을 부여한 것과 같은 맥락이다.

### event를 이용한 정보 전달
- `event`를 이용해 전달하는 방법은 사용자 정의 이벤트를 활용한다. 자식 컴포넌트에서 이벤트를 **발신(emit)**하고
부모 컴포넌트에서 `v-on` 디렉티브를 이용해 이벤트를 수신한다.
~~~
    <!-- 자식 컴포넌트 -->
    this.$emit('examClick', e.target.value);
    <!-- 부모 컴포넌트 -->
    <child-component v-on:examClick="examClickEvent"></child-component>
    ...
    methods: {
        examClickEvent: function(e) {
            ...
        }
    }
~~~

### 이벤트 버스 객체를 이용한 통신
- 형제 관계인 경우에는 `이벤트 버스(Event Bus)` 객체를 만들어 정보를 전달할 수 있다. 이때 빈 Vue 인스턴스를 사용한다.
- 이벤트를 수신하는 컴포넌트는 미리 **이벤트 핸들러**를 등록해두어야 한다.
- Vue 인스턴스 생명주기의 `created` 이벤트 훅을 이용하여, Vue 인스턴스가 만들어질 때 `$on` 메서드를 사용해 이벤트 수신 정보를 등록해둔다.
~~~
    var eventBus = new Vue();
    ...
    Vue.component('child1-component', {
        template: '#child1Template',
        data: function() {
            return {
                value: ""
            }
        }
        methods: {
            exam : function() {
                eventBus.$emit('exam', this.value);
            }
        }
    })
    ...
    Vue.component('child2-component', {
        template: '#child2Template',
        data: function() {
            return {
                output: "";
            }
        }
        created: function() {
            eventBus.$on('exam', this.examEvent);
        },
        methods: {
            examEvent: function(value) {
                this.output = value;
            }
        }
    })
~~~

## References
---
1. 원형섭님 저서, Vue.js Quick Start
2. Vue.js doc., [https://kr.vuejs.org/v2/guide](https://kr.vuejs.org/v2/guide)
