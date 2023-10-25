# Heap
* 완전 이진 트리
* max heap : 각 노드의 값이 자식 노드의 값보다 크다
* min heap : 각 노드의 값이 자식 노드의 값보다 작다

<br/>

### Application
* Priority Queue 구현
* Heap Sort
* Huffman code

<br/>

### Insertion
```python
push(item):
    if heap full:
        resize()
    
    heap.size += 1
    idx = heap.size

    while idx != 1 and heap[idx] > heap[idx/2]:
        heap[idx] = heap[idx/2]
    heap[idx] = item
```
* 최대 힙의 삽입 예시 수도코드
* 힙의 마지막 노드 위치에 노드 삽입
* 루트 노드까지 부모 노드와 값을 비교하며 올바른 위치에 배치
* 시간 복잡도 : $O(log_2n)$

<br/>

### Deletion
```python
pop():
    if heap empty:
        EmptyError

    item = heap[1]
    temp = heap[heap.size]
    heap.size -= 1
    parent = 1, child = 2

    while child <= heap.size:
        if child < heap.size and heap[child] < heap[child+1]:
            child += 1
        if temp >= heap[child]:
            break
        heap[parent] = heap[child]
        parent = child
        child *= 2
    
    heap[parent] = temp
    return item
```
* 최대 힙의 삭제 수도코드
* 루트 노드 (가장 크거나 가장 작은 노드) 반환
* 마지막 위치의 노드 `temp`를 루트 노드 위치로 옮기고 자식 노드와 비교하며 올바른 자리에 배치
* 시간 복잡도 : $O(logn)$

<br/>

### Deletion of Arbitrary Element
#### Idea1
1. 요소를 삭제
2. 빈 칸을 메우기 위해 shift : $O(N)$
3. shift된 요소들이 heap 조건 만족하는지 모르므로 각각 재정렬: $O(N * logN)$

#### Idea2
1. 삭제하고자 하는 노드의 값을 현재의 최대(최소)값보다 큰(작은) 값으로 변환
2. 값을 바꾼 이후 루트 노드 위치까지 올라가며 위치 재정렬 : $O(logN)$
3. 루트 노드의 위치로 올라간 요소를 삭제 : $O(logN)$

#### Idea3
1. 삭제하고자 하는 노드를 마지막 위치의 노드와 스왑
2. 힙을 재정렬 : $O(logN)$

#### Summary
* 위 경우는 인덱스를 기반으로 요소를 $O(logN)$ 시간에 삭제할 수 있음을 보임
* 요소의 값을 통한 삭제를 하려면 먼저 탐색($O(N)$)이 필요함
* 요소의 값을 통한 삭제는 Binary Search Tree 등 $O(logN)$ 시간 복잡도가 필요한 구조를 사용하는 것이 유리