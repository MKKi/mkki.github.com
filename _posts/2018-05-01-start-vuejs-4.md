---
layout: post
title:  "[Vue.js Quick Start] Vue.js 스타일"
date:   2018-05-01 21:43:11 +0900
background: '/img/posts/02.jpg'
categories: vue.js
---

## 스타일
---
### 스타일 적용
- HTML 요소의 스타일 특성은 모두 **문자열**이며, `케밥 표기법(kebab casting)`을 사용한다.
- 스타일과 클래스는 동시에 적용될 수 있으며, 한 HTML 요소에 여러 개의 클래스를 적용할 수 있다.
- 만약 동일한 스타일 속성이 주어진다면 `<style>` 태그에 작성된 순서대로 CSS 스타일이 적용된 후에 인라인 스타일이 적용된다.

### 인라인 스타일
- 기존 HTML에서와 마찬가지로 Vue.js에서도 인라인 스타일의 사용은 권장되지 않는다.
> HTML 코드가 난잡해지고, 유지보수를 어렵게 하기 때문이다.
- Vue.js에서의 인라인 스타일은 `v-bind:style`로 작성한다. 다만 스타일 속성을 `카멜 표기법(camel casting)`으로 사용해야하며, 속성은 `콤마(,)`로 구분해야 한다.
> 자바스크립트에서는 변수명과 속성명으로 `대쉬(-)` 기호를 사용할 수 없기 때문이다.


- 만약 인라인 스타일을 사용해야 한다면, 스타일 객체를 직접 바인딩하는 방법이 권장된다.
```
<p v-bind:style="style1">...</p>
...
<script>
    var vm = new Vue({
        el: "#example",
        data: {
            style1: {
                ...
            }
        }
    })
</script>
```

- 다음과 같은 형태로 여러 개의 스타일 객체를 바인딩할 수도 있다.
```
<p v-bind:style="[ style1, style2 ]">...</p>
...
<script>
    var vm = new Vue({
        el: "#example",
        data: {
            style1: {
                ...
            },
            style2: {
                ...
            }
        }
    })
</script>
```

### CSS 클래스 바인딩
- CSS 클래스를 바인딩하기 위해서 `v-bind:class`를 사용한다. 개별적인 클래스 단위로 `true`가 되면 클래스가 주어진다.
```
...
<style>
    .set1 { ... }
    .set2 { ... }
    .set3 { ... }
</style>
...
<div id="example">
    <button id="btn1" v-bind:class="mystyle"></button> 
</div>
...
<script>
    var vm = new Vue({
        el: "#example",
        data: {
            mystyle: {
                set1: true,
                set2: false,
                set3: false
            }
        }
    })
</script>
```
> `set1` CSS 클래스가 적용된다.

### 계산형 속성, 메서드를 이용한 스타일 적용
- `computed` 옵션 내 함수가 `{ [CLASS_NAME]: [true|false] }` 형태로 리턴하는 형태이면 로직에 따른 CSS 클래스를 지정할 수 있다.
- 메서드도 동일하지만 `v-bind:class="style1()"`처럼 함수 호출 형태로 바인딩 시켜줘야 한다.

### 컴포넌트에서의 스타일 적용
- 컴포넌트 단위에 대해서도 클래스와 스타일을 적용할 수 있다.
```
.center {
    text-align: center;
}
...
<center-box></centerbox>
...
Vue.component('center-box', {
    template: '<div class="center">...</div>'
})
```

## References
---
1. 원형섭님 저서, Vue.js Quick Start
2. Vue.js doc., [https://kr.vuejs.org/v2/guide](https://kr.vuejs.org/v2/guide)
