---
author: <1>
title: Docker Compose
date: 2022-11-17
categories: [Docker]
tags: [Docker]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### docker-compose

 > 애플리케이션의 구성요소를 정의하고, 이를 사용하여 컨테이너를 생성하고 실행하며, 네트워크와 볼륨을 설정하고, 로그 및 모니터링을 수행할 수 있습니다

 * version: Compose 파일의 버전을 지정
 * services: 실행할 서비스를 정의
 * networks: 서비스 간의 네트워크를 정의
 * volumes: 서비스에서 사용할 볼륨을 정의

### install

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version
```

### 예제 파일

```yaml
# 이 Compose 파일에서는 nginx와 Node.js 서비스를 정의하고 있습니다
version: '3.9'

services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80" #호스트 80번 포트와 컨테이너의 80번 포트를 연결
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro #/etc/nginx/nginx.conf 경로에 읽기 전용으로 마운트
    depends_on:
      - node #서비스 간의 종속성을 지정

  node:
    build: . #Dockerfile을 사용하여 이미지를 빌드
    command: npm start #명령어를 실행
    volumes:
      - .:/app #현재 디렉토리를 컨테이너의 /app 경로에 마운트
    ports:
      - "3000:3000"
```

> network 미지정시 같은 compose 파일의 서비스는 같은 network를 기본으로 바라본다.

### Command

```shell
#  Docker Compose로 정의된 애플리케이션을 시작
docker-compose up

# background
docker-compose up -d

# Docker Compose로 정의된 애플리케이션을 중지하고, 관련 리소스를 삭제
docker-compose down

#  Docker Compose로 정의된 애플리케이션을 중지
docker-compose stop

# Docker Compose로 정의된 애플리케이션을 시작
docker-compose start
```

```shell
# Docker Compose로 정의된 애플리케이션의 상태를 확인
docker-compose ps

# 로그를 출력
docker-compose logs

# 프로세스 상태를 출력
docker-compose top
```

```shell
# Docker Compose로 정의된 애플리케이션의 이미지를 빌드
docker-compose build

# Docker Compose로 정의된 애플리케이션의 이미지를 Docker Hub와 같은 레지스트리에 업로드
docker-compose push

# Docker Compose로 정의된 애플리케이션에서 사용되는 이미지를 레지스트리에서 다운로드
docker-compose pull
```

```shell
# Docker Compose로 정의된 애플리케이션의 이미지를 빌드
docker-compose build

# Docker Compose로 정의된 애플리케이션의 이미지를 Docker Hub와 같은 레지스트리에 업로드
docker-compose push

# Docker Compose로 정의된 애플리케이션에서 사용되는 이미지를 레지스트리에서 다운로드
docker-compose pull
```

```shell
#  Docker Compose로 정의된 애플리케이션에서 실행 중인 컨테이너에 명령을 실행
docker-compose exec
docker-compose exec -it container-name /bin/sh

# Docker Compose로 정의된 애플리케이션에서 새 컨테이너를 생성하고, 명령을 실행
docker-compose run
```
