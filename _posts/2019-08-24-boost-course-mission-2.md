---
layout: post
title:  "[boostcourse] 예약 시스템: 상세페이지 - 1"
date:   2019-08-24 04:22:11 +0900
background: '/img/posts/02.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

---
이번 포스팅은 두 번째 미션인 상세페이지 구현의 백엔드 관련 내용이다.

[기획서](https://docs.google.com/presentation/d/1i2IC1yIH5ACFCvCH4EMVv_3Zw2oltRvHK94amyNEKbs/edit#slide=id.p11)에서
9-14페이지에 해당하는 내용을 구현해야 했고, 세부 루브릭은 다음과 같다.

![mission-2-1](https://user-images.githubusercontent.com/28993371/63611045-f3e51400-c615-11e9-8908-ae02145ba27f.JPG)
![mission-2-2](https://user-images.githubusercontent.com/28993371/63611044-f34c7d80-c615-11e9-8324-7188aea7a8cc.JPG)

### SQL Query
---
데이터 구조가 지난 미션보다 복잡했는데 쿼리를 작성하며 여러 궁금증이 생겼다.

#### JOIN
다음과 같이 어떤 상품의 댓글을 조회하는 쿼리를 작성해야 했다.
해당 상품을 조회하는 쿼리와, 댓글과 이미지를 조회하는 3개의 쿼리가 필요하다고 판단했다.

```javascript
{
    "product": {
        "name": ""
    },
    "comment": {
        "commentImage": [
            {
                "fildName": ""
            }
        ]
    }
}
```

해당 데이터를 요청하는 `URL`은 `/product/{product_id}`와 같이 작성되어 있었고,
댓글 이미지를 조회하기 위해서는 `product_id`가 아닌 `comment_id`가 필요했다.

이 상황에서 `product_id`에 해당하는 `comment_id`를 조회하기 위해 `product` 테이블을 조인해야 하는지,
아니면 `comment_id`를 조회하는 쿼리를 별도로 생성하는 것이 좋을지 의문이 생겼다.

프로젝트 제출 당시 질문했던 내용인데, 성능적인 면에서 조인을 하는 것이 더 좋은 방법이라는 답변을 받았다.
> 쿼리 복잡도보다 DB에 접근하는 비용이 더 큰 셈이다.

#### GROUP BY vs LIMIT
위와 같은 데이터 구조에서 특정 타입의 이미지를 최대 1개만 가져오는 쿼리를 작성해야 했다.
`GROUP BY` 절 혹은 `LIMIT` 절을 사용하는 방법이 존재했다.

구글링 결과, `GROUP BY` 절의 경우 **가상 테이블**을 생성하여 성능이 떨어질 수 있다는 결과를 얻었다.
> 실행 계획을 보면 `using temporary`이 나온다. 가상 테이블 생성 시 나오는 값이고, 제거해주는 편이 좋다고 한다. 

또한, `LIMIT` 절은 질의의 최종 결과 개수를 제한하여 성능 향상을 도와준다고 하였다.

리뷰어님께서는 둘의 큰 차이는 없으나, `LIMIT` 절을 사용할 경우 `ORDER BY` 절을 함께 사용하는 것이 좋다는 답변을 해주셨다.

#### NULL
댓글의 평균 평점을 구하기 위해 쿼리를 작성해야 했다. 그러나 댓글이 없는 상품의 경우 해당 쿼리를 실행하면 `Null` 값이 리턴되어,
`JDBC Template`의 `queryForObject()` 메서드에서는 예외가 발생했다.

예외 처리도 비용이라는 소리를 들은 기억이 있었기에,
`ResultSetExtractor`를 파라미터로 받는 `query()` 메서드를 사용하여 처리했다.

5 버전대의 `JDBC Template`에서 JAVA 8에 추가된 `Optional`이 사용 가능할 지는 모르겠는데,
만약 가능하다면 `Null` 값을 처리하는데 보다 수월할 것 같다.
> **JPA**에서는 여러 `Annotation`과 `Optional` 사용이 가능한 것으로 알고 있다.

리뷰에서는 쿼리 내에서 사용할 수 있는 `IFNULL()` 사용도 조언해주셨는데,
특정 벤더에 종속되는 함수를 사용하지 말라는 루브릭이 있어서 수정하지는 않았다.

#### DATE
`Date` 타입을 가진 데이터를 조회하면 `ms` 단위로 리턴되어 오는 것을 발견했다.

`Swagger`에 존재하는 API 스펙에는 특정 날짜 포맷의 데이터가 리턴되었기에, 날짜 데이터의 변환이 필요했다.

데이터 필드에 `@JsonFormat`을 사용하는 방법과, 쿼리 내에서 사용할 수 있는 `DATE_FORMAT()`과 같은 함수를 사용하는 방법이 존재했다.

이 역시 특정 벤더에 종속되는 함수를 사용하지 말라는 루브릭을 준수하여 `@JsonFormat`을 사용하는 방법을 선택하였다.

### Transaction Annotation
리뷰 결과, `MySQL`의 경우 기본값으로 `REPEATABLE READ`의 격리 수준을 갖는다고 한다.
> 선행 트랜잭션이 읽은 데이터는 종료 전까지 후행 트랜잭션이 갱신하거나 삭제하는 것이 불가능하다.

그렇기 때문에 굳이 `@Transactional(readOnly = true)`를 데이터를 가져오는 메서드에 붙일 필요가 없다는 답변을 받았다.

### Integration Test vs Unit Test
테스트는 이번 코스 외적인 부분이었지만, 구현이 잘 이루졌는지를 `Postman`과 같은 툴로 확인하기 귀찮았다.

구현한 코드를 실제 데이터와 일치하는지 확인하는 코드를 작성하여 제출하였고, 제대로 작성한 테스트 코드가 맞는지 질문하였다.

우선 테스트 용어에 대해 학습하라는 조언을 받았다.
- `단위 테스트(Unit Test)`는 구현한 **로직**을 검증하기 위한 테스트이다.
- `통합 테스트(Integration Test)`는 로직 뿐만 아니라, 데이터에 대한 검증을 위한 테스트이다.

내가 작성했던 코드는 통합 테스트에 해당했는데,
테스트 코드를 작성하는 비용이 클 수 있으니 로그를 출력하는 정도만 수행해도 이번 프로젝트에서는 큰 무리가 없다는 답변을 받았다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- Naver D2, [성능 향상을 위한 SQL 작성법](https://d2.naver.com/helloworld/1155)