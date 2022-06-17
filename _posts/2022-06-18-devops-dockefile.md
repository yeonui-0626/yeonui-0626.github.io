---
layout: post
title: "[Docker]Docker File 작성하고 image 생성하기"
subtitle:
categories: DevOps
tags: [DevOps, Docker]
---

## Docker File 작성하고 image 생성하기

<br/>
<br/>
<br/>

도커에 대한 개념을 익혔다면  
도커에서 어플리케이션을 실행하기 위해 Dockerfile을 생성하고  
image 를 생성한 뒤, image 를 실행하여 동작을 확인해 봅시다 !  
<br/>

> Docker 와 Dockerfile, Docker image, container 에 대한 설명을 [이전 포스팅](http://localhost:4000/devops/2022/06/17/devops-docker.html)을 참고 해주세요!

먼저 실습을 할 프로젝트는 socket.io 를 이용해 채팅 프로그램을 간단히 구현한 어플리케이션입니다.  
node.js 서버를 사용하고 필요한 모듈을 설치한 뒤 socket.js 파일을 실행하면 됩니다.
![docker1](/assets/images/posts/dockerfile1.png)

서버를 따로 사용하지 않고 로컬에서 테스트를 할 것이기 때문에  
**Dockerfile을 생성→ image 생성 → image 실행** 순서로 간단하게 진행했습니다.  
<br/>

### 1. Docker File 생성

이 어플리케이션을 도커에서 실행시키기 위한 Dockerfile을 만듭니다.

Dockerfile는 어플리케이션을 실행하기 위해 어떻게 container를 만들지에 대한 정보가 담겨있는 파일입니다.

필요한 라이브러리, 파일, 환경 변수, 구동 방법에 대한 내용을 작성하면 됩니다.

```docker
FROM node:16.15.0
LABEL "purpose"="practice"
RUN mkdir -p /usr/src/test
WORKDIR /usr/src/test
COPY package.json /usr/src/test
RUN npm install
COPY . /usr/src/test
CMD node socket.js
```

명령어를 순서대로 살펴보겠습니다.

`FROM node:16.15.0` : node 를 기반으로 이미지를 생성하겠다.

`RUN mkdir -p /usr/src/test` : /user/src/test 폴더 생성

`WORKDIR /usr/src/test` : 생성한 /user/src/test 를 작업 디렉토리로 설정

`COPY package.json /usr/src/test` : 모듈들을 설치하기 위해 package.json 파일을 작업 디레토리에 복사

`RUN npm install` : 모듈 설치

`COPY . /usr/src/test` : 실행에 필요한 파일들 복사

`CMD node socket.js` : 어플리케이션 실행

- 여기서 `COPY` 는 어플리케이션에 필요한 파일들을 docker container 내부로 복사한다는 의미입니다.

### .dockerignore

.gitgnore 처럼 도커에서 빌드시 제외할 파일들을 설정할 수 있습니다.
.dockerignore 파일을 생성한 뒤, 제외할 폴더/파일을 작성하면 됩니다.
그럼 image 를 생성하기 위한 build 를 할 때, 해당 폴더/파일들을 제외하고 빌드가 진행됩니다.

![docker2](/assets/images/posts/dockerfile2.png)

### 2. Image 생성

- 아래의 명령어를 통해 dockerfile을 build하고 image 를 생성합니다.

```shell
docker build --tag [repository 이름]:[태그]  [dockerfile이 있는 경로]
```

`--tag` : build name 을 name:tag 형식으로 작성

`[repository 이름]:[태그]`

- 태그는 버전과 비슷한 역할

`[dockerfile이 있는 경로]`

- `.` : 현재 경로

```shell
docker build --tag sockerbuild:1.0 .
```

![docker3](/assets/images/posts/dockerfile3.png)

빌드를 하면 dockerfile의 script 들이 실행되며 성공적으로 끝나면 image가 생성됩니다.

### 3. mage 생성 확인

`$ docker images` : 생성된 이미지와 이미지 정보 확인

![docker4](/assets/images/posts/dockerfile4.png)

### 4. image 실행

- image 생성이 완료되었다면 아래의 명령어로 image를 실행합니다.

```docker
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

```sql
docker run -p 3000:3000 -d sockerbuild:1.0
```

`-q` : 포트설정  
`-d` : 컨테이너 background에서 실행

### 5. docker container 확인

- image 를 실행하면 containr가 생성된 것을 확인할 수 있습니다.
- `docker ps` : container 확인
  ![docker5](/assets/images/posts/dockerfile5.png)

<br/>
<br/>

---

<br/>
<br/>

> 참고
>
> https://taekwang.tistory.com/31  
> https://code-masterjung.tistory.com/133 > https://wooono.tistory.com/123
