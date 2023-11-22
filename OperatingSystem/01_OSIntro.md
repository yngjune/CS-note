# OS Introduction

## Computer System Organization
* CPU와 디바이스 컨트롤러들이 공통 버스를 이용해 공유 메모리에 연결됨
* CPU와 디바이스 컨트롤러는 동시에 동작하며 메모리 사이클을 경쟁

### Device Controller
* 키보드, 마우스 등의 장치를 관리하는 하드웨어
* 장치와 로컬 버퍼 사이에 데이터를 옮기는 역할을 수행
* 로컬 버퍼와 특수 목적 레지스터를 유지
* **디바이스 드라이버** : 장치의 동작을 처리하는 소프트웨어
* 디바이스 컨트롤러가 디바이스 드라이버의 동작을 돕는 인터페이스처럼 동작


### Interrupt
* 하드웨어가 시스템 버스를 통해 CPU에 신호를 보냄
* 인터럽트 발생 시 CPU는 ISR로 이동해 인터럽트를 처리 후 원래 루틴을 실행
* interrupt table : 장치별로 처리해야 할 인터럽트 서비스 루틴의 위치가 저장된 테이블

#### Synchronous vs Asynchronous I/O
* `synchronous` : IO 동작이 완료될때까지 CPU가 대기
* `asynchronous` : IO 동작이 수행되는 동안 CPU가 다른 작업을 수행

#### maskable vs non-maskable Interrupt
* `maskable` : 인터럽트를 무효화할 수 있는 경우. 중요한 명령을 수행해야 하면 인터럽트를 끔
* `non-maskable` : 빠른 처리가 필요한 중요한 오류를 처리해야 하는 인터럽트

#### Interrupt Chaining
* 시스템이 인터럽트 테이블보다 더 많은 양의 장치를 가지고 있는 경우
* 인터럽트 테이블의 각 요소가 핸들러의 리스트에 대한 포인터

### DMA (Direct Memory Access)
* CPU의 개입 없이 주변 장치가 메모리에 직접 접근해 데이터를 읽고 쓸 수 있는 IO 처리 방식
* 빈번한 ISR 처리로 CPU 사이클이 낭비되는 것을 방지함
    * 예를 들어, 빠른 IO 장치인 디스크가 매 바이트를 전송할 때마다 인터럽트를 발생시키는 경우
    * 많은 CPU 사이클을 디스크 ISR 처리를 위해 할당해야 할 것임

<br/>

## OS Operations
### Initial (Bootstrap) program
* 펌웨어에 저장되어 시스템이 처음 시작할 때 동작
* OS 커널을 메모리에 적재하고 실행해 사용자에게 서비스 제공
* 리눅스의 systemd

### Interrupt & Exception
* `interrupt` : IO 등 하드웨어가 발생시키는 신호
* `trap(exception)` : 소프트웨어에서 발생한 에러, 혹은 시스템 콜

### Multiprogramming
* 메모리에 동시에 여러 프로세스를 유지해 CPU 활용율을 높임
* CPU가 여러 프로세스 간 전환하며 번갈아 실행

### Dual-mode & Multi-mode
* 악의적인 사용자 프로그램이 시스템의 동작과 관련된 부분을 이용하지 못하도록 하드웨어적으로 방지
* `previleged instruction` : IO, 타이머 등 시스템 동작과 관련된 명령들
* `user mode`
    * 사용자 프로그램이 실행 중일 때. 특권 명령을 수행할 수 없음
    * 트랩, 시스템 콜 등으로 커널 모드로 진입해 특권 명령을 수행
* `kernel mode`
    * 특권 명령을 수행할 수 있는 모드
    * 초기 부팅 시, 인터럽트 및 시스템 콜 처리 시 커널 모드
* 멀티 모드 : 명령어 권한을 여러 개의 단계로 나눔

### Timer
* 일정 시간이 지나면 CPU에 인터럽트 발생시키는 IO 장치
* 사용자 프로그램이 무한루프에 빠지는 등의 상황을 방지