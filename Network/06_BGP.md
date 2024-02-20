# Border Gateway Protocol (BGP)
* IP 데이터그램의 배달을 위해 경로를 계산하는 라우팅 프로토콜의 일종
* 망 단위의 네트워크 라우팅 경로를 계산하기 위해 BGP를 사용
* 망 내부에서 이동하는 인터페이스 경로를 계산하는 것은 IS-IS(주로 ISP)와 OSPF(주로 학교 등 말단net)

<br/>
<br/>


## 라우팅의 이층 구조
* IP 주소의 구조가 네트워크ID와 호스트ID로 이루어진 것처럼, 라우팅을 2개의 계층으로 수행함
    * `Autonomous System(AS)` : 기관이 소유하고 관리하는 네트워크
    * 네트워크ID를 통해 기관의 네트워크(AS)를 찾아가는 라우팅 - BGP
    * AS 내부에서 목적지를 찾아가는 라우팅이 따로 존재 - IS-IS, OSPF
* 이층 구조 덕에 수많은 라우터가 존재함에도 라우팅 계산이 가능하게 됨

<br/>
<br/>

## 망 수준의 인터넷 연결 구조
* AS는 기관이 할당받은 IP 주소 블럭 (프리픽스)를 가짐
* 인터넷의 코어 라우터들에 기관의 네트워크 프리픽스를 엔트리로 만들어 주는 것이 BGP의 역할

<br/>

### Autonomous System (AS)
* 각 AS는 AS number (ASN) 을 부여받음
* `transit AS` : ISP 등 자신의 망을 통해 AS 간 패킷을 전달해 주는 중간 AS
* `stub AS` : 학교, 회사 등 다른 AS의 패킷을 중간에서 전달하지 않는 AS
    * 주로 stub AS는 두 개 이상의 ISP(transit AS)와 연결해 인터넷 연결 신뢰성을 보장
* stub AS는 인터넷 연결 위해 ISP 경유해야 하므로 굳이 BGP를 이용해 경로를 계산하지 않아도 상관 없음
    * 기본 라우팅 테이블 엔트리를 ISP 망(transit AS)으로 향하게 설정
    * 대신 stub AS의 네트워크 프리픽스를 ISP가 BGP를 통해 대신 공표
    
<br/>

### AS 간 관계
* 계층적인 관계
    1. stub AS가 인터넷 연결을 위해 ISP(transit AS)에게 돈을 주고 연결
    2. transit AS 간 계층적 관계
        * 작은 규모의 AS만으로는 전 세계에 패킷을 전달할 수 없음
        * 더 규모가 큰 상위 AS와 계층적인 관계를 맺어 패킷을 전달
* 피어링
    * AS 간 연결을 통해 망 연결성이 보완되는 등 이익이 되는 관계
    * 사적 피어링 : 두 AS가 계약을 맺고 직접 연결
    * 공용 피어링 : 피어링을 위한 공용 LAN을 두고 여러 AS가 연결

<br/>

### BGP 피어링
* eBGP (외부) 피어링
    * 서로 다른 AS에 있으면서 직접 연결되어 BGP로 통신하는 라우터 간 관계
* iBGP (내부) 피어링
    * ISP의 경우 보통 다른 AS와 맞닿은 경계 라우터가 여러 개 존재함
    * 같은 AS 내부에서 알아낸 경로를 공유하는 관계
    * iBGP peer들끼리는 완전 연결(full-mesh) 되어야 함
    * 피어로부터 얻은 정보를 다른 피어에게 전파하지 않음 (중복 갱신이나 핑퐁 방지)

<br/>
<br/>

## BGP 특성
* 정책 기반 프로토콜
    * 무조건 최단 경로가 아닌 라우팅 정책을 통해 BGP 공고를 전달할지 결정함
* 경로 벡터 프로토콜
    * AS path 애트리뷰트를 통해 BGP 패킷이 전파되며 거친 AS를 기록함
    * 경로에 루프가 형성되었는지 검사하거나, path 길이가 짧은 경로를 선택

<br/>
<br/>

## BGP 메시지 형식
|필드|길이(bit)|상세|
|---|:---:|---|
|Marker|128||
|Length|16|헤더+바디의 총 길이|
|Type|8||
|Msg body|||

### Marker
* 동기화와 인증을 목적으로 사용되는 필드. 동기화는 deprecated
* 인정 전에는 인증 비트를 모두 1로 채우고, peer 사이의 MD5 인증 방법 등 합의 후 패턴

<br/>

### Type
* 1 연결(Open), 2 갱신(Update), 3 통고(Notification), 4 킵얼라이브(Keepalive)

<br/>

### BGP Open 메시지
1. 자신의 소속 AS를 밝히고 정보 교환을 원함을 알리기 위해
2. 인증 방법 등 세션에 쓰일 파라미터 값 협상
* BGP 세션 생성 과정
    1. BGP peer 간 TCP 연결을 만듬
    2. BGP OPEN 메시지를 전송하고 조건에 문제가 없다면 BGP KEEPALIVE로 회신
    3. 2번 과정을 양쪽 방향으로 수행
* 조건에 동의가 되지 않는다면 BGP Notification 메시지를 전송

<br/>

### BGP Update 메시지
* 주어진 정책으로 경로 선택 후, 선택한 경로와 애트리뷰트를 BGP peer 라우터에게 알림
* 두 종류의 정보를 포함 가능
    1. `announcement` : 한 경로에 대한 애트리뷰트
    2. `withdrawal` : 더 이상 도달이 불가능한 프리픽스

<br/>

### BGP Notification 메시지
* BGP 에러 메시지.

<br/>

### BGP Keepalive 메시지
1. BGP Open 메시지에 대한 수락의 의미로 전송
2. 주기적으로 세션이 살아있음을 peer에게 알리기 위해 전송
* 헤더로만 이루어짐

<br/>
<br/>

## BGP 경로 애트리뷰트

### AS-PATH
* BGP announcement가 지금까지 오는 과정에서 거친 AS들의 시퀀스
* 시퀀스의 가장 오른쪽 값이 프리픽스를 처음 내보낸 AS
* AS 단위의 라우팅 루프를 감지하는 데 사용
* path의 길이가 짧은 것을 선호하는 path vector 라우팅

<br/>

### NEXT-HOP
* 프리픽스에 도달하기 위해 거쳐야 하는 다음 홉의 IP 주소
* eBGP에서는 통상 eBGP peer의 IP 주소가 next-hop

<br/>

### LOCAL-PREF
* 경로 선택 시 (현재 AS에서 밖으로 나가는 방향) 고려하는 선호도 값
* 특정 목적지 프리픽스에 도달할 때 여러 AS를 거칠 수 있다면, 선호도 값이 높은 경로를 선택

<br/>

### MULTI-EXIT-DESC
* LOCAL-PREF와 반대로, 들어오는 트래픽에 대한 선호도 값
* 다른 AS가 현재 AS에 트래픽을 보낼 때 입구가 여러 개인 경우 MED가 낮은 경로를 선택

<br/>

### ORIGIN
* 프리픽스 정보를 어디서 얻었는지 표시
* OSPF 등 IGP / EGP (deprecated) / incomplete (나머지 경우 혹은 알수 없음)

<br/>
<br/>

## BGP 경로 선택
* 먼저 고객-ISP, 혹은 ISP 간 계약 등 정책 결정 이후 BGP Open해 세션 생성
* BGP 라우터가 peer로부터 announce 받았을 때 처리 단계
    1. 내부 라우팅 정보를 갱신
    2. 다른 BGP peer에게 전파
* 경로 애트리뷰트를 이용한 최적 경로 선택 알고리즘
    1. 같은 AS의 peer로부터 경로를 배웠다면, LOCAL-PREF 가장 높은 경로 선택
    2. 다른 AS에서 온 정보라면, AS-PATH가 가장 짧은 경로 선택
    3. ORIGIN 값이 가장 작은 경로, 즉 BGP에서 만든 경로
    4. MED 값이 가장 작은 경로
    5. iBGP보다 eBGP로부터 배운 경로
    6. NEXT-HOP까지의 거리(IGP로 계산)가 가장 짧은 경로
    7. BGP 라우터의 라우터 ID가 가장 작은 경로

<br/>
<br/>