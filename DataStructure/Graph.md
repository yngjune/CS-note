# Graph
* $vertex$ (정점)
* $edge$ (간선)
* $G(V,E)$
* 계층 구조가 아닌 일반적인 $N:M$ 관계
* 사이클이 존재할 수 있음

<br/>

### 그래프 개념
#### Directed vs Undirected
* Directed Graph : 간선에 방향 존재. $[u,v] \ne [v,u]$
* Undirected Graph : 간선에 방향 없음. $(u,v) = (v,u)$

#### Complete Graph
* Complete Graph : 간선의 수가 최대인 그래프
* 즉, 각 정점이 다른 모든 정점과 간선으로 연결됨

#### Edge Relationships
* Degree of vertex : 정점에 연결된 간선의 수.
* 방향 그래프는 out degree와 in degree 구분
* 간선 $[u, v]$에 대하여,
* Adjacent (인접) : $u$와 $v$는 인접함
* Incident (부속) : 간선 $[u, v]$는 $u$와 $v$에 부속됨

#### Path
* path (경로) : 정점 u에서 v까지 간선으로 이어진 경로 상 정점들의 나열
* length of a path : 경로 상 간선의 수
* simple path : 정점을 반복되게 거치지 않는 경로
* cycle : 처음과 끝 정점이 같은 simple path

#### Connected
* Connected(u,v) : 정점 u에서 v까지 경로가 존재
* Connected(G) : 그래프의 모든 서로 다른 정점 쌍 u, v에 대해 경로가 존재
* Strongly Connected : 방향 그래프에서 각 정점 쌍에 대해 정점 u에서 v까지, v에서 u까지의 경로가 존재
* Connected Component : maximal connected subgraph

#### Weighted Graph
* 간선에 가중치를 부여한 그래프
* 정점에서 다른 정점까지 도달하는 비용 등에 활용

<br/>

### 그래프 구현
#### Adjacency Matrix
* 2차원 배열을 이용
* 간선 $[u,v]$를 $adjMat[u][v]$로 표현
* 간선이 있으면 1(true), 없으면 0(false), 가중치 그래프의 경우 가중치를 기입
* 무방향 그래프는 symmetric
* 무방향 그래프에서 차수(degree)는 행의 합
* 방향 그래프에서 행의 합은 진출 차수, 열의 합은 진입 차수

#### Adjacency Lists
* 각 정점에 대해 연결된 정점을 체인(연결 리스트)로 표현
* 방향 그래프의 경우, 진출 간선 체인과 진입 간선 체인을 모두 유지해야 함

#### Comparison
|G(V,E)|matrix|list|
|--|--|--|
|edge membership|$O(1)$|$O(deg(U))$ or $O(deg(V))$|
|neighbor query|$O(V)$|$O(deg(V))$|
|space complexity|$O(V^2)$|$O(V+E)$|
* edge membership : 간선 [u,v]가 그래프에 포함되는가?
* neighbor query : 정점 v의 인접 정점들


#### Adjacency MultiLists
* 무방향 그래프에서, 하나의 간선이 두 개의 노드에서 중복으로 표현됨!
* $[marked, v1, v2, path1, path2]$로 각 간선을 표현
* ajdLists는 간선에 대한 포인터
* path 필드를 따라가며 정점에 연결된 간선 구함