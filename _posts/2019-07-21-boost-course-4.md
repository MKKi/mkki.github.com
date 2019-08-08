---
layout: post
title:  "[boostcourse] 웹 앱 개발:예약서비스 1/4 - 2"
date:   2019-07-21 00:30:11 +0900
background: '/img/posts/04.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

## Spring Core - BE
---
### Spring이란?
**Spring Framework**
- 모듈화가 잘 되어 있는 프레임워크
- `IoC(Inversion of Control)` 컨테이너이다.
- 선언적으로 트랜잭션을 관리할 수 있습니다.
- 완전한 기능을 갖춘 `MVC Framework`를 제공합니다.
- AOP 지원합니다.
- 스프링은 도메인 논리 코드와 쉽게 분리될 수 있는 구조로 되어 있습니다.

![framework-modules](https://cphinf.pstatic.net/mooc/20180201_180/1517452205302mNfIy_PNG/2_10_1___.png)

### Spring IoC/DI 컨테이너
`컨테이너(Container)`는 인스턴스의 생명주기를 관리하고, 추가적인 기능을 제공하는 제공한다.
> `WAS(Tomcat)`는 `Servlet Container`를 가지고 있고,
`Servelt` 인스턴스 생생과 `JSP`를 `Servelt`으로 바꾸는 추가적인 기능을 수행한다.

`IoC(Inversion of Control)`란 컨테이너가 코드 대신 오브젝트 제어권을 갖고 있는 것을 의미한다.
> 예로 `Servlet` 클래스는 개발자가 작성하지만, 메서드에 알맞게 호출하는 것은 `WAS`가 수행한다.

`DI(Dependency Injection)`이란 클래스 사이의 의존 관계를 컨테이너가 자동으로 연결해주는 것을 의미한다.

**Spring IoC/DI Container**
- `BeanFactory` : IoC/DI에 대한 기본 기능을 가지고 있습니다.
- `ApplicationContext` : BeanFactory의 모든 기능을 포함하며, 일반적으로 BeanFactory보다 추천됩니다. 트랜잭션처리, AOP등에 대한 처리를 할 수 있습니다. BeanPostProcessor, BeanFactoryPostProcessor등을 자동으로 등록하고, 국제화 처리, 어플리케이션 이벤트 등을 처리할 수 습니다.
- `BeanPostProcessor` : 컨테이너의 기본로직을 오버라이딩하여 인스턴스화 와 의존성 처리 로직 등을 개발자가 원하는 대로 구현 할 수 있도록 합니다.
- `BeanFactoryPostProcessor` : 설정된 메타 데이터를 커스터마이징 할 수 있습니다.

**Spring Bean Scope**
- 스프링은 기본적으로 **싱클톤**으로 빈을 생성하여 관리한다. 
    > 일반적으로 아는 `Singleton Pattern`과 다르다. 자세한 내용은 [링크](http://blog.daum.net/rollin/8097082) 참고
- `singleton`: `IoC Container`내에 하나의 빈 객체만을 가진다.
- `prototype`: 하나의 빈 정의에 여러 객체가 존재할 수 있다.
- `global session`: `global HTTP Session`의 생명주기 내에 하나의 빈 객체만 존재한다.
- `request`: `HTTP request`의 생명주기 내에 하나의 빈 객체만을 가진다.
- `session`: `HTTP Session`의 생명주기 내에 하나의 빈 객체만을 가진다.
- `application`: `ServletContext`의 생명주기 내에 하나의 빈 객체만을 가진다.
- **동기화 비용이 객체 생성 비용보다 큰 경우**에만 비싱글톤 객체를 사용한다.

**Java Annotation**;
- `comfile time`, `runtime`에 처리될 수도 있다.
- `AnnotationConfigApplicationContext`가 `JavaConfig` 클래스를 읽어들여 **IoC와 DI를 적용**한다.
- `@Configuration`
    -스프링 설정 클래스를 선언하는 어노테이션
- `@Bean`
    - `bean`을 정의하는 어노테이션
- `@ComponentScan`
    - `@Controller`, `@Service`, `@Repository`, `@Component` 어노테이션이 붙은 클래스를 찾아 컨테이너에 등록
- `@Component`
    - 컴포넌트 스캔의 대상이 되는 애노테이션 중 하나로써 주로 유틸, 기타 지원 클래스에 붙이는 어노테이션
- `@Autowired`
    - 주입 대상이되는 `bean`을 컨테이너에 찾아 주입하는 어노테이션
    - `Constructor Injection`을 사용하기를 권장한다. `null` 값 방지, 생성자 파라미터로 의존관계 파악, `final` 키워드로 `immutable`하게 관리 가능
        ~~~
            private final Engine engine;
            
            @Autowired
            public Car(Engine engine) {
                this.engine = engine;
            }
        ~~~
                
## Spring JDBC - BE
---
**Spring JDBC**
- `Connection` 오픈, `Statement` 연결 등 저수준 세부사항을 프레임워크가 처리해준다.

**ConnectionPool**
- `Database Connection`은 비용이 높은 작업이다. 따라서 미리 커넥션을 여러 개 맺어 두고 저장해두는 곳이 `커넥션 풀(Connection Pool)`이다.

**DataSource**
- `커넥션 풀(Connection Pool)`을 관리하는 목적으로 사용되는 객체이다. 커넥션을 얻고 반납하는 작업을 한다.

**JPA vs Mybatis**
- `JPA(Java Persistence API)`는 자바 ORM 기술에 대한 API 표준 명세를 의미한다. 구현체인 `Hibernate`가 많이 쓰인다.
- `ORM(Object Relational Mapping)`은 객체와 테이블을 매핑시켜주는 역할을 한다.
생산성이 높고 `JDBC`를 노출시키지 않으나, 복잡한 쿼리를 표현하는데 한계가 있다.
- `Mybatis`는 `DAO`와 `Table` 간의 강한 의존성을 갖고 있으며, 객체지향적이지 못하다는 의견이 커지며 최근 사용이 줄어들고 있는 추세이다.
- 각각 장단점이 명확하기에 **제공하는 서비스**에 따라 선택적으로 적용하는 것이 좋다.
    > 실습에 사용된 `Spring JDBC Template`는 개인적으로 매우 별로이다. 개인적으로 `ORM`을 추천한다.

## Spring MVC - BE
---
### Spring MVC란?
**MVC**
- `Model-View-Controller`의 약자
- `Model`
    - 뷰가 렌더링하는데 필요한 **데이터**
- `View`
    - 웹 애플리케이션에서 실제로 보이는 부분
- `Controller`
    - 사용자의 액션에 응답하는 컴포넌트
    - 모델을 업데이트하고, 다른 액션을 수행

**MVC Model 1 Architecture**
- `JSP`를 사용하는 환경에서 요청만큼 `JSP Page`가 존재한다.
- `Java Bean(DAO Class 등)`을 사용하여 DB에 접근하고, `JSP Page`를 응답한다.
- `HTML` 코드와 `Javascript` 코드가 혼합되어 있는 단점이 있다.

**MVC Model 2 Architecture**
- `MVC Model 1 Architecture`의 단점을 보완하고자 등장하였다.
- `Controller(Servlet)`이 요청과 데이터를 처리하고, `View(JSP Page)`가 결과를 보여주는 역할을 한다.
- 보다 발전된 형태는 요청을 받고 처리 결과를 `View Template`로 넘겨주는 `Front Controller`,
요청을 위임 받아 처리하는 `Controller`, 요청에 대한 처리 결과로 뷰를 생성하는 `View Template`으로 구성되어 있다.
    > 보통 이와 같은 구조를 `Spring MVC`이라 하고, `Web Module`에 구현되어 있다.

### Spring MVC 구성요소
![spring-mvc-work-flow](https://cphinf.pstatic.net/mooc/20180219_116/1519003779294ejdEx_PNG/1.png)

**DispatcherServlet**
- `Front Controller`이다. 클라이언트의 요청을 받아 이를 처리할 핸들러에게 넘기고, 처리 결과를 받아 응답 결과를 보여준다.
- 요청이 들어오면 선처리 작업을 하고, `HandlerExecutionChain`을 결정한다. 예외가 없다면 뷰 렌더링을 처리한다.
- 선처리 작업에서는 다음과 같은 작업을 수행한다.
    - `Locale`를 결정하고 `Thread Local Object`에 요청을 저장한다. 
    - **redirect**를 위한 `FlashMap`을 복원한다.
    - `Mutipart Request`를 처리한다.
    

**Front Controller Setting**
- `DispatcherServlet`을 `Front Controller`로 등록하기 위해 `WebApplicationInitalizer` 인터페이스를 구현하여 사용하거나, `web.xml` 파일에 설정한다.
    > Spring MVC 5.x 버전에서는 `Java Configuration`이 권장된다.
- `@EnableWebMvc`는 웹에 필요한 빈을 자동으로 설정해주며, 기본 설정 이외의 설정은 `WebMvcConfigurerAdapter`를 상속한 `Java Configuration`을 작성하면 된다.

## 레이어드 아키텍처(Layered Architecture) - BE
---
중복되는 로직을 별도의 `Service` 객체에 구현하여 컨트롤러에서는 이를 사용하도록 한다.
> 서비스 객체란, `Business Logic`을 수행하는 메서드를 가지고 있는 객체를 의미하고, 하나의 비즈니스 로직은 하나의 트랜잭션으로 동작한다.

**트랜잭션(Transaction)**
- 하나의 논리작인 작업을 뜻한다.
- 특징은 크게 4가지로 구분된다.
    - `원자성(Atomicity)`
        - 전체가 성공하거나 실패하는 것을 의미
    - `일관성(Consistency)`
        - 트랜잭션의 작업 처리 결과가 일관성 있어야 한다는 것을 의미
        - 트랜잭션 진행 중 데이터가 변경되어도 첫 참조 데이터로 트랜잭션을 수행
    - `독립성(Isolation)`
        - 실행되는 트랜잭션 간 서로의 연산에 끼어들 수 없음을 의미
    - `지속성(Durability)`
        - 트랜잭션이 성공적으로 완료되면 결과는 영구적으로 반영되는 것을 의미
- `JDBC`에서는 `auto commit` 사용을 중지하고 완료시에만 `commit()` 메서드를 호출한다.
- 스프링에서는 `@EnableTransactionManagement`를 사용한다.

**Layered Architecture**
- `Presentation Layer`에서는 `Controller` 객체가 동작
- `Service Layer`에서는 `Service` 객체가 동작
- `Repository Layer`에서는 `DAO` 객체가 동작
- 설정은 다음과 같이 구성한다.
    - `Presentation Layer(Controller)` 관련 내용은 `DispatcherServlet`이 읽도록 한다.
    - 그 이외의 설정은 `ContextLoaderListener`가 읽도록 한다.
        > `DAO`, `DB Connection`, `DataSource` 관련 설정 등
    - `DispatcherServlet`이 2개 이상 설정되면 각각 독립적이기에 빈을 공유할 수 없다.
        > `ContextLoaderListener`를 사용하여 빈을 공유할 수 있도록 할 수 있다.
    - `ContextLoaderListener`가 생성하는 `ApplicationContext`가 `Root Context`가 된다.

## Controller - BE
---
**Rest Controller**
- **REST API** 혹은 **Web API**를 구현하기 위한 컨트롤러이다.
- `Spring 3`에서는 `@Controller`와 `@ResponseBody`를 이용하여 REST API, Web API를 구현했다.
- `Spring 4`부터 `@Controller`와 `@ResponseBody`를 포함하는 `@RestController`가 등장했다.
- 만약 `Content-Type: application/json`으로 요청이 들어오면,
`DispatcherServlet`은 내부적으로 `JsonMessageConvert`가 리턴 객체를 `JSON` 포멧으로 변환하여 전송한다.

**Message Converter**
- `Java Object`와 `HTTP Request/Response body`를 변환하는 역할을 한다.
- `@EnableWebMvc`에 의해 기본 설정으로 제공된다.
    > `JSON` 포멧을 사용하기 위해서는 `Jackson` 라이브러리를 사용한다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- slipp, [spring - 6주차 Bean Scope](https://www.slipp.net/wiki/pages/viewpage.action?pageId=25528177)
- 글돌님 블로그, [Spring의 Singleton과 Java static기반 Singleton 패턴의 차이](http://blog.daum.net/rollin/8097082)
- victolee님 블로그, [[Spring JPA] ORM과 JPA 그리고 Hibernate](https://victorydntmd.tistory.com/195)