---
layout: post
title: "[Server] Nginx 설정하기 with.Proxy Server"
subtitle:
categories: DevOps
tags: [DevOps, Server]
---

## Nginx 란?

간단하게 말하자면 웹 서버 ! 

클라이언트로 부터 요청을 받았을 때 요청에 맞는 정적 파일을 응답해주는 HTTP Web Server로 활용되기도 하고, Reverse Proxy Server 로 활용하여 WAS 서버의 부하를 줄일 수 있는 로드 밸런서로 활용되기도 한다.

가장 유명한 Apache 서버와의 다른 점 ?

- Apache 와 같은 웹 서버는 클라이언트로부터 받은 요청을 처리할 때 **새로운 프로세스 또는 쓰레드를 생성**하여 처리한다. 요청마다 쓰레드가 생성되므로 접속하는 사용자가 많으면 그만큼 쓰레드가 생성되어 CPU와 메모리 자원의 소모가 커진다.
- Nginx 는 Event-Driven 구조로 동작하기 때문에 한 개 또는 지정된 프로세스만 생성하여 사용하고, 비동기 방식으로 요청들을 병렬적으로 처리할 수 있다. 그래서 새로운 요청이 들어오더라도 새로운 프로세스로 쓰레드를 생성하지 않기 때문에 프로세스와 쓰레드 생성 비용이 존재하지 않고, 적은 자원으로도 효율적인 운용이 가능하다. 단일 서버에서도 동시에 많은 연결을 처리할 수 있다.

## Nginx의 구조

- 하나의 Master와 여러개의 Worker Process 로 구성.
- Master 는 설정 파일을 읽고, 유효성을 검사한다.
- Worker Process 를 관리한다.
- Worker Process 사이에 요청을 효율적으로 분배하기 위해 OS에 의존적인 메커니즘을 사용한다.

## Nginx 설정

### 설정파일

- Nginx는 설정파일의 지시어에 따라 작동한다.
- 설정파일 위치 : `/etc/nginx/nginx.conf`
- 지시어의 종류
    - simple directive : `이름` `값` `;`
        
        ```bash
        worker_process 1;
        ```
        
    - block directive : simple directive 구조에 `{ }` 을 감싼 형태
        
        ```bash
        events{
        	worker_connections 1024;
        }
        ```
        
    - block directive는 해당 directive 안에 또 다른 block directive가 포함될 수 있음
        
        ```bash
        http {
          server {
        
            location / {
              root /path/to/html ;
            }
        
            location /images/ {
              root /path/to/image ;
            }
        
          }
        }
        ```
        
    - `include` 지시어는 특정 파일을 포함시킨다. 파일의 내용을 지시어가 있는 바로 그 위치에 해당 파일 내용이 삽입된다.

### /etc/nginx/nginx.conf

```bash
# worker 프로세스를 실행할 사용자 설정
# - 이 사용자에 따라 권한이 달라질 수 있다.
user  nginx;
# 실행할 worker 프로세스 설정
# - 서버에 장착되어 있는 코어 수 만큼 할당하는 것이 보통, 더 높게도 설정 가능
worker_processes  1;

# 오류 로그를 남길 파일 경로 지정
error_log  /var/log/nginx/error.log warn;
# NGINX 마스터 프로세스 ID 를 저장할 파일 경로 지정
pid        /var/run/nginx.pid;

# 접속 처리에 관한 설정을 한다.
events {
    # 워커 프로레스 한 개당 동시 접속 수 지정 (512 혹은 1024 를 기준으로 지정)
    worker_connections  1024;
}

# 웹, 프록시 관련 서버 설정
http {
    # mime.types 파일을 읽어들인다.
    include       /etc/nginx/mime.types;
    # MIME 타입 설정
    default_type  application/octet-stream;

    # 엑세스 로그 형식 지정
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # 엑세스 로그를 남길 파일 경로 지정
    access_log  /var/log/nginx/access.log  main;

    # sendfile api 를 사용할지 말지 결정
    sendfile        on;
    #tcp_nopush     on;

    # 접속시 커넥션을 몇 초동안 유지할지에 대한 설정
    keepalive_timeout  65;

    # (추가) nginx 버전을 숨길 수 있다. (보통 아래를 사용해서 숨기는게 일반적)
    server_tokens off

    #gzip  on;

    # /etc/nginx/conf.d 디렉토리 아래 있는 .conf 파일을 모두 읽어 들임
    include /etc/nginx/conf.d/*.conf;
}
```    


### block directive

nginx 의 설정파일에는 http, server, location 블록에 대한 설정을 할 수 있다.

세 블록은 계층 구조를 가지고 있으며 많은 지시어가 각 블록에서 동시에 사용될 수 있는데 http 블록의 내용은 server 블록의 기본값이 되고 server 블록의 내용은 location 블록의 기본값이 된다. 만약, 상위 블록에서 선언된 지시어를 하위 블록에서 다시 선언하면 상위의 지시어는 무시된다. 

1. http 블록
    - HTTP 부분과 관련된 모듈의 지시어와 블록을 정의한다.
    - server와 location의 루트 볼록이다.
    - http 블록 안에 한 개 이상의 server 블록을 선언할 수 있다.
2. server 블록 
    - server 블록은 하나의 호스트를 선언하는데 사용한다.
    - https 블록 안에서만 사용할 수 있다.
    - server 블록에는 한 개 이상의 location 블록을 선언할 수 있다.
3. location 블록
    - location 블록에는 server 블록 안에 정의된다.
    - 특정 URL을 처리하는 방법을 정의한다.
4. events 블록
    - events 블록은 네트워크의 작동 환경을 설정하는 지시어를 제공한다.
    - 이벤트 블록의 지시어는 이벤트 블록에서만 사용할 수 있다.
    - http, server, location 블록과 상속관계를 갖지 않으나, 아래의 지시어들은 반드시 events 블록 안에서만 사용한다.
        - `accept_mutex on;` : LISTEN 소켓을 오픈하기 위한 accept 뮤텍스의 사용/해제를 설정
        - `accept_mutex_delay 500ms;` : 자원 획득을 다시 시도하기 전에 작업자 프로세스가 기다려야 하는 시간을 정의한다. accept_mutex 지시어가 off 로 설정되어있으면 이 값은 사용되지 않는다.
        - `worker_connections 1024;` : Worker Process 가 동시에 처리할 수 있는 접속자 수를 정의한다. (worker_processes * worker_connections = 최대 접속자 수)
    
## Reverse Proxy 

- Nginx는 리버스 프록시로도 활용할 수 있다.
- `Reverse Proxy` ? 외부 클라이언트에서 서버로 접근 시, 중간에서 중개자 역할을 하여 내부 서버로 접근할 수 있도록 도와주는 서버이다.
    - 보완 : 외부 사용자로부터 내부망에 있는 서버의 존재를 숨길 수 있다. 모든 요청을 리버스 프록시 서버에서 받으며, 매핑되는 내부 서버로 요청을 전달한다. nginx는 SSL 설정도 가능하다.
    - 로드밸런싱 : 리버스 프록시 서버가 내부 서버에 대한 정보를 알고 있으므로, 각 서버의 상태에 따라 부하를 분산시키며 요청을 전달할 수 있다.

## Nginx 설정하기 with.Reverse Proxy

```bash
http {
    server {
        listen 80;
        location / {
            proxy_pass http://127.0.0.1:8080;
        }
				location /test {
					 proxy_pass http://127.0.0.1:8080/test;
				}
    }
}
```

- listen 에 지정된 포트(defautl : 80)로 요청이 들어오면 location / 에 해당되고 이 요청은 proxy 서버에 의해 http://127.0.0.1:8080로 포워딩 한다.
- 마찬가지로 ~:80/test 로 요청이 들어오면 location /test에 해당되고 이 요청은 proxy 서버에 의해 [http://127.0.0.1:8080/test](http://127.0.0.1:8080/test) 로 포워딩 한다.



> 참고       
>
[https://icarus8050.tistory.com/57](https://icarus8050.tistory.com/57)  
[https://medium.com/sjk5766/nginx-reverse-proxy-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-e11e18fcf843](https://medium.com/sjk5766/nginx-reverse-proxy-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-e11e18fcf843)


