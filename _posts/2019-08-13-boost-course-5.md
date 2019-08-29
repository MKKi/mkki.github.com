---
layout: post
title:  "[boostcourse] 웹 앱 개발: 예약서비스 2/4"
date:   2019-08-13 17:07:11 +0900
background: '/img/posts/04.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

#### 배열의 함수형 메소드
`SPA`의 등장으로 프론트엔드에서의 데이터 조작이 늘어났고, 이를 위한 배열 메서드의 중요성이 증가했다.

`javascript`는 함수를 인자와 리턴 값으로 사용할 수 있어 함수형 프로그래밍에 적용하기에 적합하다.
> `Array.prototype.map()`, `Array.prototype.reduce()` 등은 함수형 프로그래밍을 위한 매우 강력한 내장 메서드이다.

#### Immutability in javascript
`primitive data type`은 변경 불가능한 값이다.
- Boolean
- null
- undefined
- Number
- String
- Symbol

그렇기에 아래 코드에서 문자열이 수정되는 것이 아니라, 다른 메모리 주소가 참조되는 것이다.
```javascript
var str = "foo";
str = "bar";
```

그러나 객체는 `mutable`하다. 아래의 경우는 같은 주소를 참조하기에 발생하는 현상이다.
```javascript
var obj1 = {
    name: 'foo'
};

var obj2 = obj1;
obj2.name = 'bar';

console.log(obj1.name); // 'bar'
console.log(obj2.name); // 'bar'
```

`ECMAScript 6`에 추가된 `Object.assign()`은 객체를 불변객체로 만들어 복사해주는 역할을 한다.
그러나 **완전한 deep copy를 지원하지 않는다.
> `Nested Object`는 shallow copy 된다.

`Object.freeze()`는 객체를 불변객체로 만들어준다. 그러나 이 역시 `Nested Object`는 `mutable`하다.
> 재귀적인 방법으로 deep freeze 가능하다.

#### function in javascript
`javascript`에서 함수는 **1급 객체(First-class citizen)**이다. 1급 객체의 조건은 다음과 같다.
1. 변수에 값으로 할당될 수 있어야 한다.
2. 함수의 매개변수로 전달될 수 있어야 한다.
3. 동적 프로퍼티 생성이 가능해야 한다.
4. 무명의 리터럴로 표현이 가능해야 한다.

```javascript
var add = function (x, y) {
    return x + y;
};

add.result = 3;
console.log(add.result);    // 3
```

또한, `javascript`는 **프로토타입** 기반 언어이다. 클래스가 없기에 상속 기능도 없다.
그래서 프로토타입(Prototype)으로 상속을 **흉내내도록** 구현한다.
> 그렇기에 모든 함수는 `Function`객체이다.

다음은 함수와 `new operator`를 사용하여 클래스를 흉내낸 코드이다.
객체를 만들때 마다 프로퍼티가 메모리에 할당되는 단점이 있다.

```javascript
function Obj() {
    this.name = 'foo';
    this.value = 'bar';
}

var obj = new Obj();
```

프로토타입을 사용하면 이 문제가 해결된다.

```javascript
function Obj() {}

Obj.prototype.name = 'foo';
Obj.prototype.value = 'bar';
```

프로토타입에는 `Prototype Object`와 `Prototype Link`라는 것이 존재한다.

#### Prototype Object
객체는 언제나 **함수**로 생성된다.

```javascript
function Obj() {}
var obj1 = new Obj();

var obj2 = {};  // 'var obj2 = new Object();'와 동일
```

함수가 정의될 때, 다음이 이뤄진다.
1. 함수에 생성자 자격이 부여된다.
    - `new operator`를 사용하기 위해서는 생성자 자격이 있어야 한다.
2. `Prototype Object`가 생성되고 연결된다.
    - 함수 내 `prototype` 속성을 통해 `Prototype Object`에 접근할 수 있다.

`Prototype Object`에는 `constructor`와 `[[Prototype]]('__proto__' in Chrome)` 속성이 존재한다.

#### Prototype Link
`[[Prototype]]` 속성은 모든 객체들이 가지고 있으며, `Prototype Link`라고 한다.
그리고 값으로 객체가 생성될 때 조상이었던 함수의 `Prototype Object`를 가리킨다.

```javascript
function Person() {}

Person.prototype.name = 'foo';

var obj = new Person();
console.log(obj.name);  // foo
```

위 코드에 `obj` 객체에는 `name` 속성이 존재하지 않고 해당 속성을 찾기 위해 상위 프로토타입을 탐색한다.
이렇게 최상위인 `Object Prototype Object`까지 올라가고, 찾기 못하면 `undefined`를 리턴한다. 이를 `Prototype Chain`이라 한다.
> `Object` 객체의 메서드를 사용할 수 있었던 이유가 바로 이 때문이다.

#### 객체 리터럴
`객체 리터럴(Object Literal)`은 객체를 생성하는 표현식이다.

```javascript
var person = {
    name: 'foo',
    getName: function() {
        return this.name;
    }
}
```

추가된 속성은 `delete operator`로 삭제 가능하다. `person` 변수 자체를 삭제할 수는 없다.
> `x = 5;`와 같은 암묵적 변수는 삭제 가능하다. 그렇기에 암묵적 변수 사용을 지양해야 한다.

```javascript
delete person.name;

console.log(person); // {getName: function(){...}}
console.log(delete person); // false
```

#### this 바인딩
`javascript`의 경우 함수 호출 방식에 의해 `this`에 바인딩되는 객체가 **동적**으로 결정된다. 즉, 함수가 어떻게 **호출**되었는지가 관심사이다.
> 함수의 상위 스코프를 결정하는 `lexical scope`는 함수 **선언**시 결정된다.

함수의 호출 방식은 다음과 같다.
- 함수 호출
- 메소드 호출
- 생성자 함수 호출
- `apply`, `call`, `bind` 호출

기본적으로 `this`는 **전역 객체(Global Object)**에 바인딩된다. 내부 함수와 콜백 함수의 경우도 마찬가지이다.
> 브라우저의 경우 `window`, 노드의 경우 `global`

```javascript
var obj = {
    foo: function() {
        console.log(this);  // obj
        function inner() {
            console.log(this);  // window
        }
    },
    bar: function() {
        setTimeout(function() {
            console.log(this);  // window
        }, 1000);
    }
};

obj.foo();
obj.bar();
```

함수가 객체의 프로퍼티 값이면 메서드로서 호출된다. 이 경우 `this`는 메서드를 호출한 객체에 바인딩된다.

생성자 함수가 `new operator`와 함께 호출되면 다음과 같이 동작한다.
1. 빈 객체 생성 및 `this` 바인딩
    - `this`는 생성된 빈 객체를 가리킨다.
    - `prototype` 프로퍼티가 가리키는 객체를 자신의 `Prototype Object`로 설정한다.
2. `this`를 통한 프로퍼티 생성
    - 설정된 `this`를 통해 동적으로 프로퍼티를 생성할 수 있다.
3. 생성된 객체 반환
    - 명시적으로 `this`가 아닌 다른 객체를 반환하는 경우 생성자 함수로서의 역할을 수행할 수 없기에, 리턴문을 사용하지 않는다.

객체 리터럴과 생성자 함수의 차이는 `Prototype Object`에 있다. 
- 객체 리터럴의 경우는 `Object.Prototype`이다.
- 생성자 함수의 경우는 `Function.name.Prototype`이다.

`Function.prototype.apply`, `Function.prototype.call` 메서드는 명시적으로 특정 객체에 `this`를 바인딩하는 방법을 제공하는 메서드이다.
> `apply()`의 경우는 배열 형태로 파라미터를 넘기고, `call()`은 하나하나 넘기는 것이 차이점이다.

```javascript
Calculator.call(obj, 1, 2);

Calculator.apply(obj, [1, 2]);
```

`Function.prototype.bind`는 함수에 파라미터로 전달한 `this`가 바인딩된 **새로운 함수**를 리턴한다.
그렇기에 명시적으로 함수를 호출할 필요가 있다.

```javascript
function Foo(name) {
  this.name = name;
}

Foo.prototype.getName = function (cb) {
  if (typeof cb == 'function') {
    // 명시적으로 this를 바인딩해주지 않으면 전역 객체로 바인딩 된다.
    cb.bind(this)();    
  }
};

function printName() {
  console.log(this.name);
}

var obj = new Foo('foo');
obj.getName(printName);
```

#### 화살표 함수의 this
`arrow function`은 함수를 선언할 때 `this`에 바인딩할 객체가 **정적**으로 결정된다.
일반 함수와 달리 언제나 상위 스코프의 `this`를 가리키고, `lexical this`라고도 한다.
> `lexical scope`와 유사한 방법으로 바인딩할 객체를 결정한다.

또한, `call`, `apply`, `bind` 메서드로 `this`를 변경할 수 없다.

```javascript
window.x = 3;
const fn = function () { return this.x; }
const arrow = () => this.x;

console.log(fn.call({ x: 5 }));     // 5
console.log(arrow.call({ x: 5 }));  // 3
```

`arrow function`을 사용하지 말아야 할 경우는 다음과 같다.
##### 메서드를 정의하는 경우
- 메서드의 경우 호출한 객체를 가리켜야하지만, 아래와 같이 `this`가 전역 객체를 가리킨다.

```javascript
const obj = {
    name: 'foo',
    getName: () => console.log(this.name);
}

obj.getName();  // undefined
```

##### `prototype`에 메서드를 할당하는 경우
메서드를 정의하는 경우와 동일한 문제가 발생한다.

##### 생성자 함수를 사용할 경우
- `arrow function`은 `prototype` 프로퍼티를 갖고 있지 않기에 `constructable`하지 않다.

##### `addEventListener` 함수의 콜백 함수로 사용할 경우
- 일반적으로 `addEventListener` 함수에 사용되는 콜백 함수 내부의 `this`는 이벤트 리스너에 바인딩된 요소를 가리킨다.
그러나 `arrow function`을 사용할 경우 상위 컨텍스트로 `this`가 바인딩된다.

```javascript
const slider = document.getElementById(".slider");

slider.addEventListener("click", () => {
    console.log(this);  // window
    ...
});
```

#### jQuery
`IE` 하위 버전 호환성 이슈를 해결해주고, `DOM` 조작을 편하게 해주는 라이브러리이다.

그러나 현재 다음과 같은 단점으로 점유율이 줄어들고 있는 추세이다.
1. 브라우저 호환성 이슈가 줄어들었다.
2. `DOM` 조작이 편리하나 네이티브 메서드에 비해 성능이 떨어진다.
3. `View`와 `Controller`를 연동시켜 `Anti-MVC Pattern`을 만든다.
4. `SPA`가 성행하는 모던 웹 개발 환경에서 `modular`하지 않고, `virual DOM`과의 상성이 좋지 않다.
5. `promise` 이슈 등 웹 표준을 준수하지 않는다.
6. `ES6+`에 `jQuery`의 유용한 기능이 구현되어 있다.

#### handlebar를 활용한 템플릿 작업
`handlebar`는 자바스크립트 진형에서 사용되는 `template engine` 중 하나이다.

기본적인 사용법은 다음과 같다.

```html
<script type="text/x-handlebars-template" id="template">
    <h1>{{name}}</h1>
</script>
```

```javascript
var source = document.getElementById("template").innerHtml;
var template = Handlebars.compile(source);
```

분기와 반복 처리 등을 수행할 수 있는 [built-in helpers](https://handlebarsjs.com/builtin_helpers.html)도 지원한다.

#### ES6 Tagged Template Literals
`tagged template Literals`은 `template literals`를 함수로 파싱해주는 문법이다.

```javascript
const highlight = (strings, ...values) => {
    return strings.reduce((result, string, i) => {
        return `${result}${string}<cite>${values[i] || ''}</cite>`;
    }, '');
};

const name = "mkki";
const quoto = highlight`hello ${name} world`;
console.log(quoto); // hello <cite>mkki</cite> world<cite></cite>
```

`sanitization`에 사용할 수도 있다.

`react`에서의 `styled-components`와 페이스북의 `graphQL`에서도 사용되었다.

```javascript
import React from 'react';

import styled from 'styled-components';

// Create a <Title> react component that renders an <h1> which is
// centered, palevioletred and sized at 1.5em
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

// Create a <Wrapper> react component that renders a <section> with
// some padding and a papayawhip background
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;

// Use them like any other React component – except they're styled!
<Wrapper>
  <Title>Hello World, this is my first styled component!</Title>
</Wrapper>
```

#### 클린코드
강의에서 나온 `naming convention guide`
- 함수는 목적에 맞게 이름이 지어져 있는가?
- 함수 안의 내용은 이름에 어울리게 하나의 로직을 담고 있는가?
- 함수는 동사 + 명사이며 함수의 의도를 충분히 반영하고 있는가?
- 함수는 카멜표기법 또는 _를 중간에 사용했는가?
- 변수는 명사이며 의미 있는 이름을 지었는가?

`javascript`에서는 전역변수를 줄이는 습관을 들이고, `eslint`와 같은 정적 분석 도구를 사용하면 보다 깔끔한 코드를 작성할 수 있다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [Array](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array#)
- PoiemaWeb, [Immutability](https://poiemaweb.com/js-immutability)
- PoiemaWeb, [함수 호출 방식에 의해 결정되는 this](https://poiemaweb.com/js-this)
- PoiemaWeb, [화살표 함수](https://poiemaweb.com/es6-arrow-function)
- GeeksforGeeks, [Functional Programming Paradigm ](https://www.geeksforgeeks.org/functional-programming-paradigm/)
- velog.io, [[JS] 함수 객체: 일급 객체로써의 함수, 함수의 property, 내부함수와 스코프 체인](https://velog.io/@imacoolgirlyo/JS-%ED%95%A8%EC%88%98-%EA%B0%9D%EC%B2%B4-%EC%9D%BC%EA%B8%89-%EA%B0%9D%EC%B2%B4%EB%A1%9C%EC%8D%A8%EC%9D%98-%ED%95%A8%EC%88%98-%EA%B8%B0%EB%B3%B8-property)
- 오승환님 블로그, [[Javascript] 프로토타입 이해하기](https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67)
- Bharathvaj Ganesan, [Tagged Template literals — Its more than you think](https://codeburst.io/javascript-es6-tagged-template-literals-a45c26e54761)