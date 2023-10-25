# Stack
* 선형 자료구조
* 요소 간 순서 유지됨
* LIFO (후입선출)


### ADT
* `push(element)` : 스택에 요소를 삽입
* `pop()` : 스택에서 `top` 위치의 요소를 제거
* `peek()` : 
* `isEmpty()` : 스택이 비었는지 확인


### 스택의 구현
1. `linked list`
    * `push` : worst `O(1)`
    * `pop` : worst `O(1)`
    * `O(1)`이지만 노드를 동적 할당하기 때문에 비용이 클 수 있음

2. `dynamic array`
    * `push` : amortized O(1). 대부분의 경우 `O(1)`이지만 배열의 최대 크기를 다 사용한 경우 배열의 크기를 늘려 새로 할당(`O(n)`)
    * `pop` : worst O(1). 삭제 시 사용하지 않게 된 배열 공간을 회수하려 하는 경우 `O(n)`

### Problems
* 중위 > 후위 표기법 변환
* 후위 표기법 계산
* 후위 표기식을 이용한 수식 이진 트리 구성