---
layout: post
title: "[JAVA] JAVA의 빌드 관리 도구, Maven과 Gradle"
subtitle: JAVA의 빌드 관리 도구, Maven과 Gradle
categories: JAVA
tags: [JAVA ]
---


> 스프링 강의를 들으면서 spring boot 프로젝트를 생성할 때, 
> 빌드 관리 도구로 maven와 gradle을 선택할 수 있었다.    
> 웹 프로젝트를 진행하기 위해서 둘 중 하나가 있어야하는 것 같은데,     
> 이 둘의 역할과 차이를 알아보자



## Maven 과 Gradle

Maven과 Gradle은 자바의 대표적인 빌드 관리 도구이다.     

### 빌드 관리 도구란?
필요한 라이브러리를 알아서 다운 받아주고 버전도 관리도 관리해주는 등     
프로젝트 정보 관리를 편리하게 해주는 도구 ! 

- 프로젝트에서 필요한 파일(xml, properties, jar)들을 자동으로 인식하여 빌드해주는 도구
- **외부 라이브러리를 참조하여 자동으로 다운로드 해주고 업데이트 관리도 해줌**
- 소스 코드를 컴파일, 테스트, 정적분석 등을 하여 실행가능한 앱으로 빌드해줌
- 프로젝트 정보 관리, 테스트 빌드, 배포 등의 작업을 진행해줌
- 자바의 대표적인 빌드 도구 : Ant, Maven, Gradle
- 태초에 Ant 라는 빌드 도구가 있었고, 이를 보완하기 위해 Maven이 나왔다.


### Maven

**Maven**

- Ant를 대체하기 위해 개발됨.
- 프로젝트의 외부 라이브러리를 쉽게 참조할 수 있게 **pom.xml 파일로 명시하여 관리.**
- 참조한 외부 라이브러리에 연관된 다른 라이브러리도 자동으로 관리됨.
- 라이프 사이클 도입
- pom.xml로 편하게 의존성(Dependency) 관리

> Ant는 라이프 사이클도 없구,,, 자동으로 라이브러리도 안 가져오구,,,
> 대규모 프로젝트에서는 복잡해진다고 하네유 

**pom.xml**

- POM : Project Object Model
- 프로젝트 정보 관리
- 프로젝트에서 사용하는 외부 라이브러리 관리
- 프로젝트의 빌드 관련 설정
- 프로젝트 정보 : 프로젝트의 이름, 개발자 목록, 라이센스 등
- 빌드 설정 : 소스, 리소스, 라이프 사이크별 실행한 등 빌드와 관련된 설정
- 빌드 환경, 사용자 환경 별로 달라질 수 있는 프로파일 정보
- POM 연관 정보 : 의존 프로젝트(모듈), 상위 프로젝트, 포함하고 있는 하위 모듈 등
- pom.xml 예시
    - `<properties>`
        - maven 내부에서 반복적으로 사용될 상수 값을 정의
        - `<java-version>1.8</java-version>`로 정의하면 `${java-version}` 로 사용 가능 하다. 값은 1.8
    - `<dependencies>`
        - 의존성 라이브러리 정보
        - `<dependency>` 하위에 `groupId`, `artifactId`, `version`정보가 필요함.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ssafy</groupId>
	<artifactId>guestbook</artifactId>
	<name>guestbook_file_interceptor</name>
	<packaging>war</packaging>
	<version>1.0.0-BUILD-SNAPSHOT</version>
	<properties>
		<java-version>1.8</java-version>
		<m2eclipse.wtp.contextRoot>guestbook</m2eclipse.wtp.contextRoot>
		<maven.compiler.source>${java-version}</maven.compiler.source>
		<maven.compiler.target>${java-version}</maven.compiler.target>
		
		<org.springframework-version>5.3.18</org.springframework-version>
		<org.aspectj-version>1.9.9.1</org.aspectj-version>
		<log4j-version>1.2.17</log4j-version>
		<org.slf4j-version>1.7.36</org.slf4j-version>
		
		<mysql-connector-java-version>8.0.28</mysql-connector-java-version>
		
		<servlet-version>4.0.1</servlet-version>
		<jsp-version>2.3.3</jsp-version>
		<jstl-version>1.2</jstl-version>
		<commons-fileupload-version>1.4</commons-fileupload-version>
		
		<junit-version>4.13.1</junit-version>
		
	</properties>
	<dependencies>
		<!-- Spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${org.springframework-version}</version>
			<exclusions>
				<!-- Exclude Commons Logging in favor of SLF4j -->
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				 </exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
		<dependency>
		    <groupId>mysql</groupId>
		    <artifactId>mysql-connector-java</artifactId>
		    <version>${mysql-connector-java-version}</version>
		</dependency>
				
		<!-- Servlet -->
		<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>${servlet-version}</version>
			<scope>provided</scope>
		</dependency>
	</dependencies>
</project>
```

**Maven 라이브러리 사이트** -> [https://mvnrepository.com/](https://mvnrepository.com/)    
- 필요한 의존성 라이브러리 설정을 위한 xml 코드를 가져올 수 있음.

### Gradle

**Gradle?**

- Groovy 스크립트를 활용한 빌드 관리 도구
- 안드로이드 프로젝트의 표준으로 채택
- 멀티 프로젝트의 빌드에 최적화하여 설계됨
- **Maven에 비해 더 빠른 처리 속도, 더 간결한 구성**
- **Maven과 동일하게 라이브러리 관리, 의존성 관리 설정 가능 → `build.gradle`**

**Gradle 용어**

- repositories : 라이브러리가 저장된 위치 등 설정
- mavenCentral : 기본 Maven Repository
- dependencies : 라이브러리 의존성 설정
- `build.gradle` 예시

```
plugins {
	id 'org.springframework.boot' version '2.6.4'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.community'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-web-services'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}
```

### Maven vs. Gradle

- Gradle에 비해 Maven이 점유율이 높음(하지만 Gradle이 상승세)
- Gradle이 성능이 더 좋고, 대규모 프로젝트에 적합하다.
- 설정 관리 파일
    - Maven : pom.xml
    - Gradle : build.gradle
- Grable은 설치없이 사용할 수 있음

> 참고
> 
[https://www.youtube.com/watch?v=3Jp9kGDb01g](https://www.youtube.com/watch?v=3Jp9kGDb01g)    
[https://hyojun123.github.io/2019/04/18/gradleAndMaven/](https://hyojun123.github.io/2019/04/18/gradleAndMaven/)