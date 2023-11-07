# Minimum Spanning Tree
## Spanning Tree
* 신장 트리
* 그래프의 모든 노드를 포함
* 간선의 개수를 최소($n-1$)로 선택
* BFS 또는 DFS를 통해 그래프를 탐색하며 간선을 추가

### pseudocode
```python
BreadthFirstSpanningTree(v):
    tree = {}
    queue = Queue()
    queue.add(v)
    visited[v] = True

    while queue not empty:
        u = queue.pop()
        for w in adjacent[u]:
            if not visited[w]:
                tree.add([u,w])
                visited[w] = True
                queue.add(w)

    return tree


DepthFirstSpanningTree(u):
    visit[u] = True

    for w in adjacent[u]:
        if not visited[w]:
            tree.add([u,w])
            DepthFirstSpanningTree(w)
```

<br/>

## MST
* 최소 비용 신장 트리
* 간선에 가중치가 있는 그래프에서 가중치의 합이 최소인 신장 트리
* 네트워크 구성, 이미지 segmentation 등에 활용

### Greedy Choice Lemma
* `cut` : 정점을 두 그룹으로 나눔
* `light` : `cut`에 포함되는 간선 중 최소 비용 간선
* 간선의 집합 $S$가 MST에 포함된다 가정
* $S \cup  light$ 또한 MST에 포함됨

#### Proof of Lemma
* light edge $[u,v]$를 트리에 추가하면 사이클이 발생할 것
* cut을 가로지르는 다른 간선 $[x,y]$가 적어도 하나 존재
* $[x,y]$를 $[u,v]$로 대체한 새로운 트리 $T'$를 구성
* $[u,v]$는 light이므로 여전히 비용이 최소이고, 최소 신장 트리임이 보장됨

<br/>

## Prim's algorithm
* 정점 하나에서 시작해 트리를 키워 나감
* visited와 unvisited를 가르는 `cut` 고려
* 매 단계마다 비용이 가장 낮은 `light` 간선을 MST에 추가
* lemma에 의해 현재 단계의 선택이 전체 해에 위반하지 않음

### slow prim
```python
SlowPrim(s):
    # for G = (v, E) and starting vertex s
    [s,u] be light edge
    mst = {[s,u]}
    visited = {s, u}
    while visited.size() < |V|:
        [x,v] = light that x in visited, v not in visited
        visited.add(v)
        mst.add([x,v])

    return mst
```
* 시간 복잡도 $O(VE)$ : while문에서 매번 모든 간선을 검사하므로


### Efficient Implementation
* 각 정점이 자라는 트리로부터 거리 `dist`를 유지
* 하나의 간선으로 도달할 수 없는 경우 따로 관리

```python
Prim(s):
reached = {}
dist = [inf,...]
parent = [-1,]
unreached = V # priority queue
mst = {}

while all V in reached:
    u = unreached.pop()
    for v in adj[u]:
        dist[v] = min(dist[v], weight(u,v))
        if dist[v] updated:
            parent[v] = u
    reached.add(u)
    mst.add([p[u], u])

return mst
```
* 우선순위 큐(힙) 기반으로 동작
* 시간 복잡도 $O(E + Vlog(V))$ amortized
* 레드 블랙 트리를 이용해 구현 시 $O(Elog(V))$

<br/>

## Kruskal's algorithm
* 매 단계마다 사이클을 발생시키지 않는 가장 비용이 낮은 간선 선택
* 문제는 사이클이 발생하는지를 어떻게 검사?  => forest 유지
* union-find 자료구조 유지

```python
Kruskal(G(V,E)):
    E.sort(key=weight)
    MST = {}
    for v in V:
        make_set(v)
    for (u,v) in E:
        if find(u) != find(v):
            MST.add((u,v))
            union(u,v)
    return MST
```

### complexity
|function|# of call|
|--|--|
|makeSet|V|
|find|2E|
|union|V-1|

* 간선을 정렬 : $O(Elog(E))$ 또는 기수 정렬 시 $O(E)$
* 총 시간 복잡도 $O(Elog(E))$ 또는 $O(E)$