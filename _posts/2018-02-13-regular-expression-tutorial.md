---
layout: post
title:  "정규 표현식(Regular Expression) 기초"
date:   2018-02-13 10:49:16 +0900
background: '/img/posts/01.jpg'
categories: regex
---

#### 정규 표현식이란?
특정한 `패턴(pattern)`을 가진 문자열의 집합을 표현하는데 사용하는 표현식 `언어`이다.

많은 언어에서 `텍스트`를 검색하거나 치환하고자 사용된다.

---
#### 정규표현식 문법
- `.` 임의의 한 문자를 의미한다. 해당 위치에 반드시 임의의 한글자가 위치하여야 한다.
> `a.b`는 `acb`, `adb` 등을 의미

- `*` 바로 앞에 문자가 없거나, **하나 이상 반복**한다는 의미이다.
> `Hello*`는 `Hell`, `Hello`, `Helloo` 등을 의미  
> `*a`는 표현 불가능한 표현식

- `+` 바로 앞에 하나 이상의 문자가 반복한다는 의미이다.
> `Hello+`는 `Hello`, `Helloo` 등을 의미

- `?` 바로 앞에 문자가 없거나, 하나임을 의미한다.
> `A?c`는 `c`, `Ac` 중 하나를 의미

- `^` 문장의 **처음**을 나타내며, `^`가 포함된 단어로 문장이 시작됨을 의미한다.
> `^Hello`는 `Hello World`, `Hello ..` 등을 의미

- `$` 문장의 **끝**을 나타내며, `$`가 포함된 단어로 문장이 끝남을 의미한다.
> `Wolrd$`는 `Hello Wolrd`, `.. World` 등을 의미

- `[]` 괄호 안에 문자 중 **일치하는 것**을 검색할 경우 사용한다.
> `[abc]`는 문자열에 `a`, `b`, `c`가 포함된 단어를 검색  
> `[a-z]`는 알파벳 소문자가 포함된 모든 문자열을 검색  
> `[A-Z]`는 알파벳 대문자가 포함된 모든 문자열을 검색  
> `[0-9]`는 숫자가 포함된 모든 문자열을 검색
> `^[a-zA-Z0-9]`는 영문대소문자나 숫자로 시작하는 모든 문자열을 검색

- `[]` 안에서의 `^`는 괄호 안에 있는 문자를 **포함하지 않는** 모든 문자열을 검색한다.
> `[^abc]de`는 `de`, `dde` 등을 의미

- `{}` 괄호 앞에 문자가 반복되는 횟수를 의미한다.
> `a{3}b`는 `aaab`를 의미

- `()` 괄호안의 문자열을 **하나의 문자**로 취급한다.
> `(Hello)*`는 `Hello`, `HelloHello` 등을 의미

- `|` OR 연산을 수행한다.
> `Hi|Hello`는 `Hi`나 `Hello`가 포함된 문자열을 의미

- [문자 클래스][zeta]는 자주 사용되는 패턴들을 미리 키워드로 정의한 문자이다.
> `[[:alpha]]`는 모든 영문자인 `[a-zA-Z]`를 의미

- `메타 문자(*, + 등)`을 패턴으로 사용하려면 해당 문자 앞에 `\`를 사용해야 한다.

- 다음은 표현식의 예이다. 
~~~
// e-mail
^[a-zA-Z0-9]+@[a-zA-Z0-9]+(.[\w]+)
^[_0-9a-zA-Z-]+@[0-9a-zA-Z-]+(.[_0-9a-zA-Z]+)*$

// mobile phone
^01(?:0|1|[6-9]) - (\d{3,4}) - \d{4}$

// landline phone
^\d{2,3} - \d{3,4} - \d{4}&

// resident registration number
\d{6} \- [1-4]\d{6} 

// IP Address
([0-9]{1,3})\.{([0-9]{1,3})\.{([0-9]{1,3})\.{([0-9]{1,3})

// URL
([\\p{Alnum}]+)://([a-z0-9.\\-&%=?:@~\\_]+)“
~~~

---
#### 자바에서의 정규 표현식
자바에서는 정규 표현식을 다루기 위한 `API`를 지원한다.

- `java.util.regex` 패키지 내에는 주어진 정규 표현식으로부터 패턴을 생성하는 `Pattern` 클래스, 
문자열이 주어진 패턴과 일치하는지 확인하는 `Matcher` 클래스가 존재한다.  
~~~ java
// 패턴 생성
static Pattern compile(String regex);

// 패턴을 검사하는 Mather 객체 반환
static Matcher matcher (CharSequence input); 

// 정규 표현식을 String으로 처리
String pattern();

// 패턴에 따라 분리
String split(CharSequence input);
~~~

- `String` 클래스에는 정규표현식을 지원하는 메서드들이 존재한다.
~~~java
// 문자열과 패턴의 일치 여부 반환
boolean matches(String regex);

// 패턴과 일치하는 모든 부분을 치환
String replaceAll(String regex, String replace);

// 패턴과 일치하는 첫 부분을 치환
String replaceFirst(String regex, String replaceStr);

// 패턴과 일치하는 구분자를 기준으로 분할하여 배열로 반환
String[] splite(String regex);
~~~

- 다음은 활용 예이다.
~~~java
// input data
String data[] = {
    "aab", "baa", "dFd", "aaa", "Cab", "ca", 
    "c12", "cAA", "cab", "dde", "aaaaab3"
};

// create pattern
Pattern pattern = Pattern.compile("c[a-z]*");

// print matched data. (ouput) : ca cab
for (int i=0; i<data.length; i++) {
    Matcher match = pattern.matcher(data[i]);
    if (match.matches()) {
        System.out.print(data[i]+" ");
    }
}
~~~

추가적 내용에 관한 추천 포스팅은 [Outsider님 블로그][outsider]

---
#### Reference
- 위키백과, [http://en.wikipedia.org/wiki/Regular_expression](http://en.wikipedia.org/wiki/Regular_expression)
- 제타위키,
[https://zetawiki.com/wiki/정규식_문자_클래스](https://zetawiki.com/wiki/%EC%A0%95%EA%B7%9C%EC%8B%9D_%EB%AC%B8%EC%9E%90_%ED%81%B4%EB%9E%98%EC%8A%A4)


[zeta]:https://zetawiki.com/wiki/%EC%A0%95%EA%B7%9C%EC%8B%9D_%EB%AC%B8%EC%9E%90_%ED%81%B4%EB%9E%98%EC%8A%A4
[outsider]:https://blog.outsider.ne.kr/360
