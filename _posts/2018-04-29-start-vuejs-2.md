---
layout: post
title:  "[Vue.js Quick Start] Vue 인스턴스"
date:   2018-04-29 19:45:11 +0900
background: '/img/posts/06.jpg'
categories: vue.js
---

## Vue 인스턴스
---
### el, data, computed 옵션
1. `data` 옵션
- data 옵션에 주어진 모든 속성들은 Vue 인스턴스 내부에서 직접 이용되지 않고 Vue 인스턴스와 data 옵션에 주어진 객체 사이에 `프록시`를 두어 처리한다.
> 여기서 프록시란, 다른 클래스의 인터페이스로 동작하는 클래스이다.
- 다음 예시를 크롬 개발자 도구에서 실행해보자. data 옵션값이 Vue 인스턴스에 의해 프록시 처리되어 `vm.name`과 같이 사용할 수 있다.
직접 data 옵션을 통해 접근하고 싶다면 `vm.$data.name`과 같이 접근할 수 있다.
> 내장 옵션들은 모두 `$`식별자를 앞에 붙이고 있는데, 이름 충돌을 피하기 위한 것이다.
~~~
    <div id="test">
        {% raw %}{{name}}{% endraw %}
    </div>
    
    <script>
        var model = {
            name: 'sample name'
        }
        var simple = new Vue({
            el: '#test',
            data: model
        })
    </script>
~~~

2. `el` 옵션
- el 옵션은 Vue 인스턴스에 연결할 `HTML DOM 요소`를 지정한다. 단 여러 개 요소에 지정할 수 없다.
> el 옵션에 클래스 선택자를 사용하면 첫 번째 요소에만 연결된다.
- `vm.$mount('#test')`와 같이 동적으로 연결할 수도 있지만 권장되는 방법은 아니다.
Vue 인스턴스가 HTML 요소와 연결되면 도중에 연결된 요소를 변경할 수 없기 때문이다.

3. `computed` 옵션
- computed 옵션에 지정한 값은 함수였지만, Vue 인스턴스는 프록시 처리하여 마치 속성처럼 취급한다.
> `vm.$option.computed.sum` 형태로 실제 함수를 확인할 수 있다.
- `set 메서드`를 사용하면 쓰기 작업도 가능하다. 자바의 getter/setter 메서드랑 비슷한 맥락이다.
> 책의 예시 코드 중 `/,/g`란 정규표현식이 있었는데, 자바스크립트는 `/pattern/` 형식으로 정규표현식을 표기한다고 한다.

### 메서드
- `methods`는 Vue 인스턴스에서 사용할 메서드를 등록하는 옵션이다. 직접 호출할 수도 있고, 디렉티브 표현식, 콧수염 표현식에서도 사용할 수 있다.
> 메서드이기에 {% raw %}`{{sum()}}`{% endraw %}와 같이 호출해야 한다.
- `computed property`은 종속된 값에 의해 결과값이 캐싱되어 바로 리턴하지만, `methods`는 매번 실행된다는 점이 차이점이다.
> 즉, 둘 중 어떤 것을 사용할 것인지 결정할 때 중요한 요소가 **캐싱 여부**가 이다.
- 메서드 작성 시 주의사항은 ES6 문법인 `화살표 함수(Arrow Function)`을 사용하면 안된다는 점이다. 화살표 함수의 `this`는 Vue 인스턴스가 아닌 전역 객체에 바인딩되기 때문이다.

### 관찰 속성(Watched Property)
- 데이터 연산 시, 값이 바뀔 때 마다 매번 함수가 호출된다. 긴 처리 시간이 필요한 비동기 처리에 적합한 옵션이다. 외부 서버와의 통신 시 매우 유용하다.
> 서버와 통신 기능을 제공하는 라이브러리는 `JQuery Ajax`, promise 기반 HTTP Client 기능을 수행하는 `axios`, `fetch` 등이 존재한다.

### Vue 인스턴스 라이프 사이클
- Vue 인스턴스는 객체로 생성되고, 데이터에 대한 관찰 기능을 설정하는 등의 작업을 위해 초기화를 수행한다. 그리고 이 과정에서 다양한 `라이프 사이클 훅 메서드`를 적용할 수 있다.
- 라이프 사이클 훅은 Vue 컴포넌트를 만들고 관리할 때 유용하다.

![life-cycle](/img/vue-component-life-cycle.png)
> Vue Component Life Cycle

## References
---
1. 원형섭님 저서, Vue.js Quick Start
2. HyunSeob님 블로그, [https://hyunseob.github.io/2016/08/17/javascript-proxy](https://hyunseob.github.io/2016/08/17/javascript-proxy)
3. Vue.js doc., [https://kr.vuejs.org/v2/guide/instance.html](https://kr.vuejs.org/v2/guide/instance.html)