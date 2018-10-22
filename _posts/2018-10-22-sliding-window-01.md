---
layout: post
title:  "[Algorithm] Sliding Window Algorithm"
date:   2018-07-26 22:32:11 +0900
background: '/img/posts/02.jpg'
categories: [algorithm]
---


### Sliding Window
---
~~~
두 개의 네트워크 호스트간, 패킷의 흐름을 제어하기 위한 방법이다. 

TCP와 같이 데이터의 전달을 보증하는 프로토콜에서는 패킷 하나하나가 전달되었음을 확인 신호(acknowledgement)를 받아야 하며, 만약 패킷이 중도에 잘못되었거나 분실되어 확인 받지 못하는 경우, 해당 패킷을 재전송해야 하는 필요가 있다.

...
~~~

여기서 **윈도(Window)**는 메모리 버퍼의 일정 영역이고, 패킷 전송이 확인되는대로 윈도를 **옮기기에(Slide)** `Sliding Window`라고 한다.

알고리즘 문제 풀이에서 이 슬라이딩 윈도우 기법을 사용하여 알로리즘의 복잡도를 줄일 수 있다고 한다.
`투 포인터(Two Pointers)`와 유사한 알고리즘이지만, 슬라이드 윈도우 같은 경우 구간의 넓이가 동일한 것이 차이점이다.

`O(N^4)`의 탐색 문제를 `O(N^3)`으로 풀이한다던가, 상수의 공간복잡도로 DP 문제를 해결하는데 활용될 수 있다고 한다.


### References
---
위키, [슬라이딩 윈도](https://ko.wikipedia.org/wiki/%EC%8A%AC%EB%9D%BC%EC%9D%B4%EB%94%A9_%EC%9C%88%EB%8F%84)
라이님 블로그, [투 포인터(Two Pointers Algorithm), 슬라이딩 윈도우(Sliding Window)](http://blog.naver.com/PostView.nhn?blogId=kks227&logNo=220795165570)