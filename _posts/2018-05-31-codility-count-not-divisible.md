---
layout: post
title:  "Codility Lesson 11 CountNotDivisible"
date:   2018-05-31 16:57:11 +0900
background: '/img/posts/05.jpg'
categories: [codility]
---

### Codility Lesson 11 CountNotDivisible
1. 원소의 등장 횟수를 기록해준다.
2. `sqrt(A[i])` 보다 작은 약수를 먼저 구하고, `sqrt(N)` 보다 큰 약수를 구하여 카운팅한다.
> 예를 들면, `100`은 약수 `1, 2, 4, 5, 10, 20, 25, 50, 100`을 갖는다.
> `sqrt(100) = 10` 보다 작은 약수 `1, 2, 4, 5`를 구하고, `100 / 1 = 100, ... , 100 / 5 = 20`을 구한다.
3. `N`에서 약수의 갯수를 빼준다.

[문제 보기](https://app.codility.com/programmers/lessons/11-sieve_of_eratosthenes/count_non_divisible/)

~~~
    class Solution {
        public int[] solution(int[] A) {
            // write your code in Java SE 8
            int N = A.length;
            int[] result = new int[N];
            int[] table = new int[N*2 + 1];
            
            for(int i : A) {
                table[i]++;
            }
            
            for(int i=0; i<N; i++) {
                int divisors = 0;
                int current = A[i];
                for(int j=1; j*j <= current; j++) {
                    if(current % j == 0) {
                        divisors += table[j];
                        
                        if(current / j != j) {
                            divisors += table[current / j];
                        }
                    }
                }
                result[i] = N - divisors;
            }
            
            return result;
        }
    }
~~~