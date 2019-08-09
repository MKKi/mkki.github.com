---
layout: post
title:  "[boostcourse] DB 연결 웹 앱"
date:   2019-07-15 02:57:11 +0900
background: '/img/posts/02.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

## JavaScript - FE
---
### 자바스크립트 변수-연산자-타입
자바스크립트의 타입은 **런타임**에 결정된다. `typeof`로 변수 타입을 확인할 수 있다.

`typeof` 키워드로 확인할 수 없는 경우에는 `Object.prototype.toString.call()`로 확인할 수 있다.
~~~javascript
Object.prototype.toString.call("foo");
> "[object String]"
Object.prototype.toString.call(15);
> "[object Number]"
Object.prototype.toString.call(undefined);
> "[object Undefined]"
~~~

`==` 연산자는 암시적으로 타입을 변경하기에, `===` 연산자를 사용하는 것이 낫다.

### 자바스크립트 비교-반복-문자열
**if 문** 대신에 **삼항연산자**와 **switch 문**을 이용하는 것도 코드 가독성면에서 더 좋을 수 있다.

반복문의 경우 **반대로 반복문을 동작시키는(reverse iteration)** 경우가 성능이 더 좋다고 한다.
> 브라우저의 자바스크립트 엔진이 반복문을 최적화하기 때문에, 성능차이는 미미하다고 한다.

`for-of`도 가능하지만 호환성 이슈가 있을 수 있다. `for-in`은 객체를 탐색할 때 사용하는 반복문이다.
> `for-in`은 **속성 이름**을 통해 반복, `for-of`는 **속성 값**을 통해 반복한다.

`forEach`는 `Array` 객체에서만 사용 가능한 메서드이다.
~~~javascript
var arr = [1, 2, 3];
arr.forEach(v => console.log(v));
> 1
> 2
> 3
~~~

### 자바스크립트 함수
할당되지 않는 변수는 `undefined`로 할당된다.

함수 표현 방법
- **함수선언문**
    - `function name()`와 같은 형태를 함수선언문이라 한다.
    - 호이스팅(Hoisting)의 영향을 받는다.
        > 호이스팅(Hoisting)이란, 변수와 함수 선언이 컴파일 타임에 메모리에 저장되는 것이다.
        따라서 사용자는 코드의 선언부가 끌어올려지는 것 처럼 느끼는 것이다.
- **함수표현식(function expression)**
    - `var name = function () {...}`
    
별도의 `return` 문이 존재하지 않으면 `undefined`가 반환된다.

### 자바스크립트 함수 호출 스택
자바스크립트에서 `null`, `undefined`, `white space`는 `false`이다.

자바스크립트는 **함수 스코프**를 따르고, 함수 선언 시 함수 스코프가 생성된다.

함수를 어디서 선언했는지에 따라 함수 스코프를 결정하는데, 이를 `Lexical Scope`라고 한다.
~~~javascript
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // 10
bar(); // 1
~~~

## WEB UI 개발- FE
---
### window 객체(setTimeout)
브라우저의 전역 객체이다. 디폴트 개념이기 때문에 생략할 수 있다.
~~~javascript
window.setTimeout(...);
setTimeout(...);
~~~

`callback function`은 즉시 시작되지 않고, 필요한 시점에 실행되는 함수이다.

`setTimeout()`은 비동기로 작동한다. `setInterval()`과 달리 일정 시간 이후 함수가 한 번만 실행된다.
> `setTimeout()`은 실행간 딜레이를 보장할 수 있다.

**비동기(asynchronous)**
- 프로그래밍 관점에서 애플리케이션의 흐름이 끊기지 않고 진행되는 것이다.
- **API Call**을 보낸 후 실행 여부와 상관없이 바로 응답을 받는다.
- **System Call**이 반환될 때, 실행된 결과와 함께 반환되지 않는다.
- 요청에 대한 처리 완료 여부와 상관없이 바로 응답한다.

자바스크립트는 단일 쓰레드(Single Thread)에서 이벤트 드리븐(Event-Driven) 방식으로 동작하지만,
**이벤트 루프(Event Loop)**를 사용하여 동시성(Concurrency)을 지원한다.

자바스크립트 엔진은 단순히 작업을 순차적으로 실행하기 위해 **호출 스택(Call Stack)**을 사용하고,
브라우저 또는 `Node.js`가 이벤트를 포함한 비동기 요청 처리를 담당한다.

**Event Queue(Task Queue)**
- 비동기 처리 콜백 함수와 이벤트 헨들러, Timer 함수의 콜백 함수가 보관되는 영역이다.

**이벤트 루프(Event Loop)**
- 호출 스택에 현재 실행 중인 작업이 있는지, Event Queue에 작업이 있는지 확인한다.
- 호출 스택이 비어있다면, Event Queue 내의 작업을 호출 스택으로 이동시키고 실행한다.

### DOM과 querySelector
브라우저는 `DOM(Document Object Model)` 객체로 HTML 코드를 저장한다.
이렇게 저장된 정보를 **DOM Tree**라고 한다.

브라우저에서는 DOM Tree를 쉽게 조작하기 위해 **DOM API**를 제공한다.
- `getElementById()`
- `querySelector()`
- `querySelectorAll()`
- `createElement()`

### Browser Event, Event object, Event handler
이벤트가 발생했을 때 실행되는 함수를 `Event Handler(Event Listener, Callback Function)`이라고 한다.

`Element.addEventListener()` 메서드로 이벤트를 등록할 수 있다.

### Ajax통신의 이해
**Ajax**는 페이지 내 컨텐츠 일부를 동적으로 갱신하기 위해 등장한 기술이다.
포멧으로 `JSON(Javascript Object Notation)`이 사실상 표준으로 자리잡았다.

`XMLHttpRequest`를 이용한 예
~~~javascript
function ajax(data) {
    var oReq = new XMLHttpRequest();
    oReq.addEventListener("load", function() {
        console.log(this.responseText);
    });    
    oReq.open("GET", "http://www.example.org/getData?data=data");//parameter를 붙여서 보낼수있음. 
    oReq.send();
}
~~~
> `Fetch API`로 쉽게 비동기 통신이 가능하다.

CORS(Cross Origin Resource Sharing)는 보안상의 이유로 다른 도메인으로부터의 요청을 제한하는 `XMLHttpRequest` 때문에 등장하였다.
    > 프로토콜, 포트, 호스트가 동일해야 동일 도메인이라는 **same-origin policy**을 따른다고 볼 수 있다.
    
CORS 요청은 4가지가 존재한다.
**Simple Request**
- `GET`, `HEAD`, `POST` 중의 한 가지 방식을 사용해야 한다.
- `POST` 방식일 경우 `Content-type`이 아래 셋 중의 하나여야 한다.
    - `application/x-www-form-urlencoded`
    - `multipart/form-data`
    - `text/plain`
- 커스텀 헤더를 전송하지 말아야 한다.

**Preflight Request**
- Simple Request 조건에 부합하지 않는 경우에 해당 방식으로 요청된다.
- `Access-Control-*` 계열의 **Response Header**를 정해주면 예비 요청과 본 요청의 처리를 서버가 처리해준다.

**Request with Credential**
- `Cookie`와 `HTTP Authentication` 정보를 인식할 수 있게 해주는 요청이다.
- 서버는 Response Header에 반드시 `Access-Control-Allow-Credentials: true`를 포함해야 하고, 
`Access-Control-Allow-Origin` 헤더 값으로 구체적인 도메인이 와야 한다.

**Request without Credential**
- CORS 요청은 **기본적으로 Non-Credential 요청**이다.
- `xhr.withCredentials = true`를 지정하지 않으면 Non-Credential 요청이다.

## JSP - BE
---
### JSP란?
스크립트 언어 중 하나로, 모든 `JSP`는 `Servlet`으로 바뀌어 동작한다.

### JSP 라이프싸이클
**JSP 실행순서**
- 브라우저가 서버에 요청
- 최초 요청 시에만
    - `JSP` 코드를 `Servlet` 코드로 변환
    - 서블릿 코드를 컴파일하여 **bytecode**로 변환
    - 서블릿 클래스를 로딩하고 인스턴스를 생성
- 서블릿이 실행되어 요청을 처리하고 응답

### JSP 문법
**선언문(Declaration)**은 `<%! %>`을 사용하여 전역 변수나 메서드 선언에 쓰인다.

**스크립트릿(Scriptlet)**은 `<% %>`을 사용하여 프로그래밍 코딩에 쓰인다.

**표현식(Expression)**은 `<%= %>`을 사용하여 뷰에 출력할 내용을 기술하는데 쓰인다.

주석은 `JSP`, `Java`, `HTML` 주석 모두 사용 가능하다.
> 코드가 JSP, Servlet(Java), HTML 순서를 거치기에 가능

### JSP 내장 객체
**JSP 내장 객체**는 `_jspService()` 내에 선언된 객체들이다.
> `request`, `response`, `pageContext`, `session`, `application`, `out`, `config`, `page`, `exception`이 존재

## redirect & forward - BE
---
### redirect
`HTTP Protocol`로 정해진 규칙이다.
서버가 요청을 받은 후, 클라이언트에게 특정 **URL**로 이동하라고 요청하는 것을 의미한다.
- 서버는 `302 state`, `Location` 헤더 값에 URL 정보를 담아 클라이언트에 응답한다.
- 클라이언트는 `Location` 헤더 값으로 재요청을 보낸다.

`Servlet/JSP`는 `HttpServletResponse.sendRedirect()`를 사용한다.

### forward
`Servlet/JSP`가 요청을 처리하다가, 추가적인 처리를 **동일 애플리케이션** 내의 `Servlet/JSP`에 위임하는 경우를 의미한다.
> **redirect**와의 가장 큰 차이점은 요청과 응답 객체의 갯수와 URL의 변경 유무

### servlet & jsp연동
비즈니스 로직을 `Servlet`으로 작성하여 `JSP`로 포워딩한 후 뷰를 작성하면 해당 환경에서의 개발이 편리해진다.


## scope - BE
---
### scope란?
`Servlet/JSP`에는 4가지 스코프가 존재한다.

- **Application**
    - 애플리케이션이 시작되고 종료될 때까지 변수가 유지되는 경우 사용
    - 모든 클라이언트가 사용해야하는 데이터가 있는 경우에 사용
- **Session**
    - 브라우저(클라이언트) 별로 변수가 관리되는 경우에 사용
    - 예로 장바구니나 로그인 정보를 유지하기 위해 사용
- **Request**
    - 요청을 받아 응답할 때까지 변수가 유지되는 경우에 사용
    - `forward` 시에 값을 유지하기 위해 사용
- **Page**
    - 페이지 내에서 지역변수처럼 사용
    - `forward` 시에는 새로운 **Page Context**가 생긴다.

## JSTL & EL - BE
---
### EL(Expression Language)
값을 표현하는데 사용되는 스크립트 언어로, `JSP`의 문법을 보완하는 역할을 한다.
> `${expression}` 처럼 사용

### JSTL(JSP Standard Tag Library)
`JSP`와 `HTML` 코드를 분리시키기 위해 등장한 기술이다. 커스텀 태그 문법이라 `JSP`에 비해 가독성이 높아진다.

다음과 같은 문법으로 사용한다.
~~~
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
    ...
    <c:set var="" scope="" value=""/>
~~~


## SQL - BE
---
### DB
**특징**
- 실시간 접근성(Real-time Accessability)
    - 사용자의 요구를 즉시 처리 가능
- 계속적인 변화(Continuous Evolution)
    - 정확한 값을 유지하려고 삽입/삭제/수정 작업 등을 이용해 데이터를 지속적으로 갱신 가능
- 동시 공유성(Concurrent Sharing)
    - 사용자마다 서로 다른 목적으로 사용하므로 동시에 여러 사람이 동일한 데이터에 접근 가능
- 내용 참조(Content Reference)
    - 저장한 데이터 레코드의 위치나 주소가 아닌 데이터 값에 따라 참조 가능    

### SQL이란?
데이터를 보다 쉽게 검색하고 추가, 삭제, 수정 같은 조작을 할 수 있도록 고안된 컴퓨터 언어이다.

특징은 다음과 같다.
- DML (Data Manipulation Language)
    - 데이터를 조작하기 위해 사용
    - `INSERT`, `UPDATE`, `DELETE`, `SELECT`
- DDL (Data Definition Language)
    - 데이터베이스의 스키마를 정의하거나 조작하기 위해 사용
    - `CREATE`, `DROP`, `ALTER`
- DCL (Data Control Language)
    - 데이터를 제어하는 언어입니다.
    - 권한을 관리하고, 테이터의 보안, 무결성 등을 정의
    - `GRANT`, `REVOKE`
    
권한은 다음과 같이 부여할 수 있다.
~~~sql
grant all privileges on db이름.* to 계정이름@'%' identified by ＇암호’;
grant all privileges on db이름.* to 계정이름@'localhost' identified by ＇암호’;
flush privileges;
~~~
- `@'%'`는 어떤 클라이언트에서든 접근 가능
- `@'localhost'`는 해당 클라이언트만 접근 가능

`\c`로 쿼리를 즉시 취소할 수 있다.

### DML
`SELECT` 구문에서 칼럼 합성은 `CONCAT()` 함수를 사용하면 된다.

`CAST(expression AS type)`, `CONVERT(expression, type)`으로 형변환도 가능하다.
~~~sql
select cast(now() as date);
select cast(1-2 as unsigned);
~~~

### DDL(create, drop)
칼럼의 길이가 수정하려고 하는 길이보다 크면 에러가 발생한다.

`CHAR`는 고정 길이이고, `VARCHAR`는 가변 길이이다.

`EUC-KR`에서 한글과 한자는 2바이트이고, `UTF-8`에서는 3바이트이다.

## JDBC - BE
---
**JDBC?**

`Java`로 개발된 애플리케이션에서 쿼리문을 실행하기 위한 **API**

`JDBC`는 인터페이스이고, 데이터베이스 벤더 별로 구현되어 있다.

`SQL Injection`을 막아주는 `PreparedStatement`를 쓰는 편이 좋다.

**Work Flow**
- `DriverManager`로 `Connection` 인스턴스 생성
- `Statement` 객체를 생성, 질의 수행
- `ResultSet` 객체를 생성

**용어**

`VO(Value Object)`는 **변하지 않는** 값을 위한 객체이다. 데이터베이스와 밀접하다.

`DTO(Data Transfer Object)`는 `VO`와 비슷하나 보다 협소한 범위로 쓰인다.
계층 간 데이터 전송이 목적이다. 예로 컨트롤러와 뷰의 로그인 정보 전달을 들 수 있다.

`DAO(Data Access Object)`는 데이터베이스의 데이터에 접근하기 위한 객체이다.

## WEB API - BE
---
**API?**
- 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스
- 사용자는 어떻게 구현되었는지 몰라도 기능을 사용할 수 있다.

**REST API?**
- `REST` 형식의 API를 의미한다.
- `REST API`들을 조합하여 만든 애플리케이션을 `매시업(Mashup)`이라고 한다.
- `REST`는 다음과 같은 스타일을 반드시 지켜야 한다.
    - client-server
    - stateless
    - cache
    - uniform interface
    - layered system
    - code-on-demand (optional)
- `HTTP Protocol`을 사용하면 **uniform interface**을 제외하고는 쉽게 구현할 수 있다.

**REST API 디자인 가이드**
- `URI`는 정보의 자원을 표현해야 한다.
- 자원에 대한 행위는 `HTTP Method(GET, POST, PUT, DELETE)`로 표현한다.

**Web API(HTTP API)**
- `REST`의 모든 것을 제공하지 않는 API를 의미한다.


## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [MDN - 루프와 반복](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Loops_and_iteration#for_%EB%AC%B8)
- Poiemaweb, [Poiemaweb](https://poiemaweb.com)
- w3c, [w3c](http://www.w3.org/)