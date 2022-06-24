---
layout: post
title: "[Docker]Docker File 작성하고 image 생성하기"
subtitle:
categories: DevOps
tags: [DevOps, Docker]
---

도커에 대한 개념을 익혔다면  
도커에서 어플리케이션을 실행하기 위해 Dockerfile을 생성하고  
image 를 생성한 뒤, image 를 실행하여 동작을 확인해 봅시다 !
\_posts\2022-06-17-devops-docker.md

> Docker 와 Dockerfile, Docker image, container 에 대한 설명을 ![이전 포스팅](2022-06-17-devops-docker.md)을 참고 해주세요!

먼저 실습을 할 프로젝트는 socket.io 를 이용해 채팅 프로그램을 간단히 구현한 어플리케이션입니다.     
node.js 서버를 사용하고 필요한 모듈을 설치한 뒤 socket.js 파일을 실행하면 됩니다.
[docker1](../assets/images/posts/dockerfile1.png)