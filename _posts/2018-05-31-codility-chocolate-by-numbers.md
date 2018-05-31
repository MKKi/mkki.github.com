---
layout: post
title:  "Codility Lesson 12 ChocolateByNumbers"
date:   2018-05-31 17:23:11 +0900
background: '/img/posts/02.jpg'
categories: [codility]
---

### Codility Lesson 12 ChocolateByNumbers
`Euclidean Algorithm`을 사용하는 문제이다. 

`Euclidean Algorithm`은 `O(log(min(a, b)))`의 시간복잡도를 가지며, 다음이 성립한다.

`f(a, b) = gcd(a, b)`일 때, `a mod b = 0`이면 `f(a, b) = b`이고, `a mod b != 0`이면 `f(a, b) = f(b, a mod b)`이다.

그리고 최소 공배수의 성질에 따라 `lcm(a, b) = a * b / gcd(a, b)`이 성립한다.

~~~
    private int gcd(int a, int b) {
        if(a % b == 0) {
            return b;
        }
        
        return gcd(b, a % b);
    }
    
    private int lcm(int a, int b) {
        return a * b / gcd(a, b);
    }
~~~

[문제 보기](https://app.codility.com/programmers/lessons/12-euclidean_algorithm/chocolates_by_numbers/)
~~~
    class Solution {
        public int solution(int N, int M) {
            int gcd = gcd(N, M);
            
            return N / gcd; 
        }
        
        private int gcd(int a, int b) {
            if(a % b == 0) {
                return b;
            }
            
            return gcd(b, a % b);
        }
    }
~~~

### Reference
- 위키백과, [유클리드 호제법](https://ko.wikipedia.org/wiki/%EC%9C%A0%ED%81%B4%EB%A6%AC%EB%93%9C_%ED%98%B8%EC%A0%9C%EB%B2%95)