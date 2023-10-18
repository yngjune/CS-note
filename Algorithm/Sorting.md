# 기본적인 정렬 알고리즘
### Bubble Sort
```python
BubbleSort(arr, n):
for i = 1 to n-1
    for j = 2 to n-i
        if compare(arr[j-1], arr[j]) > 0:
            tmp = arr[j-1]
            arr[j-1] = arr[j]
            arr[j] = tmp

```
* 이웃하는 두 요소를 비교해 순서를 교환하는 과정을 반복
* 각 패스 통과 시 한 개의 요소가 맞는 자리를 찾아감

### Insertion Sort
```python
InsertionSort(arr, n):
    for i = 1 to n-1:
        cur = arr[i]
        j = i - 1
        while j >= 0 and compare(arr[j], current) > 0:
            arr[j+1] = arr[j]
            j -= 1
        arr[j+1] = current
```
* 각 단계에서 미정렬 구간의 첫 번째 데이터를 정렬된 구간의 적절한 위치에 삽입
* k번 패스 수행 후 맨 앞에서 (k+1)개의 데이터는 정렬된 상태

### Selection Sort
```python
SelectionSort(arr, n):
    for i = 0 to n-2:
        min_idx = i
        for j = i to n-1:
            if compare(arr[j], arr[min_idx]) < 0:
                min_idx = j
        
        tmp = arr[i]
        arr[i] = arr[min_idx]
        arr[min_idx] = tmp
```
* 각 단계에서 미정렬 구간의 가장 작은(큰) 데이터를 찾아 맨 앞 데이터와 교환
* k번 패스 수행 시 맨 앞 k개의 데이터는 정렬된 상태

### Summary
* 세 정렬 알고리즘이 동작함을 Proof by Induction으로 증명 가능
* 셋 모두 in-place 알고리즘으로 추가 메모리 필요 없음
* 셋 모두 `O(N^2)`의 시간 복잡도

<br/>
<br/>

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
* 시간 복잡도는 `O(nlogn)`. `log(n)`개의 레벨에서 각각 `merge`하는 데에 `O(n)` 소요


### 연결 리스트의 정렬
* 퀵소트와 같이 random access가 필요한 알고리즘은 연결 리스트에서 불리
* 힙소트 등은 연결 리스트에 대해 동작 불가능
* 위와 같은 이유로 연결 리스트를 정렬할 때 병합정렬이 선호됨
* merge를 random access를 사용하지 않고 재귀적으로 link를 연결하도록 [구현](https://www.geeksforgeeks.org/merge-sort-for-linked-list/)


### Summary
* 외부 정렬의 기본이 되는 알고리즘
* 연결 리스트의 데이터를 정렬할 때 퀵/힙정렬보다 효율적
* GPU를 이용한 정렬 알고리즘 병렬화에 유리