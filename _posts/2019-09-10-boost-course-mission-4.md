---
layout: post
title:  "[boostcourse] 예약 시스템: 예약하기 - 1"
date:   2019-09-10 11:34:11 +0900
background: '/img/posts/01.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

---
이번 포스팅은 네 번째 미션인 예약 기능 구현의 **백엔드** 관련 내용이다.

[기획서](https://docs.google.com/presentation/d/1i2IC1yIH5ACFCvCH4EMVv_3Zw2oltRvHK94amyNEKbs/edit#slide=id.p11)의
15-19페이지에 해당하는 내용을 구현해야 했고, 세부 루브릭은 다음과 같다.

![mission-4-2](https://user-images.githubusercontent.com/28993371/64594357-d2907000-d3ea-11e9-8bf1-794aed412531.PNG)
![mission-4-1](https://user-images.githubusercontent.com/28993371/64594358-d2907000-d3ea-11e9-9f1e-bb93ae7519cb.PNG)

### 예약하기 구현
예약 데이터를 DB에 등록하기 위해서는 두 개의 테이블에 `insert`해야 했는데,
선행 트랜잭션의 결과 값으로 나온 `id` 값을 사용하기 위해 `SimpleJdbcInsert`의 `executeAndReturnKey()` 메서드를 사용했다.

기존 `NamedParameterJdbcTemplate`와는 다르게 생성시 테이블과 칼럼를 지정해줄 수 있다.

```java
public ReservationDao(DataSource dataSource) {
    this.simpleJdbcInsert = new SimpleJdbcInsert(dataSource)
                                    .withTableName("reservation")
                                    .usingColumns("reservation_email")
                                    .usingGeneratedKeyColumns("id");
}
```

파라미터 값으로 `Map`이나 `SqlParameterSource` 인터페이스를 사용할 수 있다.
스프링에서 제공하는 구현체는 `BeanPropertySqlParameterSource`와 `MapSqlParameterSource`가 있다.

```java
public int insert(Reservation reservation) {
    SqlParameterSource parameters = new BeanPropertySqlParameterSource(reservation);
    return simpleJdbcInsert.executeAndReturnKey(parameters).intValue();
}
```

제출한 프로젝트에서는 테이블 칼럼명이랑 DTO 필드명이 달라 `Map`으로 파라미터 값을 구성했고,
`yyyy.M.d` 타입인 날짜 데이터를 DB에 입력할 수 있는 포멧인 `yyyy-M-d` 형태로 바꿔주었다.

```java
public int insert(Reservation reservation) {
        Map<String, Object> paramMap = new HashMap<>();
        paramMap.put("reservation_date", reservationParam.getReservationYearMonthDay().replaceAll("\\.", "-"));

        return simpleJdbcInsert.executeAndReturnKey(paramMap).intValue();
    }
```

### Issue for Date type data
저장된 예약일 데이터가 실제로 들어온 값과 다른 이슈가 발생했다.
그렇지만 뷰에는 정상적인 값이 넘어갔고, 문제를 해결하지 못한채 리뷰어님께 질문할 수 밖에 없었다.

**serverTimezone**을 UTC 표준 시간으로 설정해놓아서 생긴 문제였다.

객체 매핑 시 기본적으로 서버의 **timezone**으로 설정해주어 뷰에서는 이상이 없었던 것이고,
데이터베이스 접속 url을 수정해주어 문제를 해결할 수 있었다.

### Server Side Validation
루브릭에는 없는 내용이었지만, 서버 사이드에서의 **Validation**을 구현해보았다.

스프링에서는 Servlet 2.3 표준 스펙 중 **JSR-303 Validator**를 확장하여 Validation을 제공한다.

먼저 프로젝트 의존성으로 `validation-api`, `hibernate-validator`를 추가하고,
Validation이 필요한 필드에 `@NotBlank`, `@Pattern`과 같은 Annotation을 적용해준다.


```java
public class Reservation {
    @NotBlank(message = "insert correct name")
    private String reservationName;
}
```

컨트롤러 파라미터에 `@Valid`를 붙여주면 넘어온 데이터의 Validation이 수행된다.

```java
@RestController
@RequestMapping(path = "/reservations")
public class ReservationController {
    @Autowired
    private ReservationService reservationService;

    @PostMapping
    public long create(@RequestBody @Valid Reservation reservation) {
        return reservationService.insert(reservation);
    }
}
```

### Exception Handling
Validation 에러 발생 시 해당 필드와 메시지를 전달하려면 `ResponseEntityExceptionHandler` 클래스의
`handleMethodArgumentNotValid()` 메서드를 오버라이딩하여 구현해야 한다.

```java
@ControllerAdvice
public class CustomGlobalExceptionHandler extends ResponseEntityExceptionHandler {
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid(MethodArgumentNotValidException ex, HttpHeaders headers, HttpStatus status, WebRequest request) {
        Map<String, Object> body = new LinkedHashMap<>();
        
        // TODO: implementation
        return new ResponseEntity<>(body, headers, status);
    }
}
```

`@ControllerAdvice`는 스프링에서 제공하는 예외 처리 핸들러이다.
별도의 패키지나 클래스 지정해두지 않으면, 모든 컨트롤러에 적용된다.
 
```java
@ControllerAdvice
@ControllerAdvice(basePackages = "my.sample.package")
@ControllerAdvice(basePackageClasses = MyController.class)
```

`@ExceptionHandler`는 컨트롤러 메서드에서 발생하는 예외를 핸들링해준다.

```java
@ControllerAdvice
@RequestMapping(produces = "application/vnd.error+json")
public class PersonControllerAdvice {

  @ExceptionHandler(PersonNotFoundException.class)
  public ResponseEntity<VndErrors> notFoundException(final PersonNotFoundException e) {
    return error(e, HttpStatus.NOT_FOUND, e.getId().toString());
  }
}
```

### LocalDate, LocalDateTime
`Date` 클래스는 다음과 같은 문제점을 가지고 있었다.
- **Mutable Object**이다. 즉, **Thread-safe** 하지 않다.
- 생성 비용이 큰 `Calendar` 클래스를 중간 객체로 사용해야 한다.
- `Calendar.JANUARY(= 0)`와 같은 혼동스러운 상수 필드가 남용된다.
이를 이용한 연산은 컴파일 타임에서 잡아낼 수 없다.

**Java 8**에서는 `Date` 클래스의 문제점을 보완한 `LocalDate`, `LocalDateTime` 클래스가 등장했다.

이번 미션에 현재 날짜부터 이후 5일까지의 날짜를 임의로 생성하고, 예약일로 설정하라는 요구사항이 있었다.
예약일 칼럼이 데이터베이스에서 `String` 타입이여서, `LocalDate`로 날짜를 생성 후 `Random` 객체로 생성된 정수를 더하고 포맷팅 해주었다. 

```java
private String createRandomDate() {
    int randomInt = (int) (Math.random() * 6);

    return LocalDate.now().plusDays(randomInt).format(DateTimeFormatter.ofPattern("yyyy.MM.dd"));
}
```

기존 DTO 클래스들의 날짜 필드를 `Date` 타입으로 해놓아서 문제가 있었는데,
앞으로는 `LocalDate`와 `LocalDateTime` 클래스를 사용하는 습관을 들여야겠다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- Outsider님 블로그, [[Spring 레퍼런스] 13장 JDBC를 사용한 데이터 접근 #2](https://blog.outsider.ne.kr/883)
- 창천향로님 블로그, [Spring Validation 공통모듈 만들기](https://jojoldu.tistory.com/129)
- Mkyoung.com, [Spring REST Validation Example](https://www.mkyong.com/spring-boot/spring-rest-validation-example/)
- SLiPP, [https://www.slipp.net/questions/600](https://www.slipp.net/questions/600)
- LANKY DAN BLOG, [Global exception handling with @ControllerAdvice](https://lankydan.dev/2017/09/12/global-exception-handling-with-controlleradvice)
- Oracle Docs, [LocalDate (Java Platform SE 8)](https://docs.oracle.com/javase/8/docs/api/?java/time/LocalDate.html)
- NAVER D2, [Java의 날짜와 시간 API](https://d2.naver.com/helloworld/645609)