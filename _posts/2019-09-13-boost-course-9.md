---
layout: post
title:  "[boostcourse] 웹 앱 개발: 예약서비스 4/4 - 2"
date:   2019-09-13 04:05:22 +0900
background: '/img/posts/03.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

### Logging
**로그(Log)**는 성능에 관한 통계 정보를 제공해주고, 디버깅에 도움을 준다.

콘솔에 로그를 생성하는 것은 다음과 같은 단점이 있다.
- 로그의 양이나 수준을 설정할 수 없다.
- 로그를 파일에 저장하기 불편하다.
- 성능이 떨어진다.

그렇기에 로깅 라이브러리를 사용하는 편이 좋고, 스프링은 기본적으로 **commons-logging**을 사용한다.

**SLF4J(Simple Logging Facade for Java)** 다양한 로깅 라이브러리들을 감싸는 추상화된 **인터페이스**이다.
어떤 라이브러리를 사용하던 참조만 바꿔주면 정상적으로 하기 때문에, **logback**이나 **log4j2**와 같은 로깅 라이브러리와 함께 사용된다.

#### Appender
**Appender**란, 어디에 어떤 **포맷**으로 로그를 남길 것인지에 대한 방법을 제공하는 것이다.

대표적으로 다음과 같은 종류가 있다.
- **ConsoleAppender**는 콘솔에 로그를 어떤 포맷으로 출력할지를 설정한다.
- **FileAppender**는 파일에 로그를 어떤 포맷으로 출력할지를 설정한다.
- **RollingFileAppender**는 하루 단위로 로그를 관리하고자 할 경우 사용된다.
   
#### Log Level
다음 로그 레벨은 큰 순서대로이며, 각 레벨별 설명은 다음과 같다.
- `TRACE`는 모든 메세지를 나타낸다.
- `DEBUG`는 프로그램을 디버깅하기 위한 메세지를 나타낸다.
- `INFO`는 상태변경과 같은 정보성 메세지를 나타낸다.
- `WARN`은 처리 가능한 문제이거나 향후 시스템 에러의 원인이 될 수 있는 경고성 메세지를 나타낸다.
- `ERROR`는 요청을 처리하는 중 문제가 발생한 경우의 메세지를 나타낸다.
- `FATAL`은 아주 심각한 에러가 발생하여 애플리케이션 작동이 불가능할 경우의 메세지를 나타낸다.

#### Log Name
Logger는 `LoggerFactory.getLogger()` 메서드로 인스턴스를 반환 받아 생성된다.
보통 클래스 이름을 파라미터로 넘겨주기 때문에 Logger의 이름은 `package.className` 형태로 구성된다.
> Lombok을 사용하면 Annotation으로 이를 대체할 수 있다.

이렇게 생성된 Logger는 Tree Hierarchy 구조로 level을 적용 받으며,
**root logger**는 이름이 `"""`인 Logger이다.

### File Upload
`HttpServletRequest`는 클리이언트로부터 전달 받은 multipart 데이터를 처리하는 메서드를 제공하지 않는다.
> request body를 읽어들이는 `InputStream`만이 존재한다.

그렇기 때문에 파일 업로드를 처리하는 라이브러리가 필요하다. 대표적인 라이브러리는 **commons-fileupload**이다.

`DispatcherServlet`은 `multipart/form-data`로 요청이 들어올 경우 `MultipartResolver`를 사용한다.

컨트롤러에서의 다운로드 처리를 위해서는 여러 헤더가 설정되어야 한다.
강의에서는 다음과 같은 설정을 통해 파일 정보와 캐시를 사용하지 않는다는 정보를 헤더에 추가한다.

```java
response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\";");
response.setHeader("Content-Transfer-Encoding", "binary");
response.setHeader("Content-Type", contentType);
response.setHeader("Content-Length", fileLength;
response.setHeader("Pragma", "no-cache;");
response.setHeader("Expires", "-1;");
```

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- spring boot docs, [Part IV. Spring Boot features - 26. Logging](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-logging.html)