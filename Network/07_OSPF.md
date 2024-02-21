# Open Shortest Path First (OSPF)
* AS(망) 내부에서 라우터 단위로 길을 계산하는 IGP 프로토콜의 일종
* 비슷한 IGP 프로토콜인 IS-IS는 보통 ISP가 많이 사용. OSPF는 학교 등 기관에서 많이 사용.

<br/>
<br/>

## IGP의 분류
1. distance vector (거리 벡터) 방식
    * 라우팅 경로 수렴 속도 등 문제가 있어 deprecated
    * 링크가 생기거나 없어지는 등 소식을 통해 라우팅 정보를 업데이트하고 전파
2. link state (링크 상태) 방식
    * 링크 상태 : 자신과 연결된 링크를 건널 때 치러야 하는 값
    * 자신의 인접 라우터에만 알리는 거리 벡터 방식과 달리, 링크 상태를 모든 라우터에 알림
    * 모든 라우터가 네트워크 전체 토폴로지와 링크 정보를 알게 됨
    * 라우팅 경로 계산 속도가 빠르고 수렴이 보장됨


<br/>
<br/>

## 다익스트라 알고리즘
* 링크 상태 전파를 통해 망의 정보를 얻은 후 라우팅 경로 계산을 위해 다익스트라 알고리즘 사용
* $O(E + VlogV)$의 시간 복잡도로 빠르게 계산 가능
* [다익스트라 알고리즘](../Algorithm/Graph/ShortestPath.md)
* 각 노드까지의 거리를 계산하는 원 다익스트라 알고리즘과 달리, 다음 홉을 계산

<br/>
<br/>

## OSPF의 라우팅 지원

### 계층적 네트워크 구조 지원
* 네트워크 내의 라우터 수가 너무 많아지면 $O(E + VlogV)$의 시간 복잡도도 부담이 커짐
* 대신 AS를 여러 개의 area로 나누어 각 area별로 다익스트라 알고리즘을 통해 경로 계산
* 여러 area를 연결해 주는 백본 area (혹은 area 0)
    * 다른 area들이 백본 area에 연결된 2계층 구조로 OSPF 경로 계산
    * 연속된 서브넷을 하나의 area로 묶으면 집속을 통해 분기되는 지점의 라우팅 테이블 엔트리 개수를 줄임

<br/>

### Load Balancing
* OSPF의 링크 상태 값은 0~65535의 단위 없는 값
    1. 네트워크 관리자의 판단 하에 링크에 값을 할당
        * 특정 링크의 값을 임의로 높게 설정해 트래픽을 덜 가게 하는 등 로드 밸런싱 가능
    2. 100Mbps 기준으로 링크의 속도가 얼마인지 표현하는 값
        * 빠른 링크일수록 값이 작음. 최단 경로 트리에 포함될 가능성이 커짐
* 다익스트라 알고리즘의 원리를 활용 - 링크의 값을 조정해 로드 밸런싱 수행함

<br/>

### OSPF를 위한 IP 멀티캐스트
* OSPF 멀티캐스트 주소인 224.0.0.5를 이용해 OSPF 패킷을 멀티캐스트
* 224로 시작하는 주소이므로, 1홉까지만 멀티캐스트됨
* 브로드캐스트가 아니므로 OSPF 처리할 필요 없는 라우터에 부하를 주지 않음

<br/>
<br/>

## OSPF 프로토콜
### 패킷 형식
* IP 위에서 돌아감. IP Protocol 번호는 89

<br/>

### OSPF 라우터의 대표 IP 주소
* 보통 라우터는 여러 개의 인터페이스를 통해 여러 링크로 연결되므로 여러 개의 IP주소를 가짐
* 다른 링크로 인접 라우터로 소통할 때마다 다른 주소를 사용하면 토폴로지 구성이 어려움
* 대신 라우터마다 대표 IP 주소를 설정해 Source OSPF Router 필드에 기입
* 대표 IP 주소 설정 우선순위
    1. 수동으로 설정한 router ID
    2. loopback 인터페이스 중 가장 큰 IP 주소
    3. loopback이 아닌 인터페이스 중 가장 큰 IP 주소
    * loopback 인터페이스는 물리 NIC가 없어 다운될 일이 없으므로 우선시함

<br/>

### OSPF Hello 메시지
* 인접한 OSPF 라우터와 링크를 확인하기 위해 사용
* AreaID 필드에 라우터가 속한 area 정보를 포함
* NeighborID 필드를 통해 라우터가 인접 관계를 맺고 있는 OSPF 라우터들의 IP주소 포함
* 인접 OSPF 라우터는 받은 Hello 메시지의 NeighborID에 자신의 IP주소가 있으면 상호 연결 가능함을 확인

<br/>

### Link State Database (LSDB)
* 다익스트라 알고리즘에서 설명한 전체 네트워크 토폴로지에 상응함
* 링크 정보 (LSA)들을 모아 LSDB를 구성. 네트워크(area) 내부의 모든 라우터의 LSDB는 일치해야 함

<br/>

### OSPF DBD (DB Description) 메시지
* LSDB에 대한 메타데이터 (LSA의 헤더들)을 인접 라우터 간 교환하는 메시지
* DBD를 받고 업데이터해야 하는 부분이 있다면 LSR 메시지를 통해 필요한 LSA를 전달함
* RIP 프로토콜과는 달리 변화가 생긴 link state만을 교환해 네트워크 부하가 적음
* 새 링크가 연결되었을때도 DBD를 전송해 모든 라우터의 LSDB가 일치하도록 함

<br/>

### OSPF LSR (Link State Request) 메시지
* OSPF LSR 메시지를 통해 LSDB 업데이트에 필요한 LSA를 인접 라우터에 요청
* LSA는 <LSA타입, Link State ID, advertising router>를 통해 식별함


<br/>

### OSPF LSU (Link State Update) 메시지
* LSR에 대한 응답으로 LSDB 중 업데이트된 부분의 정보를 실어 보냄
* LSU를 받은 라우터는 LSDB 업데이트 후  받은 인터페이스를 제외한 다른 모든 인터페이스로 전파

<br/>

### OSPF LSAck (Link State Acknowledgement) 메시지
* 받은 LSU 메시지에 대한 확인 메시지. 한 번에 여러 LSA들에 대해 ack.
* `reliable flooding` : ack를 받지 못하면 LSU를 재전송함

<br/>

### 네트워크 토폴로지를 계산하는 경우
1. 링크 값이 변경되거나, 새로 만들어지거나, 없어지는 등 토폴로지에 변동
2. 제대로 모니터링 되지 않은 경우를 대비해 주기적으로 수행

<br/>

### LSDB 동기화 로직
1. 받은 LSA가 LSDB에 없는 경우
    1. LSDB에 LSA를 추가
    2. LSAck를 전송
    3. LSA를 다른 인터페이스로 전파
    4. 다익스트라 알고리즘을 통해 라우팅 테이블 재계산
2. 받은 LSA가 LSDB에 있는 경우
    1. LSA의 seqnum이 같다면 무시
    2. LSA의 seqnum이 나보다 작다면, LSA 보낸 라우터에게 LSU를 보냄
    3. LSA의 seqnum이 나보다 크다면, 1을 수행

<br/>

### Link State Advertisements (LSAs)
* LSDB 업데이트 위해 LSU에 실려 이동하는 링크에 대한 정보
* LSA는 <LSA타입, Link State ID, advertising router>를 통해 식별함
* LS age
    * 0에서 3600초 (1시간) 사이의 값으로 LSA의 수명을 나타냄
    * 무효화를 막기 위해 LS age를 0으로 설정한 LSA를 주기적으로 flooding
    * LSA wraparound를 위해 사용
        * seqnum이 최댓값까지 오른 이후 최신 LSA의 seqnum은 다시 최솟값을 가짐
        * seqnum이 작기 때문에 최신 버전임을 인지하지 못함
        * 이전 버전의 LSA의 age를 3600으로 flooding하면 전파된 후 age가 만료되어 모든 라우터에서 삭제됨
        * seqnum이 작아진 최신 LSA를 다시 전파해 모든 라우터의 LSDB에 반영
* LS Type : 11가지 타입이 존재
    * Router LSA
        * link state ID는 LSA를 만든 라우터의 ID임
    * Network LSA
        * DR이라는 라우터만 생성
        * 공유형 링크에 여러 개의 라우터가 연결되면 원래는 $O(N^2)$의 메시지 교환이 필요
        * 대신 DR(Designated Router)를 선정해 다른 라우터는 DR과만 이웃 관계 맺어 $O(N)$ 메시지 교환
* area 내부의 LSDB에는 Router LSA와 Network LSA만 저장하고, 다른 are로 전파하지 않음

<br/>
<br/>