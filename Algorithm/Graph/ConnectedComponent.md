# Connected Components
### Connected Component
* 그래프에서 연결 요소를 구하기
* 방문되지 않은 정점에 대해 DFS 또는 BFS를 반복 호출

```python
Connected():
    for i = 0 to MAX_VERTICES:
        if not visited[i]:
            DFS(i)
```
<br/>

# Biconnected Components
* `articulation point`(단절점) : 무방향 연결 그래프에서 하나의 정점과 부속된 간선들을 삭제했을 때 2개 이상의 연결 요소가 만들어지는 정점
* `biconnected graph` : 단절점이 없는 그래프
* `biconnected component` : maximal connected subgraph
* 통신 네트워크 등 단절이 없어야 하는 상황에서 biconnected 여부 중요

### algorithm
1. depth first spanning tree를 통해 노드를 방문하는 순서 `dfn` 계산
2. 각 정점의 `low`값 계산
    * $low[u] = min(dfn[u], min(low[w]|w \in child(u)), min(dfn[w]|(u,w) backedge))$
    * `backedge` : 깊이 우선 신장 트리에 포함되지 않는 간선
    * `low[u]` : u의 자식들과 최대 하나의 backedge를 이용해 도달 가능한 가장 작은 dfn
3. 단절점 계산
    * 정점 u의 자식 w에 대해 $dfn[u] \le low[w]$ 인 경우 단절점
4. 단절점을 기준으로 그래프를 나누어 이중 결합 요소 계산

### pseudocode
```python
GetArticulationPoints(cur, d):
    visited[cur] = True
    dfn[cur] = d
    low[cur] = d
    child_count = 0
    is_articulation = false

    for nxt in adj[cur]:
        if not visited[nxt]:
            parent[nxt] = cur
            GetArticulationPoints(nxt, d+1)
            childCount += 1
            if low[nxt] >= dfn[cur]:
                is_articulation = True
            low[cur] = min(low[cur], low[nxt])
        else if nxt != parent[cur]:
            low[cur] = min(low[cur], dfn[nxt])
    if (parent[cur] != null and is_articulation) or (parent[cur] == null and child_count > 1):
        output cur as articulation point
```

<br/>

# Strongly Connected Components