---
layout: default
title: NW - Packet Traveling
parent: Linux
tags:
- linux
---

### Terms
---
- 호스트(Host): 인터넷 상에서의 end-device (랩탑, 모바일, 차량, 가전제품 등등). 일반적으로 네트워크 또는 인터넷을 통해 통신하는 2개의 호스트 사이의 관계를 Client와 Server라 명명한다.
- 클라이언트(Client): 정보/데이터/서비스를 호출하는 요청을 실행하는 측 entity
- 서버(Server): Client 측에서 요청한 정보/데이터/서비스를 보유한 측 entity
- 네트워크(Network): 2개 이상의 연결된 디바이스를 의미하며 유사한 목적을 보유하거나 가까운 지리적 위치에 있음

### 네트워크 인터페이스 카드(NIC)의 패킷이 어떻게 애플리케이션 계층으로 이동하며, 커널 API 및 데이터구조가 이를 용이하게 하는가?
> How a packet from network interface card flows back to the application layer and how some of the kernel APIs and data structures facilitate the flow?


### Wire 레벨에서 네트워크 패킷이란?
---
> **Headers**
![[Pasted image 20240123103640.png]]
- Ethernet Header
	- Mac address information
		- Source 
		- Destination
- IP Header
	- IP address information
		- Source 
		- Destination
- TCP Header
	- Port information
		- Source
		- Destination

> Actual Payload
- Data

### 패킷의 인입(Input)/포워딩(Forward)/출력(Output)/라우팅(Routing)
---
1. 인입(Input)
    1.1. 내 장비가 처리해야 하는 Local-In 목적의 패킷 (목적지 MAC,IP = 나의 MAC,IP)
    1.2. 다른 장비에게 넘겨주어야 하는 Forward 목적의 패킷
2. 출력(Output)
    2.1. 내 장비에서 발생하여 출력해야 하는 Local-Out 목적의 패킷
    2.2. 다른 장비에서 인입(Input) -> 포워드(Forward)를 거쳐 넘어온 패킷을 출력하는 Forward 목적의 패킷
3. 라우팅(Routing): 포워딩(Forward)을 결정짓는 정책
    3.1. Prerouting
    3.2. Postrouting

### Mapping Tables
> 라우팅 테이블을 제외하고는 패킷이 이동하는 동안 유동적으로 생성됨
- ARP Table: mapping of IP <-> MAC addresses
- MAC Address Table: mapping of Switchports <-> MAC addresses
- Routing Table: mapping of IP networks <-> Interfaces

### 네트워크 디바이스가 하는 일?
---
> 네트워크 디바이스는 physical 또는 virtual 디바이스(i.e. tun/tap software bridges) 일 수 있다

1. `MTU(Maximum Transmission Unit)`를 정의
    - MTU: 디바이스가 제어할 수 있는 최대 프레임 사이즈를 정의하는 값
    - ethtool 유틸리티로 변경 가능

2. 디바이스 세팅을 변경하는 데 사용할 수 있는 함수 포인터를 제공

3. CPU당 전송(Transmission) / 수신(Receive) 큐 (한 세트) 유지
    - 디바이스 초기화 시 tx, rx 큐 설정

4. DMA를 통해 네트워크 디바이스가 패킷을 넣을 때 사용할 메모리 영역을 설정
    - DMA(Direct Memory Access): CPU 도움 없이 주변장치들이 직접 메모리에 읽고 쓸 수 있는 방식
    - DMA에 의해 각 장치들은 PC 메인 버스(시스템 버스: CPU, 노스브리지 칩셋 간의 데이터 통로)를 통해 CPU 개입 없이 직접 데이터를 메인 메모리에 전달 가능


### MTU(Maximum Tranmission Unit)란?
---
- 데이터 링크에서 하나의 프레임 또는 패킷에 담아 운반 가능한 최대 크기를 의미하며, 헤더가 포함된 전체 사이즈

- **하나의 패킷으로 전송할 수 있는 최대 데이터 사이즈 MSS?**
	- MSS(Maximum Segment Size)로, TCP 상에서 전송할 수 있는 최대 데이터 크기를 의미하며, 헤더를 제외한 전체 사이즈이다. 설정된 MTU 값에 의해 결정된다
	- MTU 설정값 - IP Header size - TCP Header Size = MSS(Maximum Segment Size)
	- 예: Ethernet) 1500 - 20 - 20 = 1460 (Bytes)

- **한 번에 전송할 수 있는 최대 MTU 값(Bytes)은?**
	- 매체에 따라 달라질 수 있으며 아래와 같은 기본값 및 권장값이 있음
		- Ethernet 환경 기본값: 1500 (Official Max MTU= 65535)
		- FDDI: 4000
		- X.25: 576
		- Giga Bit MTU: 9000 
- **Linux 시스템에서 MTU값 확인?**
	- ifconfig


### OSI 7계층에서의 MTU
---
- 데이터링크 계층에서는 자신이 속한 네트워크에 대한 MTU 값을 알아야 하며
- 그 상위 프로토콜에 해당 값을 알려줘야 함 


### SK_Buff란?
---
> Socket Buffer Descriptors, 패킷에 대한 메타 데이터를 정의하는 네트워크 패킷 제어에 핵심적인 리눅스 커널의 데이터 구조(자료형)

- 패킷이 L2, L3, L4를 거쳐 애플리케이션 레이어로 이동하는 동안 포인터들은 sk_buff 내에서 조정된다


### 네트워크 패킷의 이동 흐름
---
1. 패킷이 네트워크 인터페이스에 도착
2. 패킷은 DMA에 의해 커널 메모리에 복사됨
3. 패킷 처리를 위한 코드 실행을 위해 인터럽트 발생
4. 리눅스는 2개 부분으로 인터럽트 처리를 제공
    a. Bottom Half: Softirqs, Tasklets와 같이 여러가지 방식으로 구현 가능
    b. Top Half: 더 많은 인터럽트 처리 및 Bottom Half 스케쥴링을 위해 빨리 비워짐
5. 네트워크 디바이스가 초가화되는 동안 tx, rx function을 통해 softirq 핸들러가 등록됨
    - open_softirq(NET_TX_SOFTIRQ, net_tx_action);
    - open_softirq(NET_RX_SOFTIRQ, net_rx_action);
6. net_rx_action은 igb_clean_rx_queue를 호출
    - 버퍼로부터 패킷을 가져오고 skb_buf 구조를 생성
7. 패킷 이동을 위해 netif_receive_skb 함수 호출
8. ip primitives 제어를 위한 ip-receive 함수 호출:
    - NF 훅을 통한 IP tables
    - 패킷이 local 전달인지 포워드인지 라우팅 판단
9. 패킷을 소켓 버퍼에서 sk_buff가 대기하는 TPC 레이어로 전송하기 위해 tcp_receive 함수 호출: 
10. 애플리케이션 레이어에서는 파일 디스크립터를 통해 소켓을 읽고, 소켓 read buffer에 있던 패킷들은 애플리케이션 레이어로 복사됨


### 네트워크 패킷의 이동 경로 최적화
1. Receive 측 스케일링(Scaling)
    - NIC가 다중 tx/rx 큐를 지원하는 경우 CPU당 tx/rx 쌍을 보유할 수 있음
    - 이를 통해 CPU 코어 별로 패킷 병렬 처리 흐름을 만들 수 있음

2. Receive 측 스티어링(Steering)
    - NIC가 다중 tx/rx 큐를 지원하지 않을 경우 단일 큐로 패킷을 받은 뒤 소프트웨어 적으로 여러 개의 큐로 분산할 수 있음

3. GRO(Generic Receive Offloading)
    - 여러 개의 비슷한 작은 패킷들을 하나의 패킷으로 합칠수 있음(Combine) -> 오버헤드 감소

References: 
https://skstp35.tistory.com/284
http://ktword.co.kr/test/view/view.php?nav=2&opt=&m_temp1=638&id=484
https://medium.com/@jain.sm/network-packet-flow-in-linux-part-1-14edb8bb4a81
https://www.minzkn.com/moniwiki/wiki.php/skbuff
https://www.youtube.com/watch?v=rYodcvhh7b8
https://www.practicalnetworking.net/series/packet-traveling/packet-traveling/