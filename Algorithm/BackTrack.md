# BackTracking
* Brute Force 및 재귀 알고리즘
* 점진적으로 해를 쌓아나가 정답 후보를 생성
* 정답 후보가 조건을 만족하지 못한다면 되돌아가 다른 후보 생성 및 검사
* 정답을 찾을 때까지 모든 후보에 대해 검사

<br/>

### 어떤 경우에 사용하는가?
* CSP (constraint satisfaction problem) 해결에 사용
* partial candidate solution (후보의 일부를 보고 문제의 조건에 부합하는지 판단)
* 후보의 일부를 보고 정답으로 완성될 수 있는지 판단할 수 있는 경우
* 이러한 문제들에 대해서는 Brute Force 보다 빠르게 동작

<br/>

> [CSP](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem) : 주어진 도메인에서 문제의 조건을 만족하는 객체의 집합을 찾는 문제

### pseudo code
```python
BackTrack(P, c):
    if reject(P, c) then return
    if accept(P, c) then output(P, c)

    for s be extension of c:
        backtrack(P, s)
        s = next(P, s)
```
1. sub-candidate c가 문제의 조건을 만족하는지 검사
2. 만족하지 않을 경우 종료
3. 부분 후보 c를 확장한 여러 후보 s에 대해 재귀적으로 BackTrack

* depth first 방식으로 순회, 부분 해를 점진적으로 쌓아가며 트리를 구성
* 부분 후보가 조건을 만족하지 않는다면 더 이상 트리 확장하지 않음
* 조건을 만족하는 후보와 그 확장들만 탐색함


<br/>


### Constraint Satisfaction Problem (CSP)



<br/>

### 활용
* 스도쿠, 낱말문제 등 퍼즐 풀기 등 CSP
* knapsack problem
* prolog 등 논리형 프로그래밍 언어의 구현
* combinatorial optimization
* 순열, 조합 생성

<br/>

### 참고 자료
* [Backtracking - wikipedia](https://en.wikipedia.org/wiki/Backtracking)