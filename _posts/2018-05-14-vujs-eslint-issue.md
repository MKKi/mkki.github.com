---
layout: post
title:  "[Vue.js + ESLint] 'v-for' directives require 'v-bind:key' directives Issue"
date:   2018-05-14 10:34:11 +0900
background: '/img/posts/02.jpg'
categories: [vue.js, eslint]
---

## Vue.js + ESLint
---
기존 예제 코드에 `ESLint`를 적용하니 `vue/required-v-for-key...`와 같은 에러가 발생했다. 키를 사용하라는 내용인데, 다음과 같은 방법들이 존재했다.
> 1. Use arrays of objects instead, explicitly declaring a key for each item.
> 2. Don't use a key in the v-for.
> 3. Use item’s index as the key.
> 4. Use item itself as the key.

별도의 키 값이 없는 배열이라 인덱스를 키 값으로 바인딩하여 해결하였다.
~~~
  <tr v-for="(index, contact) in contactlist.contacts" :key="index">
    ...
  </tr>
~~~

## References
---
- [Vue.js Forum](https://forum.vuejs.org/t/v-for-with-simple-arrays-what-key-to-use/13692)