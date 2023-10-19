# Merge Sort
```python
MergeSort(arr):
n = len(arr)
if n <= 1:
    return arr

left = MergeSort(arr[0:n/2])
right = MergeSort(arr[n/2:n])
return merge(left, right)
```
* 배열을 절반으로 나누어 재귀적으로 왼쪽, 오른쪽을 정렬한 후 병합

### Complexity
* 시간 복잡도 : worst `O(nlogn)`
* 공간 복잡도 : `O(n)`


### 연결 리스트의 정렬
* 퀵소트와 같이 random access가 필요한 알고리즘은 연결 리스트에서 불리
* 위와 같은 이유로 연결 리스트를 정렬할 때 병합정렬이 선호됨
* merge를 random access를 사용하지 않고 재귀적으로 link를 연결하도록 [구현](https://www.geeksforgeeks.org/merge-sort-for-linked-list/)


### Summary
* stable한 정렬 알고리즘
* 외부 정렬의 기본이 되는 알고리즘
* 연결 리스트의 데이터를 정렬할 때 퀵/힙정렬보다 효율적
* GPU를 이용한 정렬 알고리즘 병렬화에 유리

<br/>
<br/>

# Quick Sort
```python
QuickSort(arr):
if len(arr) <= 1:
    return

pivot = random.choice(arr)
L, R = partition(arr)
arr = [L, pivot, R]

QuickSort(L)
QuickSort(R)
```

### Complexity
* 시간 복잡도의 점화식 `T(N) = (|L|) + T(|R|) + O(N)`
* 이상적인 경우 (L과 R이 반으로 나누어질 때) : `T(N) = 2 * T(N/2) + O(N)` = `O(NlogN)`
* 최악의 경우 (피봇이 한쪽 끝일 때) : `T(N) = T(N-1) + O(N)` = `O(N^2)`
* 공간 복잡도 : `O(logn)`. 매 재귀 단계마다 콜 스택을 호출하므로

### 랜덤 피봇 선택?
* 피봇을 랜덤으로 선택할 경우 Expected Running Time은 `O(nlogn)`
* 두 개의 요소를 비교하는 횟수를 세어 시간 복잡도를 측정
* `X(a,b)`를 배열의 a번째 요소와 b번째 요소가 비교되는지 나타내는 랜덤변수
* 두 요소가 비교될 확률은 $\frac{2}{b-a+1}$ : 두 요소 사이의 요소들 중 a나 b가 먼저 피봇으로 선정될 경우
* 총 비교 횟수는 $\sum_{a=0}^{n-1}{\sum_{b=a+1}^{n-1}{E[X(a,b)]}} \le O(nlogn)$



### Summary
* good cache locality
* not stable
* 외부 정렬에 적용 가능
* divide and conquer
* in-place
* expected `O(nlogn)`, worst `O(n^2)`

<br/>
<br/>

# Heap Sort
### 힙의 성질
* 완전 이진 트리. 부모 노드의 값이 자식 노드의 값보다 크다(최대 힙)/작다(최소 힙).
* 인덱스가 1로 시작하는 배열로 구현
* `a[i]`의 부모 `a[i/2]`, 왼쪽 자식 `a[2i]`, 오른쪽 자식 `a[2i+1]`
* `push` : 값을 마지막 노드 위치에 삽입 후 위치를 조정. `O(logn)`
* `pop` : 루트 노드를 삭제. 마지막 노드의 위치를 루트 노드로 스왑 후 위치를 조정. `O(logn)`

### pseudo code - ascending order & max heap
```python
Heapify(arr, root, n):
    tmp = arr[root]
    child = 2 * root
    while child <= n:
        if child < n and (arr[child] < arr[child+1]):
            child += 1
        if tmp > arr[child]:
            break
        else:
            arr[child/2] = arr[child]
            child *= 2
    arr[child/2] = tmp

HeapSort(arr, n):
    for i = n/2 downto 1:
        Heapify(arr, i, n)
    for i = n-1 downto 1:
        swap(arr[1], arr[i+1])
        Heapify(arr, 1, i)
```
* `Heapify(arr, root, n)` : 크기가 n인 배열에서 인덱스가 root로 시작하는 서브트리를 힙으로 구성. `O(logn)`
1. 입력 배열을 초기 힙으로 구성. 모든 Internal 노드에 대해 Heapify 수행. `O(logn) * (n/2)`
2. 첫 노드 (가장 큰 노드)를 마지막 노드와 스왑하고 힙 재구성 => 가장 큰 노드가 정렬된 위치를 찾아감
3. 2를 (n-1)번 반복 `O(logn) * (n-1)`

### Complexity
* 시간 복잡도 : `O(nlogn)` worst
* 공간 복잡도 : `O(1)` worst

### Summary
* unstable
* 메모리 사용량을 고려하는 경우 선택
