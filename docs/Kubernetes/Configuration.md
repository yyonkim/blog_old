---
layout: default
title: k8s - Components
parent: Kubernetes
tags:
- Kubernetes
---

## Kubernetes Configurations

### Pod 생성 Overview
---
> Kubernetes Configuration은 Json 또는 Yaml로 정의할 수 있으며 Declarative 형식을 취한다. Yaml은 문법은 단순하나 indentation에 민감하기 때문에 에디터에서 Yaml 플러그인 사용을 권장한다

```yml
api version: apps/v1
kind: Deployment
metadata: 
	name: my-app
	labels:
		app: my-app
spec: 
	replicas: 2
	selector: 
		matchlabels:
			app: my-app
	template: 
		metadata:
			labels: 
				app: my-app
		spec: 
			containers:
			- name: my-app
			  image: my-image
			  [... 중략 ...]
```

위와 같이 정의한 deployment의 is == should(Desired State)를 의미한다. 예를 들어, 복제할 Pod의 replicas: 2 로 정의한 상태에서 Pod가 죽어서 1개인 상태라면 Controller Manager(CM)은 Is(Actual State)와 Deployment에 정의된 Is(Desired State)를 비교하여 자동으로 해당 Pod의 Replica를 생성하는 식이다.


### Configuration file (.yaml)
---
> 각 컴포넌트(i.e. Deployment, Service...) Configuration은 공통적으로 3가지 파트로 작성된다. 이 중에서 metadata, spec 부분만 관리자가 작성하고 status는 Kubernetes가 자동으로 작성 및 지속적으로 업데이트 한다. 이러한 Configuration 은 애플리케이션이 동작하는 인프라를 Code로 관리하는 컨셉이므로 Git Repository에 따로 yaml 파일만 보관하기도 한다.

1. metadata
2. specification
	1. `kind(예: Deployment)` 에 따른 속성값(attributes)를 정의한다 
3. status
	1. CM이 Is(Actual State)와 Configuration의 Is(Desired State)를 지속적으로 체크하여 반영한다.

### Kubernetes는 Status 데이터를 어디서 가져오는가?
---
> 컨트롤 플레인의 etcd가 Kubernetes의 모든 컴포넌트에 대한 현재 상태를 저장하고 있다.
