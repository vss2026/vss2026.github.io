---
author: <1>
title: Docker File
date: 2022-11-15
categories: [Docker]
tags: [Docker]     # TAG names should always be lowercase
img_path: ../../assets/img/
---

### docker file 속성

|----------|:------:|
| FROM	 | 베이스 이미지를 지정 **모든 Dockerfile은 이 명령어로 시작** |
| COPY, ADD	 | 파일이나 디렉토리를 컨테이너 안으로 복사 |
| RUN	 | 명령어를 실행합니다. **Dockerfile을 빌드할 때 실행** |
| WORKDIR	 | 명령어가 실행될 작업 디렉토리를 설정 |
| ENV	 | 환경 변수를 설정 |
| ARG	 | docker file 내 변수 |
| EXPOSE	 | 호스트와 연결할 포트 번호를 설정 |
| CMD	 | 컨테이너가 시작될 때 실행할 명령어를 지정 **1개만 작성 가능**, **docker run 명령어에서 실행된 명령어가 더 높은 우선순위** |
| ENTRYPOINT	 | 컨테이너가 시작될 때 실행할 명령어를 지정 **ENTRYPOINT와 CMD를 함께 사용의 경우 ENTRYPOINT는 실행문 CMD 옵션 또는 인자**|
| VOLUME	 | 호스트와 공유할 디렉토리를 설정 |


### 예제 파일
```docker
# 베이스 이미지 설정
FROM node:14

# 작업 디렉토리 설정
WORKDIR /app

# 소스 코드 복사
COPY . .

# 환경 변수 설정
ENV NODE_ENV=production

# 인수 정의
ARG PORT=3000

# 포트 설정
EXPOSE $PORT

# 빌드할 때 필요한 패키지 설치
RUN npm install --production

# 시작 명령어 정의
ENTRYPOINT ["node", "index.js"]

# 추가 인자 정의
CMD ["--port", "$PORT"]
```

1. 베이스 이미지로 Node.js 14 이미지를 사용

2. /app 디렉토리를 작업 디렉토리로 설정

3. 현재 디렉토리의 소스 코드를 컨테이너 안으로 복사

4. NODE_ENV 환경 변수를 production으로 설정

5. Dockerfile을 빌드할 때 전달할 PORT 인수를 정의

6. 컨테이너와 호스트를 연결할 포트 번호를 설정

7. npm install 명령어를 실행하여 필요한 패키지를 설치

8. ENTRYPOINT로 node index.js 명령어를 설정

9. CMD로 --port 인자와 PORT 값을 전달

### Docker Multi Stage

> Docker Multi-Stage는 Dockerfile에서 여러개의 FROM 명령어를 사용하여 여러개의 이미지 빌드 단계를 사용하는 기능 
>> 단일 Dockerfile 내에서 여러개의 이미지를 빌드하고 이를 각각의 단계로 캡슐화하여 빌드 크기를 줄이고, 이미지의 보안성과 안정성을 높일 수 있습니다.

``` docker
# 첫 번째 단계 - Build
FROM node:14-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 두 번째 단계 - Production
FROM node:14-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY package*.json ./
RUN npm install --only=production
CMD ["npm", "start"]
```

1. Node.js 패키지를 설치하고 어플리케이션을 빌드합니다. 
2. 두 번째 단계에서는 이전 단계에서 빌드된 어플리케이션을 실행하기 위해 필요한 파일들만 가져와서 이미지를 빌드합니다. 

##### 최종 이미지에는 필요한 파일만 포함되어 이미지 크기가 줄어들고, 보안성과 안정성이 향상됩니다.
