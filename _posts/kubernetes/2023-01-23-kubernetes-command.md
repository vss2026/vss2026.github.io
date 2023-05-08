---
author: <1>
title: Kubernetes Command
date: 2023-01-23
categories: [Kubernetes, Command]
tags: [Kubernetes]     # TAG names should always be lowercase
---

# Command

##### Exec
``` shell
# Pod이름이 request-pod인 Container로 들어가기 (나올땐 exit)
kubectl exec request-pod -it /bin/bash
```
##### Describe
``` shell
# Endpoints 상세보기
kubectl describe endpoints endpoint1
``` shell
##### Watch
``` shell
# Object들의 모든 Event 정보를 지속적으로 조회해서 | 그중에 pod-readiness-exec1라는 단어와 매칭되는 내용만 출력
kubectl get events -w | grep pod-readiness-exec1
# pod-readiness-exec1이름의 Pod 상세 내용중에 | Events와 매칭되는 단어에서 20번째 줄까지 지속적으로 출력
watch "kubectl describe pod pod-readiness-exec1 | grep -A20 Events"
```
##### Get
``` shell
# defalut 이름의 Namespace에서 svc-3 이름의 Service 조회
kubectl get service svc-3 -n defalut
```
##### Get All Objects in Namespaces
``` shell
kubectl get all -n ${nameSpace}
```

##### Force Deletion
``` shell
kubectl delete persistentvolumeclaims pvc-fast1 --namespace=default --grace-period 0 --force
kubectl delete persistentvolume pvc-b53fd802-3919-4fb0-8c1f-02221a3e4bc0 --grace-period 0 --force
```

##### Label
``` shell
# Add
kubectl label nodes k8s-node1 os=centos
# Remove
kubectl label nodes k8s-node2 os-
```

##### Get Secret

``` shell
kubectl get secret {secret} -n {namespace} -o go-template={% raw %}'{{range $k,$v := .data}}{{printf "%s: " $k}}{{if not $v}}{{$v}}{{else}}{{$v | base64decode}}{{end}}{{"\n"}}{{end}}'{% endraw %}
```
