# Dynamic Programming
### Optimal sub-structure
* 큰 문제를 작은 부분 문제로 분할 가능
* 큰 문제의 해를 부분 문제의 해를 이용해 표현
* `Fibonacci` : $F(i+1) = F(i) + F(i-1)$
* `Bellman-Ford` : $d^{(i+1)}[v] \leftarrow min\{ d^{(i)}[v], min_u\{ d^{(i)}[u] + weight(u,v) \} \}$
* 부분 문제에 대한 최적의 해를 상위 문제의 최적의 해를 구하는 데 이용

### Overlapping sub-problems
* 같은 부분 문제를 여러 번 반복해서 계산
* 부분 문제의 해를 저장한 뒤 다시 풀지 않고 가져다 쓰면 효율적일 것이다!

### vs divide-and-conquer
* `memo-ization`
* 단순 분할 정복과는 다르게, 동일한 부분 문제를 다시 계산하지 않도록 메모리를 이용해 기록

### 풀이 절차
1. optimal substructure 찾기
2. 상위 문제를 부분 문제의 최적의 해를 이용해 풀기 위한 점화식 구성
3. 부분 문제의 정답 및 추가 정보를 저장할 자료구조 정의
4. bottom-up 또는 top-down(재귀) 알고리즘을 작성

<br/>
<br/>

# DP Examples
### Longest Common Subsequence
두 문자열 간의 가장 긴 부분 문자열 또는 그 길이를 구하는 문제

#### Optimal Substructure
* `C[i,j]` : $A[0:i]$ 와 $B[0:j]$ 의 LCS
1. $A[i] = B[j]$ : $C[i,j] = 1 + C[i-1,j-1]$
2. $A[i] \ne B[j]$ : $C[i,j] = max(C[i,j-1], C[i-1,j])$

#### Complexity
* 테이블 구성 시간 : $O(MN)$
* LCS 역추적 시간 : $O(N + M)$
* 공간 복잡도 : $O(MN)$

### Knapsack Problem
무게가 한정된 가방에 물건을 넣어 최대 가치를 만드는 문제

#### Optimal Substructure - unbounded knapsack
* `K[x]` : 무게가 $x$일 때의 최대 가치
* $K[x] = max_i\{ K[x-w_i]+v_i \}$
* $O(nW)$의 시간 복잡도 (물건의 개수 $n$, 담고자 하는 무게 $W$)

#### Optimal Substructure - 0/1 knapsack
* `K[x,j]` : 무게가 $x$이고 $j$번째 물건까지 사용했을 때 최대 가치
1. $j$번째 물건 사용 : $K[x-w_j,j-1]$
2. $j$번째 물건 사용하지 않음 : $K[x,j-1]$