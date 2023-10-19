# O(nlogn)보다 빠른 정렬 알고리즘?
### Comparison based Sorting
* 한 번에 최대 두 개 요소의 대소관계를 비교할 수 있다
* 퀵정렬, 병합정렬, 힙정렬은 배열의 두 요소의 비교를 기반으로 동작

### Decision Tree
* 이진 결정 트리를 통해 비교 기반 정렬의 과정을 모델링
* `leaf node` : 정렬된 결과. 총 `n!`가지 리프 노드 존재
* `internal node` : 배열의 두 요소를 비교하는 과정
* 트리의 높이가 정렬 알고리즘의 시간 복잡도
* 트리의 높이는 $\log{n!} \ge \Omega(nlogn)$ 의 시간 복잡도
* 즉 비교 기반 정렬 알고리즘의 최적 시간 복잡도는 $O(nlogn)$

<br/>
<br/>

# Counting Sort
1. 배열의 모든 요소를 포함하는 메모리 공간(버킷) 준비
2. 배열의 각 요소를 버킷에 담기
3. 버킷을 이어붙이기

### Complexity
* 시간 복잡도 : $O(N)$
* 공간 복잡도 : 나타날 수 있는 모든 값의 범위만큼 메모리 필요

<br/>
<br/>

# Radix Sort
* 정수의 정렬, 문자열의 사전 순서 정렬 등에 사용
* 단순 Counting Sort보다 더 적은 메모리 사용

```python
RadixSort(arr):
for lsd to msd:
    initialize r buckets
    for i = 0 to len(arr) - 1
        insert arr[i] into bucket
    arr = concat(buckets)
```

### Complexity
* `M`보다 작거나 같은 `n`개의 정수를 `r`을 베이스로 정렬
* digit의 수 $ d=\lfloor\log_{r}{M}\rfloor +1$
* 각 단계에서 `r`개의 버킷을 초기화 뒤 `n`개의 요소를 삽입. $O(n+r)$
* 베이스 `r`이 크면 시간복잡도는 적지만 공간복잡도는 크다
* 베이스 `r`이 작으면 시간복잡도는 크지만 공간복잡도는 작다
* 시간 복잡도 : $O(d(n+r)) = O((\lfloor\log_{r}{M}\rfloor +1)(n+r))$

### vs Comparison based Sorting
* 비교 기반 정렬은 임의의 비교 가능한 원소가 와도 정렬 가능
* $\pi$ 등 소수점이 무한히 이어지거나 매우 큰 수는 Radix Sort에서 불리
* 매우 큰 수를 비교하려 하면 시간, 공간을 많이 사용하게 되므로