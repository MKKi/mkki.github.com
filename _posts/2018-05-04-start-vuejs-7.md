---
layout: post
title:  "[Vue.js Quick Start] ECMAScript 2015 - 2"
date:   2018-05-04 21:26:11 +0900
background: '/img/posts/05.jpg'
categories: [vue.js, js]
---

## 새롭게 추가된 라이브러리
---
### Number 객체의 새로운 프로퍼티와 메서드
- 숫자 앞에 `0b`를 붙이면 2진수, `0o`를 붙이면 8진수로 표기 가능하다.
~~~
    let a = 0b00001111;
    console.log(a);       // "15" 출력
    
    let b = 0o17
~~~

- `Number.isNaN()`은 숫자가 아닌 값은 `true` 그 외엔 `false`를 리턴한다.
~~~
    console.log(Number.isNaN(NaN));   //true
    console.log(Number.isNaN(0 / 0));   //true
    console.log(Number.isNaN(Number.isNaN));   //true
~~~

### String 객체의 새 메소드
- 유니코드 캐릭터셋의 모든 캐릭터는 `코드 포인트(code point)`라는 10진수 숫자로 나타낸다.  
`코드 유닛(code unit)`은 코드 포인트를 저장할 메모리상의 **고정 비트 수**이며, `인코딩 스키마(encoding schema)`의 길이에 따라 그 길이가 결정된다.
코드 유닛과 맞지 않는 코드 포인트는 여러 코드 유닛으로 쪼개진다.
> 예를 들어 UTF-8의 코드 유닉은 8비트이고, UTF-16은 16비트이다. 

- 자바스크립트는 기본적으로 UTF-16 코드 유닛으로 표현한다. 코드의 인코딩 스키마가 UTF-8이면 자바스크립트 엔진이 UTF-8 코드 유닛으로 해석하도록 지시한다.
따라서 자바스크립트의 유니코드 캐릭터는 `65536(2^16)` 이하의 코드 포인트를 갖는다. `\u`를 붙여 이스케이프 할 수 있으며, 다음에는 16진수 캐릭터 코드 4개가 온다.
~~~
    var \u0061 = "\u0061\u0062\u0063";
    console.log(a);     // "abc" 출력
~~~

- ES5 이전에는 저장 공간이 16비트 이상인 캐릭터를 이스케이프하려면 유니코드 2개가 필요했다. 예를 들어, `\u1F680`을 문자열에 추가하려면 `\uD83D\uDE80`와 같이 이스케이프해야 했다.
이처럼 하나의 캐릭터를 표현하기 위해 붙인 2개의 유니코드를 `써로게이트 페어(surrogate pair)`라 한다. ES6부터는 써로게이트 페어 없이도 16비트 이상인 캐릭터를 표현할 수 있고, 이 문자열의 길이는 2다.
~~~
    console.log("\u1F680");     // "🚀" 출력
~~~

- `length` 프로퍼티는 써로게이트 페어를 무시하고 무조건 16비트를 하나의 캐릭터로 간주한다. `==` 연산자 역시 2진수 단위로 비트 수를 대조하기에 써로게이트 페어를 무시한다.
`[]` 연산자도 매 16비트가 인덱스여서 마찬가지다.
> 이를 해결하는 방법이 `normalize()` 메서드이다. 써로게이트 페어를 16비트 캐릭터로 정규화 시켜주기에, 내부적으로 문자열을 비교/검색할 수 있게 해준다.

- `repeat(count)` 메서드는 문자열을 원하는 갯수만큼 복사하여 연결된 문자열을 반환한다.
~~~
    console.log("a".repeat(5));     // "aaaaa" 출력
~~~

- `includes(string, index)` 메서드는 주어진 문자열이 있는지 찾아보고 `true/false`로 리턴한다.
~~~
    let str = "hello world";
    console.log(str.includes("world"));     // "true" 출력
~~~
> 특정 인덱스 다음부터 찾고 싶으면 두 번째 파라미터에 인덱스 값을 준다.

### 템플릿 문자열
- `String.raw` 태그 함수를 사용하면 템플릿 문자열의 원래 모습이 출력된다.
~~~
    let s = String.raw `xy\nz`
    console.log(s);     // "xy\nz" 출력
~~~

- 문자열 처리를 함수를 따로 정의하는 경우, 템플릿 문자열을 `Tagged Template String`이라 하고, 처리 함수를 `Tag Function`이라 한다.
~~~
    let tag = function(strings, ...values) {
        let result = "";
        
        for(let i = 0; i<strings.length; i++) {
            result += strings[i];
            
            if(i < values.length) {
                result += values[i];
            }
        }
        
        return result;
    };
~~~

- 이를 통해 함수 표현식을 콜백시킬 수 있는 값으로 사용할 수 있다. 기존 템플릿 표현식에서 함수 표현식은 단순히 `String` 취급될 것이다.
~~~
    const myFunction = () => {
      return 'Returned from myFunction!';
    };
    
    const templateResult = `Function expression in template: ${() => myFunction()}`;
    console.log(templateResult); //Outputs -> Function expression in template: () => myFunction()
    
    const myTag = (literals, func) => {
      return literals[0] + func();
    };
    
    const taggedResult = myTag `Function expression in template: ${() => myFunction()}`;
    console.log(taggedResult); //Outputs -> Function expression in template: Returned from myFunction!
~~~
> 소스 출처 - ['ES6 Tagged Template Literals'](https://medium.freecodecamp.org/es6-tagged-template-literals-48a70ef3ed4d)

### 배열
- `Array.from(iterable, mapFunc, this)` 메서드는 이터러블 객체에서 새 배열 인스턴스를 생성하는 메서드이다.
~~~
    let str = "0123";
    let obj = { num: 1 };
    let arr = Array.from(str, function(value) {
        return parseInt(value) + this.num;
    }, obj);
    
    console.log(arr);       // "[1, 2, 3 ,4]" 출력
~~~
> `String`, `TypedArray`, `Array`, `Map`, `Set`은 이터러블 객체이다.

- `Array.of(values...)` 메서드는 인자 값을 유일한 원소로 하는 배열을 생성한다.
~~~
    let arr1 = new Array(2);    // 길이가 2인 빈 배열 생성
    let arr2 = new Array.of(2);     // 원소의 값이 2이고, 길이가 1인 배열 생성
~~~
> 배열 인스턴스를 동적으로 생성할 때, 원소의 타입이나 갯수를 알 수 없다면 `Array.of()`를 사용하자.

- `find(testingFunc, this)` 메서드는 테스트 함수에 만족하는 배열 원소를 리턴하며, 만족하지 않으면 `undefined`를 리턴한다.

- `findIndex(testingFunc, this)` 메서드는 `find()`와 비슷하지만, 원소 대신 인덱스를 반환한다.

- `entries()` 메서드는 인덱스를 키로, 원소를 값으로 키-값 쌍으로 묶어 이터러블 객체로 반환한다. `keys()` 메서드는 인덱스 키를 이터러블 객체로 반환한다.
`values()`는 원소 값을 이터러블 객체로 반환한다.
> 리턴 값은 배열 객체가 아니다.

- `타입화 배열(TypedArray)`은 일반 배열을 다루는 것 처럼 `배열 버퍼(ArrayBuffer)` 객체에 읽기/쓰기할 수 있게 해준다. 배열 버퍼 객체의 `wrapper`로 볼 수 있다.
~~~
    var buffer = new ArrayBuffer(80);   // 80바이트 배열 버퍼 생성
    var typed_array = new Float64Array(buffer);     // 64비트 부호가 있는 소수를 처리하는 타입화 배열 생성
    typed_array[4] = 1l;
    
    console.log(typed_array.length);    // "10" 출력
    console.log(typed_array[4]);    // "1l" 출력
~~~

## 컬렉션
- `WeakSet`과 `WeakMap`은 객체 참조값만 저장할 수 있고, 저장된 객체가 참조하는 값이 없을 땐 **가비지 컬렉션 대상**이 된다. 또한, 열거형이 아니라 크기를 알 수 없다.
> 즉, 키 목록을 제공해주는 메서드가 없다는 뜻이다. `private`한 데이터를 제공하기 위해 사용된다. 
- 다음 예에서 모든 인스턴스가 하나의 `WeakMap`을 공유하기에 메모리 낭비를 막을 수 있다.
~~~
    // use let keyword for private props
    function Person(name) {
      let age = 20; // this is private
      this.name = name; // this is public
      this.greet = function () {
        // here we can access both name and age
        console.log(`name: ${this.name}, age: ${age}`);
      };
    }
    let joe = new Person('Joe');
    joe.greet();
    // here we can access name but not age


    // use WeakMap
    let Person = (function () {
      let privateProps = new WeakMap();
      class Person {
        constructor(name) {
          this.name = name; // this is public
          privateProps.set(this, {age: 20}); // this is private
        }
        greet() {
          // Here we can access both name and age
          console.log(`name: ${this.name}, age: ${privateProps.get(this).age}`);
        }
      }
      return Person;
    })();
    let joe = new Person('Joe');
    joe.greet();
    // here we can access joe's name but not age
~~~
> 소스 출처 - [StackOverflow, 'Private properties in JavaScript ES6 classes'](https://stackoverflow.com/questions/22156326/private-properties-in-javascript-es6-classes)

### 객체
- 자바스크립트 객체는 자신이 상속한 객체를 참조하기 위해 내부에 `[[prototype]]` 프로퍼티를 둔다. 이 값은 직접 읽거나 수정이 불가능하다.
그렇기에 `Object.getPrototypeOf()` 메서드로 읽거나, 동일한 `[[prototype]]`으로 객체를 생성하려면 `Object.create()` 메서드를 사용해야만 했다.

- ES6에서는 프로토타입을 수월하게 다루고자 `[[prototype]]` 프로퍼티 대신에 `__proto__` 프로퍼티를 표준으로 제정했다. 
`__proto__` 프로퍼티는 `Prototype Link`라고도 불린다. 객체가 생성될 때 조상이었던 함수의 `Prototype Object`를 가리킨다.
> 모든 객체는 함수이다. 함수가 정의 될 때, `constructor` 자격이 주어지고 `Prototype Object` 생성 및 연결이 이루어진다.


## References
---
1. 나라얀 프루스티님 저서, ECMAScript 6 길들이기
2. MDN web docs, ['Keyed Collections'](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Keyed_collections) 