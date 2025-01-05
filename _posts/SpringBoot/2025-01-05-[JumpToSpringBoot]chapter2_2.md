---
title: "(점프 투 스프링부트) 2장 스프링 부트의 기본 기능 익히기(하)"
date: 2025-01-05 1:35:23 +0900
updated: 2025-01-05 1:35:23 +0900
category: SpringBoot
tags: 스프링부트, 독학
permalink: /springboot/chapter2_2/
---
## 전체코드
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/e44dbe4)

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

## 2.11 URL 프리픽스 알아 두기 
- 컨트롤러를 보면 Url이 중복되어 있다.
  > @GetMapping("/question/list")<br>
  > @GetMapping("/question/detail/{id}")
- ``@RequestMapping``어노테이션을 활용해 중복된 url을 제거할 수 있다.
- MainController.java
  ```java
  @RequestMapping("/question")
  public class QuestionController {
    @GetMapping("/list")
    public String list(Model model) {
          /* 생략 */
    }
  
    @GetMapping("/detail/{id}")
    public String detail(
        Model model,
        @PathVariable("id") Integer id) {
          /* 생략 */
    }
  }
  ```
  
## 2.12 답변 기능 만들기 
1) Form 만들기 
- post요청을 보내는 form을 추가한다. 
- post요청을 보내면 question의 id 값과 body에 content를 전송 한다.

```html
<form th:action="@{|/answer/create/${question.id}|}" method="post">
  <textarea name="content" id="content" rows="15"></textarea>
  <input type="submit" value="답변등록">
</form>
```

2) Mapping 하기 
- 서버에서 id path로 question 객체를 생성하고 
- question 객체와 content를 이용하여 answer를 생성한후 repository에서 저장한다. 
- AnswerController.java

  ```java
  @Controller
  @RequestMapping("/answer")
  @RequiredArgsConstructor
  public class AnswerController {
    private final AnswerService answerService;
  
   @PostMapping("/create/{id}")
   public String createAnser(
        Model model,
        @PathVariable("id") Integer id,
        @RequestParam("content") String content) {

      Question question = questionService.getQuestion(id);
      this.answerService.create(question, content);

      return String.format("redirect:/question/detail/%d", id);
    }
  }
  ```

3) Service 구현하기
- 컨트롤러 단에서 넘어온 객체를 가지고 비즈니스 로직을 처리한다.
- AnswerService.java
  ```java
  @Service
  @RequiredArgsConstructor
  public class AnswerService {
    private final AnswerRepository answerRepository;
  
    public void create(Question question, String content) {
      Answer answer = new Answer();
      answer.setQuestion(question);
      answer.setContent(content);
      answer.setCreateDate(LocalDateTime.now());
      this.answerRepository.save(answer);
    }
  }
  ```

## 2.13 css 적용하기 
- css를 적용하면 웹페이지의 디자인을 꾸밀 수 있다.
- resources/static 디렉토리에 css파일을 추가한다.
  ```css
  textarea {
    width:100%;
  }
  
  input[type=submit] {
    margin-top:10px;
  }
  ```
- html에서는 link태그를 사용하여 css파일을 불러온다.
  ```html
  <link rel="stylesheet" type="text/css" th:href="@{/style.css}">
  ```

## 2.14 부트스트랩으로 화면 꾸미기 
- 부트스트랩은 웹사이트를 쉽게 디자인할 수 있는 프론트엔드 프레임워크이다.
### 부트스트랩 적용하기 
- [적용](https://wikidocs.net/161523)
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/06831198-6d8a-4f09-b17a-f9d1d429c27b" />
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/4f2b6dd4-6f51-4d03-9c50-c4c31b1cf268" />

## 2.15 표준 HTML로 만들기 
1) layout 만들기 
- [적용](https://wikidocs.net/161873)

## 2.16 질문 등록 기능 추가하기
1) html 수정
- table 태그 다음으로 질문버튼을 만든다. 
- question_list.html
  ```html
  <a th:href="@{/question/create}" class="btn btn-primary">질문 등록하기</a>
  ```
  
2) Mapping 추가하기
- QuestionController.java
  ```java
  @GetMapping("/create")
  public String questionCreate() {
    return "question_form";
  }
  ```
  
3) Form 만들기
- question_form.html
  ```html
  <html layout:decorate="~{layout}">
  <div layout:fragment="content" class="container">
      <h5 class="my-3 border-bottom pb-2">질문등록</h5>
      <form th:action="@{/question/create}" method="post">
          <div class="mb-3">
              <label for="subject" class="form-label">제목</label>
              <input type="text" name="subject" id="subject" class="form-control">
          </div>
          <div class="mb-3">
              <label for="content" class="form-label">내용</label>
              <textarea name="content" id="content" class="form-control" rows="10"></textarea>
          </div>
          <input type="submit" value="저장하기" class="btn btn-primary my-2">
      </form>
  </div>
  </html>
  ```

  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/4c710d65-d466-4593-b23b-b1225b9c85ee" />

4) Mapping 추가하기
- QuestionController.java
  ```java
  @PostMapping("/create")
  public String questionCreate(
      @RequestParam("subject") String subject,
      @RequestParam("content") String content) {

    questionService.create(subject, content);
    return "redirect:/question/list";
  }
  ```
  
5) Service 추가하기
- QuestionService.java
  ```java
  public void create(String subject, String content) {
    Question q = new Question();
    q.setSubject(subject);
    q.setContent(content);
    q.setCreateDate(LocalDateTime.now());
    this.questionRepository.save(q);
  }
  ```
### Spring Boot Validation 라이브러리 사용하기 
- Spring Boot Validation : 입력값을 검증하는 라이브러리이다.
- 의존성 추가
  ```gradle
  dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-validation'
  }
  ```
- Spring Boot Validation 라이브러리를 설치하면 다음과 같은 애너테이션을 사용하여 사용자가 입력한 값을 검증할 수 있다.
| 항목 | 설명 |
|------|------|
| `@Size` | 문자 길이를 제한한다. |
| `@NotNull` | Null을 허용하지 않는다. |
| `@NotEmpty` | Null 또는 빈 문자열("")을 허용하지 않는다. |
| `@Past` | 과거 날짜만 입력할 수 있다. |
| `@Future` | 미래 날짜만 입력할 수 있다. |
| `@FutureOrPresent` | 미래 또는 오늘 날짜만 입력할 수 있다. |
| `@Max` | 최댓값 이하의 값만 입력할 수 있도록 제한한다. |
| `@Min` | 최솟값 이상의 값만 입력할 수 있도록 제한한다. |
| `@Pattern` | 입력값을 정규식 패턴으로 검증한다. |

### Form클래스 만들기 
1) Form 클래스 생성
- Form클래스를 만들어서 사용자가 입력한 값을 검증한다.
- QuestionForm.java
  ```java
  package com.ll.jump.dto;
  
  import lombok.Getter;
  import lombok.Setter;
  
  import javax.validation.constraints.NotEmpty;
  import javax.validation.constraints.Size;
  
  @Getter
  @Setter
  public class QuestionForm {
    @NotEmpty
    @Size(min = 1, max = 100)
    private String subject;
  
    @NotEmpty
    private String content;
  }
  ```
  
2) Controller수정
- 클라이언트에서 넘어오는 form을 직접 받지 않고 form클래스를 통해 받는다.
- form클래스를 통해 받은 것을 BindingResult를 통해 검증한다.
- QuestionController.java
  ```java
  @PostMapping("/create")
  public String questionCreate(
      @Valid QuestionForm form,
      BindingResult bindingResult) {
    if (bindingResult.hasErrors()) {
      return "question_form";
    }
    questionService.create(form);
    return "redirect:/question/list";
  }
  ```

3) 클라이언트단 메시지 수정
- 에러가 발생하면 에러에 대한 알림창을 띄운다.
  ```html
  form th:action="@{/question/create}" th:object="${questionForm}" method="post">
          <div class="alert alert-danger" role="alert" th:if="${#fields.hasAnyErrors()}">
              <div th:each="err : ${#fields.allErrors()}" th:text="${err}" />
          </div>
  ```
  
4) ``Get create`` 메서드 수정
- @GetMapping으로 매핑한 questionCreate 메서드에 매개변수로 QuestionForm 객체를 추가했다. 이렇게 하면 이제 GET 방식에서도 question_form 템플릿에 QuestionForm 객체가 전달된다.
- QuestionController.java
  ```java
  @GetMapping("/create")
  public String questionCreate(QuestionForm questionForm) {
      return "question_form";
  }
  ```
## 답변 적용하기 
- 위 방법과 동일하기 답변 템플릿을 구현한다. 
