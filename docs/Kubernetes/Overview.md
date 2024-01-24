---
layout: default
title: k8s - Overview
parent: Kubernetes
tags:
- Kubernetes
---

# Kubernetes, Official Definition?
- 오픈소스 컨테이너 오케스트레이션 툴
- 구글에서 개발
- 온프렘, 클라우드 환경에서 컨테이너화 된 애플리케이션들 관리


### 오케스트레이션 툴의 기능
---
{: .note }
- 고가용성(High Avaliability)로 애플리케이션 서비스의 다운타임 최소화
- 빠르고 자동화된 확장(Scalability)
- 스냅샷을 통한 백업/복원(Disaster Recovery)


### Kubernetes 아키텍처
---
![](../../attachments/kubernetes/Pasted%20image%2020240124090226.png)
![](../../attachments/kubernetes/Pasted%20image%2020240124090444.png)
![](../../attachments/kubernetes/Pasted%20image%2020240124090852.png)

> Master Nodes(Control Plane):

가용성 확보를 위해 최소 2개 이상의 마스터 노드(Control Plane) 유지하는 것이 권장된다. Worker 노드에 비해 워크로드는 작은 편이지만 클러스터 액세스를 제공하는 유일한 엔트리 포인트이기 때문에 가용성 확보가 중요하다.

> Master Processes

| Process      | Feature          | 
|:-------------|:------------------|
| API 서버      | K8s에서 뭘 하든 이 API 서버와 통신해야 함(Client: UI, API, Kubectl)|
| Controller Manager(CM) |  컨트롤러는 클러스터의 현재 상태를 지속적으로 관찰하여, 의도한 상태에 가깝게 변경한다. | 
| Scheduler    | 어느 노드에 Pod를 배치할지 정의    | 
| etcd         | 스냅샷, Kubernetes의 모든 컴포넌트(예: Deployment, Service 등)에 대한 현재 상태 저장|

요청 포맷
- .json
- .yaml

> Worker Nodes

- 각 워커 노드에는 Kubernetes 프로세스인 kubelet이 인스톨 되어 있다.
- 각각의 다른 애플리케이션으로 구성된 컨테이너(Pod)가 각 노드에 배포된다.

### 컨트롤 플레인-노드 간 통신
---

> **노드에서 컨트롤 플레인으로의 통신**

쿠버네티스에는 "허브 앤 스포크(hub-and-spoke)" API 패턴을 가지고 있다. 노드(또는 노드에서 실행되는 파드들)의 모든 API 사용은 API 서버에서 종료된다. API 서버는 하나 이상의 클라이언트 인증 형식이 활성화된 보안 HTTPS 포트(일반적으로 443)에서 원격 연결을 수신하도록 구성된다. 노드는 유효한 클라이언트 자격 증명과 함께 API 서버에 안전하게 연결할 수 있도록 클러스터에 대한 공개 루트 인증서(root certificate)로 프로비전해야 한다. 클라이언트 인증서(client certificate) 형식으로 kubelet의 클라이언트 자격 증명을 사용하는 것은 좋은 방법이다. 

> **컨트롤 플레인에서 노드로의 통신** 

컨트롤 플레인(API 서버)에서 노드로는 두 가지 기본 통신 경로가 있다. 첫 번째는 API 서버에서 클러스터의 각 노드에서 실행되는 kubelet 프로세스이다. 두 번째는 API 서버의 프록시 기능을 통해 API 서버에서 모든 노드, 파드 또는 서비스에 이르는 것이다.

**1. API 서버에서 kubelet으로의 통신**: API 서버에서 kubelet으로의 연결은 다음의 용도로 사용된다.

    - 파드에 대한 로그를 가져온다.
    - 실행 중인 파드에 (보통의 경우 kubectl을 통해) 연결한다.
    - kubelet의 포트-포워딩 기능을 제공한다.

Note: 신뢰할 수 없는 네트워크 또는 공용 네트워크를 통한 연결을 피하기 위해 필요한 경우, API 서버와 kubelet 간 SSH 터널링을 사용해야 한다. SSH 터널링을 대체하는 Konnectivity를 사용할 수 있다.

**2. API 서버에서 노드, 파드 및 서비스로의 통신(프록시)**

API 서버에서 노드, 파드 또는 서비스로의 연결은 기본적으로 일반 HTTP 연결로 연결되므로 인증되거나 암호화되지 않는다. 이 연결에서 URL을 노드, 파드 또는 서비스 이름에 접두어 https: 을 붙여 보안 HTTPS 연결이 되도록 실행할 수 있지만, HTTPS 엔드포인트가 제공한 인증서의 유효성을 검증하지 않으며 클라이언트 자격 증명도 제공하지 않는다. 그래서 연결이 암호화되는 동안 그 어떤 무결성도 보장되지 않는다. 이러한 연결은 신뢰할 수 없는 네트워크 및/또는 공용 네트워크에서 실행하기에 현재는 안전하지 않다.


 




