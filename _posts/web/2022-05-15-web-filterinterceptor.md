---
layout: post
title: "[Spring] 공통기능 처리를 위한 Filter와 Interceptor (+AOP)"
subtitle:
categories: Spring WEB
tags: [WEB, Spring, Filter, Interceptor]
---

<br/>

## 공통기능 처리를 위한 Filter와 Interceptor (+AOP)

> 앞에서 AOP로 공통기능을 처리하는 방법을 공부했다.
> 공통기능(cross-cutting concern) 을 핵심 로직 실행 전, 후이나 중간에 자동으로 실행되는 것처럼 하기 위해 어노테이션을 이용한 AOP로 구현했는데, 공통기능을 다른 방법으로도 처리할 수 있다. 다른 방법들에 대해 알아보고 직접 구현해보자.

---

## Filter

### Servlet Filter

- 요청과 응답을 거른 뒤 정제하는 역할.
- 클래스 형태로 존재하며 클라이언트로부터 오는 요청(request)을 먼저 Filter 에서 받아 필터링을 하고 ServletRequest, ServletResponse를 전달한다.
- Filter는 Spring만의 기능은 아니고 자바에서도 Filter 라이브러리를 제공하고 있다.
  - [자바의 Filter 인터페이스](https://javaee.github.io/javaee-spec/javadocs/index.html?javax/servlet/package-summary.html)
    - `javax.servlet.Filter`
- Filter 클래스를 구현하고 web.xml에서 설정한다.
- Spring에서 Servlet Filter는 DispatcherServlet 이전에 실행이 된다.
- 사용 예시
  - 인코딩, XSS 방어
  - 데이터 압축
  - 암호화
  - 캐싱
    등의 기능에 적용한다.

## Filter 동작 과정

- `javax.servlet.Filter` 에는 3개의 메소드가 있다.
  init()을 통해 초기화 진행 후, 초기화 작업이 성공적으로 종료되면 doFilter()에 작성된 필터링을 수행하고 destroy()를 통해 filter 는 소멸되는 생명주기를 갖는다.
  - `init(FilterConfig filterConfig)`
    - web container에 의해 한번 불려진다.
    - servlet container가 필터를 인스턴스화 한 후 init() 메소드를 호출한다.
    - init() 이 성공적으로 완료되어야 필터링을 진행할 수 있다.
    - `FilterConfig` : 필터의 구성 및 초기화 매개변수를 포함하는 객체
    - 기본적으로 구현되어 있다.
  - `doFilter(ServletRequest res, ServletResponse res, FilterChain chain)`
    - 필터에서 수행할 로직을 작성한다.
    - 개발자가 구현.
    - requst, response 가 filter chain을 통과할 때마다 serlvet container에 의해 호출된다.
    - 이전 필터의 정보를 받고 필터링 수행 후, 다음 필터 또는 리소스를 호출한다.
    - `ServletRequest` : client의 요청
    - `ServletResponse` : filter의 응답
    - `FilterChain chain` : 다음 필터 또는 리소스
  - `destroy()`
    - 메소드 내 모든 스레드가 종료되더나 설정한 제한 시간이 지난 후에 servlet container에 의해 한번 호출된다.
    - 호출되면 인스턴스는 소멸한다.

## Filter 구현

간단하게 encoding을 바꿔주는 Fileter를 구현해보자.

POST 방식에서 한글 처리를 위해 encoding 설정을 해야하는데 Filter를 사용하면 코드의 중복을 줄일 수 있다.

1. web.xml

   `<filter>` : filter 클래스를 등록

   `<filter-mapping>` : filter를 적용시킬 url를 mapping 한다.

   ```xml
   <filter>
   	<!-- filter 이름  -->
   	<filter-name>encodingFilter</filter-name>
   	<!-- filter class 이름 (패키지 까지 작성 ) -->
   	<filter-class>com.exam.hello.filter.EncodingFilter</filter-class>
   	<!-- 파라미터 설정 -->
     <init-param>
   		<param-name>encoding</param-name>
       <param-value>UTF-8</param-value>
     </init-param>
   </filter>
   <!-- filter-name : 적용할 필터, url-pattern : 적용될 요청 url -->
   <filter-mapping>
   	<filter-name>encodingFilter</filter-name>
   	<url-pattern>/*</url-pattern>
   </filter-mapping>
   ```

2. Filter 클래스 구현

   **EncodingFilter.java**

   - Filter 인터페이스 구현
   - `ServletResponse` , `ServletRequest` 를 받는다.

   ```java
   package com.exam.hello.filter;

   import java.io.IOException;

   import javax.servlet.Filter;
   import javax.servlet.FilterChain;
   import javax.servlet.FilterConfig;
   import javax.servlet.ServletException;
   import javax.servlet.ServletRequest;
   import javax.servlet.ServletResponse;

   public class EncodingFilter implements Filter{
   	private String encoding="";
   	@Override
   	public void init(FilterConfig filterConfig) throws ServletException {
   		// web.xml 에 등록된 encoding init-parameter를 가져올 수 있다.
   		encoding = filterConfig.getInitParameter("encoding");
   	}

   	@Override
   	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
   			throws IOException, ServletException {
   		// 1. filter 로직 수행
   		if(request.getCharacterEncoding() == null ) {
   			request.setCharacterEncoding(encoding);
   		}
   		// 2. 다음 filter 또는 리소스에 요청과 응답 넘겨주기
   		chain.doFilter(request, response);
   	}

   	@Override
   	public void destroy() {
   	}

   }
   ```

3. 출력 확인

   확인하기 위해 작성한 jsp 파일, 텍스트를 입력하면 아래에 출력된다.

   ```html
   <%@ page language="java" contentType="text/html; charset=UTF-8"
   pageEncoding="UTF-8"%> <%@ taglib prefix="c"
   uri="http://java.sun.com/jsp/jstl/core" %>
   <c:set var="root" value="${pageContext.request.contextPath}" />
   <!DOCTYPE html>
   <html>
     <head>
       <meta charset="UTF-8" />
       <title>Insert title here</title>
     </head>
     <body>
       <h3>EncodingFilter Test</h3>
       <form method="POST" action="${root }/msg">
         <input type="text" id="msg" name="msg" />
         <input type="submit" value="전송" />
       </form>

       ${returnMsg }
     </body>
   </html>
   ```

   - filter 미적용 시

   ![filter 적용](/assets/images/posts/web/2022-05-15-1.png)

   - filter 적용시

   ![filer 미적용](/assets/images/posts/web/2022-05-15-2.png)

## Spring에서 Filter 사용하기

- Spring에서도 동일하게 Filter를 적용할 수 있다.
- 앞에서 filer 클래스를 만들고 web.xml 에 클래스를 등록하고 url을 매핑한 것과 동일하게 web.xml에 <filter>를 작성하면 되는데, spring에서 제공하는 필터들이 있다.
  - `org.springframework.web.filter` [(공식문서)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/filter/package-summary.html)
    - CharacterEncodingFilter, CorsFilter, Logging 관련 필터들이 있다.
  - 그래서 web.xml 만 작성하면 끝 ~
- **web.xml**

  ```xml
  <!-- POST 방식의 한글 처리. -->
  <filter>
  	<filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
     <init-param>
  	  <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>

  <filter-mapping>
  	<filter-name>encodingFilter</filter-name>
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

# Spring & Interceptor

Filter는 Spring 과 무관하게 서블릿 컨테이너에 의해 관리되었다.

하지만 Interceptor는 Spring의 DispatcherServlet 이 컨트롤러를 호출하기 전, 후로 끼어든다.

때문에 스프링 context 영역 내부에서 **Controller에 관한 요청과 응답에 대한 처리**를 한다.

- 인터셉터는 한 요청에 대해 여러개를 적용시킬 수 있다.
- `servlet-context.xml` 에 정의
- 로그인 체크, 권한체크, 프로그램 실행 시간 계산 작업 로그 확인 등의 업무 처리

## Interceptor 구현

1. Interceptor 클래스 구현 → HandlerInterceptor 인터페이스를 상속한다.

   **HandlerInterceptor 인터페이스**

   - preHandle : 컨트롤러 들어가기 전 실행
   - postHandle : 컨트롤러 종료 후 실행
   - afterCompletion : postHandler 이후 실행

   ```java
   public interface HandlerInterceptor {

   	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
   			throws Exception {

   		return true;
   	}

   	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
   			@Nullable ModelAndView modelAndView) throws Exception {
   	}

   	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
   			@Nullable Exception ex) throws Exception {
   	}

   }
   ```

- 로그인 확인을 위해 SessionInterceptor 클래스 구현
  SessionInterceptor.java

  - `HttpServletRequest`, `HttpServletResponse` 를 사용할 수 있다.

  ```java
  package com.exam.ws.interceptor;

  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import javax.servlet.http.HttpSession;

  import org.springframework.stereotype.Component;
  import org.springframework.web.servlet.HandlerInterceptor;

  @Component
  public class SessionInterceptor implements HandlerInterceptor{
  	/**
  	 * 사용자의 요청을 처리하기 전에 session에 로그인을 한 유저가 있는지 확인
  	 * 로그인 정보가 있다면 그대로 진행하고, 없다면 index 페이지로 이동.
  	 */
  	@Override
  	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
  		HttpSession session = request.getSession();
  		// 세션에 로그인 정보가 있다면 그대로 진행한다.
  		if(session.getAttribute("loginUser")!=null) {
  			return true;
  		}
  		// 세션 정보가 없다면 index로 redirect 시킨다.
  		else {
  			response.sendRedirect(request.getContextPath()+"/index");
  			return false;
  		}
  	}
  }

  ```

1. sercvlet-context.xml 에서 url 매핑
   - 여러url 에 대해 동일한 interceptor 적용 가능
   - mapping path에 설정된 url로 부터 요청이 들어오면, 요청을 처리 할 때 interceptor 확인 후, 각 실행 시점에서 interceptor 실행.
   - 당연히 interceptor 클래스도 Bean 객체를 생성해야한다.
     ```xml
     <!-- interceptor 등록 -->
     <beans:bean id="sessionInterceptor" class="com.exam.ws.interceptor.SessionInterceptor "/>
     <!-- /regist 요청에 대해 sessionInterceptor를 매핑한다. -->
     <interceptors>
     	<interceptor>
     		<mapping path="/regist" />
     		<beans:ref bean="sessionInterceptor" />
     	</interceptor>
     </interceptors>
     ```

# SpringBoot & Interceptor

- Intercerptor 생성

```java
package com.exam.ws.interceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

@Component
public class SessionInterceptor implements HandlerInterceptor{

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		HttpSession session = request.getSession();
		// 세션에 로그인 정보가 있다면 그대로 진행한다.
		if(session.getAttribute("loginUser")!=null) {
			return true;
		}
		// 세션 정보가 없다면 index로 redirect 시킨다.
		else {
			response.sendRedirect(request.getContextPath()+"/index");
			return false;
		}
	}
}

```

- Interceptor 등록

  - 이제 xml 설정 파일이 아닌 `@Configuration`을 이용해 설정 클래스를 만든다.
  - `@Configuration` : 설정 파일임을 알림

  1. `WebMvcConfigurer` 인터페이스 상속

     - `WebMvcConfigurer` : MVC 설정을 커스텀할 수 있는 인터페이스

  2. `addInterceptors` 메소드 오버라이드
     - `addInterceptor` : interceptor 구현 클래스
     - `addPathPatterns` : url mapping
     - `excludePathPatterns` : 제외할 url

```java
import java.util.Arrays;
import java.util.List;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import com.exam.ws.interceptor.SessionInterceptor ;

@Configuration
@EnableAspectJAutoProxy
@MapperScan(basePackages = {"com.ssafy.**.mapper"})
public class WebMvcConfiguration implements WebMvcConfigurer {

	@Autowired
	private SessionInterceptor session; // 사용할 interceptor

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(comfirm)
						.addPathPatterns("/user/list")  // interceptor를 적용할 url
						.excludePathPatterns("/board"); // 해당 경로는 interceptor 적용 X
	}
}
```

# Filter 와 Interceptor

**Filter**

- web.xml 에서 관리
- DispathcherServlet으로 전달되기 전 (요청들어오고 제일 먼저 Filter 수행)

**Interceptor**

- servlet-context.xml 에서 관리
- DispatcherServlet이 Controller를 호출하기 전, 후

## +AOP

AOP, Filter, Interceptor 세가지 모두 요청을 처리하기 전, 후로 공통기능을 위한 처리가 가능한다.

Filter 와 Interceptor는 url로 구분하고 요청의 전 후로 HttpResponse, ServletResponse 등을 사용하고

AOP의 경우 어노테이션, 주소 등으로 실행 지점을 세밀하게 나눌 수 있고 JoinPoint를 사용한다는 점에서 큰 차이가 있다.

> 아직 세가지를 어떤 공통기능을 처리하기 위해 적절하게 사용해야하는지 체감이 되지 않는다.
> 앞으로 구현을 해나가며 공통기능을 구현할 때, 세가지 모두 고려하며 구현을 해야겠다는 생각이 든다.

아래는 [이 블로그](https://baek-kim-dev.site/61)에서 참고한 세가지의 사용 예시이다.

1. Filter
   - 전체적인 Requset에 대한 처리가 필요할 때,
   - 인증, 이미지 변환, 데이터 압축, 암호화 필터
   - 문자 인코딩 등
2. Interceptor
   - 세션 및 쿠키 체크
   - http 프로토콜 단위로 처리해야하는 업무
   - 로그인 세션 체크
3. AOP
   - 로깅, 트랜잭션, 에러 처리

> 참고

Filter, Interceptor, AOP

- [https://goddaehee.tistory.com/154](https://goddaehee.tistory.com/154)
- [https://baek-kim-dev.site/61](https://baek-kim-dev.site/61)

Filter

[https://live-for-myself.tistory.com/169](https://live-for-myself.tistory.com/169)

[https://livenow14.tistory.com/60](https://livenow14.tistory.com/60)

[https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=adamdoha&logNo=221665607853](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=adamdoha&logNo=221665607853)

Spring Boot Interceptor

[https://dejavuhyo.github.io/posts/spring-boot-interceptor/](https://dejavuhyo.github.io/posts/spring-boot-interceptor/)
