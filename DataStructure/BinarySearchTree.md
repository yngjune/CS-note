# Binary Search Tree
* 요소의 값을 기반으로 동작
* 탐색, 삽입, 삭제 효율적

### Features
* 이진 트리
* 모든 노드의 키 값이 다르다 (하나의 키 값은 하나만 존재)
* 왼쪽 서브 트리의 값들이 루트 노드의 값보다 작다
* 오른쪽 서브 트리의 값들이 루트 노드의 값보다 크다

<br/>

### Search
```python
# Recursive
Search(root, target):
    if not root:
        return NULL
    if root.key == targe:
        return root.data
    if target < root.key:
        return Search(root.left, target)
    return Search(root.right, target)

# Iterative
Search(root, target):
    while root:
        if target == root.key:
            return root.data
        if target < root.key:
            root = root.left
        else
            root = root.right
    return NULL
```
1. 현재 노드와 타겟 키 값을 비교
2. 같다면 현재 노드의 값 리턴
3. 타겟이 더 작다면 왼쪽 서브 트리 탐색
4. 타겟이 더 크다면 오른쪽 서브 트리 탐색
5. 트리의 끝에 도달할 때까지 찾지 못한 경우 (`not root`) 탐색 실패
* 시간 복잡도 : $O(H)$

<br/>

### Insert
```python
SearchPosition(root, elem):
    if not root:
        return NULL
    
    prev = NULL
    while root:
        if root.key == elem.key:
            return NULL
        if root.key < elem.key:
            prev = root
            root = root.right
        if root.key > elem.key:
            prev = root
            root = root.left
    
    return prev

Insert(root, elem):
    last = SearchPosition(root, elem)
    node = Node(elem.key, elem.data)

    if last or not node:
        if root:
            if elem.key < last.key:
                last.left = node
            else:
                last.right = node
        else:
            root = node
```
1. `SearchPosition` : 트리가 공백이거나 elem이 검색되면 NULL 반환. 아니면 마지막 검사한 노드 위치 반환
2. 비어 있지 않은 트리에서 값이 검색된 경우를 제외하고 삽입 수행
3. 마지막으로 검사한 노드와 값을 비교해 왼쪽 혹은 오른쪽 자식으로 삽입
* 시간 복잡도 : $O(H)$

<br/>

### Delete
삭제하려는 노드의 자식의 개수에 대해 다음을 수행
1. 자식이 없는 경우
    * 노드를 삭제
2. 자식이 1개인 경우
    * 자식 노드를 현재 노드로 대체
3. 자식이 2개인 경우
    * 왼쪽 서브 트리의 가장 큰 노드
    * 오른쪽 서브 트리의 가장 작은 노드
    * 둘 중 하나를 삭제되는 노드로 대체
* 3번 경우에 대해, 대체하려는 노드가 자식이 2개인 경우는 존재하지 않음
* 자식이 2개라면 더 큰(작은) 노드를 발견했을 것
* 시간 복잡도 : $O(H)$

<br/>

### Balanced BST
* 탐색, 삽입, 삭제의 시간 복잡도는 $O(H)$
* 편향된 트리의 경우 $O(N)$의 복잡도
* 편향이 발생하지 않도록 트리를 `rotate`

#### AVL Tree

#### Red Black Tree
