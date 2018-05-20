---
layout: post
title:  "Codility Lesson 8 EquiLeader"
date:   2018-05-20 15:27:11 +0900
background: '/img/posts/03.jpg'
categories: [algorithm]
---

### Codility Lesson 8 EquiLeader
이전 문제인 [Dominator](https://app.codility.com/programmers/lessons/8-leader/dominator)와 비슷한 문제이다.

[Boyer-Moore Majority Vote Algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)을 사용하여
`leader element`를 찾고, 배열을 순회하면서 `EquiLeader`를 찾는 문제이다. 

[문제 보기](https://app.codility.com/programmers/lessons/8-leader/equi_leader)
~~~
    public class Solution {
        public int solution(int[] A) {
            // Boyer-Moore Majority Vote Algorithm
            int cnt = 1;
            int el = A[0];
            
            for(int i=1; i<A.length; i++) {
                if(A[i] == el) {
                    cnt++;
                } else{
                    cnt--;
                }
                if(cnt == 0) {
                    cnt = 1;
                    el = A[i];
                }
            }
            
            // check el is a leader
            cnt = 0;
            for(int n : A) {
                if(n == el) {
                    cnt++;
                }
            }
            
            if(cnt <= (A.length / 2)) return 0;
            
            // find # of equi-leader case
            int totalCnt = 0;
            int cntLeftPart = 0;
            for(int i=0; i<A.length; i++) {
                if(A[i] == el) {
                    cntLeftPart++;
                }
                int cntRightPart = cnt - cntLeftPart;
                if(cntLeftPart > (i + 1) / 2 && cntRightPart > (A.length - 1 - i) / 2) {
                    totalCnt++;
                }
            }
            
            return totalCnt;
        }
    }
~~~

### References
- 위키, [Boyer-Moore majority vote algorithm](https://en.wikipedia.org/wiki/Boyer%E2%80%93Moore_majority_vote_algorithm)