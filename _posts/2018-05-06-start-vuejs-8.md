---
layout: post
title:  "[Vue.js Quick Start] ECMAScript 2015 - 3"
date:   2018-05-06 19:47:11 +0900
background: '/img/posts/06.jpg'
categories: [vue.js, js]
---

## 이터레이터
---
### 심볼
- 심볼은 ES6부터 적용된 새로운 `primitive` 타입이다. 심볼 값은 유일하며 변경할 수 없다.

- 리터럴 형식이 없고 `Symbol()` 함수로 생성하며, `description string`을 선택 인자로 줄 수 있다.

- `typeof` 연산자로 변수가 심볼인지 확인할 수 있으며, `new` 연산자로 생성이 불가능하다.
> 자바스크립트는 모든 `primitive type constructor`의 임의 호출을 금지한다.

- `Object.getOwnPropertyNames()`로는 심볼 프로퍼티 조회가 불가능하기에, `Object.getOwnPropertySymbols()` 메서드가 고안됐다.
> `for..in loof` 또한 하위 호환성 보장 때문에 객체에서 심볼 조회가 불가능하다.

- 심볼 객체는 키(description)-값(symbol) 쌍의 `레지스트리(registry)`를 갖고 있다.
`Symbol.for(string)` 메서드를 사용하면 심볼을 생성할 때 마다 레지스트리에 추가되며, 결과 값으로 심볼을 리턴한다.
> 전역 스코프에서 심볼을 생성하므로, `Symbol()` 보다 낫다. 

- ES6에는 `상용 심볼(well-known symbol)`이라는 내장 심볼 셋이 준비되어 있다. `@@iterator`와 같이 표기되어 구별하기 편리하다.

### 이터레이터 규약
- 이터레이터는 이터레이터 규약을 따르는 객체로, 다음 요소를 반환하는 `next()` 메서드를 구현해야 한다.
~~~
    let obj = {
        array: [1, 2, 3, 4, 5],
        nextIndex: 0,
        next() {
            return this.nextIndex < this.array.length ? { value: this.array[this.nextIndex++], done: false } : { done: true };
        }
    }
    
    console.log(obj.next().value);      // "1" 출력
    console.log(obj.next().value);      // "2" 출력
~~~

### 이터러블 규약
- 이터러블은 이터러블 규약을 구현한 객체로, 반드시 `@@iterator` 메서드를 제공한다. 즉, `Symbol.iterator` 심볼을 프로퍼티 키로 갖고 있으며
`@@iterator` 메서드는 항상 이터레이터 객체를 반환한다.
~~~
    let obj = {
        array: [1, 2, 3, 4, 5],
        nextIndex: 0,
        [Symbol.iterator]: function() {
            return {
                array: this.array,
                nextIndex: this.nextIndex,
                next: function() {
                    this.nextIndex < this.array.length ? { value: this.array[this.nextIndex++], done: false } : { done: true };
                }
            }
        }
    };
    
    let iterable = obj[Symbol.iterator]();
    console.log(iterable.next().value);      // "1" 출력
    console.log(iterable.next().value);      // "2" 출력
~~~

### 제너레이터
- `제너레이터(generator)` 함수는 한 번에 하나씩 여러 값을 반환하는 함수다. 함수 호출 시, 제너레이터 객체의 새 인스턴스를 반환한다.
- 제너레이터 객체는 제너레이터 함수의 실행 컨텍스트를 갖고, `next()` 메서드를 호출하면 `yield` 값을 반환한다. 다음 `next()` 메서드 호출 시,
중지 지점에서부터 다시 실행되어 다음 `yield` 값을 반환한다.
- 제너레이터 함수는 `function*`으로 표기한다.
~~~
    function* generator_function() {
        yield 1,
        yield 2,
        yield 3,
        yield 4,
        yield 5
    }
    
    const generator = generator_function();
    
    console.log(generator.next().value);    // "1" 출력
    console.log(generator.next().value);    // "2" 출력
    
    const iterable = generator[Symbol.iterator]();
    
    console.log(generator.next().value);    // "3" 출력
    console.log(generator.next().value);    // "4" 출력
    ...
~~~

- `return(value)` 메서드를 사용하여 도중에 하차할 수도 있다.
~~~
    function* generator_function() {
        yield 1;
        yield 2;
        yield 3;
    }
    
    var generator = generator_function();
    
    console.log(generator.next().value);          // "1" 출력
    console.log(generator.return(4).value);       // "4" 출력
    console.log(generator.next().done);     // "true" 출력
~~~

- `yield*` 키워드는 다른 이터러블 객체를 순회한 후 yield 할 수 있게 해준다.
~~~
    function* generator_function1() {
        yield 2;
        yield 3;
    }
    
    function* generator_function2() {
        yield 1;
        yield* generator_function1();
        yield* [4, 5];
    }
    
    var generator = generator_function2();
    
    console.log(generator.next().value);          // "1" 출력
    console.log(generator.next().value);          // "2" 출력
    console.log(generator.next().value);          // "3" 출력
    console.log(generator.next().value);          // "4" 출력
    console.log(generator.next().value);          // "5" 출력
    console.log(generator.next().done);           // "true" 출력
~~~

- `for...of` 루프는 이터러블 객체를 `next()` 메서드를 사용하지 않고 편하게 순회할 수 있게 도와준다.
~~~
    function* generator_function1() {
            yield 2;
            yield 3;
    }
        
    function* generator_function2() {
        yield 1;
        yield* generator_function1();
        yield* [4, 5];
    }
    
    for(let value of generator_function2()) {
        console.log(value);
    }
    
    let arr = [1, 2, 3]
    for(let value of arr) {
        console.log(value);
    }
~~~

### 꼬리 호출 최적화(Tail Call Optimization)
- 함수 끝에서 `return` 문에 함수를 호출하면 새로운 실행 스택을 만들지 않고 기존 스택을 재사용할 수 있게 해준다.
~~~
    "use strict";
    
    function add(x, y) {
        return x+y;
    }
    
    function add1(x, y) {
        x = parseInt(x);
        y = parseInt(y);
        
        // tail call
        return add(x, y);
    }
    
    function add2(x, y) {
        x = parseInt(x);
        y = parseInt(y);
        
        // not tail call
        return 0 + add(x, y);
    }
~~~

## 프록시
---
### 프록시란?
- 프로퍼티 탐색(lookup) 및 할당, 생성자 호출, 열거 등 객체의 기본 동작에 사용자 로직을 넣기 위해 사용하는 일종의 `Wrapper Object`이다.
객체를 프록시로 감싼 후에는 모든 처리를 프록시 객체를 대상으로 하기 때문에 원하는 작업을 넣을 수 있게 된다.

### 용어 정의
1. `target` 프록시로 감쌀 객체이다.
2. `trap` 타겟 객체의 동작을 가로채는 함수로, 사용자 임의의 동작을 부여한다. 트랩 내의 `this`는 항상 `handler`를 가리킨다.
3. `handler` 트랩에 있는 객체로 프록시 객체에 붙인다.

### 프록시 API
- 프록시는 `Proxy` 생성자로 생성하며, `target`과 `handler`를 인자로 받는다.
~~~
    var target = {
        age: 12
    };
    
    var handler = {};
    
    var proxy = new Proxy(target, handler);
    
    proxy.name = "foo";
    console.log(target.name);   // "foo" 출력
    console.log(proxy.name);    // "foo" 출력
    console.log(target.age);    // "12" 출력
    console.log(proxy.age);     // "12" 출력    
~~~

### 트랩
- `get(target, property, receiver)`, `set(target, property, value, receiver)`, `has(target, property)`, `isExtensible(target)` 등
여러 메서드를 적용할 수 있다.

- 다음은 `get` 메서드의 예이다.
~~~
    var proxy = new Proxy({
        age: 12
    }, {
        get(target, property, receiver) {
            if (property in target) {
                return target[property];
            }
            
            return "none";
        }
    };
~~~
> `receiver`는 접근하려는 프로퍼티가 위치한 객체의 참조값이다. `target.age`이면 `target`, `proxy.age`이면 `proxy`가 된다.

- 보다 자세한 내용은 [MDN web docs, 'Proxy'](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)를 참고하자.

## References
---
- 나라얀 프루스티님 저서, ECMAScript 6 길들이기