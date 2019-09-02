---
layout: post
title:  "[boostcourse] 웹 앱 개발: 예약서비스 3/4 - 2"
date:   2019-09-02 03:15:11 +0900
background: '/img/posts/03.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

### 상태 정보
`HTTP(Hypertext Transfer Protocol)`는 상태가 유지되지 않는 프로토콜이다.

현재 상태에 따라 클라이언트가 서버에 요청을 보내고 서버가 응답하는 네트워크 유형이다.
서버가 여러 요청의 세션 정보나 상태를 유지할 필요가 없다.

그렇기에 서버 설계는 단순화되고, 세션 정보를 추적할 필요가 없기에 자원을 아낄 수 있다.

이와 반대 개념인 **Stateful Protocol**로는 `FTP(File Transfer Protocol)`, `Telnet`이 있다.
클라이언트가 요청하고 서버가 응답하는 과정은 유사하나, 응답이 없으면 요청을 다시 보낸다.
> **TCP Session**은 수명주기 동안 정보를 유지하므로 **Stateful Protocol**을 따른다고 볼 수 있다.

#### Cookie
쿠키는 `HTTP`에서 상태 정보를 유지하기 위해 사용되는 기술이다.

key-value 쌍으로 구성되어 있고, 브라우저마다 다르지만 `4kb`정도의 데이터를 저장할 수 있다.
쿠키는 클라이언트에 저장되며, **매 요청마다 전송된다.**
> `Domain`, `Path`, `Expires/Max-age`, `Secure`, `HttpOnly` 속성도 저정된다.

실제 서비스에서는 자동 로그인, 팝업 보지 않기, 이전 스크롤링 값 저장 등에 사용된다.

쿠키의 종류는 두 가지로 나뉜다.
- **Session Cookie**는 만료일이 따로 정해지지 않은 쿠키로 브라우저가 종료되면 제거된다.
- **Permanent Cookie**는 `Expires/Max-age` 속성 값이 부여된 쿠키로 브라우저가 종료되도 유지된다.

**Spring MVC**에서는 `@CookieValue` 애너테이션을 사용하면 쿠키 정보를 파라미터에 담아 사용할 수 있다.

```java
@Controller
public class TestController {
    public String testCookie(@CookieValue(value="TEST_COOKIE", required=false, defaultValue="hello") String testCookie) {
        return "index";
    }
}
```

#### Session
세션은 보안이 취약한 쿠키의 단점을 개선하고자 등장하였다. **일정 기간동안** 들어오는 클라이언트 요청을 하나의 상태로 보고 유지하는 기술이다.
즉, 웹에서 클라이언트인 브라우저가 종료될 때까지 상태가 유지된다.
> **상대적으로** 안전할 뿐이지, 세션도 쿠키를 이용한 기술이기에 보안적인 조치가 필수적이다.

세션은 다음과 같이 동작한다.
1. 클라이언트가 첫 요청을 보낸다.
2. 서버는 쿠키 값에 **session id**가 있는지 확인하고, 없다면 발급하여 응답한다.
3. 다음 요청부터는 **session id** 값이 포함된 쿠키가 헤더에 포함된다.
4. 서버는 이를 통해 클라이언트를 식별한다.

**Spring MVC**에서는 `@SessionAttribute` 애너테이션을 사용하여 세션을 사용할 수 있다.

컨트롤러 위에 사용될 경우 `@ModelAttribute` 애너테이션으로 같은 이름의 객체에 세션 값을 설정할 수 있다.

```java
@Controller
@SessionAttribute("member")
public class MemberController {
    @PostMapping("/add")
    public String addMember(@ModelAttribute("member") Member member) {
        return "redirect:/";
    }
}
```

메서드에 `@SessionAttribute` 애너테이션을 사용하여 파라미터로 지정된 세션 정보를 읽어올 수도 있다.

```java
@Controller
public class MemberController {
    @PostMapping("/add")
    public String addMember(@SessionAttribute("member") Member member) {
        return "redirect:/";
    }
}
```

내장 타입인 `SessionStatus`를 사용하여 세션 객체를 제거할 수도 있다.

```java
@Controller
@SessionAttribute("member")
public class MemberController {
    @PostMapping("/add")
    public String addMember(@ModelAttribute("member") Member member, SessionStatus sessionStatus) {
        sessionStatus.setComplete();
    }
}
```

#### Cache
브라우저 캐시는 쿠키와 같이 브라우저에 데이터를 저장한다는 점에서 유사하지만,
**빠른 렌더링과 네트워크 자원 절약**이라는 목적에서 명확한 차이점이 존재한다.
> `asset`, `.js`, `.css` 데이터를 캐시하여 페이지 리로드 시 빠른 렌더링을 지원한다던가,
> 비디오, 오디오 파일을 캐시하여 네트워크 자원을 아낀다던가 하는 예시가 있다.

캐시는 위치에 따라 다음과 같이 분류된다.
- **Browser Cache**는 웹브라우져 혹은 HTTP 요청을 하는 클라이언트 어플리케이션들이 내부적으로 갖고있는 캐시이다.
- **Proxy Cache**는 실제 서버가 아닌 네트워크 상에 설치되는하는 캐시다. 일반적으로 **ISP**의 방화벽에 설치된다.
다수의 사용자들에게 공유되고, 레이턴시와 트래픽을 줄이는데 매우 도움이된다.
- **Gateway Cache(Reverse Proxy Cache)**는 실제 서버 앞단에 설치되며, 캐시와 요청 분배를 통해 응답 성능과 확장성을 높여준다.
로드 밸런서를 사용해서 요청을 실제 서버가 아닌 Gateway Cache로 라우팅한다. **CDN**이 대표적인 예이다.

### 인터셉터
![spring-request-lifecycle](https://user-images.githubusercontent.com/28993371/64099627-b5352380-cda4-11e9-914b-9d0fa47a3d9b.jpg)
> Spring MVC request life-cycle

**인터셉터(Interceptor)**는 `DispatcherServlet`와 `Handler` 사이에서 동작한다. 
**Servlet WebApplicationContext**에 등록하여 사용할 수 있다. **HTTP Protocol 단위**의 인증, 권한 처리에 사용된다.

**필터(Filter)**는 `DispatcherServlet` 앞 단에서 클라이언트 요청과 응답 전에 처리해야될 로직을 수행한다.
**Root WebAppliactionContext**에 등록하여 사용할 수 있다. 인코딩, 보안 관련 처리에 사용된다.

이 외에도 둘의 차이점은 예외 처리다. 필터는 루트 컨텍스트에서 에러를 처리한다. `WAS`에서 에러 페이지를 출력해주거나,
예외 처리를 미루어야한다. 그러나 인터셉터의 경우 컨텍스트 내에서 예외를 처리할 수 있다. 

**Spring MVC**에서 인터셉터를 사용하는 방법은 다음과 같다.
- `org.springframework.web.servlet.HandlerInterceptor` 인터페이스를 구현하거나,
`org.springframework.web.servlet.handler.HandlerInterceptorAdapter` 클래스를 상속 받는다.
- `WebMvcConfigurerAdapter` 클래스의 `addInterceptors()` 메서드를 오버라이딩하고 인터셉터를 등록한다.

### 아규먼트 리졸버
`아규먼트 리졸버(Argument Resolver)`는 컨트롤러 메서드의 파라미터 값으로 임의의 값을 전달할 수 있도록 도와준다.

**Spring MVC**에서 아규먼트 리졸버를 사용하는 방법은 다음과 같다.
- `org.springframework.web.method.support.HandlerMethodArgumentResolver` 인터페이스를 구현한다.
- `supportsParameter()` 메서드를 오버라이딩하여 타입을 체크해주고, `resolveArgument()` 메서드를 오버라이딩하여 로직을 구현한다.
- `WebMvcConfigurerAdapter` 클래스의 `addArgumentResolvers()` 메서드를 오버라이딩하고 아규먼트 리졸버를 등록한다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- GeeksforGeeks, [Difference between Stateless and Stateful Protocol](https://www.geeksforgeeks.org/difference-between-stateless-and-stateful-protocol/)
- letmecompile, [HTTP Cache 튜토리얼](https://www.letmecompile.com/http-cache-튜토리얼/)
- supawer0728님 블로그, [(Spring)Filter와 Interceptor의 차이](https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/)