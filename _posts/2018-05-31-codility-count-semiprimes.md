---
layout: post
title:  "Codility Lesson 11 Count Semiprimes"
date:   2018-05-31 15:50:11 +0900
background: '/img/posts/04.jpg'
categories: [codility]
---

### Codility Lesson 11 Count Semiprimes
`O(N*log(log(N))+M)`의 시간 복잡도에 충족하기 위해 누적 합으로 결과를 구해주는게 포인트이다.

[문제 보기](https://app.codility.com/programmers/lessons/11-sieve_of_eratosthenes/count_semiprimes/)
~~~java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] P, int[] Q) {
        // write your code in Java SE 8
        int M = P.length;
        int[] result = new int[M];
        int[] accSemiprimes = getAccumulatedSemiprimes(N);
        
        for(int i=0; i<M; i++) {
            result[i] = accSemiprimes[Q[i]] - accSemiprimes[P[i] - 1];
        }
        
        return result;
    }
        
    
    private int[] getAccumulatedSemiprimes(int N) {
        ArrayList<Integer> primes = getPrime(N);
        int[] semiprimes = new int[N+1];
        for(int prime1 : primes) {
            for(int prime2 : primes) {
                int semiprime = prime1 * prime2;
                if(semiprime > N) {
                    break;
                }
                semiprimes[semiprime] = 1;
            }
        }
        
        for(int i=1; i<=N; i++) {
            semiprimes[i] +=semiprimes[i-1];
        }
        
        // for(int i : semiprimes) {
        //     System.out.print(i + " ");
        // }
        
        return semiprimes;
    }

    private ArrayList<Integer> getPrime(int N) {
        boolean[] isPrime = new boolean[N+1];
        ArrayList<Integer> primes = new ArrayList<>();
        
        for(int i=2; i*i<=N; i++) {
            if(isPrime[i] == false) {
                for(int j=i*i; j<=N; j+=i) {
                    isPrime[j] = true;
                }
            }
        }
         
        for(int i=2; i<=N; i++) {
            isPrime[i] = !isPrime[i];
        }
        
        for(int i=2; i<=N; i++) {
            if(isPrime[i]) {
                primes.add(i);
            }
        }
        
        return primes;
    }
}
~~~