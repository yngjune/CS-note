# Synchronization
* 여러 프로세스가 협력해 수행할 때, 같은 위치의 데이터에 접근하는 경우
* **데이터 일관성** 문제가 발생할 수 있음
* 주소 공간을 공유하는 프로세스들의 **순차적 실행**을 보장하고자 함!
    * 즉, 수행 결과가 임의의 순서로 프로세스를 실행한 결과와 같도록

<br/>

## Critical Section Problem
### 임계 구역이란?
```c
entry
    // CRITICAL SECTION
exit
    // REMAINDER SECTION
```
* `critical section` : 다른 프로세스와 공유되는 데이터를 포함하는 코드 구간
* 임계 구간에 한 번에 하나의 프로세스만 진입해 수행하도록 스케줄

### 임계 구역 문제의 요구사항
1. `mutual exclusion`
    * 프로세스가 임계 구역을 실행하고 있다면, 다른 프로세스는 임계 구역에 진입할 수 없음
2. `progress`
    * 임계 구역 진입을 희망하는 프로세스 중 하나를 선정하는 시간이 유한해야 함
3. `bounded waiting`
    * 프로세스가 임계 구역 진입을 요청한 뒤 진입까지 대기하는 시간이 유한해야 함

### 임계 구역 문제의 중요성
* 수많은 커널 스레드가 동시에 수행되며 경쟁 조건이 발생함
    * 두 스레드가 동시에 다른 파일을 열면 커널의 open file list를 접근해야 함
    * 새 프로세스를 생성하려면 커널의 next_available_pid를 접근해야 함
* 경쟁 조건이 발생하기 쉬운 커널 자료구조
    * 메모리 할당을 위한 자료구조
    * 프로세스 리스트
    * 인터럽트 테이블 등 인터럽트 처리를 위한 자료구조

<br/>

## Peterson's Solution for C-S Problem
* 두 개의 프로세스 간 공유 자원을 경쟁하는 임계 구역 문제를 해결하는 알고리즘
* 현대의 컴퓨터 시스템은 명령어를 Out-of-Order 실행하기 때문에 정확성이 보장되지는 않음

### 알고리즘
```c
// Process P_i
while(true) {
    flag[i] = true;
    turn = j;
    while (flag[j] && turn == j)
        // CRITICAL SECTION
    flag[i] = false;
        // REMAINDER SECTION
}

// Process P_j
while(true) {
    flag[j] = true;
    turn = i;
    while (flag[i] && turn == i)
        // CRITICAL SECTION
    flag[j] = false;
        // REMAINDER SECTION
}
```
* `flag`를 통해 프로세스가 임계 영역에 들어갈 준비가 되었음을 표시
* `turn`을 통해 임계 영역을 진입할 순서를 표시
* mutual exclusion, progress, bounded waiting 성질 모두 만족함

<br/>

## HW 지원을 통한 C-S 문제 해결
* 동기화 문제의 소프트웨어 해법인 피터슨 알고리즘은 현대 컴퓨터 시스템의 명령어 재배치에 의해 정확성이 보장되지 않음
* 대신 하드웨어적 지원을 통해 동시성 문제를 해결하려는 시도

### Memory Barrier
* 컴퓨터 구조가 프로그램에게 메모리를 제공하는 방법을 모델링함
* memory barrier **명령어**를 제공해 메모리 변경 사항을 다른 프로세서에게 전파함

### HW Instruction
* atomic하게 실행되는 하드웨어 명령어를 제공

#### test_and_set
```c
bool test_and_set(bool *target) {
    bool rv = *target;
    *target = true;
    return rv;
}

// Initial value of lock == false
do {
    while (test_and_set(&lock));
        // WAIT
    lock = false;
        // REMAINDER SECTION
} while (true);
```

#### compare_and_swap
```c
int compare_and_swap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected)
        *value = new_value;
    return temp;
}

// Process P_i structure
while (true) {
    while (compare_and_swap(&lock, 0, 1) != 0);
        // WAIT
    lock = 0;
        // REMAINDER SECTION
}
```

### Atomic Variable
* 원시 타입에 대한 변수로, atomic한 동작이 수행됨을 보장함