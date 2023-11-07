# Shortest Path
* 가중치가 있는 그래프에서 두 정점 간 최저 비용의 경로를 구하는 문제
* 지도 길찾기, 네트워크 라우팅 등에 활용 가능

<br/>

## Dijkstra's Algorithm
* single source shortest path
* 음수 가중치 간선이 없을 때 적용 가능

### pseudocode
```python
Dijkstra(G,s):
    d[v] = INF for all v in V
    d[s] = 0

    while not_sure node exist:
        u = smallest d[u] in not_sure
        for v in adj[u]:
            d[v] = min(d[v], d[u] + weight(u,v))
        mark u as sure
```
1. 모든 정점을 not_sure로 표시
2. 모든 정점의 초기 거리는 무한대
3. not_sure에서 거리가 가장 짧은 정점 u를 선택
    * 인접 노드 v에 대해 거리를 갱신
    * u를 sure로 표시 (u의 최단거리 계산됨)
4. 모든 정점이 sure일 때까지 3을 반복

### Complexity
* $V*(T(findMin) + T(removeMin)) + E * T(updateKey)$

#### array
|operation|complexity|
|--|--|
|findMin|$O(V)$|
|removeMin|$O(V)$|
|updateKey|$O(1)$|
|Total|$O(V^2)$|

#### RBTree
|operation|complexity|
|--|--|
|findMin|$O(logV)$|
|removeMin|$O(logV)$|
|updateKey|$O(logV)$|
|Total|$O((V+E)logV)$|

#### Heap
|operation|complexity|
|--|--|
|findMin|$O(1)$|
|removeMin|$O(logV)$|
|updateKey|$O(1)$|
|Total|$O(VlogV+E)$|

<br/>

## Bellman-Ford Algorithm
* single source shortest path
* [**DP**](./DynamicProgramming.md) 아이디어를 활용
* 음수 가중치 처리 가능 (음수 사이클 감지)
* 음수 사이클이 존재하면 최단 비용의 개념이 성립하지 않음

### pseudocode
```python
BellmanFord(G,s):
    initialize d(0) to d(V-1) of length V
    d(0)[v] = INF for v in V
    d(0)[s] = 0

    for i = 0 to V-2:
        for v in V:
            d(i+1)[v] = min(d(i)[v], d(i)[u] + w(u,v))
```
* `d(i)[v]`는 최대 i개의 간선을 사용했을 때 s와 v 간의 최단 거리
* `d(V-1)[v]`는 음수 사이클이 없을 경우 s와 v 간의 최단 거리
* 메모리 절약을 위해 두 개의 리스트만 유지하도록 구현
* 시간 복잡도 $O(VE)$

### 음수 사이클 감지
```python
BellmanFord(G,s):
    for i = 0 to V-1:
        ...
    if d(V-1) != d(V):
        return NEGATIVE_CYCLE
    else dist(s,v) == d(V-1)[v]
```
* 음수 사이클이 존재하면 최단 거리의 개념이 성립하지 않음
* 반복문을 한 번 더 실행해 최단거리 결과가 달라졌다면 음수 사이클 존재

### Dynamic Programming
* Bellman-Ford 알고리즘은 DP 패러다임의 일종
* `d(i+1)[v]`를 계산하기 위해 `d(i)[u]`라는 부분 문제의 해를 활용
* `d(i)[u]`는 `i+1`번째 단계에서 여러번 다시 계산됨
* 즉, optimal substructure와 overlapping subproblem 구조가 존재함

<br/>

## Floyd-Warshall Algorithm
* all-pairs shortest path
* [**DP**](./DynamicProgramming.md) 아이디어를 활용

### pseudocode
```python
D(k) = list(V) for k=0 to V
D(k)[u,u] = 0 for all u, all k
D(k)[u,v] = INF for u!=v, all k
D(0)[u,v] = weight(u,v) for (u,v) in E

for k=1 to V:
    for (u,v) in V^2:
        D(k)[u,v] = min(D(k-1)[u,v], D(k-1)[u,k] + D(k-1)[k,v])

return D(n)
```
* `D(k)[u,v]`는 경로 상 종단 노드로 1부터 k를 사용했을 때 정점 u에서 v까지의 최단거리
* 메모리 절약을 위해 두 개의 2차원 배열만 사용하도록 구현 가능
* 시간 복잡도 $O(V^3)$
* 공간 복잡도 $O(V^2)$

### 음수 사이클
* $D^{(V)}[u,u] \lt 0$인 정점이 존재할 경우 음수 사이클 존재

### Dynamic Programming
* `D(k)[u,v]`를 계산 위해 `D(k-1)`을 사용
* 정점 k를 사용하지 않는 경우 `D(k-1)[u,v]`, 사용하는 경우 `D(k-1)[u,k] + D(k-1)[k,v]`
* optimal substructure와 overlapping subproblem 만족