---
layout: single
title: "Spring Boot Project Setting 2"
toc: true
toc_sticky: true
categories: Spring Boot
tag: [Spring Boot, framework, Java, BE]
author_profile: false
sidebar:
    nav: "docs"
---
# Spring Boot 프로젝트 설정 가이드 2 - Backend Structure


## Controller Layer

```java
package com.example.sisi.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

import com.example.sisi.model.Student;
import com.example.sisi.service.StudentService;

/**
 * Controller 클래스: 학생 데이터에 대한 사용자 요청을 처리하고, 응답을 뷰로 전달.
 */
@Controller
public class StudentController {

    // Service 계층을 사용하여 비즈니스 로직 처리
    @Autowired
    private StudentService studentService;

    /**
     * 홈 페이지 처리
     * @param model - 뷰로 전달할 데이터를 담는 객체
     * @return index.html 뷰 이름
     * 
     * 사용자가 "/"로 접근하면 모든 학생 데이터를 가져와 홈 페이지에 전달
     */
    @GetMapping("/")
    public String viewHomePage(Model model) {
        model.addAttribute("allStudents", studentService.getAllStudents()); // 모든 학생 데이터 추가
        return "index"; // index.html 템플릿으로 반환
    }

    /**
     * 새로운 학생 등록 폼 표시
     * @param model - 뷰로 전달할 데이터를 담는 객체
     * @return new_student.html 뷰 이름
     * 
     * 사용자가 "/showNewStudentForm"으로 접근하면 새로운 학생 데이터를 입력할 폼을 표시
     */
    @GetMapping("/showNewStudentForm")
    public String showNewStudentForm(Model model) {
        Student student = new Student(); // 빈 학생 객체 생성
        model.addAttribute("student", student); // 뷰에 전달
        return "new_student"; // new_student.html 템플릿으로 반환
    }

    /**
     * 학생 데이터 저장
     * @param student - 사용자가 입력한 학생 데이터를 담은 객체
     * @return 홈 페이지로 리다이렉트
     * 
     * 사용자가 새로운 학생 데이터를 저장 요청하면 서비스 계층에서 처리하고,
     * 저장 후 홈 페이지로 리다이렉트
     */
    @PostMapping("/saveStudent")
    public String saveStudent(@ModelAttribute("student") Student student) {
        try {
            studentService.saveStudent(student); // 학생 데이터 저장
        } catch (Exception e) {
            e.printStackTrace(); // 예외 처리
        }
        return "redirect:/"; // 홈 페이지로 리다이렉트
    }

    /**
     * 학생 데이터 수정 폼 표시
     * @param id - 수정하려는 학생의 ID
     * @param model - 뷰로 전달할 데이터를 담는 객체
     * @return update_student.html 뷰 이름
     * 
     * 사용자가 "/showFormForUpdate/{id}"로 접근하면 해당 ID의 학생 데이터를 수정할 폼을 표시
     */
    @GetMapping("/showFormForUpdate/{id}")
    public String showFormForUpdate(@PathVariable(value = "id") long id, Model model) {
        Student student = studentService.getStudentById(id); // ID로 학생 데이터 가져오기
        model.addAttribute("student", student); // 뷰에 전달
        return "update_student"; // update_student.html 템플릿으로 반환
    }

    /**
     * 학생 데이터 삭제
     * @param id - 삭제하려는 학생의 ID
     * @return 홈 페이지로 리다이렉트
     * 
     * 사용자가 "/deleteStudent/{id}"로 접근하면 해당 ID의 학생 데이터를 삭제하고,
     * 홈 페이지로 리다이렉트
     */
    @GetMapping("/deleteStudent/{id}")
    public String deleteStudent(@PathVariable(value = "id") long id) {
        studentService.deleteStudent(id); // 학생 데이터 삭제
        return "redirect:/"; // 홈 페이지로 리다이렉트
    }
}
```

## Model Layer

```java
package com.example.sisi.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import jakarta.persistence.Column;
import lombok.Data;

/**
 * Student 엔티티 클래스
 * 데이터베이스의 "student" 테이블과 매핑되는 클래스
 * 각 필드는 테이블의 열(column)에 매핑
 */
@Entity // 이 클래스가 JPA 엔티티임을 명시
@Table(name = "student") // 이 엔티티가 "student" 테이블과 매핑됨을 명시
@Data // Lombok을 사용하여 Getter, Setter, toString 등을 자동 생성
public class Student {

    /**
     * ID 필드
     * - Primary Key로 설정
     * - 자동 생성됨 (IDENTITY 전략 사용)
     * - null 값 불허 및 수정 불가능 (updatable = false, nullable = false)
     */
    @Id // Primary Key 지정
    @GeneratedValue(strategy = GenerationType.IDENTITY) // IDENTITY 전략으로 자동 생성
    @Column(name = "id", updatable = false, nullable = false) // 컬럼 설정
    private Long id;

    /**
     * 이름 필드
     * - "name" 열과 매핑
     * - null 값 불허
     */
    @Column(name = "name", nullable = false) // 컬럼 설정
    private String name;

    /**
     * 전공 필드
     * - "major" 열과 매핑
     * - null 값 불허
     */
    @Column(name = "major", nullable = false) // 컬럼 설정
    private String major;

    /**
     * 전화번호 필드
     * - "phone" 열과 매핑
     * - null 값 불허
     */
    @Column(name = "phone", nullable = false) // 컬럼 설정
    private String phone;

    /**
     * 주소 필드
     * - "address" 열과 매핑
     * - null 값 불허
     */
    @Column(name = "address", nullable = false) // 컬럼 설정
    private String address;
}

```

**주석 설명**
1. **클래스 어노테이션**
   - `@Entity`: JPA에서 이 클래스를 엔티티로 사용.
   - `@Table(name = "student")`: 매핑될 데이터베이스 테이블 이름을 지정.
2. **필드 어노테이션**
   - `@Id`: Primary Key 필드임을 지정.
   - `@GeneratedValue(strategy = GenerationType.IDENTITY)`: ID 값을 데이터베이스에서 자동 생성하도록 설정.
   - `@Column`: 데이터베이스 열에 매핑되며, 열의 세부 설정을 지정.
3. **Lombok 사용**
   - `@Data`: Lombok이 Getter, Setter, equals, hashCode, toString 등을 자동으로 생성.
4. **각 필드 설명**
   - `id`: Primary Key로 사용되는 필드로, 자동 증가 값을 가짐.
   - `name`, `major`, `phone`, `address`: 학생의 주요 정보를 저장하는 필드로, null 값이 허용되지 않음.


## Repository Layer
```java
package com.example.sisi.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import com.example.sisi.model.Student;

/**
 * StudentRepository 인터페이스
 * - JPA를 활용하여 Student 엔티티와 데이터베이스 간의 상호작용을 처리
 */
public interface StudentRepository extends JpaRepository<Student, Long> {
}

```
**주석 설명**
1. **클래스 개요**
   - `StudentRepository`는 데이터베이스에 접근하기 위한 **DAO(Data Access Object)** 역할을 함.
   - Spring Data JPA가 제공하는 `JpaRepository`를 상속하여 기본적인 CRUD 및 페이징, 정렬 기능을 자동으로 구현.

2. **`JpaRepository` 상속**
     ```
     JpaRepository<T, ID>
     ```
     - `T`: 엔티티 클래스 타입 (`Student`).
     - `ID`: 엔티티의 Primary Key 타입 (`Long`).

3. **자동으로 제공되는 메서드**
   - Spring Data JPA가 기본적으로 제공하는 주요 메서드:
     - `findAll()`: 모든 레코드 조회.
     - `findById(ID id)`: 특정 ID의 레코드 조회.
     - `save(T entity)`: 엔티티 저장 또는 업데이트.
     - `deleteById(ID id)`: 특정 ID의 레코드 삭제.
     - `count()`: 전체 레코드 개수 조회.

4. **Custom Query 작성 가능**
     ```
     JpaRepository
     ```
     는 사용자 정의 메서드를 선언할 수 있다.
     예를 들어, 특정 필드로 조회하는 메서드를 선언:
     ```java
      List<Student> findByMajor(String major);
     ```
   - 위 메서드는 JPA가 자동으로 구현하며, `major` 필드값에 따라 데이터를 검색함.

5. **추가 설정이 불필요**
   - Spring Boot가 자동으로 `StudentRepository`를 빈으로 등록하여 `@Autowired`를 통해 다른 클래스에서 사용할 수 있다.


## Service Layer
```java
package com.example.sisi.service;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import com.example.sisi.model.Student;
import com.example.sisi.repository.StudentRepository;

/**
 * StudentService 클래스
 * - 비즈니스 로직을 처리하는 Service 계층
 * - Controller와 Repository 사이에서 데이터를 관리하고 처리
 */
@Service // Spring이 관리하는 서비스 빈으로 등록
public class StudentService {

    // Repository를 사용하여 데이터베이스 작업 처리
    @Autowired
    private StudentRepository studentRepository;

    /**
     * 모든 학생 데이터를 조회
     * @return 모든 학생 리스트
     * 
     * Repository의 `findAll()` 메서드를 호출하여 데이터베이스에 저장된 모든 학생 데이터를 반환
     */
    public List<Student> getAllStudents() {
        return studentRepository.findAll();
    }

    /**
     * 학생 데이터를 저장 또는 업데이트
     * @param student 저장할 학생 객체
     * 
     * - Repository의 `save()` 메서드를 호출하여 학생 데이터를 저장
     * - `@Transactional` 어노테이션으로 트랜잭션을 보장
     */
    @Transactional
    public void saveStudent(Student student) {
        studentRepository.save(student);
    }

    /**
     * 특정 ID로 학생 데이터 조회
     * @param id 조회할 학생의 ID
     * @return 조회된 학생 객체 (없으면 null)
     * 
     * - Repository의 `findById()` 메서드를 호출하여 특정 ID의 데이터를 반환
     * - 데이터가 없을 경우 `orElse(null)`을 통해 null 반환
     */
    public Student getStudentById(Long id) {
        return studentRepository.findById(id).orElse(null);
    }

    /**
     * 특정 ID의 학생 데이터를 삭제
     * @param id 삭제할 학생의 ID
     * 
     * - Repository의 `deleteById()` 메서드를 호출하여 데이터베이스에서 해당 ID의 학생 데이터를 삭제
     * - `@Transactional` 어노테이션으로 트랜잭션을 보장
     */
    @Transactional
    public void deleteStudent(Long id) {
        studentRepository.deleteById(id);
    }
}
```

**주석 설명**
1. **클래스 어노테이션**
   - `@Service`: 이 클래스가 Spring의 서비스 계층으로 사용됨을 나타냄.

2. **주요 메서드 설명**
    ```
     getAllStudents()
     ```
     - Repository의 `findAll()` 메서드를 호출하여 데이터베이스에 저장된 모든 학생 데이터를 리스트 형태로 반환.

    ```
     saveStudent(Student student)
     ```
     - Repository의 `save()` 메서드를 호출하여 학생 데이터를 저장하거나 업데이트.
     - `@Transactional`은 데이터의 일관성과 무결성을 보장.

    ```
     getStudentById(Long id)
     ```
     - `findById()` 메서드를 호출하여 특정 ID의 학생 데이터를 검색합니다. 데이터가 없으면 `null`을 반환.

    ```
     deleteStudent(Long id)
     ```
     - `deleteById()` 메서드를 호출하여 데이터베이스에서 특정 ID의 학생 데이터를 삭제.
     - 삭제 작업이 중간에 실패할 경우 데이터 무결성을 유지하기 위해 `@Transactional` 사용.

3. **Repository 사용**
   - `StudentRepository`를 통해 JPA의 기본 CRUD 작업을 수행.
   - `@Autowired`를 사용하여 `StudentRepository`를 자동으로 주입받음.

4. **트랜잭션 관리**
   - `@Transactional`: 데이터베이스 작업이 중단되거나 실패할 경우 자동으로 롤백되도록 보장합니다.
   - 데이터의 일관성을 위해 중요한 작업(저장 또는 삭제)에 추가됩니다.


## Main C
```java
package com.example.sisi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * SisiApplication 클래스
 * - Spring Boot 애플리케이션의 시작점
 * - 애플리케이션 설정 및 실행을 담당하는 클래스
 */
@SpringBootApplication // Spring Boot 애플리케이션의 기본 설정을 자동으로 구성하는 어노테이션
@EnableTransactionManagement // 트랜잭션 관리 활성화
public class SisiApplication {

    /**
     * 애플리케이션 실행 메서드
     * @param args 애플리케이션 실행 시 전달되는 인자
     * 
     * Spring Boot 애플리케이션을 시작
     */
    public static void main(String[] args) {
        // SpringApplication.run() 메서드를 호출하여 애플리케이션을 실행
        SpringApplication.run(SisiApplication.class, args);
    }
}
```

**주석 설명**<br/>

- `@SpringBootApplication` : Spring Boot 애플리케이션의 시작점을 정의하고,<br/>
    내부적으로 다음과 같은 설정들을 자동으로 활성화함:
    - `@Configuration`: 이 클래스가 Spring의 설정 클래스임을 의미.
    - `@EnableAutoConfiguration`: Spring Boot가 애플리케이션의 환경을 자동으로 설정하도록 활성화.
    - `@ComponentScan`: `com.example.sisi` 패키지 및 그 하위 패키지 내에서 <br/>
        컴포넌트(예: @Controller, @Service 등)를 자동으로 스캔하고 등록.

- **`@EnableTransactionManagement` Annotation**
    - 이 어노테이션은 Spring에서 트랜잭션 관리를 활성화.
    - 트랜잭션 처리가 필요한 메서드에 `@Transactional`을 사용하여 <br/>
        데이터베이스 작업의 일관성과 원자성을 보장할 수 있다.

- **`main()` Method**
    - `SpringApplication.run(SisiApplication.class, args)`: Spring Boot 애플리케이션을 실행함.<br/>
        이 메서드가 호출되면 애플리케이션이 초기화되고, 내장 서버(예: Tomcat)가 시작됨.
    - 애플리케이션이 실행되면, 자동으로 스프링 컨텍스트가 설정되고, 모든 설정된 빈들이 초기화됨.
