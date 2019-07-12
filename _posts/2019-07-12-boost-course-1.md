---
layout: post
title:  "[boostcourse] 웹 프로그래밍 기초"
date:   2019-07-12 17:38:11 +0900
background: '/img/posts/03.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

## Web 개발의 이해 - FE/BE
---
### 웹의 동작
부스트코스 웹 과정의 첫 파트로 웹 전반에 대한 내용이다. 올해 초에 학습했던 내용이 있어서 학습하는데 도움이 됐다.
- [URL & Resource](https://mkki.github.io/http/2018/01/29/http-the-definitive-guide-1-2.html)
- [HTTP Message](https://mkki.github.io/http/2018/01/29/http-the-definitive-guide-1-3.html)
- [HTTPS](https://mkki.github.io/http/2018/02/09/http-the-definitive-guide-1-14.html)

### Web FE/BE
`FE`는 사용자 친화적인 컨텐츠를 제공하며, 사용자의 요청에 반응하여 동작하는 역할을 한다.

`HTML`은 웹 컨텐츠의 구조를 표현해주며, `CSS`는 이에 대한 시각적인 부분을 담당한다.
`Javascript`는 사용자와 **소통**하기 위한 동적인 부분을 담당한다.

`BE`는 서버 입장에서 정보를 처리하고 저장하며, 요청에 따라 데이터를 내려주는 역할을 한다.
운영체제, 자료구조, 알고리즘, 네트워크와 같은 기초 CS 지식들이 선행되어야 학습에 수월하다.
> 운영체제는 KOCW에서 제공하는 [이화여대 반효경 교수님의 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323)를 추천합니다.

### 브라우저의 동작
브라우저에는 서버에서 전달된 데이터를 파싱하여 화면에 표시해주는 **렌더링 엔진**이 포함되어 있다.
> 파싱이란? 데이터를 토큰 단위로 나눈 후 `syntax tree`를 만드는 과정이다.

크롬의 경우 `크로미움(V8+Blink)`이라는 오픈소스 애플리케이션을 기반으로 만들어졌다.
> [v8 Engine의 Github](https://github.com/v8/v8), JS로 구현된 Merge Sort 소스를 참고했던 기억이 있다.

다음은 브라우저의 렌더링 엔진 처리과정이다. [Vue.js 첫 포스팅](https://mkki.github.io/vue.js/2018/04/27/start-vuejs-1.html)
에서 가상 DOM 관련 내용을 정리하면서 봤던 익숙한 그림이다.

![browser-work-flow](https://user-images.githubusercontent.com/28993371/61115462-7930c100-a4cd-11e9-9eb4-c56f7d2bd474.png)
> 브라우저의 Workflow

HTML과 CSS를 해석해서 `DOM Tree`와 `CSS Tree(CSS Object Model)`가 만들어지고, 다시 `Render Tree`로 다시 조합된다.
이를 바탕으로 `Painting` 과정을 거쳐 화면에 출력된다.
> 영상에서 참고된 **'How Browsers Work: Behind the scenes of modern web browsers'**는 [Naver D2](https://d2.naver.com/helloworld/59361)에 번역되어 있다.

브라우저 탐색 시 뷰에 변화가 생기면, `Render Tree`, `Layout` 재생성과 `Painting` 과정이 반복되어 화면에 결과가 출력된다.
> 결국, DOM Fragment 관리나 가상 DOM과 같은 기술이 웹 애플리케이션 성능에 큰 영향을 미친다고 볼 수 있다.

### HTML 개발 팁
- 렌더링 처리 시 브라우저가 빨리 참고할 수 있도록 JS 코드는 보통 `<body>` 태그가 닫히기 전에, CSS 코드는 `<head>` 태그 안에 위치시키는 것이 좋다.
- `<meta>` 태그는 브라우저에게 정보를 제공하기 위한 목적이다.

### 웹 서버와 WAS
웹 서버는 보통 소프트웨어를 말하지만, 웹 서버 소프트웨어가 동작하는 컴퓨터를 말한다.
> 웹 서버 소프트웨어는 `Apache`, `nginx` 등이 존재한다.
 
클라이언트는 보통 브라우저를 말하고, 클라이언트가 요청하는 리소스는 **정적인 데이터**이거나 **동적인 결과**가 될 수 있다.
> 즉, 서버는 서비스를 제공하고, 클라이언트는 서버에 서비스를 요청하여 결과를 출력한다.

`미들웨어(MiddleWare)`는 클라이언트의 비즈니스 로직을 줄이고, 관리 문제를 해결하기 위해 등장했다.
미들웨어는 클라이언트와 DBMS 사이에서 비즈니스 로직을 담당한다. 
> 미들웨어 등장 이전에는 클라이언트가 직접 DBMS에 연결하는 등의 방식을 사용했는데,
이는 클라이언트 프로그램의 거대화, 로직 변경 시 잦은 배포로 관리의 어려움, 보안적인 이슈를 야기했다.

`WAS(Web Application Server)`란 동적인 컨텐츠(프로그램이 동작해서 얻은 결과)를 제공

일종의 미들웨어로 웹 클라이언트의 요청 중 
- 실행 환경과 DB 접속 기능을 제공한다.
- 여러 개의 트랜잭션을 관리한다.
- 업무를 처리하는 비즈니스 로직을 수행한다.

### 웹 서버 vs WAS
- 웹 서버는 WAS보다 간단한 구조로 만들어져 있다.
- 오작동하는 WAS를 재시작할 시, 웹 서버가 앞 단에서 해당 WAS를 이용하지 못하도록 하여 사용자가 장애를 인지하지 못하게 한다.
> `장애 극복 기능(failover)`이라고 하며, 대규모 애플리케이션에서 무중단 서비스를 제공하는데 매우 중요한 역할을 한다.

## HTML - FE
---
### HTML Tag
태그는 의미에 맞춰서 사용하는 것이 중요하다. 개발하는데 문제가 없더라도, 
음성인식기 지원과 검색엔진 최적화를 위해 적절한 태그 사용이 중요하다.
> 이미지 태그의  `alt`, `title` 속성이 대표적이다. 

### Semantic Layout
화면의 레이아웃을 구성하는 태그들은 `header`, `section`, `nav`, `footer`, `aside` 등이 있다. 코드의 가독성과 검색엔진 최적화를 위해 사용된다.
> IE 하위 버전과의 호환성 이슈로 PC에서는 `<div class="header">` 형태로 사용되며, 모바일에서는 자주 사용된다.

### HTML 구조화 설계
개발을 시작하기 전에 화면의 큰 부분부터 분리하여 각 영역 안에 내용의 구조를 잡는 것이 좋다.
> 검색 키워드는 `html structure layout`

### class & id
`class`는 중복이 가능한 값, `id`는 고유한 값을 가져야한다. 
따라서, 유일한 UI에는 id 속성을 사용하고 일관성 있는 스타일을 적용하기 위해서는 class 속성을 사용하는 것이 낫다.
> 보통 팀에서 정해진 `Convention`을 따른다고 한다.

### Convention
Javascript은 [Airbnb Style Guide](https://github.com/airbnb/javascript)와 같이 유명한 가이드가 존재한다.

HTML의 경우 대소문자가 구별되지 않는다. W3C는 `kebab-case` 사용을 권장한다.
> `data-columns`와 같이 모두 소문자이고 하이픈으로 단어가 구분되는 룰이다. **Javascript**에서는 자동으로 `camelCase`로 변환된다.

`data-` 속성들은 표준이 아니지만 시멘틱한 HTML 요소에 추가 정보를 저장할 수 있도록 해준다. 또한, 순 HTML 속성이기에 CSS에서도 접근 가능하다.
> 자세한 내용은 [MDN docs](https://developer.mozilla.org/ko/docs/Learn/HTML/Howto/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%86%8D%EC%84%B1_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)을 참고

## CSS
---
### CSS 선언
CSS 선언 방법은 inline, internal external이 있고, inline이 가장 최우선적으로 적용된다. 
internal과 external은 우선순위가 동등하다. 따라서 나중에 선언된 속성이 반영된다.

internal은 구조와 스타일이 섞이게 되어 유지보수가 어려워지기에 추천되는 방법이 아니다. 가급적이면 external 방식으로 구현하는 것이 좋다.
> external은 `<link rel="stylesheet" href="...">`와 같은 방식이다.

동적인 CSS 변경은 별도의 Javascript 함수를 통해 구현 가능하다.
> 권장되는 방법인지는 모르겠으나, Vue.js에서도 인스턴스 메서드를 통해 동적으로 CSS를 변경했었다.

### 상속과 우선순위 결정
상위에 적용한 스타일은 하위에도 반영된다. 하지만 `box-model`이라고 불리는 속성들과 같이 크기/배치와 관련된 속성들은 하위 엘리먼트로 상속되지 않는다.
> `width`, `height`, `margin`, `padding`, `boarder`

우선 순위 결정의 특징(Cascading)
- `body > div`와 같이 구체적인 내용이 적용된다.
- id, class, element 순으로 우선 순위를 갖는다.
- 같은 선택자라면 나중에 선언한 것이 반영된다.

### CSS Selector
HTML 요소를 기본 선택자인 `tag`, `id`, `class` 등을 통해 쉽게 찾을 수 있다.

`nth-child`는 부모의 n번째 자식요소를 선택하고, `nth-of-type`은 같은 유형의 n번째 형제인 요소를 찾는다.

### CSS 기본 스타일
`font-size` 변경 시, `em` 단위는 기본 값의 배수를 의미한다. `font-family`에서 폰트는 선행된 폰트가 없을 시 나열된 순서대로 적용된다.

`background : #0000ff url(“.../gif”) no-repeat center top;`와 같이 `image`, `repeat`, `position` 등의 속성을 배경에 적용할 수 있다.

### CSS Layout
엘리먼트를 화면에 배치하는 것을 **Layout 작업**이라고 하고, **Rendering 과정**이라고도 한다.

엘리먼트는 위에서 아래로 순서대로 블럭을 이루며 배치되는 것이 기본이다.
다양한 배치를 위해 `CSS`에서는 다음과 같은 추가 속성을 제공한다.
- `display: block`, `display: inline-block`의 경우 엘리먼트는 블록처럼 아래로 쌓이듯이 채워진다. `div`, `p`가 block 태그다.
- `display: inline`은 오른쪽으로 쌓인다. `span`, `a`, `strong`이 inline 태그다.
- `position` 속성은 상대적/절대적으로 특정 위치에 엘리먼트를 배치시킬 수 있다. 기본은 `static` 이다.
- `position: absolte`는 **상위 엘리먼트의 position**이 기준점이다. `top`, `left` 를 꼭 주는것이 중요하다.
- `position: relative`는 원래 자신이 위치해야 할 곳을 기준으로 이동한다.
- `position: fixed`는 **viewport**의 좌측, 맨위를 기준으로 동작한다.
- `margin`은 상하좌우 엘리먼트와 본인간의 간격이다.
- `float`는 원래 flow에서 벗어나는 특별한 배치를 위해 사용된다. 예로 블록 엘리먼트를 좌우로 배치할 때 사용한다.
- `padding`은 엘리먼트와 컨텐츠 사이의 간격을 조절할 때 사용한다.

- `box-shadow`는 `border` 밖에 테두리를 그릴 수 있는 속성이다.

블록 엘리먼트의 크기는 기본적으로 부모의 크기를 가진다.
또한, 속성 값에 [여러 단축 표기법](https://developer.mozilla.org/ko/docs/Web/CSS/Shorthand_properties)을 사용할 수 있다.

전체 레이아웃 구현은 `float` 속성을 잘 사용하여 2단, 3단 칼럼 배치를 구현한다. 여기서 아래 블록이 올라오는 것을 막기위해 `clear` 속성을 사용한다.
또한, float 속성을 가진 엘리먼트는 자식 엘리먼트로 인식되지 않는데, `overflow: auto`를 주어 해결할 수 있다.

네비게이션 같은 엘리먼트는 block 엘리먼트에 `lnline-block`을 적용하여 가로로 배치하기도 한다.
> `bootstrap`과 같은 CSS 라이브러리에서 사용되는 `grid`나 `flex` 속성은 브라우저마다 지원되는 범위가 다르기에 확인하고 사용해야 한다.

## 개발환경 설정 - BE
---
### 환경변수 설정
JDK 설치 후 다음 3가지의 환경변수 설정이 필요하다.
- JAVA_HOME : JAVA가 설치된 경로를 지정
- CLASSPATH : JAVA 클래스가 있는 경로들을 지정
- PATH : JAVA 실행파일이 있는 경로를 추가

### WAS
톰캣은 서블릿 컨테이너의 구현체이다. 웹 서버에서 JSP를 요청하면 톰캣에서는 JSP를 서블릿으로 변환하여 컴파일을 수행하고,
서블릿의 수행 결과를 웹 서버에 전달한다.
> JSP를 실행할 수 있는 소프트웨어를 서블릿 컨테이너라 한다.

프로젝트 구조는 cmd에서 `tree /f`로 출력할 수 있다.

## Servlet - BE
---
### Servlet 이란?
`자바 웹 애플리케이션(Java Web Application)`는 WAS에 설치(deploy)되어 동작하는 애플리케이션이다. HTML, CSS, 이미지, 클래스, 각종 설정 파일 등이 포함된다.

서블릿은 자바 웹 애플리케이션의 구성 요소 중 **동적인 처리**를 하는 프로그램의 역할을 한다.
WAS에서 동작하는 자바 클래스이고, `HttpServlet` 클래스를 상속받는다.
 
### Servlet 작성 방법
Servlet 3.1 버전 이상부터는 `어노테이션(Annotation)`을 사용하여 URL을 매핑한다.

### Servlet Life Cycle
HttpServlet은 `init()`, `service()`, `destroy()` 3가지 메서드를 오버라이딩한다.

WAS가 서블릿 요청을 받으면, 서블릿 클래스가 메모리에 있는지 확인한다. 만약 없다면, 메모리에 로드하기 위해 생성자를 통해 서블릿 객체를 생성하고 `init()` 메서드를 호출한다.
애플리케이션이 종료되면 `destroy()` 메서드가 호출되고 서블릿이 소멸되고, 서블릿 클래스의 내용이 수정되었을 때 소멸 후 객체가 다시 생성된다.

`HttpServlet`에 `service()` 메서드를 템플릿 메서드 패턴으로 구현되어 있다.

### Request, Response 객체 이해하기
WAS는 브라우저로부터 서블릿 요청을 받으면, `HttpServletRequest`, `HttpServletResponse` 객체를 생성하고 서블릿에게 전달한다.

`HttpServletRequest`는 헤더 정보, 파라미터, 쿠키 등의 정보를 읽어들이는 메서드를 가지고 있다. 또한, **Request Body의 Stream**을 읽어들이는 메서드도 가지고 있다.
> `GET` 메서드로 요청 시 쿼리스트링으로 넘어오는 것이 파라미터이다.

`HttpServletResponse`는 요청을 보낸 클라이언트로 응답을 보내기 위해 생성되는 객체이다. Content-Type, 응답 메시지 등을 전송한다.

`getRemoteAddr()` 메서드는 클라이언트의 주소 값을 가져온다.

## References
---
- edwith, [boostcourse: Full-Stack Web Developer](http://www.edwith.org/boostcourse-web/joinLectures/12952)
- HTTP: The Definitive Guide