---
author: <1>
title: Kubernetes Helm
date: 2023-01-30
categories: [Kubernetes, Helm]
tags: [Kubernetes, Helm]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### helm이란?

```
* helm은 쿠버네티스 패키지 매니저이다. 
* 쉽게 표현하자면, apt, yum, pip 툴과 비슷하게 플랫폼의 패키지를 관리
* Helm은 Kubernetes 클러스터에서 배포할 수 있는 애플리케이션을 패키징하고 설치, 업데이트, 삭제하는 데 사용됩니다.
* Helm은 Helm Chart라는 패키지 형식을 사용합니다. 
* Helm Chart는 Kubernetes 애플리케이션을 구성하는 모든 Kubernetes 오브젝트 (Pod, Service, ConfigMap 등)와 이러한 오브젝트를 조작하기 위한 템플릿, 매개 변수 및 값, 의존성 등의 정보를 포함합니다. 
* 이러한 Helm Chart를 사용하면 새로운 Kubernetes 애플리케이션을 더 빠르게 배포하고 관리할 수 있습니다.
```

###  Helm Repository

```
Helm Chart가 저장되는 저장소입니다. 이 저장소에서는 Helm Chart를 검색하고 다운로드할 수 있습니다.
Docker Hub와 비슷한 개념
```

### install

```shell
brew install helm

helm version
```

### command

```shell
# 새로운 Helm Chart를 생성합니다.
helm create
```

```shell
# Helm Chart를 설치합니다.
helm install

# 릴리스 이름을 지정합니다.
--name
# 설치할 네임스페이스를 지정합니다.
--namespace
# 값을 포함하는 파일을 사용하여 설치합니다.
-f
# 값을 설정합니다.
--set
```

```shell
# 설치된 Helm Chart 목록을 확인합니다.
helm list -a
```

```shell
# 설치된 Helm Chart를 업그레이드합니다.
helm upgrade

# Chart가 설치되어 있지 않은 경우 설치합니다.
--install
# 업그레이드할 값을 포함하는 파일을 사용합니다.
-f
# 업그레이드할 값을 설정합니다.
--set
```

```shell
# 이전 버전으로 롤백합니다.
helm rollback
```

```shell
# 설치된 Helm Chart를 삭제합니다.
helm delete

# 릴리스와 함께 관련된 모든 데이터를 삭제합니다.
--purge
```

```shell
# 추가된 Helm Repository 목록을 확인합니다.
helm repo list
```

```shell
# Helm Repository를 업데이트합니다.
helm repo update
```

```shell
# Helm Repository에서 Helm Chart를 검색합니다.
helm search
```
