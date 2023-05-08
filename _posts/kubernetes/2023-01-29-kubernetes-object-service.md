---
author: <1>
title: Kubernetes Service
date: 2023-01-29
categories: [Kubernetes, Object]
tags: [Kubernetes]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### service
![service](service.png)

~~~
1. ClusterIP
- pod의 ip는 가변적 이므로  불변적 service ip를 이용
- cluster내 접근가능, 여러개 pod 연결 가능
- default type

2. NodePort
- ClusterIP 할당, 같은 기능이 포함 되어있음
- 연결된 node는 모두 같은 port로 할당, ip의 port로 연결을 하면 service 연결
- 모든 node에 port 할당
- port 범위 - 30000~32767 설정 하지 않을 시 자동으로 port 할당
- traffic 모든 node의 pod로 전달
- externalTrafficPolicy: Local 설정시 모든 node traffic 전달 하지 않음

3. Load Balancer
- NodePort 의 특징을 그대로 가지고 있음
- Load Balancer ip는 별도의 plugin으로 할당 해야함
~~~

### service (intermediate)

![service](intermediate-service.png)

```
* Service DNS 규칙: {serviceName}.{nameSpace}.svc.{Dns name}
* Pod DNS 규칙: {ip}.{nameSpace}.pod.{Dns name}
* 같은 namespace 안에서는 service는 앞자리만 입력 해도 되지만 pod는 모든 주소를 입력 해야함

1. Headless
  - Service로만 통신 하는 것이면 clusterIp로 해결이 가능 하지만 특정 pod를 통신해야 하는 경우 Headless를 사용
  1. Service 생성시 clusterIp: None 추가해서 Service에 Ip가 생성되지 않도록 함
  2. pod 생성시 hostname, subdomain 설정
  3. Service는 연결된 pod의 ip를 모두 할당 받으며 hostname.subdomain으로 해당 pod에 요청이 가능하게 됨

2. Endpoint
  - Service와 Pod 연결시 selector와 label 값을 설정해서 하는데 실제 생성시 내부적으로는 endpoint를 생성함
  - endpoint 직접 연결
    1. service 생성시 selector지정 x
    2. pod 생성시 label 지정 x
    3. service명으로 endpoint 생성후 접속정보 연결

3. ExternalName
  - externalName에 domain 등록시 Dns Cache가 외부와 내부 Dns를 찾아 ip를 알아내게됨
  - 접속하기 위한 외부 주소가 바뀌더라도, CNAME은 그대로 유지할 수 있어 애플리케이션을 다시 작성하거나 빌드하지 않아도 된다.
```

### Authentication
![authentication](authentication.png)

```
1. X509 Client Certs
  - Cluster의 kubeConfig정보를 가져와서 인증 요청이 가능
  - CA key: 발급기관 개인키
  - Client Key: 클라이언트 개인키
  - kubectl 설치시 kubeconfig 내용을 등록하는 과정을 거쳐 kubectl을 이용 api요청이 가능한 상태가 됨
  - accept-hosts를 이용해 8001 포트로 proxy 열어두면 외부에서도 http로 접근이 가능

2. 외부 서버 kubectl
  - 여러 kubeconfig를 등록 가능 -> 여러 cluster에 접근이 가능

3. service Account
  - NameSpace 생성시 default라는 이름의 serviceAccount가 자동으로 생성
  - Secret정보가 담겨있음 (token, CA crt)
  - token값을 알고 있다면 사용자도 api 서비스 접근이 가능
```


##### kubectl

1. GET
  - defalut 이름의 Namespace에서 svc-3 이름의 Service 조회

  ```
  kubectl get service svc-3 -n defalut
  ```

### Reference
----
**Service** : <https://kubernetes.io/docs/concepts/services-networking/service/>

**Kubernetes NodePort vs LoadBalancer vs Ingress?** : <https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0>

**DNS for Services and Pods** : <https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/>
