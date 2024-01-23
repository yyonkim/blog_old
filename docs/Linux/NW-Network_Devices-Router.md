---
layout: default
title: NW-네트워크 장비(Network Devices)-라우터
parent: Linux
permalink: /docs/Linux/
---

### 라우터(Router)
---
> 라우터(Router)는 네트워크 내에서 통신을 원활하게 하는 것이 주된 목적인 네트워크 장치이다. 라우터의 각 인터페이스를 기준으로 네트워크 경계(network boundary)가 형성된다.

- 라우터는 OSI 7계층 모델의 Layer 3에서 작동하므로 Layer 3 헤더까지의 각 데이터 그램만 확인한다.
- Layer 3 헤더에는 End-To-End 전달을 가능하게 하는 소스/목적지 IP 주소가 포함되어 있다.
- 라우터는 통신 및 패킷 전송을 위해 라우팅 테이블(Routing Table)을 참조한다.

### 라우팅 테이블(Routing Table)
---
> 각각의 라우터 입장에서 라우팅 테이블은 존재하는 모든 네트워크에 대한 지도와 같다. 따라서, 수신한 패킷의 목적지를 라우팅 테이블에서 식별할 수 없다면 그 네트워크는 없는 곳으로 판단하여 패킷을 폐기한다(Discard). Layer 2의 스위치가 MAC Address Table을 참조한 뒤 목적지 주소를 식별할 수 없을 때 취하는 플러딩(Flood)과는 차이가 있는 부분이다.


### Address Resolution Protocol(ARP)
---
> Layer 2