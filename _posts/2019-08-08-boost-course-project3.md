---
layout: post
title:  "[boostcourse] 예약 시스템 - 메인페이지"
date:   2019-08-08 17:50:11 +0900
background: '/img/posts/02.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

## 예약 시스템 - 메인페이지
---
**부스트코스 에이스 2기**에 선발되어 첫 미션을 진행했다. 프로젝트 세부 루브릭은 다음과 같다.

![requirement-1](https://user-images.githubusercontent.com/28993371/62682849-0ea17100-b9f8-11e9-9e3a-3bb3095c7fa4.JPG)
![requirement-2](https://user-images.githubusercontent.com/28993371/62682848-0ea17100-b9f8-11e9-8df4-403d3eacb8bd.JPG)

### 프로모션 영역
`Infinite Slider`를 구현하는 내용이다. 마지막 요소가 첫 요소로 넘어가는 애니메이션에 엄격한 기준이 적용되지 않기에 `CSS`만으로 구현도 가능한 부분이었다.

하지만 루브릭에 `CSS`를 `JS`로 제어하라는 내용이 있었기에 `setInterval()`로 처리하였다. 리뷰어님께선 `CSS`의 `animation-*` 속성을 공부하면 좋을 것이라는 조언을 해주셨다.

제출한 코드에는 포함되지 않았지만, `loop` 중 마지막 요소에서 첫 요소의 넘어갈 때의 부드러운 애니메이션 처리를 위해
[링크](https://poiemaweb.com/fastcampus-exercise/carousel-slider-ui)를 참고하며 고민해보았다. 그리고 이 방법이 최선인지 리뷰에서 확인해보고자 했으나,
리뷰어님과 커뮤니케이션이 잘못되어 무산되었다.

### JS 코드 개선하기
강의를 성실하게 들었다면 강의 내용을 참고하여 구현할 수 있는 것들 요구 사항들이었지만, 더 개선된 방법과 기술을 사용하여 구현해보고 싶었다.

소스코드 네 번째 루브릭을 진행하던 중, 강의 내용에서 `ECMAScript6`의 `template literals`를 활용해보라는 내용이 있었기에 다음과 같이 작성했다.
예상대로 `template` 코드가 분리되지 않아 `Fail` 처리되었다.
```
    const template = (data) => {
        return `
            <div>${data.id}</div>
        `
    }
```

리뷰어님의 조언에 따라 `template literals`를 `HTML` 내에 위치시켜보았지만, `string interpolation`이 사라져서 적용이 어려웠다.
~~~
    // html
    <script type="my-template" id="custom-template">
        `
            <div>${data.id}</div>
        `
    </script>
    
    // js
    const template = document.querySelector("#custom-template").innerHTML;
    console.log(template); // '<div></div>'
~~~

어쩔 수 없이 `interpolation` 처리 된 문자열을 `String.prototype.replace()`로 치환해주는 작업으로 구현을 진행했다.
> `template literals`가 스크립트 단의 기능이라 분리시키면 안되는 것인가? 다음 프로젝트 제출할 때 한 번 더 질문해봐야겠다.
~~~
    // html
    <script type="my-template" id="custom-template">
            <div>{data.id}</div>
    </script>
    
    // js
    const template = document.querySelector("#custom-template").innerHTML;
    template.replace("{data.id}", data.id);
~~~

### 크로스브라우징 이슈
![review-result](https://user-images.githubusercontent.com/28993371/62688371-ace70400-ba03-11e9-8243-2c3abe2b103c.JPG)
> 잔소리라뇨!

**Tab UI** 기능 구현 중 `classList`를 사용하였는데 `IE` 하위 버전 호환성 이슈가 있다는 것을 지적해주셨다.
이후 `String.prototype.includes`로 바꾸어 다시 제출했었는데, 알고보니 `IE`에서 아예 지원하지 않는 메서드였다.

리뷰어님의 조언대로 함수나 프로퍼티를 사용하기 전에 [Can-I-use](https://caniuse.com/)를 통해 확인하는 습관을 들여야겠다.

마지막으로, 리뷰어님이 평가와 학습의 가이드를 제시해주는 분이라는 것을 명심하며 질문 리스트를 작성해야겠다는 생각이 들었다.
> 평가 항목 이상의 내용은 스스로 학습하고 구글신에게 물어보는게 당연한 것

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [animation - MDN](https://developer.mozilla.org/ko/docs/Web/CSS/animation)
- Poiema Web, [Carousel Slider UI](https://poiemaweb.com/fastcampus-exercise/carousel-slider-ui)