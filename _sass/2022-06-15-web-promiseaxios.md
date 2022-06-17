---
layout: post
title: "[WEB] JavaScript 비동기 / 동기에 대해 알 아 보 자(Promise, Axios)"
subtitle:
categories: WEB
tags: [WEB, JavaScript]
---

## JavaScript의 비동기 / 동기

자바스크립트는 코드의 연산이 끝나기 전까지 기다리지 않고 바로 다음 코드를 먼저 실행한다.
이러한 비동기적 특성을 순차적으로 다루기 위해 Promise, setTimeout, async await 를 이용한다.

## Promise 란 ?

- 자바스크립트에서 비동기 동작을 다루는 하나의 패턴이다. 어떤 일의 진행 상태를 나타내는 객체로 진행 "상태"와 "값"이라는 속성을 가지고 있다.

### Promise가 생긴 이유

- Promise이전에 비동기 처리를 콜백 함수나 ajax 메소드를 통해서 처리했다.
- 하지만 비동기 처리 순서를 보장하기 위해 여러개의 콜백함수가 중첩되어 복잡도가 높아지는 콜백 지옥이 발생한다.
- 이러한 단점 때문에 나온 것이 promise와 async await 이다.

### Promise

- Promise는 new promise 인스턴스로 생성하고 resolve, reject를 통해 값을 반환한다.
- Promise를 사용하려면 promise의 인스턴스를 반드시 리턴해야한다.
- 반환된 promise 인스턴스는 then() 메소드를 통해 처리한다.

## Axios

- axios는 node.js 와 브라우저를 위한 Promise 기반의 HTTP 통신 라이브러리이다.
- 비동기로 HTTP 통신을 가능하게 해주며 return을 Promise객체로 해주기 때문에 response 데이터를 다루기로 쉽다.
- 동일하게 then()으로 다음 작업을 처리한다.

> 출처

[https://ljtaek2.tistory.com/158?category=897337](https://ljtaek2.tistory.com/158?category=897337)
[https://ljtaek2.tistory.com/129?category=897337](https://ljtaek2.tistory.com/129?category=897337)
[https://velog.io/@eassy/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%B2%98%EB%A6%AC](https://velog.io/@eassy/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EB%B9%84%EB%8F%99%EA%B8%B0%EC%B2%98%EB%A6%AC)
