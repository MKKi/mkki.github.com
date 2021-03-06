---
layout: post
title:  "[HTTP: The Definitive Guide] 10. HTTP/2.0"
date:   2018-02-06 15:44:22 +0900
background: '/img/posts/03.jpg'
categories: http
---
## HTTP/2.0의 등장 배경
---
HTTP/1.1의 메시지 포맷은 `구현의 단순성`과 `접근성`에 주안점을 두고 최적화되었다. 그러다 보니 성능은 어느정도 포기할 수 밖에 없었다. 

커넥션 하나를 통해 요청 하나를 보내고 응답 하나 만을 받는 HTTP의 메시지 교환 방식은 단순하지만,
응답을 받아야만 다음 요청을 보낼 수 있기에 심각한 `회전 지연(latency)`을 피할 수 없었다.

이를 위해 `병렬 커넥션`이나 `파이프라인 커넥션`이 도입되었지만, 성능 개선에 대한 근본적인 해결책으론 무리였다.
> 커넥션을 언제 끊어야하는지 명확한 기준과 설명이 없다

2009년, 구글은 `SPDY` 프로토콜을 내놓았다. 기존 HTTP의 속도를 개선하고자 여러 기능을 추가한 것이다.

SPDY는 헤더를 `압축`하여 대역폭을 절약했고, 하나의 TCP 커넥션에 **여러 요청을 동시에 보내** 회전 지연을 줄였다.
또한, 클라이언트가 요청을 보내지 않아도 서버가 능동적으로 리소스를 `푸시`하는 기능도 갖추고 있다.
> 이 모든 기능은 `회전 지연`을 줄이기 위한 것이다.

2012년 10월 3일, `HTTP 작업 그룹`은 `SPDY`를 기반으로 `HTTP/2.0` 프로토콜을 설계하기로 결정하였다.
크게 변경된 점은 헤더를 압축할 때 더 이상 `deflare` 알고리즘을 사용하지 않게 되었다는 것 정도다.

자세한 내용은 2015년 5월에 공개된 [RFC 7540][RFC 7540]을 참고하면 된다.

## 개요
---
HTTP/2.0은 서버와 클라이언트 사이의 `TCP 커넥션` 위에서 동작한다. 이때 TCP 커넥션을 `초기화`하는 것은 클라이언트이다.

HTTP/2.0의 요청과 응답은 길이가 `최대 16383바이트`로 정의된 한 개 이상의 프레임에 담기며, 헤더는 압되어 담긴다.
프레임들에 담긴 요청과 응답은 `스트림`을 통해 보내진다. 한 개의 스트림이 한 쌍의 요청과 응답을 처리한다.

하나의 커넥션 위에 여러 개의 스트림이 동시에 만들어질 수 있으므로, 여러 개의 요청과 응답을 `동시에` 처리하는 것이 가능하다.

HTTP/2.0은 이들 스트림에 대한 `흐름 제어`와 `우선순위 부여` 기능도 제공한다. 기존의 요청-응답과는 약간 다른 새로운
상호작용 모델인 `푸시 서버`를 도입했다.
> `푸시(push)` 기법이란, 어떤 요청이 중앙 서버에서 시작되는 정보 전달 방식이다.  
> 이를 통해 서버는 클라이언트에게 필요하다고 판단하는 리소스를 능동적으로 보내줄 수 있다.

HTTP/2.0은 기존 웹 애플리케이션들과 호환성을 최대한 유지하고자, 요청과 응답 메시지의 의미를 `HTTP/1.1`과 같도록
유지하고 있다.
> `Content-Length` 헤더는 `HTTP/2.0`에서도 그 의미가 같으나, 이름은 `:content-length`가 되었다.
> 상태줄을 통해 표현하던 `404 Not Found`는 `404` 값을 가진 `:status` 헤더로 바뀌었다.

## HTTP/1.1과의 차이점
---
프레임
: `HTTP/2.0`에서 모든 메시지는 `프레임`에 담겨 전송된다. 모든 프레임은 `8바이트` 크기의 헤더로 시작하며,
뒤이어 최대 16383바이트 크기의 `페이로드`가 온다.
> HTTP/2.0에는 `DATA`, `HEADERS`, `PIORITY`, `RST_STREAM`, `SETTINGS`, `PUSH_PROMIS`, `PING`, `GOAWAY`, `WINDOW_UPDATE`,
`CONTINUATION`이라는 총 10개의 프레임이 정의되어 있다.

스트림과 멀티플렉싱
: `스트림`은 HTTP/2.0 커넥션을 통해 클라이언트와 서버 사이에서 교환되는 프레임들의 `독립된 양방향 시퀀스`다.

: 한 쌍의 요청과 응답은 하나의 스트림으로 이루어진다. 클라이언트는 새 스트림을 만들어 HTTP 요청을 보낸다.
서버는 그 요청과 같은 스트림으로 응답을 보내고, 스트림은 닫힌다.

: HTTP/1.1은 `회전 지연`을 줄이고자 여러 개의 `TCP 커넥션`을 만들어 여러 요청을 보낸다. 하지만 이것으로
회전 지연을 피하기는 어렵다.

: HTTP/2.0에서는 하나의 커넥션에 **여러 개**의 스트림이 동시에 열릴 수 있다. 따라서 이 문제는 `HTTP/2.0 커넥션`을
통해 해결될 수 있다.

: 또한, 스트림은 `우선순위`도 가질 수 있다. 예를 들어 사용자가 웹 브라우저로 특정 웹 페이지를 보려고 할 때,
 `네트워크 대역폭`이 충분하지 않아 프레임의 전송이 **느리다면**, 브라우저는 `보다 중요한 리소스`를 요청하는 스트림에게
 높은 우선순위를 부여한다.
 > 우선순위에 따라는 것이 의무사항은 아니기에, 요청이 우선순위대로 처리된다는 보장은 없다.

모든 스트림은 `31비트의 무부호 정수`로 된 고유한 `식별자`를 갖는다. 스트림이 클라이언트에 의해 초기화되었다면,
이 식별자는 반드시 `홀수`여야 하며 서버라면 `짝수`여야 한다.

또한, 새로 만들어지는 스트림의 식별자는 이전에 만들어졌거나 `예약된 스트림`들의 식별자보다 **커야 한다.**

이 규칙을 어기는 식별자를 받았다면 에러코드가 `PROTOCOL_ERROR`인 커넥션 에러로 응답해야 한다.

서버와 클라이언트는 스트림을 상대방과 협상 없이 `일방적`으로 만든다. 이는 협상을 위해 `TCP 패킷`을 주고받느라
시간을 낭비하지 않아도 됨을 의미한다.

`HTTP/2.0 커넥션`에서 한 번 사용한 스트림 식별자는 **다시** 사용할 수 없다. 커넥션을 오래 사용하다보면 스트림에
할당할 수 있는 식별자가 `고갈`되기도 하는데, 그런 경우에는 `커넥션`을 다시 맺으면 된다.

이처럼 동시에 여러 개의 스트림을 사용하면 스트림이 `블록`될 우려가 있다. HTTP/2.0은 `WINDOW_UPDATE` 프레임을
이용한 `흐름 제어(flow control)`을 통해 스트림들이 서로 간섭해서 망가지는 것을 막아준다.

헤더 압축
: HTTP/1.1에서 헤더는 아무런 `압축` 없이 그대로 전송되었다. 하지만 웹 페이지의 규모가 커짐에 따라 `헤더의 크기`가
`회전 지연`과 `대역폭` 양쪽 모두에 실질적인 영향을 끼치게 되었다.

: 이를 개선하고자 HTTP/2.0에서는 HTTP 메시지의 `헤더`를 `압축`하여 전송한다. 헤더는 [HPACK 명세][HPACK]에
정의된 헤더 압축 방법으로 압축된 뒤, `헤더 블록 조각`들로 쪼개져서 전송된다. 받는 쪽에서는 이 조각들을
이은 뒤 압축을 풀어 헤더 집합으로 복원한다.

: `HPACK`은 헤더를 압축하고 해제할 때 `압축 컨텍스트(compression context)`를 사용한다. 따라서 오작동하지 않으려면
항상 올바른 압축 컨텍스트를 유지해야 한다.

: 압축 컨텍스트는 수신한 헤더의 압축을 풀면 이에 영향을 받아 바뀐다. 따라서 헤더를 받은 수신 측은 반드시 `압축 해제`를
수행해야 한다. 만약 그럴 수 없으면, `COMPRESSION_ERROR`와 함께 커넥션을 끊어야 한다.

서버 푸시
: HTTP/2.0은 서버가 하나의 요청에 대해 응답으로 `여러 개의 리소스`를 보낼 수 있게 해준다.
이 기능은 클라이언트가 어떤 리소스를 요구할 것인지 **미리 알 수 있는 상황**에서 유용하다.

: 리소스를 푸시하려는 서버는 먼저 클라이언트에게 리소스를 푸시할 것임을 `PUSH_PROMISE` 프레임을 보내어 미리 알려주어야 한다.
클라이언트가 `PUSH_PROMISE` 프레임을 받으면, 해당 프라임의 스트림은 클라잉이언트 입장에서 `예약됨(원격)` 상태가 된다.

: 이 상태에서 클라이언트는 `RST_STREAM` 프레임을 보내어 푸시를 거절할 수 있다. 이후 해당 스트림은 즉시 닫히게 되며,
닫히기 전까지 클라이언트는 서버가 푸시하려고 하는 리소스를 요청해서는 안 된다.

다음은 `서버 푸시`를 사용할 때의 주의사항이다.
- 중간의 `프록시`가 서버로부터 받은 추가 리소스를 클라이언트에게 **전달하지 않을 수도 있다.** 반대로 서버가 아무런 리소스를
보내지 않았더라도 클라이언트에게 추가 리소스를 **전달할 수도 있다.**

- 서버는 `안전`하고 `캐시`가 가능하며, `본문`을 포함하지 않은 요청에 대해서만 푸시를 할 수 있다.

- 푸시할 `리소스`는 클라이언트가 보낸 `요청`과 연관이 있어야 한다. 서버가 보내는 `PUSH_PROMISE` 프레임은 원 요청을 위해
만들어진 `스트림`을 통해 보내진다.

- 클라이언트는 서버가 푸시한 리소스를 [동일 출처 정책(same-origin policy)][same-origin policy]에 따라 검사해야 한다.
> 예를 들어, `example.org`로의 HTTP/2.0 커넥션은 `www.example.org`로부터의 푸시 응답을 허용하지 않는다.

- 서버 푸시를 끄고 싶다면, `SETTING_ENABLE_PUSH`를 0으로 설정하면 된다.

## 알려진 보안 이슈
---
중개자 캡슐화 공격(Intermediary Encapsulation Attacks)
: HTTP/2.0 메시지를 `프록시(중개자)`가 HTTP/1.0 메시지로 변환할 때, 메시지의 의미가 변질될 가능성이 있다.
HTTP/1.0과 달리 HTTP/2.0은 `헤더 필드`의 이름과 값을 `바이너리`로 인코딩한다.

: 이는 HTTP/2.0이 헤더 필드로 `어떤 문자열`이든 사용할 수 있게 해준다. 다행히 `HTTP/1.1 메시지`를 `HTTP/2.0 메시지`로
번역하는 과정에서 이런 문제가 발생하지 않는다.

긴 커넥션 유지로 인한 개인정보 누출 우려
: HTTP/2.0은 사용자가 요청을 보낼 때의 `회전 지연`을 줄이기 위해 클라이언트아 서버 사이의 커넥션을 **오래 유지**하는 것을
선호한다. 이것은 `개인 정보의 유출`에 악용될 가능성이 있다.

: 예를 들어 어떤 사용자가 브라우저를 사용할 때, 이전의 그 브라우저를 사용했던 사용자가 **무엇을 했는지 알아낼 가능성**도 있다.
이것은 `HTTP`의 고질적인 문제이기도 하지만, 짧게 유지되는 커넥션에서는 위험이 적다.

## Reference
---
HTTP: The Definitive Guide


[same-origin policy]: https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy
[HPACK]: https://http2.github.io/http2-spec/compression.html
[RFC 7540]: https://tools.ietf.org/html/rfc7540