---
author: <1>
title: Kubernetes ConfigMap, Secret
date: 2023-01-23
categories: [Kubernetes, Object]
tags: [Kubernetes]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### configMap, Secret

![configMap-Secret](configMap-Secret1.png)


~~~
- 개발과 운영 환경의 환경 설정이 다른경우 사용이 가능하다 ex) db 정보
- container 이미지를 각각 관리하는 불상사를 막을 수 있다
- pod 생성시 env 설정을 해주면 container 생성시 환경변수가 설정
~~~


![configMap-Secret2](configMap-Secret2.png)

~~~
1. Literal 상수
- configMap은 key와 value로 구분
- secret은 보안적인 요소를 저장
  base64로 인코딩을 해서 value를 지정 -> 환경변수 설정시 디코딩
  데이터가 메모리에 저장되기 때문에 보안에 유리
  한 Secret당 최대 1M까지만 저장됨

2. File
- 파일이름 = key 파일 content = value
- secret 파일 내용은 base64로 인코딩 할 필요가 없음 (자동 변환)

3. Mount
- container안에 mount path를 지정 할 수 있다
- configMap 파일 변경시 container의 환경변수 설정도 달라짐
~~~


##### kubectl

1. ConfigMap
```shell
# file-c.txt 라는 파일로 cm-file라는 이름의 ConfigMap 생성
kubectl create configmap cm-file --from-file=./file-c.txt
# key1:value1 라는 상수로 cm-file라는 이름의 ConfigMap 생성
kubectl create configmap cm-file --from-literal=key1=value1
# 여러 key:value로 cm-file라는 이름의 ConfigMap 생성 
kubectl create configmap cm-file --from-literal=key1=value1 --from-literal=key2=value2
```

2. Secret Generic
```shell
# file-s.txt 라는 파일로 sec-file라는 이름의 Secret 생성
kubectl create secret generic sec-file --from-file=./file-s.txt
# key1:value1 라는 상수로 sec-file라는 이름의 Secret 생성
kubectl create secret generic sec-file --from-literal=key1=value1
```


# Reference
----
**Configure a Pod to Use a ConfigMap** : <https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/>

**Distribute Credentials Securely Using Secrets** : <https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/>
