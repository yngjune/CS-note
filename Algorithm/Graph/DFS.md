# DFS
* 길(노드)을 선택해 최대한 깊게 탐색
* 막혔다면 이전 단계로 돌아가 다른 길을 선택해 최대한 깊게 탐색
* 스택 혹은 재귀를 통해 구현

<br/>

### pseudo code
1. **재귀**를 통한 구현
```python
DFS(node v):
    visited[v] = True
    for u adjacent to v:
        if not visited[u]:
            DFS(u)
```

2. **스택**을 이용한 구현
```python
DFS(node v):
    s = stack()
    visited[v] = True
    s.push(v)

    while s not empty:
        u = s.pop()
        if not visited[u]:
            visited[u] = True
            for w adjacent to u:
                if not visited[w]:
                    s.push(w)
```

<br/>

### DFS의 시간 복잡도
* `O(V + E)`
* 각 단계가 `O(1) + O(deg(V))`의 시간 복잡도


### DFS의 활용
* 트리를 구성
* connected component 탐색
* [topological sort](./TopoSort.md)
