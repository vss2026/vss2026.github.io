---
author: <1>
title: Kubernetes Volume
date: 2023-01-23
categories: [Kubernetes, Object]
tags: [Kubernetes]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### volume

![volume](volume.png)

~~~
1. emptyDir
- container 끼리 데이터를 공유 하기 위함
- pod 문제가 생기면 없어질 수 있음 (일시적인 데이터만 관리하는걸 권장)

2. hostPath
- node의 올라가 있는 path를 기준으로 사용
- pod가 문제가 생겨도 데이터는 없어짐
- schedule의 의해서 다른 node에 pod가 생성 될 수 있음 -> path가 없는 node에 pod가 생성 될 가능성이 있다
  solution -> node 추가시 마다 mount를 걸어줄 수 있다. (이런 구성으로 작업하는 건 추천 하지 않는다)
- pod 자신이 할당 되어있는 host의 데이터를 읽거나 사용 할 때 쓰일 수 있다

** host path type
DirectoryOrCreate : 실제 경로가 없다면 생성
Directory : 실제 경로가 있어야됨
FileOrCreate : 실제 경로에 파일이 없다면 생성
File : 실제 파일이 었어야함

3. PVC / PV
- pod 영속성 있는 volume을 제공하기 위함
- 외부 원격을 이용해서 연결 할 수 있다. ex) amazon, nfs
- pv - volume을 연결하기 위한 spec 정의
- pcv - 실제 사용 하기 위한 spec 정의
  storageClassName: "" -> 다른 동작으로 사용 될 수 있기 때문에 빈 값을 정의해줌
~~~

### volume (intermediate)

![volume](intermediate-volume1.png)

```
- 데이터를 안정적으로 유지하기 위해서 쓰임
- internal
    1. 내부 경로로 지정 가능
    2. op-premise solution을 node에 설치 해서 사용 가능
    3. nfs를 이용해서 다른 서버를 이용 가능
- external
    외부 cloud storage 를 이용해서 사용
- pvc accessMode는 kubernetes는 3가지를 지원해주지만 모든 volume이 지원하는게 아니기 때문에 확인이 필요
```

![volume](intermediate-volume2.png)

```
1. Dynamic Provisioning
  - pvc 생성시 pv를 자동으로 생성
  - pvc 생성시 storageClassName 지정

2. Status
  - Available
      * 최초 pv가 만들어 졌을 때
  - Bound 
      * pvc와 연결이 된 상태
  - Released 
      * pvc 삭제 시 pv 연결이 끊어진 상태 
  - Failed
      * pv와 실제 데이터간 연동에 문제가 생긴 경우

3. ReclaimPolicy
  - Released 상태가 되었을때 동작을 정의

  - Retain 
      * default
      * 데이터 보존
      * 재사용 불가
  - Delete 
      * StorageClass 사용시 Default
      * volume의 종류에 따라 데이터 삭제
      * 재사용 불가
  - Recycle
      * Deprecated
      * 데이터 삭제
      * 재사용 가능
```

##### StorageClass

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: default
  annotations:
    # Default StorageClass로 선택 
    storageclass.kubernetes.io/is-default-class: "true" 
# 동적으로 PV생성시 PersistentVolumeReclaimPolicy 선택 (Default:Delete)
reclaimPolicy: Retain, Delete, Recycle
provisioner: kubernetes.io/storageos
# provisioner 종류에 따라 parameters의 하위 내용 다름 
parameters:   
```

##### kubectl

```shell
# 해당 배포 버전으로 rollback
kubectl rollout undo deployment deployment-1 --to-revision=2
# 배포 버전 정보 확인
kubectl rollout history deployment deployment-1
```

# Reference
----
**Volumes** : <https://kubernetes.io/docs/concepts/storage/volumes/>

**StorageClass** : <https://kubernetes.io/docs/concepts/storage/storage-classes>

**Dynamic Volume Provisioning** : <https://kubernetes.io/docs/concepts/storage/dynamic-provisioning>
