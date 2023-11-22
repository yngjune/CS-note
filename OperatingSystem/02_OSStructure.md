# OS Introduction
## OS Services
* 운영체제는 여러 프로그램이 실행하기 위한 환경을 제공!

### for user
* 유저 인터페이스 (GUI/CLI)
* 프로그램 실행 및 프로세스 간 통신
* IO 동작 수행
* 파일 시스템 관리
* 시스템 에러 감지 및 해결

### for efficient system
* 자원 할당 (CPU 사이클, 메모리, 파일 공간 등)
* 로깅을 통한 성능, 자원 추적 관리
* 정보보호, 보안

<br/>

## CLI (Command Line Interfaces)
1. 커맨드 인터프리터가 명령어 구현을 포함
    * 명령어 수행 시 인터프리터 내부의 명령어 구현 위치로 점프해 수행
    * 명령어가 클수록 인터프리터의 크기가 커짐
2. 명령어를 시스템 프로그램으로 구현
    * 인터프리터는 명령어의 구현을 알지 못함. 단순히 위치를 찾아서 실행
    * 인터프리터 크기가 작고, 새 명령어 추가가 용이함
* 자체 프로그래밍 기능이 있어 CLI를 통한 반복 작업 수행 가능
* 쉘 스크립트 : 빈번하게 수행되는 작업을 파일로 저장해 프로그램처럼 실행

<br/>

## System Calls
* OS가 제공하는 서비스를 이용하기 위한 일종의 인터페이스
* 보통 C나 C++로 작성된 API 함수로 시스템 콜 수행
    * 윈도우API, POSIX API, Java API 등
    * API 함수 호출 시 시스템 콜 인터페이스가 가로채서 필요한 시스템 콜 수행
* 시스템 콜에 필요한 파라미터는 레지스터의 값을 사용하거나 크기가 클 경우 메모리 블럭을 할당

### 프로세스 관리 시스템 콜
* 프로세스를 생성, 적재, 실행, 종료
* 프로세스의 상태 정보를 획득
* 프로세스가 이벤트 발생을 대기

### 파일 관리 시스템 콜
* 파일을 생성, 삭제
* 파일을 열고 닫기
* 파일에 대한 읽고 쓰기
* 파일에 관한 정보 획득

### 장치 관리 시스템 콜
* OS에서는 IO 등 물리적인 장치와 파일 등 가상으 장치 모두 장치로 간주
* 같은 장치에 동시에 접근하려는 여러 프로세스가 존재할 수 있음
* 장치에 대한 접근 권한을 얻은 프로세스가 읽기, 쓰기 수행

### 정보 유지 시스템 콜
* 시스템의 정보 (가용한 메모리, 디스크 공간 등)
* 프로그램 디버깅을 위한 정보
    * 리눅스의 strace : 실행 중인 모든 시스템 콜을 출력함
* 프로그램의 실행 시간 정보
* 프로세스에 관한 정보 (PCB)

### 프로세스 간 통신 시스템 콜
1. message passing
    * 메일박스를 통해 프로세스 간 메시지 교환
    * 통신을 위해 상대 프로세스의 이름을 알아야 함
    * 통신 전 connection을 열어야 통신 가능
    * 충돌이 없고 구현이 쉬움. 다른 컴퓨터의 프로세스와 통신이 용이함
2. shared memory
    * 다른 프로세스가 소유한 메모리 공간에 접근, 공유해 정보를 교환
    * 동시에 같은 위치의 메모리에 접근하지 않도록 동기화 문제 해결해야 함
    * 스레드의 경우 기본적으로 일부 메모리를 공유함
    * 성능이 좋고 통신이 편리하지만 메모리 동기화 문제 해결이 필요

### 보안 시스템 콜
* 사용자의 자원에 대한 접근 권한을 관리하는 시스템 콜

<br/>

## Linker & Loader
1. 프로그램을 컴파일해 목적 파일로 만듬
2. 링커가 여러 목적 파일과 정적 라이브러리를 합쳐 실행 파일을 만듬
3. 로더가 실행 파일을 메모리로 가져옴
    * 이전 단계까지 목적 파일은 relocatable
    * 로더가 relocation 수행해 프로그램에 최종 주소를 할당
    * 최종 주소에 맞게 코드와 데이터를 수정
    * 동적 라이브러리 : 실행 시 필요할 때만 링크/로드. 여러 프로세스가 동적 라이브러리 공유하므로 메모리가 절약됨

<br/>

## Why App is OS-Specific?
* OS마다 지원하는 시스템 콜의 양식이 다름
* OS마다 헤더, 명령어, 변수 등의 이진 형식이 다름
* CPU마다 제공하는 instruction set이 다름
* 인터프리터 언어, VM, 포팅 등으로 크로스 플랫폼 구현 가능하지만 성능이 떨어짐

<br/>

## OS Structures
### Monolithic Structure
* 커널의 모든 기능이 단일 주소 공간으로 결합됨
* 구현, 확장이 어렵고 구조가 단순함
* 시스템 콜 인터페이스의 오버헤드가 거의 없고 성능이 좋음
* 성능 상의 이점으로 리눅스, 윈도우 등의 커널이 모놀리식 구조 사용함

### Layered Structure
* 시스템의 기능들을 개별적인 작은 요소로 분리함
* loosely coupled : 변경 사항이 일부 요소에만 영향을 주기 때문에 시스템 유지보수와 디버깅이 쉬움
* 다른 계층의 연산의 동작 방식을 몰라도 됨
* 계층 별 캡슐화를 통한 보안 향상
* 다만 기능별로 계층을 나누는 기준이 불명확하고 여러 계층을 거쳐야 하므로 성능이 저하됨

### Microkernel Structure
* 핵심 기능만을 커널로 구현하고, 나머지는 유저 레벨 프로그램으로 구현
* 프로세스 간 통신이 마이크로커널과 메시지를 교환하는 간접적 방식으로 이루어짐
* 커널 수정이 불필요하므로 OS 확장에 용이
* 프로세스 간 통신 시 메시지를 복사하고 프로세스를 전환하는 오버헤드가 커 성능 문제

### Module Structure
* 커널이 핵심 기능을 보유하고, 다른 서비스들은 모듈로 필요 시마다 동적 연결
* 계층 구조는 자신의 하위 계층만 호출할 수 있는 것과 달리 임의의 다른 모듈을 호출 가능
* 리눅스 LKM : 시스템 실행 중에 동적으로 디바이스 드라이버, 파일 시스템 지원
    * USB 꽂는 등 동작 수행 시 디바이스 드라이버 모듈이 커널에 동적으로 삽입됨
    * 모놀리식 구조의 성능을 유지하며 동시에 동적 모듈식 커널 활용