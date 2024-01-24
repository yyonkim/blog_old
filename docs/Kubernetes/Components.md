---
layout: default
title: k8s - Components
parent: Kubernetes
tags:
- Kubernetes
---

### Kubernetes Components
---

#### Node
> 컨테이너를 보유하고 있는 서버

#### Pod
> 각 컨테이너들을 추상화하는 Kubernetes의 최소 단위

- 일반적으로 Pod 당 하나의 애플리케이션이 있다.
- 각 Pod는 고유 IP(Internal)을 부여 받으며, 서로 통신할 수 있다.
- 각 Pod는 비영구적 리소스(ephemeral)라 언제든 죽을 수 있다. Pod가 죽으면 사전에 정의한 Deployment(blueprint) 컨트롤러가 다시 생성하거나 복제하는데, 이렇게 Pod가 죽을 때 IP 주소를 새로 부여받게 된다. 이렇게 되면 Pod가 죽을 때 마다 연결된 엔드포인트 IP를 지속적으로 업데이트 해줘야 하는 문제가 생기기 때문에, Pod의 LifeCycle과 독립적으로 유지되는 네트워크 연결성이 필요하다. 이를 위해 Static IP를 부여하는 "Service" 컴포넌트를 사용하게 된다.

#### Service
> Communication 수단

**Features**
- Stable Network Endpoint(Static IP) 제공: 
    - 각 Pod의 IP주소나 포트가 변경되더라도 고정된 가상 IP 주소와 포트를 제공한다. 
    - 클라이언트는 서비스의 가상 IP주소와 포트에 접근하여 백엔드 Pod로 연결할 수 있다.

- Load Balancing
    - 여러 개의 Pod 인스턴스가 있는 경우 서비스는 이들 사이에서 트래픽을 분산하여 로드 밸런싱을 수행한다.
    - 이로써 서비스에 접근하는 클라이언트는 백엔드 Pod 집합 중 하나에 연결된다

- 서비스 디스커버리
    - 다른 Pod에서 서비스에 접근할 때 DNS 이름을 통해 서비스를 찾아서 사용할 수 있다.
    - 이를 통해 Pod 간 통신을 쉽게 구현할 수 있다

- NodePort, ClusterIP, LB 등의 유형
    - 쿠버네티스에서는 서비스 유형을 지정하여 다양한 네트워킹 모델을 사용할 수 있다.
    - NodePort에서는 각 노드의 특정 포트에 서비스를 노출 시킨다.
    - ClusterIP는 클러스터 내부에서만 사용되는 서비스를 정의한다.
    - LB는 외부 로드 밸런서를 이용하여 외부에서 서비스에 접근할 수 있게 한다.

- Session Affinity
    - 서비스에서는 세션 어피니티(Affinity)를 설정하여 특정 클라이언트의 요청이 항상 특정 Pod에 연결되도록 할 수 있다.


#### Ingress
---
> Routes traffic into cluster

대부분의 일반적인 쿠버네티스 배포에서 클러스터에 속한 노드는 퍼블릭 인터넷의 일부가 아니다. 인그레스는 클러스터 내부에 위치하며, 외부에서 서비스로 접속이 가능한 URL, 로드 밸런스 트래픽, SSL / TLS 종료 그리고 이름-기반의 가상 호스팅을 제공하도록 구성할 수 있다.

트래픽 라우팅은 인그레스 리소스에 정의된 규칙에 의해 컨트롤된다. 인그레스 컨트롤러가 있어야 인그레스를 충족할 수 있으며, 인그레스 리소스만 생성한다면 효과가 없다. 다음은 인그레스가 모든 트래픽을 하나의 서비스로 보내는 간단한 예시이다.

(클러스터 외부) 클라이언트 -> Ingress-Managed LB -> (클러스터 내부) Ingress -> 라우팅 규칙 -> 서비스 -> 각 파드로 라우팅

- 즉, 클라이언트 요청을 서비스 포트로 바로 전달하지 않고 Ingress를 통해 서비스로 포워딩하는 방식이다.


#### ConfigMap
---
> 노드 위의 Application Pod에서 DB 서비스가 실행되고 있고, 이 DB의 URL을 변경해야 되는 작업이 있다고 가정한다.(예: mongo-db-service -> mongo-db) DB URL과 같이 일반적으로 애플리케이션 내에서 변경해야 하는 대상들은 변경 사항을 적용한 Application 이미지를 새로 빌드한 뒤 -> Repository에 Push 하고 > Pod에 새 이미지를 다시 Pull 하는 불편한 사이클이 수반된다. Kubernetes는 해당 Pod에 대한 ConfigMap 수정을 통해 애플리케이션 리빌드 과정 없이 간단하게 변경사항을 적용할 수 있다. 민감한 데이터나 정보는 저장하면 안된다.



#### Secret
---
> Credential Store

User Name, Password와 같이 민감한 Credential 정보를 보관할 수 있다. 단, Kubernetes Secret은 컨트롤 플레인-API 서버의 기반 데이터 저장소(etcd)에 암호화 되지 않은 상태로 저장되어 있으므로 API 액세스 권한/ etcd 액세스 권한이 있다면 누구나 Secret을 조회하거나 수정할 수 있는 문제가 생긴다:[1] Kubernetes의 빌트인 보안 매커니즘은 기본적으로 비활성화이기 때문에 아래와 같은 추가 설정이 필요하다.

- Encryptions at Rest 활성화
- Secret 액세스에 대해 RBAC 활성화 및 Rule 적용
- 특정 컨테이너로 액세스 제한
- Secret 암호화를 위한 별도의 Third party 서비스 사용


#### Volumes
---
> Data Persistence

Pod에 저장중인 데이터를 영구 보관하기 위해 물리적인 저장소(Physical Volumes)를 연결할 수 있다. 

쿠버네티스는 다양한 유형의 볼륨을 지원한다. 파드는 여러 볼륨 유형을 동시에 사용할 수 있다. 임시 볼륨 유형은 파드의 수명을 갖지만, 퍼시스턴트 볼륨은 파드의 수명을 넘어 존재한다. 파드가 더 이상 존재하지 않으면, 쿠버네티스는 임시(ephemeral) 볼륨을 삭제하지만, 퍼시스턴트(persistent) 볼륨은 삭제하지 않는다. 볼륨의 종류와 상관없이, 파드 내의 컨테이너가 재시작되거나 제거되어도 마운트만 해제되고 볼륨 내 데이터는 보존된다.

기본적으로 볼륨은 디렉터리이며, 일부 데이터가 있을 수 있으며, 파드 내 컨테이너에서 접근할 수 있다. 디렉터리의 생성 방식, 이를 지원하는 매체와 내용은 사용된 특정 볼륨의 유형에 따라 결정된다.

볼륨을 사용하려면, .spec.volumes 에서 파드에 제공할 볼륨을 지정하고 .spec.containers[*].volumeMounts 의 컨테이너에 해당 볼륨을 마운트할 위치를 선언한다.

AWS EBS(CSI), Azure Disk(CSI), Cephfs 등등 다양한 볼륨 유형을 지원한다.


#### Deployment, StatefulSet
---
> Replication

Kubernetes 환경에서는 가용성 확보를 위해 워커 노드를 복제하고 집합을 구성하며 각 Replica Pods를 동일한 Service 컴포넌트에 연결하여 클러스터링을 구현한다. 이를 통해 클러스터는 여러 노드에 걸쳐 애플리케이션을 실행하여 가용성 및 부하 분산을 확보할 수 있다.

Pod가 몇 개의 Replica를 가질지 blueprint를 정의할 수 있고 그 중 하나가 Deployment 이다. Pod를 생성하거나 복제하는 등의 관리 작업 시 관리자는 이 Deployment만 정의하면 된다. 

단, DB나 메시징 시스템과 같이 현재의 상태를 유지해야 하는 애플리케이션을 배포 및 관리 할 때는 적합하지 않다. 이 때는 StatefulSet 컨트롤러를 사용할 수 있지만, StatefulSet을 사용하더라도 Stateful 애플리케이션에 대한 복제는 간단한 문제가 아니기 때문에, 클러스터의 각 Pod는 Stateless 애플리케이션만 위치하고 DB와 같은 애플리케이션은 쿠버네티스 외부에서 호스팅 하는 것을 권장하기도 한다.(예: MySQL, Elastic Search, MongoDB 등)


References:
[1] https://kubernetes.io/docs/concepts/configuration/secret/
