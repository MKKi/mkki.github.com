---
layout: post
title:  "[Vue.js Quick Start] Webpack, 컴포넌트 심화"
date:   2018-05-06 21:47:11 +0900
background: '/img/posts/06.jpg'
categories: [vue.js, js]
---

## Webpack
---
### Webpack이란?
- `Webpack`은 모던 자바스크립트 애플리케이션 개발을 위한 모듈 번들러이다. 모듈과 모듈이 사용하는 정적 자원(CSS, Images)들을 묶어 하나 혹은 몇 개의 파일로 번들링해준다.
- 번들링의 장점은 다음과 같다.
1. 하나의 스크립트 태그를 사용해 번들링된 모듈을 참조하기에 초기 로딩 타임이 상대적으로 빠르다.
2. 정적 자원들까지 모듈화 해준다.
3. `트랜스파일러(Babel, Typescript 등)`와 손쉽게 통합된다.
4. `HMR(Hot Module Replacement)`를 지원하기에 코드가 수정될 때마다 자동으로 번들링을 수행하고 페이지가 갱신된다.
5. `로더(loader)`로 번들링을 하기 전에 전처리를 수행하는 기능을 제공한다.
6. 다양한 플러그인을 제공한다.

### Webpack 구성 파일
- 구성 파일은 크게 `Entry`, `Output`, `Module`, `Plugins`의 4가지 옵션 객체가 핵심이며, 이외의 기타 옵션들이 제공된다.

- `entry`는 처음 로드하는 파일을 지정한다. 단일 파일로 지정할 수도 있고, 여러 개의 파일을 지정할 수도 있다.
> 다중 진입 파일인 경우 객체로 작성한다. 이 경우에 진입 파일 갯수만큼 번들링된 자바스크립트 코드를 만들어낸다.

- `output`은 번들링된 결과물의 출력 방법을 지정한다. `path` 옵션은 번들링된 파일의 저장 경로, `publicPath` 옵션에는 웹서버에서 이용될 때의 경로를 지정한다.
`filename`은 정적 문자열이 될 수도 있지만, `entry` 이름이 파일명에 반영되도록 `[name]`이라는 대체 문자를 사용할 수 있다.

- `module`은 프로젝트 내부에서 사용하는 다양한 유형의 모듈을 수행하는 방법을 지정한다. 주로 `로더(loader)`에 대한 설정 내용을 지정한다.

- `plugins`는 Webpack 빌드 프로세스에 사용자가 지정한 작업을 추가할 수 있는 기능을 제공한다.

## 컴포넌트 심화
---
### 단일 파일 컴포넌트
- `단일 파일 컴포넌트(Single File Component)`는 전역 수준 컴포넌트의 몇 가지 문제점을 해결한다.
1. 빌드 단계가 없으므로 최신 자바스크립트 문법을 사용할 수 없다. 따라서 HTML 내부에 직접 ES2015 이전 버전의 자바스크립트 코드를 작성해야 한다.
2. CSS를 지원하지 않는다. 컴포넌트들은 고유한 스타일 정보를 포함하는 경우가 많다. 하지만 전역 컴포넌트에서는 CSS 스타일을 빌드하고 모듈화할 수 있는 기능을 제공하지 않는다.
3. 컴포넌트의 템플릿이 작성될 때 HTML 파일 안에 여러 개의 `<template />` 태그가 작성되어 가독성이 떨어진다. 또한 템플릿마다 고유한 `id`를 부여하고, 컴포넌트도 이름을 지정해야 한다.

- `vue-loader`라는 구성 요소가 단일 파일 컴포넌트를 지원한다. 확장자가 `.vue`인 파일에 `<template />`, `<script />`, `<style />`를 작성하면 파싱한 후 다른 로더들을 활용해 하나의 모듈로 조합한다.
> `css-loader`는 CSS 스타일을 전처리할 수 있고, 스타일 정보를 모듈화할 수도 있다.

- 단일 파일 컴포넌트는 전역 컴포넌트와 다르게 `<template>`에는 id 특성을 부여하지 않고, `<script>` 영역에서는 Vue 컴포넌트의 template를 지정하지 않는다.
또한, `Vue.component()`로 이름과 template 속성을 지정하지 않는다. 반면에 `name` 속성을 지정할 수 있으며, 반드시 객체를 **export**해야 한다. 

### 컴포넌트에서의 스타일
- 특정 컴포넌트만의 스타일을 지정하려면 `Scoped CSS`와 `CSS Module`의 두 가지 방법을 사용할 수 있습니다.
- `Scoped CSS`를 사용할 때는 다음과 같은 사항을 염두해야 한다.
1. 특성 선택자(Attribute Selector)를 사용하기에 브라우저에 스타일을 적용하는 속도가 느리다.
그렇기에 속도가 빠른 ID 선택자, 클래스 선택자, 태그명 선택자로 요소를 선택해 스타일을 적용하도록 해야 한다.
2. 부모 컴포넌트에 적용된 Scoped CSS는 하위 컴포넌트에도 반영된다.

- `CSS Module`은 스타일을 마치 객체처럼 다룰 수 있게 한다. 모듈화 된 스타일은 Vue 인스턴스 내에서 `$style`이라는 계산형 속성을 통해 이용할 수 있다.
또한, 모듈화 된 스타일은 충돌을 막기 위해 생성된 다른 이름을 사용한다.

### 슬롯
- 부모 컴포넌트와 자식 컴포넌트 사이의 정보를 교환할 때, 속성으로 전달해야 할 정보가 HTML 태그가 포함된 문자열이라면 번거롭다.
> 부모 컴포넌트에서 HTML 태그 문자열을 전달하고, 자식 컴포넌트에서 마크업으로 렌더링하는 과정을 거쳐야 한다.

- `슬롯(slot)`은 이러한 불편함을 해결하는 방법이다. 자식 컴포넌트에서 `<slot>` 태그를 작성하고, 부모 컴포넌트에서는 컨텐츠 영역에 자식 컴포넌트의 `<slot>` 영역에 나타낼 **HTML 마크업**을 작성하면 된다.
~~~
<template>
    <speech-box :header-text="A.header" :footer-text="A.footer">
      <div>
        <p>{{ A.message }}</p>
      </div>
    </speech-box>
</template>
...
~~~
> `<speech-box>` 컴포넌트의 컨텐츠로 전달한 내용이 자식 컴포넌트의 `<slot>`에 나타나기에 속성에 비해 편리하다.
7
### 명명된 슬롯
- `명명된 슬롯(named slot)`을 사용하면 컴포넌트에 여러 개의 슬롯을 작성할 수 있다.
~~~
<template>
    <div>
        <header>
            <slot name="header></slot>
        </header>
        <section>
            <slot name="header></slot>
        </section>
        <footer>
            <slot name="header></slot>
        </footer>
    </div>
</template>
~~~

### 범위 슬롯
- 자식 컴포넌트에서 부모 컴포넌트로 속성을 전달한 후, 부모 컴포넌트에서 출력할 내용을 커스터마이징할 경우에 사용한다.
~~~
// child
<template>
    <div>
        <input type="text" v-model="x" /><br />
        <slot name="exam" v-bind:nx="x"></slot>
    </div>
</template>

<script>
    export default {
        data() {
            return { x: 4 }
        }
    }
</script>

// parent
<template>
    <child>
        <template slot="exam" scope="p1">
            <div>{{ p1.nx }}^2 = {[ parseInt(p1.nx) }} * {[ parseInt(p1.nx) }}</div>
        </template>
    </child>
</template>

<script>
    import Child from './child.vue';
    
    export default {
        components : { Child }
    }
</script>
~~~

### 동적 컴포넌트
- `동적 컴포넌트(Dynamic Component)`는 동일한 위치에 여러 컴포넌트를 표현하기 위해 사용된다.
`<component>` 요소를 템플릿에 작성하고 `v-bind` 디렉티브를 이용해 `is` 특성 값으로 어떤 컴포넌트를 그 위치에 나타낼지 결정하면 된다.

- 만약 자식 컨텐츠가 정적 컨텐츠라면 `<component>` 요소를 `<keep-alive>` 요소로 감싸서 캐싱하면 된다.
~~~
<div>
    <keep-alive exclude="exam1">
        <component v-bind:is="examdata"></component>
    </keep-alive>
</div>
...
<script>
    export default {
        components: { exam1, exam2, exam3},
        data() {
            return { examdata: 'exam1' }
        }
    }
</script>
~~~
> 특정 컴포넌트에서만 캐싱 유무를 설정하려면 `include`, `exclude` 특성으로 컴포넌트를 나열하면 된다.

### 재귀 컴포넌트
- `재귀 컴포넌트(Recursive Component)`는 템플릿에서 **자기 자신**을 호출하는 컴포넌트이다. 반드시 `name` 옵션을 지정해야한다.
~~~
// child
<template>
    <ul>
        <li v-for="(a, index) in examlist">
            {{ a.title }}
            <recursive :examlist="a.examlist"></recursive>
        </li>
    </ul>
</template>

<script>
    export default {
        name: 'recursive',
        props: [ 'examlist' ]
    }
</script>

// parent
<template>
    <div>
        <recursive :examlist="list"></recursive>
    </div>
</template>

<script>
    export default {
        name: 'parent',
        components : { Child },
        data () {
            return {
                list : {
                    title: "title1", type: "title"
                    examlist: [
                        {
                            title: "subtitle1", type: "subtitle",
                            examlist: [
                                { title: "A", type: "item" },
                                { title: "B", type: "item" },
                            ]
                        },
                        {
                            title: "subtitle2", type: "subtitle",
                            examlist: [
                                { title: "C", type: "item" },
                                { title: "D", type: "item" },
                            ]
                        },
                        {
                            title: "subtitle3", type: "subtitle",
                            examlist: [
                                { title: "E", type: "item" },
                                { title: "F", type: "item" },
                            ]
                        }                          
                    ]
                } 
            }
        }
    }
</script>
~~~

## References
---
- 원형섭님 저서, Vue.js Quick Start
