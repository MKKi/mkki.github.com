---
layout: post
title:  "[IntelliJ] Tomcat context.xml 생성과 JSTL 이슈 해결"
date:   2018-04-05 15:13:21 +0900
background: '/img/posts/04.jpg'
categories: intellij
---

### server.xml 생성
---
이클립스와 달리 인텔리제이는 톰캣의 **context.xml**이 없다.

![tomcat-issue](/img/tomcat-issue-1.PNG)
`Project Structrue - Modules - (모듈 선택) - web - Add Application Server specific descriptor...`
순서로 톰캣의 context.xml을 생성해 줄 수 있다.

생성된 파일은 `%PROJECT_ROOT%/web/META-INF`에 존재한다.

### JSTL 이슈 해결
---
`JSTL` 라이브러리 추가 후 톰캣이 다음과 같은 오류를 뿜어냈다.
> HTTP Status 500 - The absolute uri: http://java.sun.com/jsp/jstl/core cannot be resolved 
in either web.xml or the jar files deployed with this applicationJ-jstl-maven/

`%ORACLE_HOME%\product\..\lib`에 **jstl.jar** 파일을 넣어주니 해결되었다.

StackOverflow 검색 결과 사용자 환경에 따라 발생할 수 있다고 하는데 정확한 이유는 모르겠다.
> 개발 환경은 `Tomcat 8.5.16`, `IntelliJ IDEA 2017.5.6`, `Oracle 12c`


### Reference
---
OKKY daejoon 님의 답변, [https://okky.kr/article/281970](https://okky.kr/article/281970)
