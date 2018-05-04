---
layout: post
title:  "[Vue.js Quick Start] ECMAScript 2015"
date:   2018-05-02 00:43:11 +0900
background: '/img/posts/04.jpg'
categories: [vue.js, js]
---

## ECMAScript 2015
---
### let과 const
- ES6(ECMAScript 2015)에서 블록 단위 스코프 지원과 변수의 중복 선언을 방지하기 위해 `let` 키워드가 등장했다.
> `var` 키워드로 선언된 변수는 호이스팅하기에 중복 선언이 가능하다.
- 다음 예시를 트랜스파일하면, `outer` 함수 내 변수 `a`가 `_a`로 변경되는 것을 볼 수 있다.
~~~
    let a = "global";
    function outer() {
        let a = "outer";
        if (true) {
            let a = "block"
        }
    }
    
    // 트랜스파일 후 코드
    "use strict";
    
    var a = "global";
    function outer() {
        var a = "outer";
        if (true) {
            var _a = "block"
        }
    }
~~~

- `const` 키워드는 상수 기능을 제공하며, 블록 스코프를 지원한다.
- `const` 변수에 객체를 할당하면 참조값이 저장된다. 객체는 `가변(mutable)` 상태이므로 객체 자신은 얼마든지 바뀔 수 있지만,
다른 객체를 할당하려고 하면 예외가 발생한다.
~~~
    const a = { "name": "foo" };
    a.name = "bar";
    a = {};  // 예외 발생
~~~

### 기본 파라미터와 가변 파라미터
- ES6 이전에는 함수가 파라미터 값을 받지 못할 때 기본값을 지정하는데 별도의 코딩이 필요했다.
~~~
    function a(x, y, z) {
        x = x === undefined ? 1 : x;
        y = y === undefined ? 2 : y;
        z = z === undefined ? 3 : z;
        console.log(x, y, z);
    }
    
    a(4, 5);    // 4, 5, 3
~~~

- ES6부터는 `기본 파라미터(Default Parameter)`를 이용해 함수 파라미터의 기본값을 지정할 수 있게 되었다.
파라미터로 `undefined`를 넘기면 값이 누락된 것으로 간주하며, 표현식도 허용한다.
~~~
    function a(x = 0 + 1, y = 2, z = 3) {
       console.log(x, y, z);
    }
    
    a(4, 5);    // "4, 5, 3" 출력
    a(undefined, 4, 5);     // "1, 4, 5" 출력
~~~

- `펼침 연산자(spread operator)`는 `이터러블(iterable)` 객체를 개별 값으로 나누어주고 `...`로 표시한다.
> 이터러블은 ES6 iterable protocol에 따라 여러 값을 가지며 순회 가능한 객체이다. 대표적인 이터블 객체가 배열이다.
- `가변 파라미터(rest parameter)`는 여러 개의 파라미터 값을 **배열**로 받을 수 있게 한다.
~~~
    function a(...data) {
        console.log(data);
    }
    let data = [1, 4];
    a(data);    // "1, 4" 출력
~~~

### 구조분해 할당(destructuring assignment)
- 구조분해 할당은 배열, 객체의 값들을 추출하여 여러 변수에 할당할 수 있는 기능이다.
~~~
    let arr = [10, 20, 30];
    let [a1, a2, a3] = arr;
    console.log(a1, a2, a3);    // "10, 20, 30" 출력
    
    arr [a1, a2, a3] = [10, 20, 30];
    console.log(a1, a2, a3);    // "10, 20, 30" 출력
    
    let obj = { name: "foo", age: 20, gender: "M" }
    let { name: n, age: a, gender } = obj;
    console.log(n, a, gender); // "foo, 20, M" 출력
~~~

- 할당 없이 건너뛰는 것도 가능하다.
~~~
    let [a, , b] = [1, 2, 3];
    console.log(a);     // "1" 출력
    console.log(b);     // "3" 출력
~~~

- 가변 연산자도 사용 가능하다.
~~~
    let [a, ...b] = [1, 2, 3, 4];
    console.log(a);     // "1" 출력
    console.log(Array.isArray(b));     // "true" 출력
    console.log(b);     // "[2, 3, 4]" 출력
~~~

- 할당되지 않은 값에 기본값을 설정할 수도 있다.
~~~
    let [a, b, c = 0] = [1, 2, 3];
    console.log(a);     // "1" 출력
    console.log(b);     // "2" 출력
    console.log(c);     // "0" 출력
~~~

- 중첩 배열의 구조분해 할당도 지원한다.
~~~
    let [a, b, [c, d]] = [1, 2, [3, 4]];
    console.log(a);     // "1" 출력
    console.log(b);     // "2" 출력
    console.log(c);     // "3" 출력
    console.log(d);     // "4" 출력
~~~

- 함수의 파라미터에서도 사용할 수 있다.
~~~
    function exam({ name, gender, phone="없음", age=0 }) {
        console.log("이름 : " + name);
        console.log("성별 : " + gender);
        console.log("연락처 : " + phone);
        console.log("나이 : " + age);
    }
    
    exam({ gender: "M", name: "foo" });     // "이름 : foo ..." 출력
~~~
> 객체로 파라미터 값을 전달하는 경우 전달 순서는 결과에 영향을 미치지 않는다.

- 객체 프로퍼티 값을 추출하여 변수에 할당할 수도 있다.
~~~
   let obj = { "name": "foo", "age": 20 };
   let name, age;
   ({name, age} = obj);
   
   // 객체 프로퍼티명과 변수명을 다르게 할 경우
   let {name: x, age: y} = { "name": "foo", "age": 20 };
~~~

### 화살표 함수(Arrow Function)
- 화살표 함수는 기존 함수 표현식에 비해 간결하다. 전통적인 함수와 달리 화살표 함수는 함수 정의 영역의 `this`를 그대로 전달 받을 수 있다.
~~~
    let test = (a,b) => {
        return a+b;
    }
    
    let test2 = (a,b) => a+b;
    
    test(1, 2);     // "3" 출력
    test2(1, 2);    // "3" 출력
~~~

- 다음 예에서 Person 함수 안에서의 `this`는 객체 p1를 가리킨다. 반면 `incrAge` 함수 내의 `this`는 전역 객체에 바인딩 되어 결과값이 0으로 출력된다.
~~~
    function Person(name, yearCount) {
        this.name = name;
        this.age = 0;
        var incrAge = function () {
            this.age++;
        }
        for(var i=0; i<yearCount; i++) {
            incrAge();
        }
    }
    
    var p1 = new Person("foo", 20);
    console.log(p1.name + ", " + p1.age);   // "foo, 0" 출력
~~~
> 메서드 내의 `this`는 객체를 가리키고, 함수 내의 `this`는 전역 객체에 바인딩된다.

- 해결 방법은 다음과 같다.
~~~
    // 방법1
    ...
    var outer = this;
    var incrAge = function() {
        outer.age++;
    }
    ...
    
    // 방법2
    ...
    for(var i=0; i<yearCount; i++){
        incrAge.apply(this);
    }
    ...
~~~

- 화살표 객체를 쓰면 함수 내부에서 `this`를 사용할 수 있다.
~~~
    function Person(name, yearCount) {
        this.name = name;
        this.age = 0;
        var incrAge = () => {
            this.age++;
        }
        for(var i=0; i<yearCount; i++) {
            incrAge();
        }
    }
~~~

### 새로운 객체 리터럴
- 객체 속성을 작성할 때 변수명과 동일하면 생략이 가능하다.
~~~
    var name = "foo";
    var age = 20;
    
    var obj = { name, age };    // var obj = { name: name, age: age };
    console.log(obj);      // "{ name: foo, age: 20 }" 출력
~~~

- 새로운 메서드 표기법도 제공된다.
~~~
    let obj = {
        // func : function() { ... }
        func(param) {
            ...
        }
    }
~~~

### 조합 프로퍼티명
- ES6부터는 객체 생성과 동시에 조합한 이름의 프로퍼티를 추가할 수 있다.
~~~
    let obj = {
        ["foo"+"bar"]: "baz"
    }
    
    console.log(obj["foo"+"bar"]);
    console.log(obj["foobar"]);     // 동일한 결과
~~~

### 템플릿 리터럴
- 템플릿 리터럴은 ``역따옴표(`)``로 묶여진 문자열에서 `템플릿 대입문(${})`을 이용해 **동적**으로 문자열을 넣을 수 있는
방법을 제공한다.
~~~
    var name = "foo";
    console.log(`${name}님 환영합니다.`);     // "foo님 환영합니다" 출력
~~~

### 컬렉션
- ES6에서는 `Set`, `Map`, `WeakSet`, `WeakMap`과 같은 집합, 맵을 제공한다.
각 컬렉션에서 `set()`, `get()`, `has()`, `clear()`, `delete()` 등의 메서드를 사용 가능하다.
> 자바스크립트의 배열도 `List` 형태의 컬렉션이다.

- `Set`은 합집합(Union), 교집합(Intersect)과 같은 집합 연산을 제공한다.
~~~
    var foo = new Set([ "a", "b", "c" ]);
    var bar = new Set([ "d", "e", "a" ]);
    
    var union = new Set([ ...foo.values(), ...bar.values() ]);
    console.log(union);     // 'Set(5) {"a", "b", "c", "d", "e"}' 출력
    
    var intersection = new Set([ ...foo.values()].filter(e => bar.has(e)));
    console.log(intersection);      // 'Set(1) {"a"}' 출력
~~~

- `Map`은 `Key-Value 쌍`의 집합체이며, 키는 고유한 값이여야 한다. 
- `Object`와 `Map`은 비슷한 개념인 것 같지만 키 필드의 타입과 요소의 순서, 상속 등 여러 부분에서 차이점이 존재한다.
자세한 내용은 [ES6 — Map vs Object — What and when?](https://medium.com/front-end-hacking/es6-map-vs-object-what-and-when-b80621932373)을 참고하자.

### 클래스
- 다른 프로그래밍 언어의 클래스와 유사하게 `정적 메서드(Static Method)`, `인스턴스 메서드(Instance Method)`, `생성자(Constructor)`, 상속을 지원한다.
~~~
    class Person {
        constructor(name, tel, address) {
            this.name = name;
            this.tel = tel;
            this.address = address;
    
            if (Person.count) {
                Person.count++;
            } else {
                Person.count = 1;
            }
        }
    
        static getPersonCount() {
            return Person.count;
        }
    
        toString() {
            return `name=${this.name}, tel=${this.tel}, address=${this.address}`
        }
    }
    
    class Employee extends Person {
        constructor(name, tel, address, empno, dept) {
            super(name, tel, address);
            this.empno = empno;
            this.dept = dept;
        }
        
        toString() {
            return super.toString() + `, empno=${this.empno}, dept=${this.dept}`;
        }
        getEmpInfo() {
            return `${this.empno}: ${this.name}은 ${this.dept} 부서입니다.`
        }
    }
~~~

## 모듈
- 전통적인 자바스크립트에서는 `모듈(Module)` 개념이 불분명하다. 
ES6에서는 공식적으로 모듈 기능을 제공하며, 각 모듈을 JS 코드를 포함한 파일로 봐도 무방하다.

- 각 모듈은 `import`, `export` 구문을 통해 가져오거나 내보내질 수 있다. 모듈 내부에 선언된 변수, 함수, 객체, 클래스는 `로컬(local)`로 간주한다.
따라서 외부로 공개하고자 하는 것을 **export**해야 한다. export된 모듈은 다른 모듈에서 **import** 구문으로 참조하여 사용할 수 있다.

- 다음은 간단한 모듈을 작성하고 export하는 예이다.
~~~
    // src/exam/exam1.js
    export let var1 = 1000;
    export function add(a, b) {
        return a+b;
    }
    
    // 한 번에 export
    let var2 = 1000;
    function multiply(a, b) {
        return a*b;
    }
    
    export { var2, multiply };
~~~

- export한 요소들을 **구조분해 할당**을 사용하여 import 할 수 있다. 이때 **상대 경로**를 사용해야 한다. 
~~~
    import { var, add } from './exam/exam1.js';
    // import { var as n, add } from './exam/exam1.js';
    
    console.log(1, 2);  // "3" 출력
    console.log(var);   // "1000" 출력
    // console.log(v);
~~~
> `as` 예약어로 모듈명을 변경할 수 있다.

- 만약 모듈 단위에서 export하는 값이 단일 값이라면 `default` 키워드를 이용하여 export할 수 있다.
~~~
    // module1
    let calc = {
        add(x, y) {
            return x+y;
        },
        multiply(x, y) {
            return x*y;
        }
    }
    
    export default calc;

    // module2
    import calc from './exam/exam2.js'
    
    console.log(calc.add);
~~~

### Promise
- 기존의 방법으로 비동기 처리를 수행할 때, 비동기 처리가 완료되면 콜백 함수가 호출되도록 작성하는 것이 일반적이었다.
그러나 [콜백 지옥](http://callbackhell.com)이라는 현상이 발생해 예외 처리가 힘들어진다.

- ES6에서는 `Promise` 객체를 지원해 비동기 처리를 깔끔하게 수행할 수 있다.
- 첫 번째 인자로 전달된 `resolve` 함수를 호출하면 `Promise` 객체의 `then` 메서드에 등록된 함수가 호출되고, 두 번째 인자로
전달된 `reject` 함수를 호출하면 `catch` 메서드에 등록된 함수가 호출된다.
~~~
    function timeout(duration = 0) {
        return new Promise((resolve, reject) => {
            setTimeout(resolve, duration);
        })
    }
    
    var p = timeout(1000).then(() => {
        return timeout(2000);
    }).then(() => {
        throw new Error("hmm");
    }).catch(err => {
        return Promise.all([timeout(100), timeout(200)]);
    })
~~~
> 소스 참고 ['ECMAScript 6에 대해 알아보자'](https://github.com/roylory/es6features-ko)

- `Fetch API`를 사용하면 `Request`나 `Resposne`와 같은 HTTP의 파이프라인을 구성하는 요소를 조작하는것이 가능하다.
보다 자세한 내용은 [MDN web docs](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Fetch%EC%9D%98_%EC%82%AC%EC%9A%A9%EB%B2%95)를 참고하자.
~~~
    let url = "https://example.com/search/sample;
    fetch(url)
        .then(response => response.blob())
        .then(blob => console.log(blob))
        .catch(e => console.log(e.message));
~~~

## References
---
1. 원형섭님 저서, Vue.js Quick Start
2. 나라얀 프루스티님 저서, ECMAScript 6 길들이기