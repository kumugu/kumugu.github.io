---
layout: single
title: "Spring Boot Project Setting 1"
toc: true
toc_sticky: true
categories: Spring Boot
tag: [Spring Boot, framework, Java]
author_profile: false
sidebar:
    nav: "docs"
---

# Spring Boot 프로젝트 설정 가이드 1

## 1. 개발 환경 설정
1) 프로젝트 환경
- **사용 언어** : Java 17
- **IDE** : IntelliJ IDEA
- **SQL IDE** : HeidiSQL
- **데이터베이스** : SQLite 3.47.0.0

2) Spring Initializr 설정
- **Build Tool** : Maven
- **Java 버전** : 17
- **Spring Boot 버전** : 3.3.5
- **Dependencies** :
  - **Spring Web** : 웹 개발을 위한 기본 의존성
  - **Spring Data JPA** : 데이터 접근 계층 개발을 위한 JPA 지원
  - **Lombok** : 보일러플레이트 코드를 줄이기 위한 도구
  - **Spring Boot DevTools** : 개발 중 핫 리로드 기능
  - **Thymeleaf** : 템플릿 엔진


## 2. 데이터베이스 설정
**2.1 SQLite 설정**<br/>
- Maven 의존성 추가 (pom.xml)

```xml
<!-- SQLite JDBC 드라이버 -->
<dependency>
    <groupId>org.xerial</groupId>
    <artifactId>sqlite-jdbc</artifactId>
    <version>3.47.0.0</version>
</dependency>

<!-- Hibernate SQLite Dialect -->
<dependency>
    <groupId>org.hibernate.orm</groupId>
    <artifactId>hibernate-community-dialects</artifactId>
</dependency>
```

- Maven 탭에서 소스 생성 및 프로젝트 업데이트<br/>
    - `pom.xml`에 의존성 추가 후 업데이트 등을 해줘야한다.
    - IntelliJ IDEA에서 `Maven` 탭 열기 (우측 도구 창).
    - `Reload Project` 버튼 클릭하여 의존성을 업데이트.
    - Maven 생성을 위한 명령어 실행:
        - **`clean`**: 이전 빌드 제거.
        - **`install`**: 새 의존성 적용 후 소스 생성.<br/>

- 소스 폴더 구조 업데이트<br/>
 - Maven 프로젝트 구조가 업데이트되지 않을 경우:
 - `File > Project Structure > Modules`로 이동.
 - `src/main/java`와 `src/main/resources`를 각각 **Sources**와 **Resources**로 설정.


**2.2 테이블 생성 관리**
- 1: JPA Entity 설정
    - Spring Boot가 `id` 필드를 제대로 관리하도록 Hibernate 설정을 변경.
    - `@Table`과 `@Column`을 사용하여 테이블 이름과 각 컬럼 이름을 지정.

- 2: Schema.sql 사용
    1) `src/main/resources/schema.sql` 파일 생성
    2) SQL 테이블 생성 구문 작성

    ```sql
    DROP TABLE IF EXISTS student;

    CREATE TABLE student (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        major TEXT NOT NULL,
        phone TEXT NOT NULL,
        address TEXT NOT NULL
    );
    ```
    3) application.properties 설정 추가:

    ```properties
    spring.datasource.schema=classpath:schema.sql
    spring.datasource.initialize=true
    ```

**2.3 HeidiSQL 연결**
- 프로젝트 실행 후 생성되는 `[프로젝트명].db` 파일을 HeidiSQL에서 열어서 접속


## 3. 프로젝트 구조

**3.1 디렉토리 구조**
```
프로젝트명/
    ├── src/
    │   ├── main/
    │   │   ├── java/
    │   │   │   └── com/example/project/
    │   │   │       ├── controller/
    │   │   │       ├── model/
    │   │   │       ├── repository/
    │   │   │       └── service/
    │   │   └── resources/
    │   │       ├── static/
    │   │       ├── templates/
    │   │       └── application.properties
    └── pom.xml
```

**3.2 백엔드 구조**<br/>

**Controller Layer**
- 역할 : 클라이언트 요청을 처리하고,필요한 데이터를 서비스 계층에서 가져와 응답 반환.<br/>
- 주요 클래스 : API 엔드포인트 클래스.<br/>
- 관계 : service 계층 호출.<br/>
  - 웹 요청 처리
  - URL 매핑 및 라우팅
  - Service 계층과 통신
  - 응답 데이터 반환

**Model Layer**
- 역할 : 데이터베이스 테이블과 매핑되는 엔티티 클래스.<br/>
- 주요 클래스 : JPA 엔티티 (@Entity, @Table 사용).<br/>
- 관계 : repository와 상호작용.<br/>
  - 데이터 구조 정의
  - JPA Entity 클래스
  - DTO(Data Transfer Object) 클래스

**Repository Layer**
- 역할 : 데이터베이스 접근 로직을 담당.<br/>
- 주요 클래스 : JPA 인터페이스 (JpaRepository 상속).<br/>
- 관계 : service 계층과 데이터 상호작용.<br/>
  - 데이터베이스 접근 인터페이스
  - JPA Repository 확장
  - 커스텀 쿼리 메소드 정의

**Service Layer**
- 역할 : 비즈니스 로직을 처리.<br/>
- 주요 클래스 : 서비스 구현 클래스.<br/>
- 관계 : repository 계층 호출 및 데이터 가공.<br/>
  - 비즈니스 로직 구현
  - Controller와 Repository 사이의 중간 계층
  - 트랜잭션 관리
<br/><br/>

**3.3 프론트엔드 구조**<br/>

- **1) 기본 파일**<br/>
    - static 폴더: 정적 리소스(CSS, JavaScript, 이미지 등)를 저장.<br/>
    예: style.css, script.js.<br/>
    - templates 폴더: Thymeleaf 템플릿 파일(HTML).<br/>
    Thymeleaf는 서버 렌더링을 사용하기 때문에 정적 폴더에 HTML을 저장하지 않아도 됨.<br/>
    예: index.html, layout.html.<br/>

- **2) Thymeleaf 사용 시 특징**<br/>
    - HTML 파일 내에서 동적으로 데이터를 삽입하려면 templates 폴더에 HTML 파일을 배치.<br/>
    - Thymeleaf 템플릿 엔진 문법 사용:<br/>

    ```html
    <p th:text="${student.name}">이름</p>
    ```


## 4. 개발 프로세스
- **1) 초기 설정**
  - Spring Initializr에서 프로젝트 생성.
  - Maven 의존성 추가 및 SQLite 설정.
  - Hibernate와 데이터베이스 스키마 설정.

- **2) 백엔드 개발**
  - Model: 데이터베이스 테이블을 정의하는 엔티티(Entity) 생성.
  - Repository: 데이터 접근 계층 구현.
  - Service: 비즈니스 로직 구현.
  - Controller: API 엔드포인트 생성.

- **3) 프론트엔드 개발**
  - templates 폴더에 Thymeleaf 기반 HTML 파일 작성.
  - static 폴더에 필요한 CSS 및 JavaScript 파일 작성.

- **4) 통합 테스트**
  - 각 계층을 통합하여 기능 테스트 진행.
  - 데이터 삽입, 수정, 삭제가 제대로 동작하는지 확인.


## 5. 주의사항
- SQLite 사용 시 테이블 생성은 schema.sql 사용 권장
- Entity 설계 시 ID 필드 타입과 생성 전략 주의
- Thymeleaf 템플릿 경로는 templates 디렉토리 기준으로 설정
