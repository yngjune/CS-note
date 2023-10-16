# BFS
* `Breadth First Search` : 너비 우선 탐색
* 그래프를 탐색하는 알고리즘 중 하나
* 거리(깊이)가 낮은 노드 순서대로 방문

<br/>

### pseudo code
```python
BFS(node v):
    queue Q
    visited[v] = True
    Q.enqueue(v)

    while Q not empty:
        u = Q.deque()
        for w adjecent to u:
            if not visited[w]:
                visited[w] = True
                Q.enqueue(w)
```
<br/>

### 알고리즘 동작
1. 인자로 탐색을 시작할 노드를 받음
2. 각 노드를 방문했는지를 기록할 `visited` 자료구조 정의 및 초기화
3. 큐를 초기화 및 시작 노드 삽입
4. 큐에서 노드 하나를 제거한 뒤, 방문하지 않은 인접 노드를 방문 및 큐에 삽입
5. 큐가 빌 때까지 4번을 반복

<br/>

### 왜 큐를 이용해 구현?
* 큐의 선입선출(FIFO) 구조를 활용하기 위함
* 인접 노드(깊이가 깊어짐)를 rear 위치에 삽입
* 먼저 삽입한 (깊이가 얕은) 노드를 먼저 삭제(탐색)하므로