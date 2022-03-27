---
layout: post
title: "[WEB] Servlet - Servlet의 상속 관계와 Life Cycle"
subtitle: Servlet의 상속 관계와 Life Cycle
categories: WEB
tags: [Java, WEB]
---


## Servlet

### Servlet 이란?

- 웹 요청에 대한 동적인 처리가 가능한 Server Side 자바 프로그램.
- 요청을 처리하기 위해 개발자가 작성해야하는 Java Class.

### Servlet Container는 어떻게 Servlet를 실행할까?
 
1. <span style="color:#ff5100; font-weight:bold;">Web Server는 HTTP요청을 Servlet Container에게 보낸다.</span>    
    1. 들어온 요청 URL을 보고 해당 요청을 수행할 Servlet을 실행한다.
    2. web.xml에 요청 URL이 어떤 Servlet과 매핑되어 있는지 작성되어 있어야한다.
2. <span style="color:#ff5100; font-weight:bold;">Servlet Container는 service() 메서드를 호출하기 전에 Servlet 객체를 메모리에 올린다.</span>    
*( ❗ servlet이 최초 호출되면 메모리에 올라간다. )*
    1. Servlet Container는 Servlet 파일을 컴파일 한다. 컴파일을 하게 되면 .class 파일이 생성된다.
    2. .class 파일을 메모리에 올려 Servlet 객체를 만든다.
    3. 메모리에 로드될 때 Servlet 객체를 초기화화는 init() 메서드가 실행된다.(처음 한번만 실행 !)
1. <span style="color:#ff5100; font-weight:bold;">Servlet Container는 Request가 올 때마다 thread를 생성하여 처리한다.</span>
    - 각 thread는 Servlet의 단일 객체에 대한 service()메서드를 실행한다.    
  
    <br/>
    
    ✔️ **Thread 의 역할**

    - Servlet Program에서 thread가 수행 할 메서드가 지정/할당되면
        - Thread는 생성 후 즉시 해당 메소드를 수행한다.
        - 해당 메소드가 수행 후 return하면 thread는 종료되고 제거됨.
        - **Servlet의 doGet(), doPost()를 호출하는 주체가 Thread이다.**
  
    <br/>
    ✔️ **Servlet Container와 Thread**
    
    - Thread의 생성과 제거는 Servlet Container가 담당한다 !    
        = Servlet의 Life Cycle을 Servlet Container 관리한다.       
        - 웹 브라우저 클라이언트의 요청이 들어왔을 때 Servlet 객체 생성은 Web Container를 포함하고 있는 WAS가 알아서 처리한다.

▶️ [이전 포스팅 참고](https://yeonui-0626.github.io/web/2022/03/25/web-webser-was.html)

- 이전 포스팅에도 컨테이너 동작과정이 나와있지만 위의 내용은 thread와 servlet의 실행과정에 더 포커스를 맞춘 과정이다.

### Servlet , GenericServlet , HttpServlet

- 사용자가 구현하는 모든 Servlet 클래스는 **반드시 HttpServlet class를 상속**받아야한다.
- HttpServlet class는 GenericServlet class를 상속하고 GenericServlet class는 Servlet 인터페이스를 상속한다.

✔️ **Servlet Interface**

- servlet이 반드시 구현해야하는 모든 메서드를 선언한다.
- 이 인터페이스는 GenericServlet 이나 HttpServlet에서 구현되어진다.
- Life Cycle 관리 ( ⇒ servlet 자원 관리 )
    - servlet의 초기화, 요청 처리, servlet 삭제 메소드 선언
- method
    - `init()` : servlet 초기화
    - `service()` : 요청 처리
    - `destory()` : servlet 제거
    

✔️ **GenericServlet class**

- servlet 인터페이스를 상속하고 클라이언트-서버 환경에서 서버단의 어플리케이션으로서 필요한 기능을 구현한 추상 클래스이다.
- `service()` 를 제외한 모든 메소드를 재정의한다.
    - service()는 추상 메소드로 선언되어 있음.

✔️ **HttpServlet class**

- 웹 사이트에 적합한 http servlet을 위한 추상 인터페이스를 제공한다.
- GeneialSevlet 클래스를 상속하여 `sevice()` 를 재정의 하므로써 HTTP 프로토콜에 알맞는 동작을 수행하도록 구현한 클래스이다.
- HttpServlet을 상속받는 subclass는 적어도 하나의 method를 오버라이드 해야한다.
- method
    - `doGet` : HTTP GET request 처리
    - `doPost` : HTTP POST request 처리
    - `doPut` : HTTP PUT requests 처리
    - `doDelete` : HTTP DELETE request 처리
    - `service()` : HTTP 요청방식에 따라 적잘한 메소드가 호출되도록 구현되어있다.
    

### Servlet의 Life Cycle

> *Servlet이 생성되고 제거되기까지*

1. Servlet Container에 의해 Servlet 객체 생성
    - 객체를 생성하는 과정은 servlet class 를 메모리에 올리는 과정이다.
    - 만약 메모리게 없다면 컴파일을 통해 servlet class를 메모리에 올린다.
    - 만약 메모리에 있다면 바로 serivice() 실행
2. 생성시, `init()` 메소드 호출하여 Servlet 초기화
3. 요청이 들어올 때마다 `service()` 메소드를 호출해서 Servlet이 HTTP 요청 처리
4. 서버는 `destroy()` 메소드를 호출하여 Servlet을 제거
( 톰캣의 경우 서버 종료 전이나 reload 전 수행)

- `init()`
    - Servlet 객체 초기화
    - Servlet class가 메모리에 로드될 때 init() 이 호출된다.
    - 한번만 수행
- `service()`
    - 요청이 들어오면 service() 가 호출되고 요청을 처리한다.
    - 따라서 응답(요청에 대한 처리)에 대한 모든 내용은 service()에 구현.
        - 개발자가 service() 를 오버라이드 하지 않았다면, HttpServlet에 구현되어 있는 service()가 호출된다.
        - Http 요청 방식(GET, POST, PUT, DELETE 등)에 따라 적절한 메소드(doGet, doPost, doPut, doDelete)를 호출한다.
        - 하위 클래스(우리가 구현하는 servlet class)에서 doGet, doPost 등을 오버라이드 해두면 HttpServlet의 service() 메소드가 요청에 맞는 메서드를 알아서 호출한다.
- `destroy()`
    - Servlet 객체를 메모리에서 제거
    - Web Aplication이 갱신되거나 WAS가 종료될 때 호출
    - 한번만 수행

> Servlet의 동작 과정을 보면, 개발하는 사람들은 java의 HttpServlet 클래스를 상속 받아 HTTP 요청(GET, POST, PUT, DELETE ... )을 처리할 메소드를 구현하면 된다.  그럼 serivce() 메소드가 해당 요청을 처리할 메소드를 호출할 것이다.
> 

⭐ 여기서 주목해야할 점

- 요청을 처리할 메소드 호출하는 작업을 개발자가 하는게 아니고  servlet을 관리하는 servlet container가 하고 있다는 것 !