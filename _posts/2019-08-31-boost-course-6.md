---
layout: post
title:  "[boostcourse] 웹 앱 개발: 예약서비스 3/4 - 1"
date:   2019-08-31 02:30:11 +0900
background: '/img/posts/04.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

### Constructor Pattern
강의의 첫 주제는 [이전 포스팅](https://mkki.github.io/boostcourse/2019/08/13/boost-course-5.html)에서
개인적으로 학습했던 `Prototype Object`에 관한 내용이다.

이어지는 내용으로 어떻게 자바스크립트는 상속을 흉내내는가에 대해 알아보고자 한다.

#### Classical Inheritance
다음은 `ES6` 이전의 상속 방법이다.

```javascript
function Animal(name) {
    this.name = name;
}

Animal.prototype.getName = function () {
    return this.name;
};

function Bird(name) {
    Animal.call(this, name);
}

Bird.prototype = Object.create(Animal.prototype);
Bird.prototype.constructor = Bird;

var eagle = new Bird("eagle");
console.log(eagle.getName());           // eagle
console.log(eagle instanceof Animal);   // true
console.log(eagle instanceof Bird);     // true
```

`Object.create()` 메서드는 지정된 프로토타입 객체와 프로퍼티를 갖는 **새로운** 객체를 만들어준다.

다중 상속은 **lodash**의 `_.assign()` 메서드와 같은 **mixin 함수**가 필요하다.

#### ES6 Class
`ES6`에서 복잡한 `prototype` 기반 상속을 명료하게 사용하기 위한 **syntactic sugar**인 `class` 키워드가 등장했다.
> 새로운 객체지향 상속 모델이 아니라, 동일하게 구현된다.

클래스를 정의하는 방법은 다음과 같다. 클래스도 함수이기에, 선언식과 표현식을 제공한다.
그러나 함수 선언과 달리 **호이스팅이 발생하지 않는다.**
> 엄밀히 말하면 호이스팅은 발생하지만, **Lexical Binding**이 평가되기 전까지 **TDZ(Temporal Dead Zone)**에 있어 접근이 불가능한 것이다.

```javascript
var p = new Person("kim");  // Reference Error

class Person {
    constructor(name) {
        this._name = name;
    }
}

var Animal = class {
    constructor(name) {
        this._name = name;
    }
};
```

그리고 `constructor` 내부에서 선언과 초기화가 이루어진 클래스 필드는 언제나 `public`이다.
> **TC39**에서 `private`, `static` 필드에 관련된 표준안이 제안되었다. 현재 **stage3** 단계로, 다음 ECMAScript 버전에 포함될 예정이다.

#### ES6 Class extends
`extends` 키워드는 클래스 선언/표현식에서 다른 클래스의 자식 클래스를 생성하기 위해 사용된다.

```javascript
class Animal {
    constructor(name) {
        this._name = name;
    }
    speak() {
        return `${this._name} make a noise.`;
    }
}

class Bird extends Animal {
    // subclass에 constructor가 존재하면 super()를 호출해야 한다.
    // 호출하지 않으면 this를 사용할 수 없다.
    constructor(name, size) {
        super(name);
        this._size = size;
    }
    speak() {
        return `${this._name} cries.`;
    }
}
```

`ES5`에서 사용되는 함수 기반의 클래스를 통해서도 사용 가능하다.

```javascript
function Animal(name) {
    this._name = name;
}
Animal.prototype.speak = function() {
    return this._name + " make a noise.";
}

class Bird extends Animal {
    constructor(name, size) {
        super(name);
        this._size = size;
    }
    speak() {
        return this._name + " cries.";
    }
}
```

**객체 리터럴** 방식으로 객체를 생성하면 `prototype` 프로퍼티가 `Object.Prototype`를 가리키기에,
`extends` 키워드로 확장을 할 수 없게 된다.
> `Object.setPrototypeOf()`를 사용하면 가능하다.

#### ES6 class vs ES5 prototype
`ES5`에서 `superClass`의 생성자와 메서드 호출을 구현하는 것은 꽤나 번거로운 일이다.
프로토타입 기반 상속 구현에서 자식 클래스의 생성자 함수가 부모 클래스의 내용을 덮어쓰는 방식으로 동작하기 때문이다.

```javascript
function Parent() {
    this.a = 1;
}

function Child() {
    // call `super()`
    Parent.call(this);
    this.b = 2;
}

// inheritance
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

만약, 부모 클래스의 메서드를 호출하기 위해 `Object.getPrototypeOf()` 메서드를 사용하면,
다음과 같은 문제점이 발생한다.

````javascript
function Parent() {
    this.a = 1;
}

Parent.prototype.doSomething = function () {
    return "parent"
}

function Child() {
    // call `super()`
    Parent.call(this);
    this.b = 2;
}

// inheritance
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

Child.prototype.doSomething = function () {
    return Object.getPrototypeOf(this).doSomething() + ", child";
}

var o = new Child();
console.log(o.doSomething());   // parent, child, child
````

`Object.getPrototypeOf()`는 모든 인스턴스가 공유하고, `prototype` 프로퍼티는 재귀적으로 탐색되기 때문이다.

먼저, 생성자 함수가 호출되면 자바스크립트는 실제로 다음과 같은 작업을 수행한다.
```javascript
var o = new Object();
o.[[Prototype]] = Child.prototype;
Child.call(o);
```

`o.doSomething()`이 수행되었을 때, 자바스크립트는 `o`가 `doSomething` 프로퍼티를 가졌느지 확인한다.
없다면 `Object.getPrototypeOf(o).doSomething()`, 또 없다면 `Object.getPrototypeOf(Object.getPrototypeOf(o)).doSomething()`로 진행된다.
이는 프로퍼티가 발견되거나 `null`이 반환될 때까지 재귀적으로 반복된다.

반면, `ES6`에서는 오직 상위 상위 클래스만을 가리키는 `super` 키워드를 제공하기에 비교적 쉽게 사용할 수 있다.

```javascript
class Parent {
  constructor(){
    this.a = 1;
  }
}

class Child extends Parent {
  constructor(){
    super();
    this.b = 2;
  }
}
```

### 정규 표현식(Regular Expression)
문자열 조작을 보다 적은 코드량으로 수행하기 위한 문법이다.

실무에서는 다음과 같은 상황에서 사용한다고 한다.
- 이메일이나 주소의 규칙 검증
- `textarea`의 불필요한 입력값 추출
- 트랜스 파일링(Transpiling)

#### 생성
정규 표현식을 만드는 방법에는 두 가지가 있다.

첫 번째는 **정규식 리터럴**을 사용하는 방법이다.
스크립트가 불러와질 때 컴파일 되며, 정규 표현식이 **상수**일 경우에 성능을 향상시킬 수 있다.
```javascript
// '\d'는 패턴이고, 'i'는 플래그이다.
var regex = /\d/i;
```

두 번쨰는 `RegExp` 객체의 생성자 함수를 호출하는 방법이다.
런타임에 컴파일 되며, 패턴이 변경될 수 있거나 다른 곳에서 패턴을 가져와야하는 경우에 사용한다.
```javascript
var regex = new RegExp("\\d");
```

#### 플래그
플래그는 다음과 같은 종류가 있다.
- `i`는 대소문자를 구별하지 않고 검색한다.
- `g`는 문자열 내의 모든 패턴을 검색한다.
- `m`은 문자열의 행이 바뀌더라도 검색을 계속한다.

플래그 옵션을 사용하지 않은 경우, 첫 번째 매칭한 대상만을 검색하고 종료한다.
```javascript
const target = "ai thai themenai";
let regex = /ai/;

console.log(target.match(regex));
// ["ai", index: 0, input: "ai thai themenai", groups: undefined]

regex = /ai/g

console.log(target.match(regex));
// ["ai", "ai", "ai"]
```

#### 패턴
단순 문자열 패턴 이외에도, 여러 특수문자 패턴이 존재하여 **MDN**을 통해 학습했다.

다음은 자주 사용되는 패턴들이다.
- `.`은 임의의 문자 한 개를 의미한다.
- `[xy]`는 `x|y`와 같은 의미
- `\d`는 숫자를 의미한다. `\D`는 숫자가 아닌 문자를 뜻한다.
- `\w`는 알파벳과 숫자를 의미한다. `\W`는 이 반대를 의미한다.
- `\b`는 경계 문자를 의미한다.
- `\s`는 공백 문자를 의미한다.
- `x(?=y)`는 **y가 따라오는 x만** 대응한다. **lookahead**라고 불린다.
- `x(?!y)`는 **y가 따라오지 않는 x만** 대응한다. **nagated lookahead**라고 불린다.

### Form
브라우저는 `form` 태그를 사용하여 사용자가 입력한 데이터를 전송할 수 있게 해준다.

`<input type="submit">`, `<button type="submit">`인 엘리먼트가 있으면,
해당 엘리먼트를 클릭하거나 엔터를 누르면 입력 데이터가 자동으로 서버로 넘어간다.

`Ajax`를 사용하여 비동기적으로 처리하는게 `UX` 관점에서도 좋다.

```javascript
document.addEventListener("DOMContentLoaded", function() {
    function sendData(url) {
        var xhr = new XMLHttpRequest();
        var formData = new FormData(form);
        xhr.addEventListener("load", function() {
            console.log(this.responseText);
        });
        xhr.open("POST", url);
        xhr.send(formData);
    }
    
    var form = document.getElementById("myForm");
    form.addEventListener("submit", function(e) {
        e.preventDefault();

        sendData("/register");
    });
});
```

`input` 값이 변경되면 발생하는 `change` 이벤트 타입이 있다.

```javascript
element.addEventListener("change", function(e){});
```

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [Object.create()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create)
- MDN, [Classes](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes)
- PoiemaWeb, [Class](https://poiemaweb.com/es6-class)
- gomugom님 블로그, [ES6 Class는 단지 prototype 상속의 문법설탕일 뿐인가?](https://gomugom.github.io/is-class-only-a-syntactic-sugar/)
- MDN, [Inheritance and the prototype chain](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain)
- MDN, [Regular Expression](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/%EC%A0%95%EA%B7%9C%EC%8B%9D)
- MDN, [RegExp](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/RegExp)
- MDN, [Sending forms through JavaScript](https://developer.mozilla.org/en-US/docs/Learn/HTML/Forms/Sending_forms_through_JavaScript)