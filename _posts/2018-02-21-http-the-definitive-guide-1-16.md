---
layout: post
title:  "[HTTP: The Definitive Guide] 16. 국제화"
date:   2018-02-21 09:48:11 +0900
background: '/img/posts/03.jpg'
categories: http
---

## 국제적인 컨텐츠를 다루기 위해 필요한 HTTP 지원
---
HTTP 메시지는 어떤 언어로 된 컨텐츠든 다른 종류의 미디어 처럼 실어 나를 수 있다. 엔터티 본문은 그저 `비트`들로 이루어진
상자에 불과하다.

국제 컨텐츠를 지원하기 위해 서버는 클라이언트에게 문서의 `문자`와 `언어`를 알려준다. 
서버는 이를`HTTP Content-Type charset` 매개변수와 `Content-Language` 헤더를 통해 알려준다.

동시에 클라이언트는 서버에게 사용자가 어떤 언어를 이해할 수 있고, 
어떤 알파벳의 코딩 알고리즘이 `브라우저`에 설치되어 있는지 말해주어야 한다. 
클라이언트는 `Accept-Charset`과 `Accept-Language` 헤더를 통해 이를 알려준다.

## 문자 집합과 HTTP
---
#### 캐릭터셋(Charset)은 글자를 비트로 변환하는 인코딩이다
HTTTP 캐릭터셋 값은 어떻게 엔터티 컨텐츠 비트들을 특정 문자 체계의 글자들로 바꾸는지 말해준다.

각 `캐릭터셋 태그`는 비트를 글자로 변환하거나 혹은 반대의 일을 수행해주는 알고리즘을 명명한다.
> 캐릭터셋 태그는 등록된 MIME 문자집합에 표준화 되어 있고, IANA가 관리한다.

`UTF-8`과 `iso-2022-jp` 문자 인코딩은 글자 당 비트 수가 일정하지 않아 더 복잡한 `가변길이 코드`다.
이는 중국어나 일본어 같이 많은 글자로 이루어진 문자체계를 지원하기 위해 추가적인 비트를 사용할 수 있게 해준다.

#### 문자집합과 인코딩은 어떻게 동작하는가
비트들을 문자로 디코딩하는 것은 다음과 같이 두 단계에 걸쳐 일어난다.
- 문서를 이루는 비트들은 코딩된 문자집합의 특정 문자로 식별될 수 있는 `문자 코드`로 변환된다.
- 문자 코드는 코딩된 문자집합의 특정 요소를 선택하기 위해 사용된다.
`iso-8859-6`에서 값 255는 `ARABIC LETTER FEH`에 해당한다.

국제화된 문자 시스템의 핵심 목표는 `시각적 표현 방식`에서 `의미(글자들)`를 분리하는 것이다.

다시 말해, HTTP는 문자 데이터와 관련된 언어, 캐릭터셋의 전송에만 관심을 갖는다. 글자의 모양을 어떻게 표현하는지는
사용자의 그래픽 디스플레이 소프트웨어에게 맡긴다.

#### 표준화된 MIME 캐릭터셋 값
특정 문자 인코딩과 코딩된 문자집합의 **결합**을 `MIME 캐릭터셋`이라고 부른다.

등록된 캐릭터셋 목록은 [IANA][charset-list]에서 확인하자.

#### Content-Type charset 헤더와 META 태그
웹 서버는 클라이언트에게 MIME 캐릭터셋 태그를 `charset` 매개변수와 함께 `Content-Type` 헤더에 담아 보낸다.

만약 문자집합이 명시되지 않았다면, 수신자는 문서의 컨텐츠로부터 문자집합을 추측하려 시도한다.
HTML 컨텐츠에서 문자집합은 `<META HTTP-EQUIV="Content-Type>` 태그에서 찾을 수 있다.

#### Accept-Charset 헤더
HTTP 클라이언트는 서버에게 원하는 문자 인코딩 목록을 `Accept-Charset` 요청 헤더를 통해 알려준다.
서버는 이 목록 중 원하는 것으로 반환할 수 있다.

그러나 이에 대응하는 `Content-Charset` 응답 헤더는 존재하지 않는다. 응답 문자집합은 `MIME`과의 호환을 위해
`Content-Type` 응답 헤더의 `charset` 매개변수를 통해 서버로부터 돌려받는다.

## 다중언어 문자 인코딩에 대한 지침
---
#### 문자
문자는 쓰기의 기본적인 구성요소다. 하나의 문자는 하나의 알파벳 글자, 숫자 등 **쓰기의 기본 단위**를 표현한다.

문자는 글꼴이나 스타일에 `독립적`이다. 어떤 언어는 같은 글자라도 그 위치에 따라 다른 모양을 갖는 표기 체계도 많다.

#### 글리프(glyphs), 연자(ligatures) 그리고 표현 형태
`글리프`는 각 글자를 그리는 특정한 방버비다. 각 문자는 양식과 스크립트에 따라 여러가지 글리프를 가진다.

만약 글리프 하나를 다른 것으로 바꾸었을 때 텍스트의 의미가 바뀐다면, 두 글리프는 서로 다른 글자이다.
> 의미와 표현의 명확한 분리를 위해 이러한 상황을 피해야 한다.

`연자`는 필기체에서와 같이 인접 글자들이 부드럽게 이어지도록 하기 위해 사용된다.

#### 코딩된 문자집합(Coded Character Set)
코딩된 문자집합은 보통 코드 번호로 인덱싱된 `배열`로 구현되며, 이를 글자에 대응시킨다.
다음은 몇 가지 중요한 코딩된 문자집합이다.

- `US-ASCII` 아스키는 가장 유명한 코딩된 문자집합이다. 오직 코드 값 `0-127`만 사용하며, 코드 공간 전체를 표현하는데
`7비트`가 필요하다. HTTP 메시지의 헤더와 URI 등에서 사용된다.

- `iso-8859` 문자집합 표준은 국제적인 글쓰기를 위해 필요한 글자들을 **하이 비트**를 이용해서 추가한
`US-ASCII`의 `8비트` 확대집합들이다. 모든 유럽의 글자들을 담기에 충분하지 않아 지역 별로 커스터마이징된
문자집합을 제공한다.

- `USC` 전 세계의 모든 글자를 하나의 코딩된 문자집합으로 통합하려 노력하는 세계적인 표준이다.
`ISO 10646`으로 정의되며, `유니코드`는 이를 따르는 상업적인 컨소시엄이다.

#### 문자 인코딩 구조
문자 인코딩 구조들은 숫자로 된 문자 코드를 컨텐츠 비트로 변환하고 다른 쪽에서는 다시 문자 코드로 환원환다.
문자 인코딩 구조는 크게 세 가지 종류로 분류할 수 있다.

- `고정폭` 각 코딩된 문자를 **고정된 길이의 비트**로 표현한다. 빠르게 처리될 수 있지만 공간의 낭비가 우려된다.

- `가변폭(비모달)` 다른 문자 코드 번호에 다른 길이의 비트를 사용한다. 자주 사용하는 글자의 비트 길이를 줄이거나,
국제 문자에 여러 바이트를 사용하도록 함으로써 **8비트 문자집합과의 호환성**도 유지할 수 있다.

- `가변폭(모달)` 다른 모드로의 전환을 위해 특별한 `escape` 패턴을 사용한다. 처리는 복잡하지만, 복잡한 표시 체계를
효과적으로 지원해 줄 수 있다.

다음은 몇 가지 인코딩 구조이다.

- `8비트` 8비트 고정폭 아이덴티티 인코딩은 각 문자 코드를 그에 대응하는 8비트 값으로 인코딩한다.
256개 문자의 코드 범위 내의 문자집합만을 지원한다. `iso-8859` 캐릭터셋은 8비트 아이덴티티 인코딩을 사용한다.

- `UTF-8` UCS를 위해 설계된 가장 인기 있는 문자 인코딩 구조이다. 이는 문자 코드의 값을 위해 비모달 가변길이
인코딩을 사용한다. 첫 번째 인코딩된 바이트의 `하이 비트`가 0이라면, 길이는 단 1바이트고 나머지는 문자열을 담는다.
덕분에 아스키와 호환성이 확보된다.

- `euc-kr` 한글 인터넷 문서를 위해 사용되는 가변길이 인코딩이다. `KS X 1003`과 `KS X 1001` 두 가지 문자 집합을 지원한다.
KS X 1003은 `1바이트`로 인코딩된 로마자 문자 집합으로, `US-ASCII`에서 역슬래시를 원화 기호로 치환하기만 한 것이다.
KS X 1001은 `2바이트`로 한글, 한자와 특수문자들로 이루어진 한국어 문자 집합이다.

## 언어 태그와 HTTP
---
언어 태그는 언어에 이름을 붙이기 위한 짧고 표준화된 문자열이다. `영어(en)`, `독일어(de)`, `한국어(ko)` 등
다른 언어들의 태그가 존재한다.

그리고 `미국 영어(en-US)` 처럼 지역에 따라 변형된 언어도 표현할 수 있다.

#### Content-Language 헤더
Content-Language 엔터티 헤더 필드는 엔터티가 어떤 언어 사용자를 대상으로 하고 있는지 서술한다.
~~~
    Content-Language: ko
~~~

하지만 이 헤더가 텍스트 문서만을 위한 것은 아니다. 오디오 클립, 동영상 그리고 애플리케이션도 특정 언어 사용자를 대상으로
할 수 있다.

만약 컨텐츠가 여러 언어 사용자를 대상으로 하고 있다면, 여러 언어를 나열할 수 있다.
그러나 여러 언어가 하나의 엔터티에 사용되었다고 해서 반드시 여러 언어 사용자를 대상으로 하고 있는 것은 아니다.
~~~
    Content-Language: ko, en
~~~

#### Accept-Language 헤더
만약 웹 서버가 여러 언어로 된 리소스를 보유하고 있다면, 사용자가 선호하는 언어로 된 컨텐츠를 줄 수 있다.
클라이언트는 선호하는 컨텐츠를 요청하기 위해 `Accept-Language`와 `Accept-Charset`을 사용할 수 있다.
~~~
    Accept-Language: ko
~~~

#### 언어 태그의 종류
언어 태그는 [RFC 3066][language-tag]로 표준화된 표준 문법을 갖고 있다.
언어 태그는 다음을 표현하는데 사용될 수 있다.
- `ko`와 같은 일반적인 언어의 종류
- `en-GB`와 같은 특정 국가의 언어
- 방언과 지방어
- 표준 언어와 비표준 언어

#### 서브 태그
언어 태그는 `하이픈(-)`으로 분리된 하나 이상의 서브 태그로 이루어져 있다.
- 첫 번째 서브 태그는 `주 서브태그`라 불리며, 표준화되어 있다.
- 두 번째 서브 태그는 선택적이고, 자신만의 이름 표준을 따른다.
- 세 번째부터는 8자 이하의 알파벳과 숫자로 이루어져야 한다.
> `sgn-US-MA`는 마서스 비니어드 섬의 수화를 나타낸 언어 태그이다.

#### 대소문자의 구분 및 표현
모든 태그는 대소문자가 구분되지 않는다.
그러나 **관용적**으로 언어를 나타낼 때는 소문자를, 국가를 나타낼 때는 대문자를 사용한다.
> `ISO 3166` 표준은 이 관례를 따르라고 권고한다.

#### IANA 언어 태그 등록
첫 번째와 두 번째 언어 서브 태그의 값은 여러 가지 표준 문서와, 그것을 관리하는 조직에서 정의한다.
`IANA`는 `RFC 3066`의 규칙에 따라 표준 언어 태그의 목록을 관리한다.

만약 언어 태그가 표준 국가와 언어 값의 조합이라면, 굳이 등록하지 않아도 무방하다.

## 국제화된 URI
---
오늘날 URI에 대한 최신 명세인 RFC 3986은 URI에 `UTF-8` 문자를 사용할 수 있는 방법을 명시적으로 제시하고 있다.
그 덕분에 다양한 문자들을 별 문제 없이 사용할 수 있다.

초기 URI는 조작과 공유하기 쉽게 하고자 매우 제한된 공통 문자집합을 선택했다. 하지만 이러한 문자집합에는 제한이 있었다.
바로 URI를 비영어권 사용자들의 문자로 만들 수 있게 설계되지 못했던 것이다.

이후 URI는 가독성과 공유 가능성을 보장하고자, `ASCII` 문자들의 제한된 집합을 갖게 되었다.

#### URI에서 사용될 수 있는 문자들
`US-ASCII` 문자들의 부분집합은 예약된 문자들, 예약되지 않은 문자들, 이스케이프 문자들로 나뉜다.
예약된 문자들은 대부분 URI에서 특별한 의미 가지며, 일반적으로 사용될 수 없다.

이를 표현하고자 `이스케이프`가 사용되었다. 이스케이프는 `퍼센트 글자(%)`와 16진수 두 글자로 이루어진
**세 글자 문자열**이다. 16진수 두 글자는 `US-ASCII` 문자의 코드를 나타낸다.

이스케이프 값들은 `US-ASCII` 코드의 범위인 `0-127(0x7F)`에 있어야 하며, 무분별한 `역이스케이핑(unescaping)`으로
데이터 손실이 일어나지 않도록 주의해야 한다.

## 기타 고려사항
---
#### 헤더와 명세가 맞지 않는 데이터
HTTP 헤더는 반드시 `US-ASCII` 문자 집합의 글자들로만 이루어져야 한다.
그러나 모든 클라이언트와 서버가 이를 구현한 것은 아니기에, 메시지에 잘못된 데이터가
포함되어 있는지 주의 깊게 봐야 한다.

많은 HTTP 애플리케이션은 글자를 처리하기 위해 `운영체제`와 `라이브러리 루틴`을 사용한다. 
`UNIX`의 `crype` 문자 구분 라이브러리의 경우에는 범위에 벗어난 글자를 지원하지 않는다.

#### 날짜
HTTP 명세는 올바른 `GMT` 날짜 형식을 정의하고 있다. 하지만 이 규칙에 따르지 않는 서버와
클라이언트는 존재하기 마련이다.

HTTP 애플리케이션은 명세에 맞지 않는 날짜를 관대하게 받아들이고 충돌을 방지해야 하며, 
서버는 이를 예외적으로 처리해야한다.

#### 도메인 이름
국제화 문자를 포함하는 도메인 이름을 `국제화 도메인 이름(Internationalizing Domain Name)`이라고 한다.
오늘날 대부분의 웹브라우저가 [퓨니코드(punycode)][punycode]를 이용해 이를 지원한다.

## Reference
---
HTTP: The Definitive Guide

[charset-list]: https://www.iana.org/assignments/character-sets/character-sets.xhtml
[charset-ratio]: https://w3techs.com/technologies/overview/character_encoding/all
[language-tag]: https://tools.ietf.org/html/rfc3066
[punycode]: https://ko.wikipedia.org/wiki/%ED%93%A8%EB%8B%88%EC%BD%94%EB%93%9C