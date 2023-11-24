# Synchronization (2)
* 하드웨어 기반의 해결책은 복잡하고 응용 프로그래머에게 접근 가능하지 않음
* 대신 lock, semaphore, monitor 등 소프트웨어 기반 임계 영역 해결 가능

<br/>

## Mutex Locks
```c
while (true) {
    acquire()
        // CRITICAL SECTION
    release()
}
```
* 임계 영역에 진입하기 전 락을 획득하고, 종료 시 락을 반환

### Busy Waiting
* 한 프로세스가 임계 영역에 진입하면 다른 프로세스들은 가용할 때까지 기다려야 하는 문제 존재함
* CPU 사이클을 락이 가용한지 확인하는 데에 낭비하게 됨

### Spinlock (implements busy waiting)
* 임계 영역에 진입이 불가할 때, 다른 스레드로 문맥 전환을 하지 않고 루프를 돌면서 재시도하는 방식
* 락을 획득하고 해제되기까지 시간이 짧아 적은 시간만 대기하고 진입할 수 있는 경우 유리
* 문맥 교환이 이루어지지 않으므로, 문맥 교환 비용이 비싼 경우 유리
* 반면 오래 대기해야 할 경우 다른 스레드가 실행하지 못하게 되므로 불리

<br/>

## Semaphores
* `semaphore`
    * 커널에 의해 관리되는 0 이상의 크기를 갖는 정수 값
    * `wait`과 `signal`이라는 두 atomic 연산만으로 접근 가능 
* 뮤텍스 락과 달리, 세마포어를 통해 **여러 개**의 공유 자원에 대한 동시 접근 제어 가능
* 세마포어 값은 가용한 자원의 수로 초기화됨

### wait() and signal()
```c
signal(S) { // producer
    S++;
}

wait(S) {   // consumer
    while (S <= 0);
            // Busy Waiting
    S--;
}
```
* wait과 signal 연산, 즉 세마포 값을 변경하는 연산은 원자적으로 수행됨

### Busy Waiting을 방지하는 Sleep & WakeUp
```c
typedef struct {
    int value;
    struct PCB *list;
} semaphore;

signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) {
        remove P from S->list;
        wakeUp(P)
    }
}

wait(semaphore *S) {
    S->value--;
    if (S->value < 0) {
        add P to S->list;
        sleep();
    }
}
```
* `wait`
    * 세마포어 획득을 기다려야 하는 경우, Busy Waiting하는 대신 스스로 대기하고 다른 프로세스로 전환
    * 세마포어를 대기 중인 리스트에 PCB를 추가
* `signal`
    * 대기 중이던 프로세스를 리스트에서 꺼내서 깨움
    * 프로세스를 대기 큐에서 준비 큐로 옮김
* `PCB list`
    * 세마포어를 대기하고 있는 프로세스의 리스트
    * FIFO 혹은 다른 큐 전략 사용 가능

### atomic wait & signal
* wait과 signal이 원자적으로 실행되도록 보장해야 함
* 단일 프로세서 시스템
    * wait과 signal 실행 중에 **인터럽트 무효화**
* 멀티코어 시스템
    * 모든 코어에 대해 인터럽트 무효화 시 성능 저하 발생
    * 대신 compare_and_swap 이나 spinlock 등 기법을 활용

<br/>

## Monitors
* 데이터에 대한 동기화 도구들을 통합한 구조물
* 한 번에 하나의 프로세스만이 모니터에 진입할 수 있음

### Timing Error
* 뮤텍스 락과 세마포어의 경우, 명령어의 순서가 조금이라도 잘못되면 에러가 발생
    * 동시에 여러 프로세스가 임계 구역에 진입하거나 교착 상태가 발생하는 등
    * 사용자가 코드를 잘못 작성하는 등 문제 발생하기 쉬움
* 대신에 동기화 도구 및 함수들을 통합해 관리하는 모니터를 정의해 사용

### 모니터 구성 요소
* **shared (condition) variables**
    * 모니터의 함수를 통해서만 변수에 접근 가능
    * 각 프로세스는 모니터의 접근 권한 또는 변수와 관련된 조건을 만족하기를 대기
    * 각 상태 변수에 대해 대기하고 있는 프로세스의 큐를 유지
* **functions**
    * 모니터의 함수를 통해 모니터의 공유 변수에 접근해 상태를 변화시킴
* 모니터 내에서 한 번에 하나의 프로세스만 실행 가능하도록 보장해야 함!

### signal-and-wait
* 프로세스가 시그널을 주고, 다른 프로세스가 먼저 모니터에 진입해 실행하도록 양보

### signal-and-continue
* 프로세스가 시그널을 주고 자신이 먼저 진입해 실행한 뒤 다른 프로세스가 수행되게 함

<br/>

## Liveness
* 동시성을 제어하기 위한 동작 결과 어떤 프로세스가 **무한대로 대기**할 가능성이 생김
    * `deadlock`
    * `priority inversion`
* 데드락, 우선순위 역전 등 상황이 발생하지 않도록 동시성 제어 알고리즘 수행해야 함

### Deadlock
* 둘 이상의 프로세스가 서로가 사용하는 자원을 해제하기를 무한히 기다리는 상황


### Priority Inversion
1. 우선순위가 높은 H가 우선순위가 낮은 L이 사용하고 있는 커널 공유 자원을 대기
2. 공유 자원을 필요로 하지 않는 M이 L을 선점
3. H는 우선순위가 높음에도 공유 자원에 대한 락에 의해 실행되지 못하는 상황

#### Priority Inheritance
1. L의 작업이 끝날때까지 우선순위 H를 상속받음
    * M이 L을 선점하지 못하게 됨
2. L의 작업이 끝나면 우선순위가 원상복구되고, 공유 자원을 signal함
3. 대기 중이던 H가 다음에 실행됨