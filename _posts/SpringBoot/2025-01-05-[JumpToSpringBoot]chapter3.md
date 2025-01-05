---
title: "(점프 투 스프링부트) 3장 서비스 개발하기"
date: 2025-01-05 1:35:23 +0900
updated: 2025-01-05 1:35:23 +0900
category: SpringBoot
tags: 스프링부트, 독학
permalink: /springboot/chapter3/
---
## 전체코드
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/e44dbe4)
## 3.1 네비게이션 바 추가하기 
- 네비게이션 바는 화면 상단에 메뉴를 표시하는 역할을 한다.

1) html 파일에 네비게이션 바 추가하기

  ```html
  <nav class="navbar navbar-expand-lg navbar-light bg-light border-bottom">
      <div class="container-fluid">
          <a class="navbar-brand" href="/">SBB</a>
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
              aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
              <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarSupportedContent">
              <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                  <li class="nav-item">
                      <a class="nav-link" href="#">로그인</a>
                  </li>
              </ul>
          </div>
      </div>
  </nav>
  ```
- nav는 공통 기능이므로 하나의 조각으로 관리한다. 

## 3.2 페이징 기능 추가 
### 대량 테스트 데이터 만들기 
- test.java 파일에 예제 데이터 코드를 삽입한다. 

  ```java
  package com.ll.jump;
  
  import com.ll.jump.Question.service.QuestionService;
  import org.junit.jupiter.api.Test;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.context.SpringBootTest;
  
  import java.time.LocalDateTime;
  
  @SpringBootTest
  public class SbbApplicationTests {
  
    @Autowired
    private QuestionService questionService;
  
    @Test
    void testJpa() {
      for (int i = 1; i <= 300; i++) {
        String subject = String.format("테스트 데이터입니다:[%03d]", i);
        String content = "내용무";
        this.questionService.create(subject, content);
      }
    }
  }
  ```
### 페이징 구현하기 
- JPA 환경 구축 시 설치했던 JPA 관련 라이브러리에 이미 페이징을 위한 패키지들이 들어있다.
  > ```org.springframework.data.domain.Pageable``` : 페이징 처리를 위한 인터페이스<br>
  > ```org.springframework.data.domain.Page``` : 페이징을 위한 클래스<br>
  > ```org.springframework.data.domain.PageRequest``` : 페이징 요청을 설정하는 클래스<br> 

1) QuestionRepository.java 파일에 페이징 처리 메서드 추가하기 
- pagealbe을 파라미터로 받아 페이징 처리를 한다.
- findAll 메서드를 통해 페이징 처리를 한다.

  ```java
  package com.ll.jump.Question.repository;
  
  import com.ll.jump.Question.entity.Question;
  import org.springframework.data.domain.Page;
  import org.springframework.data.domain.Pageable;
  import org.springframework.data.jpa.repository.JpaRepository;
  
  import java.util.List;
  
  public interface QuestionRepository extends JpaRepository<Question, Integer> {
    Question findBySubject(String subject);
    Question findBySubjectAndContent(String subject, String content);
    List<Question> findBySubjectLike(String subject);
    Page<Question> findAll(Pageable pageable);
  }
  ```
  
2) QuestionService.java 파일에 페이징 처리 메서드 추가하기
- Pageable을 파라미터로 받아 페이징 처리를 한다.
- 한페이지에 10개의 Question을 보여주도록 한다.
  ```java
  public Page<Question> getList(int page) {
        Pageable pageable = PageRequest.of(page, 10);
        return this.questionRepository.findAll(pageable);
    }
  ```

3) QuestionController.java 파일에 페이징 처리 메서드 추가하기
- page를 파라미터로 받아 페이징 처리를 한다.
- Get 요청을 통해 매개변수로 처리하고 ``@RequestParam``으로 받아 처리한다.
  ```java
  @GetMapping("/list")
  public String list(Model model, @RequestParam(value="page", defaultValue="0") int page) {
        Page<Question> paging = this.questionService.getList(page);
        model.addAttribute("paging", paging);
        return "question_list";
    }
  ```
#### Paging 옵션

| 속성 | 설명 |
|---------|------|
| `paging.isEmpty` | 페이지 존재 여부를 의미한다(게시물이 있으면 false, 없으면 true). |
| `paging.totalElements` | 전체 게시물 개수를 의미한다. |
| `paging.totalPages` | 전체 페이지 개수를 의미한다. |
| `paging.size` | 페이지당 보여 줄 게시물 개수를 의미한다. |
| `paging.number` | 현재 페이지 번호를 의미한다. |
| `paging.hasPrevious` | 이전 페이지의 존재 여부를 의미한다. |
| `paging.hasNext` | 다음 페이지의 존재 여부를 의미한다. |

4) question_list.html 파일에 페이징 처리 추가하기
- 페이징 처리를 위한 코드를 추가한다.
  ```html
  <!-- 페이징처리 시작 -->
    <div th:if="${!paging.isEmpty()}">
        <ul class="pagination justify-content-center">
            <li class="page-item" th:classappend="${!paging.hasPrevious} ? 'disabled'">
                <a class="page-link"
                    th:href="@{|?page=${paging.number-1}|}">
                    <span>이전</span>
                </a>
            </li>
            <li th:each="page: ${#numbers.sequence(0, paging.totalPages-1)}"
                th:if="${page >= paging.number-5 and page <= paging.number+5}"
                th:classappend="${page == paging.number} ? 'active'" 
                class="page-item">
                <a th:text="${page}" class="page-link" th:href="@{|?page=${page}|}"></a>
            </li>
            <li class="page-item" th:classappend="${!paging.hasNext} ? 'disabled'">
                <a class="page-link" th:href="@{|?page=${paging.number+1}|}">
                    <span>다음</span>
                </a>
            </li>
        </ul>
    </div>
    <!-- 페이징처리 끝 -->
  ```

## 3.3 게시물에 번호 지정하기 
- 게시물의 번호를 지정하기 위해 번호를 추가한다.
> 게시글 번호 = 전체 게시물 개수 - (현재 페이지 * 페이지당 게시물 개수) - 나열인덱스 <br>

  | 항목 | 설명 |
  |--------|------|
  | 게시물 번호 | 최종 표시될 게시물의 번호 |
  | 전체 게시물 개수 | 데이터베이스에 저장된 게시물 전체 개수 |
  | 현재 페이지 | 페이징에서 현재 선택한 페이지 |
  | 페이지당 게시물 개수 | 한 페이지당 보여 줄 게시물의 개수 |
  | 나열 인덱스 | for 문 안의 게시물 순서(나열 인덱스는 현재 페이지에서 표시할 수 있는 게시물의 인덱스이므로, 예를 들어 10개를 표시하는 페이지에서는 0~9, 2개를 표시하는 페이지에서는 0~1로 반복된다.) |

```html
td th:text="${paging.getTotalElements - (paging.number * paging.size) - loop.index}"></td>
```
- paging.getTotalElements : 전체 게시물 개수
- paging.number : 현재 페이지
- paging.size : 페이지당 게시물 개수
- loop.index : 나열 인덱스

## 3.4 답변 개수 표시하기
- 게시글에 달린 답변의 개수를 표시하면 UX관점에서 사용자가 효율적으로 답변의 개수를 파악할 수 있다. 
- question_list.html 파일에 답변 개수 표시하기
  ```html
  <span class="text-danger small ms-2"
      th:if="${#lists.size(question.answerList) > 0}" 
      th:text="${#lists.size(question.answerList)}">
  </span>
  ```
- 답변이 달린 경우 빨간색 작을 글씨로 답변의 개수가 표시된다.
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/fddb82f7-4fa6-41d2-97ce-d80020f25279" />

## 3.5 스프링 시큐리티 적용하기
- 스프링 부트는 회원 가입과 로그인을 도와주는 Spring Security를 제공한다.
- Spring Security는 인증과 권한을 처리하는 프레임워크이다.

### 설치하기
- build.gradle 파일에 Spring Security를 추가한다.
  ```gradle
  implementation 'org.springframework.boot:spring-boot-starter-security'
  implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6'
  ```
### 설정하기
  - SecurityConfig 설정 없이 애플리케이션을 실행 하면 다음과 같은 화면이 나온다.
- 스프링 시큐리티는 기본적으로 인증되지 않은 사용자가 SBB와 같은 웹 서비스를 사용할 수 없게끔 만든다.
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/c4963d1f-91af-4854-bab0-36d1f366a902" />

  ```java
  package com.ll.jump.global.config;
  
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.security.config.annotation.web.builders.HttpSecurity;
  import org.springframework.security.web.SecurityFilterChain;
  import org.springframework.security.web.util.matcher.AntPathRequestMatcher;
  
  @Configuration
  @EnableWebSecurity
  public class SecurityConfig {
    @Bean
    SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests((authorizeHttpRequests) -> authorizeHttpRequests
                .requestMatchers(new AntPathRequestMatcher("/**")).permitAll());
    return http.build();
    }
  }
  ``` 
- @EnableWebSecurity : 스프링 시큐리티를 사용하기 위한 어노테이션
- SecurityFilterChain : 스프링 시큐리티 필터 체인을 생성하는 메서드
- http.authorizeHttpRequests : 요청에 대한 권한을 설정하는 메서드
- requestMatchers : 요청에 대한 매처를 설정하는 메서드
- AntPathRequestMatcher : URL 패턴을 설정하는 메서드
- http 요청이 들어오면 requestMatchers에 설정된 URL 패턴을 통해 접근 권한을 설정한다.

### H2 콘솔 오류 수정 하기
- 스프링 시큐리티를 적용하면 H2 콘솔 로그인 시 다음과 같은 403 Forbidden 오류가 발생한다.
- 스프링 시큐리티의 CSRF 방어 기능에 의해 H2 콘솔 접근이 거부된다.
- 스프링은 CSRF 공격을 방어하기 위해 토큰을 세션을 통해 발행하고, 웹페이지에서는 폼 전송시 해당 토큰을 함께 전송하여 인증을 한다.
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/e0b3420a-850c-4a63-9ecc-98db7b77a9ba" />
- 스프링 시큐리티에 의해 <form> 태그 안에 CSRF 토큰이 자동으로 생성된다.
- h2-console을 이용하기 위해서는 체인에 csrf 설정을 추가한다. 

  ```java
  .csrf((csrf) -> csrf
            . ignoringRequestMatchers(new AntPathRequestMatcher("/h2-console/**")))
  ```
- 하지만 로그인  하면  X-Frame-Options 헤더를 막아 테이블이 출력되지 않는다.
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/eb907566-dd98-44b2-a3bf-a52e0957a84a" />
- 이를 위해 hearder 허용 규칙을 만든다.

  ```java
  .headers((headers) -> headers
                  .addHeaderWriter(new XFrameOptionsHeaderWriter(
                      XFrameOptionsHeaderWriter.XFrameOptionsMode.SAMEORIGIN)))
  ```

## 3.6 회원가입 기능 구현 
### 회원 엔티티 만들기 
1) 회원 엔티티
- 회원 엔티티는 다음과 같다.
  
  | 속성이름     | 설명       |
  |----------|----------|
  | username | 사용자이름(ID) |
  | password | 비밀번호     |
  | email    | 이메일      |
- 이를 위해 SiteUser라는 entity 객체를 만든다.

2) repository와 service 생성하기 
- UserRepository.java

  ```java
  @Repository
  public interface UserRepository extends JpaRepository<SiteUser, Long> {
  }
  ```
- UserService.java

  ```java
  @Service
  @RequiredArgsConstructor
  public class UserService {
  
    private final UserRepository userRepository;
    private final PasswordEncoder passwordEncoder;
  
    public SiteUser create(String username, String password, String email){
      SiteUser user = new SiteUser();
      user.setUsername(username);
      user.setEmail(email);
      user.setPassword(passwordEncoder.encode(password));
      this.userRepository.save(user);
      return user;
    }
  
  }
  ```
- 비밀번호는 보안을 위해 반드시 암호화하여 저장하므로 스프링 시큐리티의 BCryptPasswordEncoder 클래스를 사용하여 암호화하여 비밀번호를 저장한다.
- SecurityConfig에서 passwordEncoder를 주입하여 service에서는 해당 클래스를 사용한다. 
  
  ```java
  @Bean
  PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
  }
  ```

3) dto생성
- 회원가입 form을 위한 클래스를 생성한다.

  ```java
  @Getter
  @Setter
  public class UserCreateForm {
    @Size(min = 3, max = 25)
    @NotEmpty(message = "사용자ID는 필수항목입니다.")
    private String username;
  
    @NotEmpty(message = "비밀번호는 필수항목입니다.")
    private String password1;
  
    @NotEmpty(message = "비밀번호 확인은 필수항목입니다.")
    private String password2;
  
    @NotEmpty(message = "이메일은 필수항목입니다.")
    @Email
    private String email;
  }
  ```

4) controller 구현
- /user/signup URL이 GET으로 요청되면 회원 가입을 위한 템플릿을 렌더링하고, POST로 요청되면 회원 가입을 진행하도록 한다.

  ```java
  @RequiredArgsConstructor
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
    private final UserService userService;
  
    @GetMapping("/signup")
    public String signup(UserCreateForm userCreateForm) {
      return "signup_form";
    }
  
    @PostMapping("/signup")
    public String signup(@Valid UserCreateForm userCreateForm, BindingResult bindingResult) {
      if (bindingResult.hasErrors()) {
        return "signup_form";
      }
  
      if (!userCreateForm.getPassword1().equals(userCreateForm.getPassword2())) {
        bindingResult.rejectValue("password2", "passwordInCorrect",
          "2개의 패스워드가 일치하지 않습니다.");
        return "signup_form";
      }
  
      userService.create(userCreateForm.getUsername(),
        userCreateForm.getEmail(), userCreateForm.getPassword1());
  
      return "redirect:/";
    }
  }
  ```
5) 기능확인 
- DB에 잘 들어와있는 것을 확인 할 수 있다.
  <img width="1366" alt="image" src="https://github.com/user-attachments/assets/ceee8502-0d86-4489-8d58-66e03bac0360" />

## 3.7 로그인과 로그아웃 기능 구현하기 
### 로그인 기능 구현하기
1) 시큐리티에 로그인 경로 추가하기
- 이 경로는 로그인 폼에 대한 경로이고, 추후 권한 있는 사용자만 특정 페이지에 접근 하게 도와준다.

  ```java
  .formLogin((formLogin) -> formLogin
                  .loginPage("/user/login")
                  .defaultSuccessUrl("/"))
  ```

2) 로그인 컨트롤러 추가
- @GetMapping("/login")을 통해 /user/login URL로 들어오는 GET 요청을 이 메서드가 처리
  ```java
  @GetMapping("/login")
  public String login() {
    return "login_form";
  }
  ```

3) 로그인 html 작성
4) UserRepository 수정
- 회원정보를 DB에 저장 했으므로 h2 데이터베이스에서 유저 객체를 가져와 로그인을 처리한다.

  ```java
  @Repository
  public interface UserRepository extends JpaRepository<SiteUser, Long> {
    Optional<SiteUser> findByusername(String username);
  }
  ```

5) UserRole 파일 생성하기
- 스프링 시큐리티는 사용자 인증 후에 사용자에게 부여할 권한과 관련된 내용이 필요하다.

  ```java
  package com.ll.jump.user;
  
  import lombok.Getter;
  
  @Getter
  public enum UserRole {
    ADMIN("ROLE_ADMIN"),
    USER("ROLE_USER");
  
    UserRole(String value) {
      this.value = value;
    }
  
    private String value;
  }
  
  ```
6) UserSecurityService 서비스 생성하기
- 스프링 시큐리티가 로그인 시 사용할 UserSecurityService는 스프링 시큐리티가 제공하는 UserDetailsService 인터페이스를 구현(implements)해야 한다.
- loadUserByUsername 메서드는 사용자명(username)으로 스프링 시큐리티의 사용자(User) 객체를 조회하여 리턴하는 메서드이다.

  ```java
  @RequiredArgsConstructor
  @Service
  public class UserSecurityService implements UserDetailsService {
  
    private final UserRepository userRepository;
  
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
      Optional<SiteUser> _siteUser = this.userRepository.findByusername(username);
      if (_siteUser.isEmpty()) {
        throw new UsernameNotFoundException("사용자를 찾을수 없습니다.");
      }
      SiteUser siteUser = _siteUser.get();
      List<GrantedAuthority> authorities = new ArrayList<>();
      if ("admin".equals(username)) {
        authorities.add(new SimpleGrantedAuthority(UserRole.ADMIN.getValue()));
      } else {
        authorities.add(new SimpleGrantedAuthority(UserRole.USER.getValue()));
      }
      return new User(siteUser.getUsername(), siteUser.getPassword(), authorities);
    }
  }
  
  ```
- loadUserByUsername 메서드는 사용자명으로 SiteUser 객체를 조회하고
- 만약 사용자명에 해당하는 데이터가 없을 경우에는 UsernameNotFoundException을 발생시킨다
- 사용자명이 ‘admin’인 경우에는 ADMIN 권한(ROLE_ADMIN)을 부여하고 그 이외의 경우에는 USER 권한(ROLE_USER)을 부여한다.
- User 객체를 생성해 반환하는데, 이 객체는 스프링 시큐리티에서 사용하며 User 생성자에는 사용자명, 비밀번호, 권한 리스트가 전달된다.
- loadUserByUsername 메서드에 의해 리턴된 User 객체의 비밀번호가 사용자로부터 입력받은 비밀번호와 일치하는지를 검사하는 기능을 내부에 가지고 있다.

7) 스프링 시큐리티 설정 수정하기
- AuthenticationManager는 스프링 시큐리티의 인증을 처리한다.
-  UserSecurityService와 PasswordEncoder를 내부적으로 사용하여 인증과 권한 부여 프로세스를 처리한다.

  ```java
  @Bean
      AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
          return authenticationConfiguration.getAuthenticationManager();
      }
  ```

8) 로그인 화면 수정하기 
9) 로그아웃 뱃지 변환하기 
- navbar.html을 수정한다.

  ```html
  <a class="nav-link" sec:authorize="isAnonymous()" th:href="@{/user/login}">로그인</a>
  <a class="nav-link" sec:authorize="isAuthenticated()" th:href="@{/user/logout}">로그아웃</a>
  ```

### 로그아웃 기능 구현하기
1) SecurityConfig 추가하기
- SecurityConfig에 로그아웃 관련 설정을 추가한다. 

  ```java
  .logout((logout) -> logout
          .logoutRequestMatcher(new AntPathRequestMatcher("/user/logout"))
          .logoutSuccessUrl("/")
          .invalidateHttpSession(true))
  ```

## 3.8 작성자 항목 추가하기 
### 작성자 추가하기
1) 질문,답변 엔티티 수정
- @ManyToOne 어노테이션을 적용하여 DB데이터 구조를 수정한다. 
  ```java
  @ManyToOne
      private SiteUser author;
  ```

2) 답변 컨트롤러와 서비스 업데이트하기
- 답변을 저장할때, 사용자 정보도 저장 할 수 있도록 controller에 매개변수 principal을 추가한다. 
- 로그인한 사용자 정보는 Principal객체를 통해 알 수 있다.

    ```java
    public class AnswerController {

      private final QuestionService questionService;
      private final AnswerService answerService;
      private final UserService userService;

      @PostMapping("/create/{id}")
      public String createAnswer(Model model, @PathVariable("id") Integer id, 
              @Valid AnswerForm answerForm, BindingResult bindingResult, Principal principal) {
          Question question = this.questionService.getQuestion(id);
          SiteUser siteUser = this.userService.getUser(principal.getName());
          if (bindingResult.hasErrors()) {
              model.addAttribute("question", question);
              return "question_detail";
          }
          this.answerService.create(question, answerForm.getContent(), siteUser);
          return String.format("redirect:/question/detail/%s", id);
      }
   }
  ```
- 답변 내용을 저장할 때 글쓴이 데이터도 저장할 수 있도록 AnswerService를 수정

  ```java
  public Answer create(Question question, String content, SiteUser author) {
    Answer answer = new Answer();
    answer.setContent(content);
    answer.setCreateDate(LocalDateTime.now());
    answer.setQuestion(question);
    answer.setAuthor(author);
    this.answerRepository.save(answer);
    return answer;
  }
  ```

3) 질문 도메인 수정
- 질문도 답변과 같은 방법으로 적용한다. 

4) 로그인 페이지 전환
- 권한 없는 사용자가 접근을 하면 500 에러가 발생한다. 
- 스프링 시큐리티에서 권한이 없으면 로그인 페이지로 변환하는 어노테이션을 추가해야한다. 
- 로그인이 필요한 기능에 ``@PreAuthorize("isAuthenticated()")`` 어노테이션을 붙이고 
- 설정에서는 ``@EnableMethodSecurity(prePostEnabled = true)``를 붙여 PreAuthorize를 활성화 해준다. 

### 답변작성 막아두기 
- 로그아웃 상태에서 답변을 작성하지 못하도록 question_detail.html을 수정한다. 

  ```html
  <textarea sec:authorize="isAnonymous()" disabled th:field="*{content}" class="form-control" rows="10"></textarea>
  <textarea sec:authorize="isAuthenticated()" th:field="*{content}" class="form-control" rows="10"></textarea>
  ```
### 질문 목록에 작성자 표시하기 
- 질문 목록 템플릿인 question_list.html에 글쓴이를 추가

  ```html
  <tr class="text-center">
      <th>번호</th>
      <th style="width:50%">제목</th>
      <th>글쓴이</th>
      <th>작성일시</th>
  </tr>
  
  <!--생략-->
  
  <td><span th:if="${question.author != null}" th:text="${question.author.username}"></span></td>
  ```
### 게시글 상세에 작성자 추가 
- 질문 상세 템플릿인 question_detail.html을 수정

  ```html
  <div class="mb-2">
      <span th:if="${question.author != null}" th:text="${question.author.username}"></span>
  </div>
  
  <!--생략-->
  
  <div class="mb-2">
      <span th:if="${answer.author != null}" th:text="${answer.author.username}"></span>
  </div>
  ```

## 3.9 수정과 삭제 기능 추가 
### 수정 일시 추가
- entity 객체(question, answer)에 수정일시를 추가한다.
  
  ```java
  private LocalDateTime modifyDate;
  ```
### 질문 수정 기능 추가
1) 수정 버튼 만들기 
- question_detail에 버튼을 추가한다. 
- 버튼이 로그인한 사용자와 글쓴이가 동일할 경우에만 노출되도록 #authentication.getPrincipal().getUsername() == question.author.username을 적용

  ```html
  <div class="my-3">
      <a th:href="@{|/question/modify/${question.id}|}" class="btn btn-sm btn-outline-secondary"
          sec:authorize="isAuthenticated()"
          th:if="${question.author != null and #authentication.getPrincipal().getUsername() == question.author.username}"
          th:text="수정"></a>
  </div>
  ```
2) 컨트롤러 수정
- @{/question/modify/${question.id}} 링크가 추가되었으므로 이 링크가 동작할 수 있도록 질문 컨트롤러를 다음과 같이 수정
- questionForm 객체에 id값으로 조회한 질문의 제목(subject)과 내용(object)의 값을 담아서 템플릿으로 전달

  ```java
    @PreAuthorize("isAuthenticated()")
    @GetMapping("/modify/{id}")
    public String questionModify(QuestionForm questionForm, @PathVariable("id") Integer id, Principal principal) {
      Question question = this.questionService.getQuestion(id);
      if(!question.getAuthor().getUsername().equals(principal.getName())) {
        throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "수정권한이 없습니다.");
      }
      questionForm.setSubject(question.getSubject());
      questionForm.setContent(question.getContent());
      return "question_form";
    }
  
    @PreAuthorize("isAuthenticated()")
    @PostMapping("/modify/{id}")
    public String questionModify(@Valid QuestionForm questionForm, BindingResult bindingResult,
        Principal principal, @PathVariable("id") Integer id) {
      if (bindingResult.hasErrors()) {
        return "question_form";
      }
      Question question = this.questionService.getQuestion(id);
      if (!question.getAuthor().getUsername().equals(principal.getName())) {
        throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "수정권한이 없습니다.");
      }
      this.questionService.modify(question, questionForm.getSubject(), questionForm.getContent());
      return String.format("redirect:/question/detail/%s", id);
  }
  ```
3) question_form 수정
- ``<form>`` 태그의 th:action 속성을 삭제해야 한다.
- th:action 속성을 삭제하면 CSRF값이 자동으로 생성되지 않아서 CSRF값을 설정하기 위해 hidden 형태로 input 요소를 이와 같이 작성하여 추가

  ```html
  <form th:object="${questionForm}" method="post">
    <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}" />
    <!--생략-->
  </form>
  ```
  
4) Service 수정
- 수정된 질문이 서비스를 통해 처리될 수 있도록 QuestionService를 다음과 같이 수정

```java
public void modify(Question question, String subject, String content) {
    question.setSubject(subject);
    question.setContent(content);
    question.setModifyDate(LocalDateTime.now());
    this.questionRepository.save(question);
}
```

### 질문 삭제 기능
1) 버튼 만들기 
- 사용자와 작성자가 동일하면 삭제 기능을 활성화 한다.
- [삭제] 버튼은 [수정] 버튼과는 달리 href 속성값을 javascript:void(0)로 설정하고 삭제를 실행할 URL을 얻기 위해 th:data-uri 속성을 추가한 뒤, [삭제] 버튼을 클릭하는 이벤트를 확인하기 위해 class 속성에 delete 항목을 추가했다.

  > data-uri 속성에 설정한 값은 클릭 이벤트 발생 시 별도의 자바스크립트 코드에서 this.dataset.uri를 사용하여 그 값을 얻어 실행할 수 있다.

  ```html
  <a href="javascript:void(0);" th:data-uri="@{|/question/delete/${question.id}|}"
   class="delete btn btn-sm btn-outline-secondary" sec:authorize="isAuthenticated()"
   th:if="${question.author != null and #authentication.getPrincipal().getUsername() == question.author.username}"
   th:text="삭제"></a>
  ```
  
2) Service와 Controller 수정
- service 수정
```java
public void delete(Question question) {
    this.questionRepository.delete(question);
}
```
- Controller 수정
```java
@PreAuthorize("isAuthenticated()")
@GetMapping("/delete/{id}")
public String questionDelete(Principal principal, @PathVariable("id") Integer id) {
    Question question = this.questionService.getQuestion(id);
    if (!question.getAuthor().getUsername().equals(principal.getName())) {
        throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "삭제권한이 없습니다.");
    }
    this.questionService.delete(question);
    return "redirect:/";
}
```
### 답변 수정 및 삭제
- 질문 수정 및 삭제와 동일한 구조를 가진다. 
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/585f34d)

### 수정 일시 표시하기 
- 수정일시는 entity에 추가된 modifyDate를 출력하면된다.
  ```html
  <div th:if="${question.modifyDate != null}" class="badge bg-light text-dark p-2 text-start mx-3">
      <div class="mb-2">modified at</div>
      <div th:text="${#temporals.format(question.modifyDate, 'yyyy-MM-dd HH:mm')}"></div>
  </div>
  ```
#### 참고 : 3.10~11 기능들은 기존 활용한 기술의 반복으로 수정된 코드만 작성
## 3.10 추천기능 추가하기
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/2b0795f)

## 3.11 앵커 기능 추가하기 
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/03ce425)

## 3.13 검색기능 추가하기 
### JPA의 Specification 인터페이스 사용하기
- sql의 검색 query

  ```sql
  select
      distinct q.id,
      q.author_id,
      q.content,
      q.create_date,
      q.modify_date,
      q.subject 
  from question q 
  left outer join site_user u1 on q.author_id=u1.id 
  left outer join answer a on q.id=a.question_id 
  left outer join site_user u2 on a.author_id=u2.id 
  where
      q.subject like '%스프링%' 
      or q.content like '%스프링%' 
      or u1.username like '%스프링%' 
      or a.content like '%스프링%' 
      or u2.username like '%스프링%' 
  ```
- JPA의 Specification

  ```java
  private Specification<Question> search(String kw) {
          return new Specification<>() {
              private static final long serialVersionUID = 1L;
              @Override
              public Predicate toPredicate(Root<Question> q, CriteriaQuery<?> query, CriteriaBuilder cb) {
                  query.distinct(true);  // 중복을 제거 
                  Join<Question, SiteUser> u1 = q.join("author", JoinType.LEFT);
                  Join<Question, Answer> a = q.join("answerList", JoinType.LEFT);
                  Join<Answer, SiteUser> u2 = a.join("author", JoinType.LEFT);
                  return cb.or(cb.like(q.get("subject"), "%" + kw + "%"), // 제목 
                          cb.like(q.get("content"), "%" + kw + "%"),      // 내용 
                          cb.like(u1.get("username"), "%" + kw + "%"),    // 질문 작성자 
                          cb.like(a.get("content"), "%" + kw + "%"),      // 답변 내용 
                          cb.like(u2.get("username"), "%" + kw + "%"));   // 답변 작성자 
              }
          };
      }
  ```
> q: Root 자료형으로, 즉 기준이 되는 Question 엔티티의 객체를 의미하며 질문 제목과 내용을 검색하기 위해 필요하다.<br>
>  u1: Question 엔티티와 SiteUser 엔티티를 아우터 조인(여기서는 JoinType.LEFT로 아우터 조인을 적용한다.)하여 만든 SiteUser 엔티티의 객체이다. Question 엔티티와 SiteUser 엔티티는 author 속성으로 연결되어 있어서 q.join("author")와 같이 조인해야 한다. u1 객체는 질문 작성자를 검색하기 위해 필요하다.<br>
>  a: Question 엔티티와 Answer 엔티티를 아우터 조인하여 만든 Answer 엔티티의 객체이다. Question 엔티티와 Answer 엔티티는 answerList 속성으로 연결되어 있어서 q.join("answerList")와 같이 조인해야 한다. a 객체는 답변 내용을 검색할 때 필요하다.<br>
>  u2: 바로 앞에 작성한 a 객체와 다시 한번 SiteUser 엔티티와 아우터 조인하여 만든 SiteUser 엔티티의 객체로 답변 작성자를 검색할 때 필요하다.<br>
  
- 두개의 코드는 동일한 기능을 한다.
- 이 기능을 적용한 코드는 다음과 같다.
- [Github](https://github.com/kknaks/likeLion_mystudy/commit/e418493)

