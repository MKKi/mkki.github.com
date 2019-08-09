---
layout: post
title:  "Codility Lesson 12 CommonPrimeDivisors"
date:   2018-05-31 18:17:11 +0900
background: '/img/posts/03.jpg'
categories: [codility]
---

### Codility Lesson 12 CommonPrimeDivisors
같은 소인수를 가진 `A, B` 쌍의 갯수를 찾는 문제이다.

각 수와 최대 공약수의 최대 공약수를 찾아 나누고, 두 수가 모두 `1`이 되면 같은 소인수를 가진 것이다.
> 즉, (두 수의 인수) = (최대 공약수의 인수)인 경우를 찾는 것이다.

[문제 보기](https://app.codility.com/programmers/lessons/12-euclidean_algorithm/common_prime_divisors/)
~~~java
class Solution {
    public int solution(int[] A, int[] B) {
        // write your code in Java SE 8
        int Z = A.length;
        int result = 0;

        for(int i=0; i<Z; i++) {
            if(isSameDivisors(A[i], B[i])) {
                result++;
            }
        }
        
        return result;
    }
    
    private int gcd(int a, int b) {
        if(a % b == 0) {
            return b;
        }
        
        return gcd(b, a%b);
    }
    
    private boolean isSameDivisors(int a, int b) {
        int gcd_ab = gcd(a, b);
        int gcd_a = 0;
        int gcd_b = 0;
        
        while(gcd_a != 1) {
            gcd_a = gcd(a, gcd_ab);
            a /= gcd_a;
        }
        
        while(gcd_b != 1) {
            gcd_b = gcd(b, gcd_ab);
            b /= gcd_b;
        }
        
        return (a == 1 && b == 1) ? true : false;
    }
}
~~~