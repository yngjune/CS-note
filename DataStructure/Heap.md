# Heap
* 완전 이진 트리
* max heap : 각 노드의 값이 자식 노드의 값보다 크다
* min heap : 각 노드의 값이 자식 노드의 값보다 작다

<br/>

## Insertion
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
* 시간 복잡도 : $O(logN)$

<br/>

## Deletion
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
* 시간 복잡도 : $O(logN)$

<br/>

## Heapify (buildHeap)
```python

```
* 길이가 $N$인 리스트를 받아 힙을 구성
* `siftDown` 또는 `siftUp` 통해 구현. 최대 힙의 경우 다음과 같이 동작
    * `siftDown` : 노드의 값이 작을 때 자식 노드 중 큰 노드와 위치를 바꿈
    * `siftUp` : 노드의 값이 클 때 부모 노드와 위치를 바꿈
* siftUp heapify의 시간 복잡도 $O(NlogN)$, siftDown heapify의 시간 복잡도 $O(N)$

### siftUp Heapify
```python
SiftUp(arr, idx):
    i = idx
    tmp = arr[idx]
    while i != 1 and arr[i/2] < arr[i]:
        arr[i] = arr[i/2]
        i /= 2
    arr[i] = tmp

SiftUpHeapify(arr):
    n = len(arr)
    for i=1 to n:
        SiftUp(arr, i)
```
* 배열의 첫 요소부터 마지막 요소까지 `siftUp` 수행
* 시간 복잡도 $O(NlogN)$
    * siftup의 각 단계의 비교는 루트까지의 거리만큼 수행
    * $(h*n/2)+((h-1)*n/4)+((h-2)*n/8)+...+(0*1)$

### siftDown Heapify
```python
SiftDown(arr, idx, n):
    parent = idx
    tmp = arr[parent]
    child = parnet * 2

    while child <= n:
        if child < n and arr[child] < arr[child+1]:
            child += 1
        if tmp >= arr[child]:
            break
        arr[parent] = arr[child]
        parent = child
        child *= 2
    arr[parent] = tmp

SiftDownHeapify(arr):
    n = len(arr)
    for i=n downto 1:
        SiftDown(arr, i)
```
* 배열의 첫 요소부터 마지막 요소까지 `siftUp` 수행
* 시간 복잡도 $O(N)$
    * siftdown의 각 단계의 비교는 말단 노드까지의 거리만큼 수행
    * $(0*n/2)+(1*n/4)+(2*n/8)+...+(h*1)$


## Deletion of Arbitrary Element
### Idea1
1. 요소를 삭제
2. 빈 칸을 메우기 위해 shift : $O(N)$
3. shift된 요소들이 heap 조건 만족하는지 모르므로 각각 재정렬: $O(N * logN)$

### Idea2
1. 삭제하고자 하는 노드의 값을 현재의 최대(최소)값보다 큰(작은) 값으로 변환
2. 값을 바꾼 이후 루트 노드 위치까지 올라가며 위치 재정렬 : $O(logN)$
3. 루트 노드의 위치로 올라간 요소를 삭제 : $O(logN)$

### Idea3
1. 삭제하고자 하는 노드를 마지막 위치의 노드와 스왑
2. 힙을 재정렬 : $O(logN)$

### Summary
* 위 경우는 인덱스를 기반으로 요소를 $O(logN)$ 시간에 삭제할 수 있음을 보임
* 요소의 값을 통한 삭제를 하려면 먼저 탐색($O(N)$)이 필요함
* 요소의 값을 통한 삭제는 Binary Search Tree 등 $O(logN)$ 시간 복잡도가 필요한 구조를 사용하는 것이 유리

<br/>

## Applications
* Priority Queue 구현
* Heap Sort
* Huffman code