---
layout: single
title: "Spring Boot Project Setting 3"
toc: true
toc_sticky: true
categories: Spring Boot
tag: [Spring Boot, framework, Java, FE]
author_profile: false
sidebar:
    nav: "docs"
---
# Spring Boot 프로젝트 설정 가이드 3 - Frontend Structure


## Index
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Student Management</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid black;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
        .center {
            text-align: center;
        }
    </style>
</head>
<body>
<h1>Student List</h1>
<table>
    <thead>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Major</th>
        <th>Phone</th>
        <th>Address</th>
        <th>Actions</th>
    </tr>
    </thead>
    <tbody>
    <!-- th:each="student : ${allStudents}" -->
    <!-- 'allStudents'는 Spring Controller에서 모델에 담겨서 전달된 학생 리스트 
         각 'student' 객체가 반복문을 돌며 출력 -->
    <tr th:each="student : ${allStudents}">
        <!-- 'student.id'는 각 학생의 ID 값을 표시 -->
        <td th:text="${student.id}"></td>
        <!-- 'student.name'은 각 학생의 이름을 표시 -->
        <td th:text="${student.name}"></td>
        <!-- 'student.major'는 학생의 전공을 표시 -->
        <td th:text="${student.major}"></td>
        <!-- 'student.phone'은 학생의 전화번호를 표시 -->
        <td th:text="${student.phone}"></td>
        <!-- 'student.address'는 학생의 주소를 표시 -->
        <td>
            <!-- 'Edit' 링크는 해당 학생의 ID를 이용해 수정 폼으로 이동
                 th:href="@{/showFormForUpdate/{id}(id=${student.id})}"을 사용하여 URL에 학생의 ID를 포함 -->
            <a th:href="@{/showFormForUpdate/{id}(id=${student.id})}">Edit</a>
            <!-- 'Delete' 링크는 해당 학생을 삭제하는 링크
                 th:href="@{/deleteStudent/{id}(id=${student.id})}"를 사용하여 URL에 학생의 ID를 포함시켜 삭제 요청을 보냄 -->
            <a th:href="@{/deleteStudent/{id}(id=${student.id})}">Delete</a>
        </td>
    </tr>
    </tbody>
</table>
<!-- 'Add New Student' 링크는 새로운 학생 등록 폼으로 이동하는 링크 -->
<div>
    <a href="/showNewStudentForm">Add New Student</a>
</div>
</body>
</html>
```

**주석 설명**
- **`th:each="student : ${allStudents}"`**:<br/> 
  - Thymeleaf에서 제공하는 반복 처리 기능.<br/> 
  `allStudents`는 Spring Controller에서 전달한 학생 목록 리스트.<br/>  
  이 리스트의 각 요소(`student`)를 반복하면서 테이블 행을 생성함.<br/> 

- **`th:text="${student.id}"`**:<br/> 
  - `student.id`는 반복되는 학생 객체의 ID를 출력<br/> 
   이 값은 데이터베이스에서 가져온 학생의 고유 ID이다.<br/> 

- **`th:text="${student.name}"`, `th:text="${student.major}"`, <br/> 
    `th:text="${student.phone}"`, `th:text="${student.address}"`**:<br/> 
  - 각각 `student` 객체의 이름, 전공, 전화번호, 주소를 출력하는 코드<br/> 
   데이터베이스에서 가져온 각 학생의 정보를 화면에 표시함.<br/> 

- **`th:href="@{/showFormForUpdate/{id}(id=${student.id})}"`**:<br/> 
  - 이 부분은 학생의 수정 페이지로 이동할 수 있는 링크를 생성함.<br/> 
  `${student.id}`를 URL에 동적으로 삽입하여, 해당 학생의 ID를 기반으로 수정 페이지로 이동.<br/> 
   Spring Controller에서 이 URL을 처리하여 해당 학생의 정보를 수정할 수 있는 폼을 띄운다.<br/> 

- **`th:href="@{/deleteStudent/{id}(id=${student.id})}"`**:<br/> 
  - 이 링크는 학생을 삭제하는 요청을 보냅니다. URL에 학생의 ID를 동적으로 삽입하여 삭제할 학생을 지정함.<br/> 
   이 링크를 클릭하면 해당 학생을 삭제하는 로직이 실행됨.<br/> 

- **`<a href="/showNewStudentForm">Add New Student</a>`**:
  - 이 링크는 새 학생을 등록할 수 있는 폼 페이지로 이동함.<br/> 
   `/showNewStudentForm` URL로 요청을 보내며, Spring Controller에서 이를 처리하여 새로운 학생을 추가할 수 있는 폼을 제공함.



## Add Form 

```html
   <!-- Add Student HTML Form -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Add Student</title>
</head>
<body>
<h1>Add Student</h1>
<!-- 'th:action'은 폼 제출 시 호출할 URL을 지정. 이 URL은 '/saveStudent'로, 학생 데이터를 저장하는 Spring Controller 메소드에 매핑 -->
<form th:action="@{/saveStudent}" th:object="${student}" method="post">
    <!-- 'th:field'는 'student' 객체의 속성에 바인딩된 필드를 렌더링
         입력된 값은 'student' 객체의 해당 필드에 자동으로 바인딩 -->
    <label>Name:</label><input type="text" th:field="*{name}" /><br />
    <label>Major:</label><input type="text" th:field="*{major}" /><br />
    <label>Phone:</label><input type="text" th:field="*{phone}" /><br />
    <label>Address:</label><input type="text" th:field="*{address}" /><br />
    <button type="submit">Save</button> <!-- 폼 데이터를 전송하는 버튼 -->
</form>
</body>
</html>
```

**Add Student Form**
- **`<form th:action="@{/saveStudent}" th:object="${student}" method="post">`**:
  - **`th:action`**: 폼이 제출될 URL을 설정함. 여기서는 `/saveStudent`로 설정되어 있으며, <br/>
  학생 데이터를 저장하는 POST 요청을 처리하는 Controller 메소드로 연결됨.
  - **`th:object="${student}"`**: 폼이 제출될 때 바인딩되는 객체를 지정함.<br/>
   이 객체는 `student`로 설정되어 있으며, 사용자 입력 값이 이 객체의 속성에 자동으로 매핑됨.
- **`<input type="text" th:field="\*{name}" />`**:
  - **`th:field="\*{name}"`**: 폼 필드를 `student` 객체의 `name` 속성과 연결함.<br/>
   사용자 입력이 `name` 속성에 저장됨.
- **`<button type="submit">Save</button>`**:
  - 폼을 제출하는 버튼. 클릭 시 입력된 데이터가 서버로 전송됨.<br/>


## Update Form

```html
<!-- Update Student HTML Form -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Update Student</title>
</head>
<body>
<h1>Update Student</h1>
<!-- 'th:action'은 폼 제출 시 호출할 URL을 지정 'saveStudent'는 POST 요청을 처리하는 메소드로, 수정된 학생 정보를 저장. -->
<form th:action="@{/saveStudent}" th:object="${student}" method="post">
    <!-- 'input type="hidden"'은 학생의 ID를 숨겨서 폼 데이터와 함께 전송. 이 ID는 수정할 학생을 식별하는 데 사용 -->
    <input type="hidden" th:field="*{id}" />
    <!-- 'th:field'는 학생 객체의 속성에 바인딩된 필드를 렌더링. 수정할 값을 입력받는 필드 -->
    <label>Name:</label><input type="text" th:field="*{name}" /><br />
    <label>Major:</label><input type="text" th:field="*{major}" /><br />
    <label>Phone:</label><input type="text" th:field="*{phone}" /><br />
    <label>Address:</label><input type="text" th:field="*{address}" /><br />
    <button type="submit">Update</button> <!-- 수정된 정보를 제출하는 버튼 -->
</form>
</body>
</html>
```

**Update Student Form**

- `<form th:action="@{/saveStudent}" th:object="${student}" method="post">`
  - 이 폼도 `/saveStudent`로 데이터를 전송함. 수정된 학생 정보를 저장하는 POST 요청을 처리함.

- `<input type="hidden" th:field="*{id}" />`
  - **`<input type="hidden">`**: 학생의 고유 ID를 숨겨서 폼 데이터와 함께 서버로 전송. <br/>
  이 ID는 수정할 학생을 식별하는 데 사용됨.

- `<button type="submit">Update</button>`
  - 수정된 학생 정보를 제출하는 버튼.