# Queue
* 선형 자료구조
* FIFO (선입선출)
* rear에서 삽입, front에서 삭제

<br/>

### ADT
|function|description|
|--|--|
|`enQueue(elem)`|rear 위치에 요소를 삽입|
|`deQueue()`|front 위치의 요소를 삭제|
|`isEmpty()`|큐가 비었는지 확인|
|`isFull()`|배열 기반 큐일 경우, 배열이 꽉 찼는지|
|`front()`|맨 앞 요소를 반환|
|`rear()`|맨 끝 요소를 반환|


<br/>

### 큐의 구현
1. **dynamic array**
    * rear 인덱스를 한 칸 오른쪽으로 움직여 배열의 빈 공간에 삽입
    * front 인덱스를 한 칸 오른쪽으로 움직여 요소를 삭제
    * 인덱스가 배열의 맨 끝일 경우 다음 위치를 배열의 맨 앞으로 옮김 (원형 배열)
    * `front == rear` : 큐가 비어 있는 경우
    * `front == (rear + 1) % len` : 큐가 꽉 찬 경우. 배열의 모든 공간을 사용하면 큐가 빈 경우와 꽉 찬 경우가 모두 `front == rear`로 구분이 불가능하므로 한 칸이 빈 경우를 큐가 꽉 찬 것으로 간주
    * 삭제 동작은 worst `O(1)`. 삽입 동작은 amortized `O(1)`. 큐가 꽉 찬 경우 배열을 새로 할당 뒤 요소를 복사하는 데에 `O(n)`.
2. **linked list**
    * 맨 앞과 맨 뒤 노드에 `front`, `rear` 포인터를 유지
    * 삽입, 삭제 동작이 worst `O(1)`이지만 노드를 동적 할당하므로 비용이 클 수 있음
3. **using two stacks**
    * 큐의 삽입, 삭제 (읽기, 쓰기) 동작을 input 스택과 output 스택으로 나눔
    * `enqueue` : input 스택에 push. worst `O(1)`
    * `dequeue` : output 스택이 비어 있을 경우 input 스택이 빌 때까지 pop하며 output 스택으로 push. 하나의 요소를 output에서 pop
    * 전체적인 시간 복잡도는 amortised `O(n)`. 삭제 동작은 output 스택이 비어 있을 경우 `O(n)` 제외 대부분의 경우 `O(1)`