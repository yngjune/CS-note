# Topological Sort
* 위상 정렬
* Directed Acyclic Graph에서 의존성 관계를 만족하는 순서를 찾기
* 즉, 모든 간선 $(v, w)$에 대해 v가 w보다 먼저 위치하는 순서를 찾기
* 선수 과목 관계가 있는 수업을 수강할 순서, 의존 관계가 있는 패키지를 설치할 순서 등

<br/>

## DFS를 이용한 구현
```python
DFS(w, curTime):
    start[w] = curTime
    curTime += 1
    visit[w] = True

    for v in adj[w]:
        if not visit[w]:
            curTime = DFS(v, curTime)
            curTime += 1
    finish[w] = curTime
    return curTime

TopologicalSort(G(V,E)):
    start = [0] * V
    finish = [0] * V
    visit = [False] * V

    curTime = 1
    for v in V:
        if not visit[v]:
            curTime = DFS(v, curTime)

    order = [v for v in V]
    order.sort(key=labmda v:finish[v], reverse=True)
    return order
```
1. 각 노드에서 DFS를 시작한 시간과 끝낸 시간을 기록하도록 DFS 구현
2. finish 시간이 늦은 순서가 위상 정렬 순서
* in-degree가 0인 정점부터 DFS를 시작하지 않아도 상관 없음
* 간선 (v,w)에 대해 v의 finish가 w의 finish보다 큰 것을 이용
* 시간 복잡도 $O(VlogV + E)$

<br/>

## 큐를 이용한 구현
```python
TopologicalSort(G(V,E)):
    queue = Queue()
    order = list(V)

    for v in V:
        if inDegree[v] == 0:
            queue.push(v)
    
    for i in range(V):
        if queue empty:
            # Cycle Detected
            exit(1)
        u = queue.pop()
        order[i] = u
        for v in adj[u]:
            inDgree[v] -= 1
            if inDegree[v] == 0:
                queue.push(v)
    return order
```
1. in-degree가 0인 노드를 큐에 삽입
2. 큐에서 요소를 꺼낸 뒤 
    * 이웃한 정점들의 in-degree를 1 감소
    * in-degree가 0이 된 이웃 정점을 큐에 삽입
3. 2를 V번 반복
* V번 정점을 꺼내기 전에 큐가 빌 경우 사이클이 발생한 것
* 시간 복잡도 $O(V+E)$