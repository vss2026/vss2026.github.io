---
author: <1>
title: Kubernetes NameSpace, ResourceQuota, LimitRange
date: 2023-01-29
categories: [Kubernetes, Object]
tags: [Kubernetes]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### Namespace, ResourceQuota, LimitRange

![namespace-resourceQuota-limitRange](namespace-resourceQuota-limitRange1.png)

~~~
- kubernetes Cluster의 자원은 한정적
- NameSpace안 여러개 pod가 존재 각 pod는 Cluster의 메모리를 가져옴

1. Resource Quota
- 프로젝트의 리소스를 제한시키는 기능을 제공합니다. 크게 세가지 타입의 리소스를 관리
- 유형에 따라 하나의 네임스페이스 내에 생성할 수 있는 객체의 수량을 제한할 수 있다
- Namespace 뿐만 아니라 Cluster 전체에 부여할 수 있는 권한
- Compute Resource (cpu, memory, ephemeral-storage 등)
- Storage Resource (pvc용량, pvc개수등)
- Object counts (pod, rc, rsourcequotas, svc, configmap 등)

2. LimitRange
- 아무런 제약을 걸지 않았을 때, 기본적으로 컨테이너는 Openshift의 리소스를 무제한으로 사용 가능
- pod이나 컨테이너의 리소스를 제한하는 정책
- pod과 컨테이너의 최소&최대 컴퓨팅 리소스 사용량 지정
- 최소&최대 pvc용량 지정
- 리소스에 대한 Request와 Limit사이의 비율을 지정
- Namespace내에서만 사용 가능
- limit Range를 설정해서 Namespace의 들어오는 pod를 검증 (메모리가 범위가 초과 되면 해당 nameSpace는 들어올 수 없음)
~~~


![namespace-resourceQuota-limitRange2](namespace-resourceQuota-limitRange2.png)

~~~
1. Namespace
- 한 Namespace별 같은 object 이름은 중복으로 만들 수 없다
- Namespace를 지우게 되면 안에 있는 모든 자원은 같이 지워진다.

2. ResourceQuota
- request - request 자원 지정
- limits - 최대 자원 지정
- ResourceQuota가 명시 되어있는 namespace의 pod 생성시 자원을 명시해야 한다 (명시하지 않은경우 pod 생성 x)
- pod 생성시 자원이 초과 되는 경우 pod 가 생성되지 않음 (ResourceQuota를 만들시 해당 namespace의 pod는 존재 하지 않도록 주의)
- 제한 가능 자원 - cpu, memory, storage
- 제한 가능 object - pod, service, configMap, PVC ... (업데이트시 계속 추가 되고 있음)

3. LimitRange
- min - 최소 할당 자원
- max - 최대 할당 자원
- maxLimitRequestRatio - request값과 limit값의 비율이 설정 한 배수를 초과하면 안됨
- default - 값을 설정 하지 않은경우 default의 명시된 값으로 자원이 할당
~~~

##### kubectl

1. Describe
```shell
# nm-3의 Namespace에 있는 ResourceQuota들의 상세 조회
kubectl describe resourcequotas --namespace=nm-3
# nm-5의 Namespace에 있는 LimitRange들의 상세 조회
kubectl describe limitranges --namespace=nm-5
```

# Reference
----
**Share a Cluster with Namespaces** : <https://kubernetes.io/docs/tasks/administer-cluster/namespaces/>

**Resource Quotas** : <https://kubernetes.io/docs/concepts/policy/resource-quotas/>

**Limit Ranges** : <https://kubernetes.io/docs/concepts/policy/limit-range/>
