---
layout: post
title: "[WEB] Web Server, Web Application Server, Servlet Container"
subtitle: 웹서버와 WAS의 차이, Servlet Conainer의 역할
categories: WEB
tags: [Java, WEB]
---


## 웹 서버, 웹 어플리케이션 서버 , 서블릿 컨테이너

## 정적 페이지와 동적 페이지

HTML, css, javascript 만으로 각 사용자에게 맞는 동적 웹페이지를 구현하기에는 어려움이 있다.

각 사용자의 요청에 따라 즉각적으로 반응을 해주고 변경사항을 쉽게 업데이트 할 수 있는 그런 웹페이지가 필요하고 그런 페이지를 Dynamic Web Page, 동적 웹 페이지라고 한다.

개발자의 입장에서 생각해보자.

만약 웹페이지에 대해 변경사항이 생겼다면 동적으로 웹 페이지를 구현했을 경우, 서버에서만 변경 사항을 처리하면 따로 HTML 파일을 수정하지 않아도 사용자의 페이지에서는 자동으로 변경되어지기 때문에 웹 페이지 관리가 더 쉬워지고 비용도 절감된다.

만약 정적인 웹이라면 ? 변경사항을 업데이트하기 위해 변경사항에 대한 HTML 을 모두 수정한 후 다시 업로드해야 사용자에게 변경사항이 보여진다.

웹 페이지의 흐름을 아주 간단하게 말하면

client가 요청을 하면 web server에서 그 요청에 대한 처리를 하고 요청에 대한 결과를 client에게 보내는 것이라고 할 수 있다.

![Untitled](/assets/images/posts/2022-03-23-web-1.png)

정적인 웹 페이지는 사용자의 요청에 따라 서버에 이미 저장된 HTML 파일들만 불러오는 것이고,

동적인 웹 페이지는 사용자의 요청을 서버 측에서 처리를 해서 요청에 맞는 결과를 보내 사용자에게 보여지게 하는 것이다.

동적인 웹 페이지에서 사용자의 요청을 처리하기 위해 Server는 어떻게 동작해야할까?

일단 각 사용자에게 맞는 웹 페이지를 보여주기 위해서는 데이터베이스와 웹 애플리케이션 서버가 필요합니다.

## 웹 서버면 웹 서버지, 웹 어플리케이션 서버?

## 웹 서버와 웹 어플리케이션 서버

### **웹 서버**

웹 서버는 하드웨어이기도 하고 소프트웨어이기도 하다.

- 하드웨어 측면
    - 소프트웨어 Web Server와 웹 사이트의 컴포넌트 파일(HTML, CSS, JavaScript, images 등 ) 을 저장하는 컴퓨터.
    - Web Server는 인터넷에 연결되어 웹에 연결된 다른 기기들이 웹 서버의 데이터를 주고받을 수 있도록 한다.
        - 서로 통신을 하기위해 Protocol(표준 인터넷 통신 규약)을 사용하여 통신하고  웹 페이지의 경우 HTML 파일을 주고 받기 위해  HTTP 프로토콜을 사용한다.
        - [HTTP 프로토콜에 대한 자세한 설명](https://velog.io/@sdc337dc/%EC%9B%B9-%EA%B0%9C%EB%85%90-Http-%ED%86%B5%EC%8B%A0#1-http-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C)
- 소프트웨어 측면
    - 웹 사용자가 어떻게 서버의 파일들에 접근하는지를 관리.
    - 웹서버는 HTTP 서버이고, HTTP 서버는 URL(웹 주소) 및 HTTP(브라우저가 웹 페이지를 보는 데 사용하는 프로토콜)를 처리하는 소프트웨어.
    - HTTP 서버는 저장된 웹 사이트의 도메인 이름을 통해 액세스할 수 있으며, 호스트된 웹 사이트의 컨텐츠를 최종 사용자의 장치에 전달한다.
- 종류
    - Apache, Nginx, IIS

✔️ <span style="color:#ff5100; font-weight:bold;">웹 서버란?</span>
> 웹 브라우저 client로부터 HTTP 요청을 받고, 정적인 콘텐츠를 제공하는 프로그램.    
> HTTP 프로토콜을 기반으로 client와 통신하고 client 의 요청을 서비스한다.


### **웹 어플리케이션 서버(WAS)**

- **동적인 컨텐츠를 제공**하기 위해 만들어진 애플리케이션 서버.
- **서블릿 컨테이너(또는 웹 컨테이너)**가 포함되어 있음
    - 컨테이너란 ? JSP, Servlet을 실행시킬 수 있는 소프트웨어.
    - 따라서 **WAS는 jsp, servlet 구동 환경을 제공**해준다.
        - Tomcat에서 Servlet관련 라이브러리를 제공하는 것을 알 수 있다.
        
        ![Untitled](/assets/images/posts/2022-03-23-web-2.png)
        
- 대부분의 WAS는 정적인 콘텐츠도 제공할 수 있기 때문에 WAS는 웹서버를 포함하는 개념이라고 할 수 있다.
 - 종류
    - Tomcat, JEUS, JBoss  
   
❓ 그럼 WAS만 쓰면 되지 왜 웹서버를 쓸까?    
 → [Web Server 와 WAS를 구분하는 이유](https://mi-nya.tistory.com/239)
1. WAS의 부하를 줄이기 위해서
    - 웹 서버에서 정적인 콘텐츠를 처리하고 웹 서버는 요청을 처리하기 위한 작업을 담당한다.
    - 정적 컨텐츠 처리를 웹 서버에 맡기므로써 WAS의 부하를 줄이고 수행기간을 줄인다.
2. 물리적으로 분리하여 보안 강화 

## 서블릿 컨테이너

> Java Sevlet을 관리하는 WAS의 구성요소.     
**클라이언트의 요청과 그 요청을 처리할 서블릿을 이어주는 역할**
> 

#### **역할**

- 서블릿, jsp, 그리고 다른 서버 처리를 다루는 파일들에 대한 요청을 다룬다. 서블릿 객체를 생성하고 서브릿을 로드, 언로드를 하며 요청 응답 객체를 생성, 관리하고 다른 서블릿 관리 작업을 수행한다.
- jsp와 servlet을 이용하는 웹 응용 프로그램은 **자바 언어**를 이용해서 작성할 수 있는데, 이렇게 작성된 jsp와 servlet을 실행시킬 수 있는 소프트웨어를 웹 컨테이너 혹은 서블릿 컨테이너 라고 한다.
- WAS 내부에서 개발자 대신 서블릿을 관리하는 것

#### **왜 필요한가**

- *client가 요청을 하면 웹서버가 받는다. 이 요청을 서버에서 처리해야한다.*
- *근데 **JAVA는 오직 JVM만 해석**할 수 있다. 자바를 해석할 수 있는 무언가가가 바로 컨테이너!*
    
    ✅ **JVM의 역할**
    
    > 요청을 서블릿에 전달하면 **JVM이 처리한 후** 동적으로 생성된 결과물을 반환한다.    
    > 즉, 서블릿을 사용하는 것은 **JVM이 각 요청을 분리된 자바 스레드 내부에서 처리**하도록 하는 것이다.
    
- 서블릿은 도움이 필요하다. 요청이 들어오면 누군가는 요청을 처리해야할 새로운 스레드를 만들어야하고 서블릿에서 필요한 메서드를 호출해야한다. 또 파라미터로 받은 값을 넘겯주기도 해야한다. 이 역할 하는 것이 바로 ! 컨테이너 !
    
    ✅ **Thread(스레드) 란?**
    
    > 프로세스 내에서 실제로 작업을 수행하는 주체
    

#### **기능**

1. 서블릿의 생명 주기 관리
    1. 서블릿 로드
    2. 서블릿 초기화
    3. 클라이언트의 요청에 따른 서브릿 메소드 호출(요청 처리)
    4. 서블릿 컨테이너가 종료되면 서블릿을 종료 시키고 메모리를 정리한다.
2. 통신 지원
    1. 웹 서버로 부터 받은 요청을 분석해 서블릿을 실행시키고, 서블릿에서는 웹서버의 정보를 확인할 수 있도록 하는 기능을 제공한다.
    2. 서블릿과 웹 서버가 서로 통신할 수 있는 쉬운 방법을 제공한다.
        1. serverr socket만들기
        2. 특정 포트 리스닝
        3. 연결 요청 들어오면 스트림 생성
3. 멀리 스레딩 지원
    1. 클라이언트의 요청에 따라 서블릿을 생성하고, 이미 생성된 서블릿에 대한 요청을 스레드를 생성해 실핸한다.
4. 선언적 보안 관리
    1. 보안에 관련된 내용을 서블릿, 자바 클래스 코드안에 하드 코딩할 필요가 없다.
    2. 즉, 필요한 데이터나 값, 코드 등을 직접 타이핑해서 집어넣는 일이 없다.
    3. 보안 관리는 배포 서술자라 불리는 `web.xml` 에 기록
5. JSP 지원

### 컨테이너의 동작 방식

1. client가 URL을 통해 요청을 보내면 HTTP Request를 Servlet 컨테이너로 전송한다.
2. HTTP Request를 전송받은 Servlet Container는 HttpServletRequest, HttpServletResponse 두 객체를 생성한다.
3. 그 다음에는 요청한  URL을 분석하여 어느 서블릿에 대해 요청을 할 것인지 찾는다.
4. 해당 서블릿에서 service 메소드를 호출한 후, POST, GET여부에 따라 doGet() 또는 doPost()를 호출한다.
5. doGet() or doPost() 메소드는 동적 페이지를 생성한 후 HttpSerlvetResponse 객체에 응답을 보낸다.
6. 응답이 끝나면 HttpServletRequest, HttpServletResponse 두 객체를 소멸시킨다.

여기서 컨테이너는 요청에 알맞는 서블릿을 찾는데 이 서블릿을 찾게 하기 위해서 개발자가 서블릿을 매핑해줘야한다.   

#### **서블릿을 매핑하는 방법**

1. `web.xml` 이용
    
    배포 서술자 web.xml 파일을 만들고 web-app 태그 내부에서 서블릿을 정의한다. 
    
2. `Annotation` 이용
    
    `@WebServlet(”매핑 주소”)` 
    

### 웹 서버와 WAS(+서블릿 컨테이너)의 요청 처리

1. 웹 서버가 HTTP 요청을 받는다.
2. 웹 서버는 요청을 서블릿 컨테이너로 보낸다.
3. 컨테이너는 서블릿을 로드하고 init()을 통해 초기화한다.
    (init()은 처음 로드 되었을 때 한번만 호출)
4. 컨테이너가 서블릿의 메소드를 호출하여 HTTP 요청 처리.
5. 서블릿은 요청을 처리한 결과인 응답을 넘겨줌(실제 요청 처리는 JVM이 수행)




<br/>
<br/>

---

<br/>
<br/>

> **참고**
> 

[LINE에서 하루 만에 정적 웹 페이지 개발해서 배포하는 방법](https://engineering.linecorp.com/ko/blog/how-to-quickly-develop-static-pages-in-line/)    
[mdn 웹서버란 무엇일까](https://engineering.linecorp.com/ko/blog/how-to-quickly-develop-static-pages-in-line/)    
[WAS와 Server의 차이? 그리고 Web Container 란?](https://doozi316.github.io/web/2020/09/13/WEB26/)    
[Servlet은 어쩌다 탄생되었을까](https://velog.io/@adam2/Servelt%EC%9D%80-%EC%96%B4%EC%A9%8C%EB%8B%A4-%ED%83%84%EC%83%9D%EB%90%98%EC%97%88%EC%9D%84%EA%B9%8C)    
[웹 서버(Web Server) / WAS / 컨테이너(Container)](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=silver889&logNo=70170794970)    
[서블릿 컨테이너(Servlet Container) 란?](https://velog.io/@han_been/%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88Servlet-Container-%EB%9E%80)    
[https://www.programcreek.com/2013/04/what-is-servlet-container/](https://www.programcreek.com/2013/04/what-is-servlet-container/)    
[위키백과](https://ko.wikipedia.org/wiki/%EC%9B%B9_%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88)    
[서블릿의 동작 구조](https://webfirewood.tistory.com/38)    