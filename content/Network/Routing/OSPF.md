
#2026-01-26

# OSPF 개요

- Link State 라우팅 프로토콜
- RFC 2328에 규정된 버전 2와 RFC 5340에 규정된 버전 3가 있음. 버전 3는 IPv6를 지원함.
- IP 패킷에서 프로토콜 번호 89번 사용
- 다른 Distance Vector 프로토콜의 단점을 개선

**장점**
- 대규모 네트워크를 안정적으로 운영 가능
- 표준 라우팅 프로토콜

**단점**
- 라우팅 계산 및 유지를 위해 자원을 많이 소모

## OSPF 동작 과정 요약

1. OSPF가 설정된 라우터 간에 Hello 패킷을 주고받아 Neighbor 및 Adjacent Neighbor를 구성. 다른 라우팅 프로토콜과 달리 모든 네이버 간에 라우팅 정보를 주고받지 않음. 라우팅 정보를 주고받는 네이버를 Ad neighbor라고 함.
2. Ad neighbor끼리 라우팅 정보 교환. 라우팅 정보를 LSA(Link State Advertisement)라고 함. LSA를 LSDB에 저장.
3. LSA 교환 후 SPF(Shortest Path First) = 다익스트라 알고리즘을 이용해 loop-free한 목적지별 최적 경로 계산, 라우팅 테이블에 저장. 
4. 주기적으로 Hello 패킷을 전송해 라우터의 정상 동작을 인접 라우터에게 고지
5. 네트워크 상태 변화 시 위 과정을 반복해 다시 라우팅 테이블 생성

## 특징

- 같은 area 내의 모든 ospf 라우터는 같은 LSDB를 가짐. 
 - 각 라우터는 자기 자신을 SPF 트리의 root 라고 여김. 
 - SPT는 OSPF 도메인 내의 모든 목적지를 가지고 있음.
 - SPT는 각 OSPF 라우터별로 다르지만, SPT를 계산하기 위한 LSDB는 모든 라우터가 똑같이 가짐. 

아래 그림은 같은 토폴로지임에도 어떤 라우터가 기준이냐에 따라 10.3.3.0/24에 대한 cost가 다른 것을 보여줌. R1 입장에서는 R4-R3 사이 연결이 없고, R4 입장에서는 R1-R3 연결이 엇ㅂ음. 
![[Pasted image 20260126191007.png]]


- OSPF는 여러 Area로 나뉠 수 있는데, 그 중 Area 0은 backbone area로 불리며, 모든 area는 이 area 0에 연결되어 있어야 한다. 
- 만약 여러 개로 area를 나눈다면, 각 area 별로 LSDB가 달라진다. 하지만 같은 area 내에서는 모두 같은 LSDB를 갖는 것은 변함 없다.


![[Pasted image 20260126191446.png]]


- 한 라우터 내에서 여러 개의 OSPF 프로세스 운영 가능
- 프로세스가 다르면 재분배를 통해서만 루트 광고 가능
- 프로세스 번호는 라우터 내부적으로만 의미 있으며 다른 라우터와 같은 필요 없음


## Inter-Router Communication

- ALLSPFRouters: ==224.0.0.5== or 01:00:5E:00:00:05 -> 모든 OSPF 라우터는 이 패킷을 받을 수 있어야 함.
- ALLDRouters: ==224.0.0.6== or 01:00:5E:00:00:06 -> DR 라우터 간의 정보 교환은 이 주소를 사용한다.


# OSPF 패킷 타입

**Type 1: Hello Packet**
- 이웃을 찾고 유지하기 위한 패킷
- 모든 OSPF 인터페이스에서 주기적으로 송신
- ==AllSPFRouter(224.0.0.5)== 주소로 보냄

**Type 2: DBD(Database description) or DDP**
- LSDB의 내용을 요약한 패킷
- adjacency가 형성되면 라우터끼리 교환

**Type 3: LSR(Link-state request)**
- 이웃의 DB를 요청하는 패킷
- 라우터가 자신의 LSDB가 최신이 아니라 판단하면 보냄

**Type 4: LSU(Link-stae update)**
- DB 업데이트를 위한 패킷
- 명시적인 LSA로, LSR의 응답으로 보내짐

**Type 5: Link-state ack**
- ack를 플러딩하기 위함
- LSA 플러딩에 대한 응답으로 보내짐




## OSPF Hello Packet Field

**Router ID(RID)**
- 라우터를 식별하는 고유의 32비트 숫자
- 각 OSPF 프로세스 내에서 중복되면 안됨

**Authentication options**
- none, clear text, MD-5 등 전송 시 암호화 설정
**Area ID**
- 해당 OSPF 인터페이스가 속하는 Area 명시
- 32비트 숫자 혹은 이진수 숫자

**Interface address mask**
- 인터페이스 네트워크 마스크

**Interface priority**
- DR 선택을 위한 라우터 인터페이스 우선순위

**Hello interval** 
- Hello 패킷을 보내는 간격

**Dead interval**
- Hello 패킷을 기다리는 간격
- 해당 시간만큼 헬로 패킷이 오지 않으면 상대 라우터가 다운되었다고 판단

**Designated router and backup designated router**
- DR과 BDR의 IP 주소

**Active neighbor**
- 해당 네트워크 세그먼트의 OSPF 이웃 리스트



## Neighbors

- adjacency neighbor: 동기화된 DB를 가지는 라우터
- 각 프로세스는 Ad neighbor 목록과 상태를 기록한 테이블을 관리함

<네이버 상태>

- **Down**
	- 라우터가 아무런 hello 패킷을 받지 않은 초기의 상태
- **Attempt**
	- 아무 정보를 받지 못했지만 통신 시도하는 단계
	- NBMA 네트워크와 관련
- **Init**
	- hello 패킷을 받았지만 양방향 통신이 설립되지 않음
- **2-Way**
	- 양방향 통신 수립됨
	- 만약 DR이나 BDR이 필요하면, 이 단계에서 선출됨
- **ExStart**
	- adjacency 형성을 위한 첫 단계. 
	- 어떤 라우터가 primary인지 secondary인지 정함 
- **Exchange**
	- DBD 패킷(요약된 LSA)을 이용해 link state 교환
	- 서로의 LSA 요약을 비교하여 없는 정보, 최신 여부 등 확인
- **Loading**
	- Exchange 단계에서 발견한 최신 LSA 요청하는 LSR 패킷 보냄 
- **Full**
	- 이웃 라우터끼리 adjacent 상태가 됨




# DR과 BDR

- Multi-access Network나 Frame Relay 같은 경우 여러 개의 라우터가 한 세그먼트에 존재할 수 있음
- 이런 구조의 경우 라우터가 하나 늘어날 때마다 증가하는 이웃 세션으로 인해 라우터 사이에 주고받는 LSA 양이 늘어나고, CPU 성능 저하, 메모리 사용, 대역폭 사용량 증가를 일으킴

- 이를 해결하기 위한 것이 ==DR(Designated Router)==로, 모든 라우터는 DR과만 full ad 관계를 맺음.
- DR은 모든 OSPF 라우터에게 업데이트를 보냄

- DR이 다운될 경우 일시적으로 경로를 잃어버린 LSA를 주고받을 수 있으므로, BDR(Backup designated router)가 새로운 DR이 됨
- 이 경우 BDR를 새로 선출하게 됨
- 전환 시간을 최소화하기 위해 모든 라우터들은 ==BDR과도 full ad 관계를 맺음==

![[Pasted image 20260208172158.png]]


<DR이 새로운 LSA를 업데이트하는 과정>

1. DROTHER 라우터가 새로운 경로를 찾으면 ==AllDRRouters(224.0.0.6)== 주소로 LSA 패킷을 보냄. 이것은 DR과 BDR만 수신할 수 있음.
2. DR이 해당 LSA 패킷을 보낸 라우터에게 unicast ack를 보냄. 
3. DR이 해당 LSA를 224.0.0.5 주소를 사용해 모든 OSPF 라우터에게 플러딩


![[Pasted image 20260208172909.png]]




# OSPF Configuration



















# 참고자료
