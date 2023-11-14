# Binary Search
* 길이가 $N$ 인 **정렬된** 리스트에 대해 $O(logN)$ 복잡도로 탐색
* 배열을 중간점을 기준으로 반으로 나누어 탐색 범위를 반씩 줄이며 탐색

<br/>

## Implementations

### 중복된 요소 없음, 값이 없을 경우 -1 반환
```python
def bsearch(lst, target):
    start = 0
    end = len(lst) - 1
    while start <= end:
        mid = (start + end) // 2
        if lst[mid] < target:
            start = mid + 1
        elif lst[mid] > target:
            end = mid - 1
        else:
            return mid

    return -1
```

### 중복 요소 없음, 값이 없을 경우 삽입할 위치 반환
```python
def bsearch(lst, target):
    start = 0
    end = len(lst) - 1
    while start <= end:
        mid = (start + end) // 2
        if lst[mid] < target:
            start = mid + 1
        elif lst[mid] > target:
            end = mid - 1
        else:
            return mid

    return start
```

### 중복된 요소 중 가장 왼쪽 위치 반환
```python
def bsearch_left(lst, target):
    start = 0
    end = len(lst) - 1
    while start <= end:
        mid = (start + end) // 2
        if lst[mid] < target:
            start = mid + 1
        else:
            end = mid - 1

    return start
```

### 중복된 요소 중 가장 오른쪽 위치 반환
```python
def bsearch_right(lst, target):
    start = 0
    end = len(lst) - 1
    while start <= end:
        mid = (start + end) // 2
        if lst[mid] <= target:
            start = mid + 1
        else:
            end = mid - 1

    return end
```