---
title: [점프 투 스프링부트] 2장 스프링 부트의 기본 기능 익히기(하)
date: 2025-01-05 1:35:23 +0900
updated: 2025-01-05 1:35:23 +0900
category: SpringBoot
tags: 스프링부트, 독학
permalink: /springboot/chapter2_2/
---
## 전체코드 
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/ab42bae)
## 2.1 스프링부트 프로젝의 구조 이해하기 
- 스프링 부트의 기본 구조는 다음과 같다 

```markdown
project-root/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── project/
│   │   │               ├── domain/
│   │   │               │   ├── user/
│   │   │               │   │   ├── controller/
│   │   │               │   │   ├── service/
│   │   │               │   │   ├── repository/
│   │   │               │   │   ├── entity/
│   │   │               │   │   └── dto/
│   │   │               │   └── article/
│   │   │               │       ├── controller/
│   │   │               │       ├── service/
│   │   │               │       ├── repository/
│   │   │               │       ├── entity/
│   │   │               │       └── dto/
│   │   │               ├── global/
│   │   │               │   ├── config/
│   │   │               │   ├── exception/
│   │   │               │   └── util/
│   │   │               └── Application.java
│   │   └── resources/
│   │       ├── static/
│   │       ├── templates/
│   │       └── application.yml
│   └── test/
│       └── java/
├── build.gradle
└── settings.gradle
```
### src/main/java : 자바 코드를 저장하는 디렉토리
- src/main/java/com/example/project : 패키지명
- Application.java : 스프링 부트 프로젝트의 메인 클래스
  > @SpringBootApplication 어노테이션을 사용하여 스프링 부트 프로젝트임을 알린다.<br>
    main 메소드를 실행하여 스프링 부트 프로젝트를 실행한다.
### src/main/resources : 자바 코드를 제외한 리소스 파일을 저장하는 디렉토리
- templates : html 파일을 저장하는 디렉토리
- static : css, js, image 파일을 저장하는 디렉토리
- application.yml : 스프링 부트 프로젝트의 설정 파일

### src/test/java : 테스트 코드를 저장하는 디렉토리

### build.gradle : 프로젝트의 의존성을 관리하는 파일
- 스프링 부트 프로젝트는 의존성 관리를 위해 gradle이나 maven을 사용한다.

## 2.2 스프링부트 프로젝트 만들기
### URL매핑과 컨트롤러
- URL매핑 : URL과 컨트롤러의 메서드를 일대일로 연결하는 것으로 GET, POST, PUT, DELETE 등의 HTTP 메서드를 사용하여 요청을 처리한다.
- 클라이언트에서 URL을 요청하면 서버는 URL에 맞는 컨트롤러의 Mapping과 연결한다. 매핑정보가 없을 때 404 에러가 발생한다.
  <img alt="" src="https://wikidocs.net/images/page/225222/image56_2.png">
- 컨틀롤러는 URL매핑을 통해 요청을 처리하고 응답을 보낸다.

### 컨트롤러 구현
- 컨트롤러는 @Controller 어노테이션을 사용하여 선언한다.
- @GetMapping 어노테이션을 사용하여 GET 요청을 처리한다.
- @ResponseBody 어노테이션을 사용하면 응답에 return 값을 직접 넣어줄 수 있다.
  ```java
  package com.ll.jump.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.GetMapping;
  
  @Controller
  public class MainController {
    @GetMapping("/sbb")
    @ResponseBody 
    public String index() {
      return "안녕하세요 sbb에 오신 것을 환영합니다.";
    }
  }
  ```
  <img alt="" src="https://wikidocs.net/images/page/225222/image60_2.png">

## 2.3 JPA로 데이터베이스 사용하기 
### ORM(Object-Relational Mapping)
- ORM은 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑해주는 것이다.
- SQL의 쿼리와 ORM 코드의 비교
- 다음 테이블을 작성한다고 가정하자
  
  | id  | subject  | content      |
  |-----|----------|--------------|
  | 1   | 안녕하세요    | 가입 인사드립니다 ^^ |
  | 2   | 질문 있습니다	 | ORM이 궁금합니다   |
  | ... | ....     | ...          |

- SQL 쿼리
  ```sql
  insert into question (id, subject, content) values (1, '안녕하세요', '가입 인사드립니다 ^^');
  insert into question (id, subject, content) values (2, '질문 있습니다', 'ORM이 궁금합니다');
  ```
- ORM 코드
  ```java
  Question q1 = new Question();
  q1.setId(1);
  q1.setSubject("안녕하세요");
  q1.setContent("가입 인사드립니다 ^^");
  this.questionRepository.save(q1);
  
  Question q2 = new Question();
  q2.setId(2);
  q2.setSubject("질문 있습니다");
  q2.setContent("ORM이 궁금합니다");
  this.questionRepository.save(q2);
  ```
- ORM은 객체와 데이터베이스의 데이터를 자동으로 매핑해주기 때문에 SQL 쿼리를 직접 작성할 필요가 없다.
- ``엔티티(Entity)객체`` : 데이터를 관리하고 사용하는 ORM의 자바 클래스 

  > ORM의 장점<br>1. 객체지향적인 코드로 인해 더 직관적이고 비즈니스 로직에 더 집중할 수 있다.<br> 2. 데이터베이스의 종류에 상관없이 동일한 코드로 데이터를 다룰 수 있다.<br> 3. SQL을 직접 다루지 않아도 되기 때문에 생산성이 향상된다.<br> 4. 객체지향적으로 데이터를 관리하기 때문에 유지보수가 쉽다.
  
  > DBMS란?<br>
  > DBMS(Database Management System)은 데이터베이스를 관리하는 소프트웨어이다. 데이터베이스를 생성하고, 데이터를 저장, 수정, 삭제, 조회하는 작업을 수행한다. 대표적인 DBMS로는 Oracle, MySQL, PostgreSQL, SQLite, SQL Server 등이 있다.
  ![image](https://wikidocs.net/images/page/225222/image63_1.png)

### JPA(Java Persistence API)
- JPA는 ORM을 사용하기 위한 자바 표준 인터페이스이다.
- 스프링 부트는 JPA를 사용하여 데이터베이스를 다룬다.
- JPA는 인터페이스 규격으로, 이를 구현한 구현체가 Hibernate, EclipseLink, DataNucleus 등이 있다.

### H2 데이터베이스 설치 및 연결
- H2 데이터베이스는 인메모리 데이터베이스로, 데이터베이스를 별도로 설치하지 않아도 된다.
- H2 데이터베이스는 스프링 부트에서 기본적으로 제공하는 데이터베이스이다.
1) H2 데이터 베이스를 설치하기 위해서는 build.gradle 파일에 다음과 같이 의존성을 추가한다.
    ```gradle
    dependencies {
      runtimeOnly 'com.h2database:h2'
    }
    ```
2) application.yml 파일에 다음과 같이 데이터베이스 설정을 추가한다.
    ```yml
    spring:
      h2:
        console:
          enabled: true
          path: /h2-console
    datasource:
      url: jdbc:h2:~/local
      driver-class-name: org.h2.Driver
      username: sa
      password:
    ```
  - h2-console.enabled : H2 데이터베이스 콘솔을 사용할 수 있도록 설정한다.
  - h2-console.path : H2 데이터베이스 콘솔의 URL을 설정한다.
  - datasource.url : 데이터베이스의 URL을 설정한다.
    - ``jdbc:h2:~/local`` : ~(홈) 디렉토리에 local(파일명) 데이터베이스를 생성한다.
  - datasource.driver-class-name : 데이터베이스에 접속할 때 사용하는 드라이버 클래스명을 설정한다.
  - datasource.username : 데이터베이스의 사용자 이름을 설정한다.

3) datasource.url에 있는 결로에 local.mv.db를 생성한다.
4) 프로젝트를 실행하고 브라우저에서 ``http://localhost:8080/h2-console``로 접속한다.
5) JDBC URL에 ``jdbc:h2:~/local``을 입력하고 Connect 버튼을 클릭한다.

### JPA 환경 설정
- 스프링부트에서 데이터베이스에 데이터를 저장하거나 조회하려면 JPA를 사용해야 한다.
1) build.gradle 파일에 다음과 같이 의존성을 추가한다.
    ```gradle
    dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    }
    ```
2) application.yml 파일에 다음과 같이 JPA 설정을 추가한다.
    ```yml
    spring:
      jpa:
        hibernate:
          ddl-auto: update
        properties:
          hibernate:
            dialect: org.hibernate.dialect.H2Dialect
    ```
  - jpa.hibernate.ddl-auto : JPA로 테이블을 생성할 때 사용하는 전략을 설정한다.
    - ``none`` : 엔티티가 변경되더라도 데이터베이스를 변경하지 않는다.
    - ``update`` : 테이블이 존재하지 않으면 테이블을 생성하고, 존재하면 변경된 부분만 수정한다.
    - ``validate`` :  엔티티와 테이블 간에 차이점이 있는지 검사만 한다.
    - ``create`` : 스프링 부트 서버를 시작할 때 테이블을 모두 삭제한 후 다시 생성한다.
    - ``create-drop`` : create와 동일하지만 스프링 부트 서버를 종료할 때에도 테이블을 모두 삭제한다.
  - jpa.properties.hibernate.dialect : 스프링 부트와 하이버네이트를 함께 사용할 때 필요한 설정 항목이다. <br>
표준 SQL이 아닌 하이버네이트만의 SQL을 사용할 때 필요한 항목으로 하이버네이트의 org.hibernate.dialect.H2Dialect 클래스를 설정했다

## 2.4 엔티티로 테이블 매핑하기
### 데이터베이스의 구성요소 
- 데이터베이스는 테이블, 열, 행으로 구성된다.
- ```기본키(Primary Key)``` : 테이블에서 각 행을 구분하는 유일한 값이다.
- ```외래키(Foreign Key)``` : 다른 테이블의 기본키를 참조하는 열이다.
  ![image](https://wikidocs.net/images/page/225222/image70_1.png)

### 엔티티 속성 구성하기
- 엔티티는 데이터베이스의 테이블과 매핑되는 자바 클래스이다.
- 이번 예제에서는 질문과 답변에 대한 엔티티를 작성한다. 
- 질문
  
  | 속성이름       | 설명             |                    
  |------------|----------------|
  | id         | 질문 데이터의 고유번호   |
  | subject    | 질문 데이터의 제목     |
  | content    | 질문 데이터의 내용     |
  | createDate | 질문 데이터를 작성한 일시 |

- 답변

  | 속성이름       | 설명             |                    
  |------------|----------------|
  | id         | 답변 데이터의 고유번호   |
  | question   | 질문 데이터         |
  | content    | 답변 데이터의 내용     |
  | createDate | 답변 데이터를 작성한 일시 |

### 질문 엔티티 만들기
1) 질문 엔티티를 만들기 위해 Question 클래스를 작성한다.
  ```java
    package com.ll.jump.entity;
    
    import jakarta.persistence.*;
    import lombok.Getter;
    import lombok.Setter;
    
    import java.time.LocalDateTime;
    
    @Entity
    @Getter
    @Setter
    public class Question {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Integer id;
    
      @Column(length = 200)
      private String subject;
    
      @Column(columnDefinition = "TEXT")
      private String content;
    
      private LocalDateTime createDate;
    }
  ```
  - @Entity : 엔티티 클래스임을 나타낸다.
  - @Id : 기본키를 나타낸다.
  - @GeneratedValue : 기본키의 생성 전략을 설정한다.
    - ``GenerationType.IDENTITY`` : 기본키를 데이터베이스에 위임한다.
    - ``GenerationType.SEQUENCE`` : 데이터베이스의 시퀀스를 사용하여 기본키를 생성한다.
    - ``GenerationType.TABLE`` : 데이터베이스의 테이블을 사용하여 기본키를 생성한다.
    - ``GenerationType.AUTO`` : 데이터베이스에 따라 자동으로 기본키를 생성한다.
  - @Column : 엔티티의 속성을 설정한다.
    - length : 문자열의 길이를 설정한다.
    - columnDefinition : 컬럼의 정의를 설정한다.
    > 엔티티의 속성은 @Column 애너테이션을 사용하지 않더라도 테이블의 열로 인식한다. 테이블의 열로 인식하고 싶지 않다면 @Transient 애너테이션을 사용한다. @Transient 애너테이션은 엔티티의 속성을 테이블의 열로 만들지 않고 클래스의 속성 기능으로만 사용하고자 할 때 쓴다
  
### 답변 엔티티 만들기
1) 답변 엔티티를 만들기 위해 Answer 클래스를 작성한다.
  ```java
    package com.ll.jump.entity;
    
    import jakarta.persistence.*;
    import lombok.Getter;
    
    import java.time.LocalDateTime;
    
    @Entity
    @Getter
    public class Answer {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Integer id;
    
      @Column(columnDefinition = "TEXT")
      private String content;
    
      private LocalDateTime createDate;
    
      @ManyToOne
      private Question question;
    }
  ```
  - @ManyToOne
    - N:1 관계를 나타낸다.
    - Answer(답변) 엔티티의 question 속성과 Question(질문) 엔티티가 서로 연결된다.
    - 실제 데이터베이스에서는 외래키(foreign key) 관계가 생성된다.

2) Question 수정하기
  ```java
  @OneToMany(mappedBy = "question", cascade = CascadeType.REMOVE)
  private List<Answer> answerList; 
  ``` 
  - @OneToMany
    - 1:N 관계를 나타낸다.
    - Question(질문) 엔티티의 answers 속성과 Answer(답변) 엔티티가 서로 연결된다.
  - mappedBy 속성은 연관관계의 주인을 설정한다.
  - casaade 속성은 연관된 엔티티를 같이 처리할 때 사용한다.
    - ``CascadeType.ALL`` : 모든 작업을 같이 처리한다.
    - ``CascadeType.PERSIST`` : 영속성 컨텍스트에 저장할 때 같이 처리한다.
    - ``CascadeType.REMOVE`` : 삭제할 때 같이 처리한다.
    - ``CascadeType.MERGE`` : 병합할 때 같이 처리한다.
    - ``CascadeType.REFRESH`` : 새로고침할 때 같이 처리한다.
    - ``CascadeType.DETACH`` : 분리할 때 같이 처리한다.


## 2.5 레포지터리로 데이터 베이스 관리
### 레포지터리(Repository) 생성
- 데이터베이스 테이블의 데이터들을 저장, 조회, 수정, 삭제 등을 할 수 있도록 도와주는 인터페이스
1) Question 레포지터리 생성
  ```java
  package com.ll.jump.repository;
  
  import com.ll.jump.entity.Question;
  import org.springframework.data.jpa.repository.JpaRepository;
  
  public interface QuestionRepository extends JpaRepository<Question, Integer> {
  }
  ```
  - JpaRepository 인터페이스를 상속받아 사용한다.
  - JpaRepository 인터페이스는 엔티티와 기본키의 타입을 받아 CRUD 메서드를 제공한다.
  - QuestionRepository 인터페이스는 Question 엔티티와 Integer 타입의 기본키를 사용한다.

2) Answer 레포지터리 생성
  ```java
  package com.ll.jump.repository;
  
  import com.ll.jump.entity.Answer;
  import org.springframework.data.jpa.repository.JpaRepository;
  
  public interface AnswerRepository extends JpaRepository<Answer, Integer> {
  }
  ```

### JUnit으로 레포지터리 테스트하기
- JUnit은 자바 프로그래밍 언어용 단위 테스트 프레임워크이다.
- JUnit을 사용하여 레포지터리를 테스트 할 수 있다.

1) JUnit 의존성 추가
  ```gradle
  dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter' 
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
  }
  ```
  - testImplementation : 테스트 코드를 작성할 때만 사용하는 의존성을 설정한다.

2) ApplicationTests.java 구현
  ```java
    package com.ll.jump;

    import com.ll.jump.entity.Question;
    import com.ll.jump.repository.QuestionRepository;
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    
    import java.time.LocalDateTime;
    
    @SpringBootTest
    public class SbbApplicationTests {
    
      @Autowired
      private QuestionRepository questionRepository;
    
      @Test
      void testJpa() {
        Question q1 = new Question();
        q1.setSubject("What is your name?");
        q1.setContent("My name is John.");
        q1.setCreateDate(LocalDateTime.now());
        this.questionRepository.save(q1);
      }
    }
  ``` 

### 질문 데이터 조회하기
- findAll메서드를 사용하여 데이터베이스에 저장된 질문 데이터를 조회할 수 있다.
  ```java
  @Test
  void testJpa() { 
      List<Question> all = this.questionRepository.findAll();
      assertEquals(1, all.size());

      Question q = all.get(0);
      assertEquals("What is your name??", q.getSubject());
  }
  ```
  
- findById메서드를 사용하여 데이터베이스에 저장된 질문 데이터를 조회할 수 있다.
  ```java
  @Test
  void testJpa() {
      Optional<Question> byId = this.questionRepository.findById(1);
      assertTrue(byId.isPresent());
      Question q = byId.get();
      assertEquals("What is your name??", q.getSubject());
  }
  ```
  
- findBySubject메서드를 사용하여 데이터베이스에 저장된 질문 데이터를 조회할 수 있다.
- findBySubject메서드는 기본적으로 제공하지 않는다. 
- findBySubject메서드를 사용하려면 QuestionRepository 인터페이스에 다음과 같이 메서드를 추가해야 한다.
- ``find + By + 속성명 + (조건)``형식으로 작성한다.

    ```java
    package com.ll.jump.repository;
    
    import org.springframework.data.jpa.repository.JpaRepository;

    public interface QuestionRepository extends JpaRepository<Question, Integer> {
        Question findBySubject(String subject);
    }
    ```

    ```java
    @Test
    void testJpa() {
        List<Question> bySubject = this.questionRepository.findBySubject("What is your name??");
        assertEquals(1, bySubject.size());
        Question q = bySubject.get(0);
        assertEquals("What is your name??", q.getSubject());
    }
    ```
  > JPA 메서드 규칙
  > - 예약어 + By + 속성명 + (조건) 형식으로 메서드를 작성한다.
  > - 반환값은 다르지만 기본적인 구조는 다음 과 같다.
  >  ```markdown
  >  // find 계열
  >  findBy...           // 검색
  >  findFirst3By...     // 처음 3개만
  >  findDistinctBy...   // 중복 제거
  >
  >  // get 계열
  >  getBy...           // find와 동일
  >  getFirstBy...      // 첫 번째 결과만
  >  
  >  // count 계열
  >  countBy...         // 개수 반환
  >  countDistinctBy... // 중복 제거 후 개수
  >  
  >  // exists 계열
  >  existsBy...        // 존재 여부 (boolean 반환)
  >  
  >  // delete 계열
  >  deleteBy...        // 삭제
  >  removeBy...        // 삭제
  >  
  >  // Top/First
  >  findTop3By...      // 상위 3개
  >  findFirstBy...     // 첫 번째 결과
  >  ```

### 질문데이터 삭제하기 
- deleteById메서드를 사용하여 데이터베이스에 저장된 질문 데이터를 삭제할 수 있다.
  ```java
  @Test
  void testJpa() {
      assertEquals(2, this.questionRepository.count());
      Optional<Question> oq = this.questionRepository.findById(1);
      assertTrue(oq.isPresent());
      Question q = oq.get();
      this.questionRepository.delete(q);
      assertEquals(1, this.questionRepository.count());
  }
  ```

#### 답변데이터 조회하기
- 답변데이터도 질문데이터와 동일하게 조회할 수 있다.
  ```java
  @SpringBootTest
  class SbbApplicationTests {
  
      @Autowired
      private QuestionRepository questionRepository;
  
      @Autowired
      private AnswerRepository answerRepository;
  
      @Test
      void testJpa() {
          Optional<Answer> oa = this.answerRepository.findById(1);
          assertTrue(oa.isPresent());
          Answer a = oa.get();
          assertEquals(2, a.getQuestion().getId());
      }
  }
  ```
  
### 답변데이터를 통해 질문 데이터 찾기
- 답변데이터를 통해 질문 데이터를 찾을 수 있다.
  ```java
  @Test
  void testJpa() {
      Optional<Answer> oa = this.answerRepository.findById(1);
      assertTrue(oa.isPresent());
      Answer a = oa.get();
      Question q = a.getQuestion();
      assertEquals("What is your name??", q.getSubject());
  }
  ```
  
### 질문데이터에서 답변 데이터 리스트 구하기
- 질문데이터에서 답변 데이터 리스트를 구할 수 있다.
  ```java
  @Test
  void testJpa() {
      Optional<Question> oq = this.questionRepository.findById(2);
      assertTrue(oq.isPresent());
      Question q = oq.get();
      List<Answer> answerList = q.getAnswerList();
      assertEquals(1, answerList.size());
  }
  ```
  - Junits에서는 테스트 메서드가 실행될 때마다 새로운 트랜잭션을 시작하고 테스트가 끝나면 롤백한다.
  - 따라서 테스트 메서드가 실행될 때마다 데이터베이스에 저장된 데이터는 초기화된다.
  - 테스트 메서드가 실행될 때마다 데이터베이스에 저장된 데이터를 유지하고 싶다면 @Transactional 애너테이션을 사용한다.
    ```java
    @SpringBootTest
    @Transactional
    class SbbApplicationTests {
    }
    ```
  - 본 실행 코드에서는 트랜젝션 없이도 잘 유지된다.
  
=============================================================

## 전체코드
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/ab42bae)

## 2.6 도메인 별로 분류 
- 도메인이란 비즈니스 로직을 처리하는 클래스를 말한다.
- 도메인을 별도의 패키지로 분류하여 관리하면 코드의 가독성이 높아진다.
- 도메인을 분류하는 방법은 다음과 같다.
  1) 도메인 패키지 생성
  2) 도메인별로 패키지 생성
  3) 도메인별로 엔티티, 레포지터리, 서비스, 컨트롤러를 생성한다.
  4) 도메인별로 패키지를 생성하여 관리한다.
  
```markdown
project-root/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── project/
│   │   │               ├── domain/
│   │   │               │   ├── user/
│   │   │               │   │   ├── controller/
│   │   │               │   │   ├── service/
│   │   │               │   │   ├── repository/
│   │   │               │   │   ├── entity/
│   │   │               │   │   └── dto/
│   │   │               │   ├── Answer/
│   │   │               │   │   ├── controller/
│   │   │               │   │   ├── service/
│   │   │               │   │   ├── repository/
│   │   │               │   │   ├── entity/
│   │   │               │   │   └── dto/
│   │   │               │   └── Question/
│   │   │               │       ├── controller/
│   │   │               │       ├── service/
│   │   │               │       ├── repository/
│   │   │               │       ├── entity/
│   │   │               │       └── dto/
```

## 2.7 질문 목록만들기 
1) QuestionController 구현
  ```java
  package com.ll.jump.Question.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.ResponseBody;

  @Controller
  @RequiredArgsConstructor
  public class QuestionController {
    private final QuestionRepository questionRepository;
  
    @GetMapping("/question/list")
  
    public String list(Model model) {
      List<Question> questionList = questionRepository.findAll();
      model.addAttribute("questionList", questionList);
      return "question_list";
    }
  }
  ```
2) question_list.html 구현 
- 타임리프를 활용하면 구현한다. 
```gradle
dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
}
```
- resources/templates/question_list.html

## 2.8 Root URL 사용히기
- Root URL을 사용하면 사용자가 웹사이트에 접속했을 때 가장 먼저 보여지는 페이지를 설정할 수 있다.
- 메인 컨트롤러를 수정한다. 
- MainController.java
  ```java
  package com.ll.jump.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.ResponseBody;
  
  @Controller
  public class MainController {
    @GetMapping("/sbb")
    @ResponseBody
    public String index() {
      return "index";
    }
  
    @GetMapping("/")
    public String root() {
      return "redirect:/question/list";
    }
  }
  ```

## 2.9 Service 활용하기 
- 서비스는 비즈니스 로직을 처리하는 클래스이다.
- 서비스 코드를 사용하는 이유
  - 복잡한 코드를 모듈화 할 수 있다.
  - 민감한 엔티티 코드를 숨길 수 있다. 

### 서비스 만들기 
1) QuestionService.java
  ```java
  package com.ll.jump.service;
  
  import com.ll.jump.entity.Question;
  import com.ll.jump.repository.QuestionRepository;
  import lombok.RequiredArgsConstructor;
  import org.springframework.stereotype.Service;
  
  import java.util.List;
  
  @Service
  @RequiredArgsConstructor
  public class QuestionService {
    private final QuestionRepository questionRepository;
  
    public List<Question> findAll() {
      return questionRepository.findAll();
    }
  }
  ```

2) 컨트롤러 수정 
  ```java
    @Controller
    @RequiredArgsConstructor
    public class QuestionController {
      private final QuestionService questionService;
    
      @GetMapping("/question/list")
    
      public String list(Model model) {
        List<Question> questionList = questionService.findAll();
        model.addAttribute("questionList", questionList);
        return "question_list";
      }
    }
  ```

## 2.10 질문 상세보기 기능 구현하기
1) 링크 추가하기
- html 파일에서 a 태그를 활용하여 링크를 추가한다. 
- question_list.html
  ```html
    <a th:href="@{|/question/detail/${question.id}|}" th:text="${question.subject}"></a>
  ```

2) Mapping 추가하기
- QuestionController.java
  ```java
  @GetMapping("/question/detail/{id}")
  public String detail(
      Model model,
      @PathVariable("id") Integer id) {

    return "question_detail";
  }
  ```
  
3) Service 추가하기
- QuestionService.java
  ```java
  public Question getQuestion(Integer id) {
    Optional<Question> question = questionRepository.findById(id);
    if (question.isPresent()) {
      return question.get();
    } else {
      throw new DataNotFoundException("Question not found");
    }
  }
  ```
- DataNotFoundException 클래스 추가
  - 예외처리 클래스를 만들어서 사용한다.
  ```java
  package com.ll.jump;
  
  import org.springframework.http.HttpStatus;
  import org.springframework.web.bind.annotation.ResponseStatus;
  
  @ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Entity not found")
  public class DataNotFoundException extends RuntimeException {
    private static final long serialVersionUID = 1L;
    public DataNotFoundException(String message) {
      super(message);
    }
  }
  ```
4) 상세페이지 구현
    ```html
    <h1 th:text="${question.subject}"></h1>
    <div th:text="${question.content}"></div>
    ```
