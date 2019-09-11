---
layout: post
title:  "[boostcourse] 웹 앱 개발: 예약서비스 4/4 - 1"
date:   2019-09-12 05:23:22 +0900
background: '/img/posts/03.jpg'
categories: [boostcourse]
---

해당 포스팅은 [커넥트재단의 edwith boostcourse - Web Programming](http://www.edwith.org/boostcourse-web)의 강의 자료를 바탕으로 작성되었습니다.

`#부스트코스` `#웹프로그래밍`

### File Upload
form 데이터를 그대로 전송한다면 input 태그로도 파일 전송이 가능하다.
**name** 속성을 지정하면 서버가 이를 인식할 것이다.

```html
<input type="file" name="profileImage" accept="image/*">
```

> **accept** 속성의 경우 브라우저 지원 범위가 넓지 않다.

### HTTP Multipart
form 데이터는 요청 헤더에 기본적으로 `Content-Type: application/x-www-form-urlencoded`를 설정한다.

그러나, 파일을 전송하기 위해서는 form 태그의 인코딩 타입을 **multipart/form-data**로 설정해야 한다.

```html
<form action="/test" enctype="multipart/form-data" method="post">
    <input type="text" name="email" id="email">
    <input type="file" name="thumbnail" id="thumbnail" accept="image/*">
    <button type="submit">submit</button>
</form>
```

**Multipart Message**는 여러 메세지를 하나의 메세지로 구성한 것이고, Content-Type 헤더에 **boundary** 파라미터를 포함한다.
이는 메세지를 구분하는 역할을 하며, 시작과 끝도 나타낸다.
결국 위의 코드는 텍스트 필드와 이미지 객체가 하나의 번들로 만들어져 전송되는 것이다.

form 데이터가 제출되면 브라우저는 아래와 같은 데이터를 서버로 전송할 것이다.

```
------WebKitFormBoundaryiUIOhJXAwxorM25j
Content-Disposition: form-data; name="email"

test@connect.co.kr
------WebKitFormBoundaryiUIOhJXAwxorM25j
Content-Disposition: form-data; name="thumbnail"; filename="example.png"
Content-Type: image/png
```

### Asynchronous File Upload
`FormData` 객체를 사용하면 **Ajax**를 이용하여 파일을 서버로 전송할 수 있다.

```javascript
var formData = new FormData();
var fileInputElement = document.getElementById("thumbnail");

formData.append("email", "test@connect.co.kr");
formData.append("image", fileInputElement.files[0]);
```

다음 코드처럼 사용하면 기존 form 내용이 `FormData` 객체에 들어간다.
```javascript
var formData = new FormData(document.getElementById("test-form"));
```

### File Extension Validation, Thumbnail
**accept** 속성이 브라우저 지원 범위가 좋지 않기 때문에, 별로도 업로드되는 파일의 확장자가 유효한지 검사할 필요가 있다.

파일이 업로드 되면 **change** 이벤트로 감지할 수 있다.

```javascript
var fileInputElement = document.querySelector("#thumbnail");
fileInputElement.addEventListener("change", function(e) {
    var image = e.target.files[0];
});
```

이미지 업로드를 **Ajax**를 사용하여 구현하는 경우, URL 값을 응답 받고 
이를 image 태그의 src 속성 값으로 적용하는 것이 일반적인 방법이다.

서버에 이미지를 전송하지 않고 이미지를 노출시키려면 다음과 같이 작성하면 된다.

```javascript
var fileInputElement = document.querySelector("#thumbnail");
fileInputElement.addEventListener("change", function(e) {
    var image = e.target.files[0];
    var thumbnailElement = document.querySelector("thumbnail-image");
    thumbnailElement.src = window.URL.createObjectURL(image);
});
```

`URL.createObjectURL()` 메서드는 파라미터로 넘겨진 객체를 표현하기 위한 URL을 포함한다.
생성된 URL은 `File`이나 `Blob` 객체가 된다.
> `Blob` 객체는 파일과 비슷한 Immutable Object로 **raw data**이다.
> `File` 인터페이스는 이를 상속받고 확장하여, 유저 시스템의 파일을 지원해준다.

## References
---
- edwith, [boostcourse: Web Programming](http://www.edwith.org/boostcourse-web)
- MDN, [Using FormData Objects](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects#Sending_files_using_a_FormData_object)
- MDN, [URL.createObjectURL()](https://developer.mozilla.org/ko/docs/Web/API/URL/createObjectURL)
- MDN, [Blob](https://developer.mozilla.org/ko/docs/Web/API/Blob)
