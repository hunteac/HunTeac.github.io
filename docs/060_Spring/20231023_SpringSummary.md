---
layout: default
title: SpringSummary
parent: Spring
nav_order: 10
---

# BackEnd

## Spring 프레임워크

### 프레임워크

- 애플리케이션 개발 시 반복적으로 사용되는 공통적인 부분을 미리 구성해 놓은 라이브러리의 집합이다.
- 프레임워크는 개발자가 특정 규약 또는 구조 내에서 개발을 진행하도록 도와주어, 빠르고 안정적인 개발을 지원한다.
- e.g. (자동차 제작) 프레임워크: 차체(프레임), 기본적인 틀, 개발자는 그 안에 엔진, 좌석, 핸들 등을 설치한다.
- 종류: Spring, Django, Ruby on Rails 등

### IoC (제어의 역전)

- **정의**: 전통적으로 프로그램의 흐름을 개발자가 제어했으나, Spring에서는 프레임워크가 이를 대신 한다.
- **예시**: 사용자가 직접 `new` 키워드로 객체를 생성하는 대신 Spring 컨테이너가 객체를 생성 및 관리한다.

### Spring Bean

- **POJO (Plain Old Java Object)**:
  - **정의**: 특정 규약 또는 인터페이스에 의존하지 않은 순수한 자바 객체
  - **예시**: `public class Person { private String name; }`
- **Bean 생명주기**:
  - Bean은 생성, 초기화, 사용, 소멸의 단계를 거친다.
  - `@PostConstruct`, `@PreDestroy` 어노테이션을 사용하여 초기화 및 소멸 시 특정 로직을 실행할 수 있다.
- **등록방식**:
  - **Annotation**: 클래스 위에 `@Component`, `@Service` 등의 어노테이션을 사용
  - **XML**: `<bean class="..." id="..."/>` 형태로 등록
  - **Java**: `@Configuration` 어노테이션과 함께 `@Bean`을 사용해 Java 코드 내에서 Bean을 등록

### DI (의존성 주입)

- **정의**: 객체와 객체 사이의 의존 관계를 Spring 컨테이너가 대신 설정해주는 것
- **예시**: 아래 예시 코드에서 `OrderService`는 `ProductService`에 의존하고 있다. 해당 의존성은 생성자를 통해 주입된다.

```java
    @Service
    public class OrderService {
        private final ProductService productService;

        @Autowired
        public OrderService(ProductService productService) {
            this.productService = productService;
        }
    }
```

- `@Qualifier`: 여러 Bean 중 특정 Bean을 지정하여 주입하고 싶을 때 사용

### AOP (관점지향 프로그래밍)

- **Aspect**: 횡단 관심사를 모듈화한 코드 (예: 로깅, 트랜잭션 관리)
  - 횡단 관심사(Crosscutting Concerns): 애플리케이션 내 여러 핵심 비즈니스 로직들에 걸쳐서 실행되어야 하는 동작들
- **PointCut**: 어떤 JoinPoint (메소드 실행 지점)에서 Aspect가 동작할지 정의
- **JoinPoint**: 프로그램 실행 중 Aspect의 코드가 삽입될 수 있는 지점
- **Target**: Aspect의 코드가 적용되는 객체
- **PointCut execution작성 방법**: `"execution([수식어] 리턴타입 [클래스이름].메소드이름(파라미터))"`
- **Advice 타입**:
  - **before**: 메소드 실행 전에 동작
  - **after**: 메소드 실행 후에 동작 (예외 발생 여부와 상관없이)
  - **after returning**: 메소드가 성공적으로 실행된 후에 동작
  - **after throwing**: 메소드에서 예외가 발생했을 때 동작

### Maven과 pom.xml

- **Maven**: Java 프로젝트의 빌드, 관리 도구
- **pom.xml**: Maven 설정 파일. 의존성, 플러그인, 프로젝트 정보 등을 정의
- **예시**: 아래 예시 코드는 `pom.xml`에 Spring Core 라이브러리를 추가하는 방법이다.

```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.0.0.RELEASE</version>
    </dependency>
```

---

### Spring MVC 동작의 흐름

- **DispatcherServlet**: 클라이언트의 요청을 최초로 받는 프론트 컨트롤러. 적절한 컨트롤러에 요청을 전달하며, 응답을 클라이언트에 반환한다.
- **HandlerMapper**: 클라이언트의 요청 URL을 기반으로 해당 요청을 처리할 컨트롤러를 찾아주는 컴포넌트.
- **Controller**: 실제 요청을 처리하는 로직이 들어있는 클래스. `@Controller`로 정의
- **Service**: 비즈니스 로직을 수행하는 계층. `@Service`로 정의
- **Dao (Data Access Object)**: 데이터베이스와의 통신을 담당하는 계층
- **ModelAndView**: 컨트롤러가 처리한 결과(데이터)와 뷰 이름을 함께 반환하는 객체
- **ViewResolver**: 컨트롤러에서 반환된 뷰 이름을 실제 뷰로 변환해주는 컴포넌트. 예를 들면, 뷰 이름 "home"을 "/WEB-INF/views/home.jsp"로 변환한다. **prefix**: /WEB-INF/views/, **suffix**: .jsp
- **Controller에서 Return하는 String의 의미**:
  - "viewName": 해당 이름의 뷰로 forward
  - "redirect:/url": 주어진 URL로 redirect
  - **예시**:
    - `return "home";`은 `home` 뷰로 forward
    - `return "redirect:/index";`은 `/index`로 redirect

### Filter

- 웹 애플리케이션에서 요청과 응답을 가로채 처리하는 컴포넌트. 주로 인코딩, 로깅, 인증 등을 처리

### Interceptor

- **preHandle**: 컨트롤러 실행 전에 호출. false 반환 시 나머지 실행 중지
- **postHandle**: 컨트롤러 실행 후, 뷰 반환 전에 호출
- **afterCompletion**: 요청 처리 완료 후 호출
- 비즈니스 로직 처리 전후에 특별한 작업이 필요할 때 사용

### Spring의 각종 Annotation

- **@Component**: 일반적인 컴포넌트 클래스. Spring이 관리하는 빈으로 등록
- **@Controller**: MVC 패턴의 컨트롤러로 정의
- **@Service**: 서비스 계층의 클래스로 정의
- **@Configuration**: Spring 설정 클래스로 정의
- **@Bean**: 메소드 위에 사용하여, 해당 메소드가 반환하는 객체를 Spring 컨테이너에 빈으로 등록
- **@RequestParam**: HTTP 파라미터를 메소드 파라미터로 바인딩
- **@ModelAttribute**: 모델 데이터를 객체로 바인딩. 폼 데이터와 객체 바인딩에 유용하다.

### XML 설정 파일들의 역할

- **root-context.xml**: 애플리케이션 전체 설정 정보를 담당. 주로 데이터 소스, Service, Dao 빈 정의.
- **servlet-context.xml**: 웹 관련 설정 정보를 담당. 주로 ViewResolver, HandlerMapper, Controller 빈 정의.
- **web.xml**: 웹 애플리케이션 전체의 설정 파일. 서블릿 정의, 필터 설정, 리스너 설정 등

---

### REST API

- HTTP Request에서 데이터 받아오는 어노테이션:
  - **@RequestBody**: 클라이언트로부터 전송된 JSON 데이터를 Java Object로 변환하여 받아온다.
    - 예시: `public ResponseEntity<String> update(@RequestBody User user) {...}`
  - **@RequestParam**: URL의 query parameter 값을 가져온다.
    - 예시: `/users?userId=123` -> `public String getUser(@RequestParam String userId) {...}`
  - **@PathVariable**: URL의 일부를 변수로 받아온다.
    - 예시: `/users/123` -> `public String getUserDetail(@PathVariable String id) {...}`
- REST API에서 사용하는 주요 어노테이션:
  - **@RestController**: `@Controller`와 유사하지만, 반환 값이 자동으로 JSON 형식으로 변환된다.
    - 예시: `@RestController public class UserController {...}`
  - **@PathVariable**: URL 경로에서 특정 값을 추출할 때 사용한다.
    - 예시: `/users/{id}` -> `public User getUser(@PathVariable Long id) {...}`
- CORS 관련 어노테이션:
  - **@CrossOrigin**: 해당 메소드나 클래스에 적용하여, 특정 origin(출처)에서의 요청을 허용한다.
    - 예시: `@CrossOrigin(origins = "<http://example.com>")`로 설정하면 "[http://example.com](http://example.com/)"에서의 요청만 허용된다.

### 파일 업로드

- **Multipart 객체**: 클라이언트에서 업로드한 파일을 담고 있는 객체. Spring에서는 MultipartFile 클래스를 통해 파일을 처리한다.
- 예시:

```java
    public String handleFileUpload(@RequestParam("file") MultipartFile file) {
        String name = file.getOriginalFilename();
        // ... 파일 처리 로직
        return "success";
    }
```

- **Form 태그에서 필요한 attribute**: 파일을 업로드 할 때, `<form>` 태그의 `enctype` 속성을 `multipart/form-data`로 설정해야 한다. 이렇게 설정하면, 폼을 통해 제출된 데이터가 여러 부분으로 나누어져서 서버로 전송되며, 이 중 하나가 파일 데이터다.
- 예시:

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <input type="file" name="file" />
  <input type="submit" value="Upload" />
</form>
```

---

### Filter, AOP, Interceptor의 차이

- **Filter**:
  - 웹 애플리케이션에서 요청 및 응답에 관한 전/후처리를 담당한다.
  - 주로 인코딩, 보안(인증 및 권한), 로깅 등의 기능을 수행한다.
  - 서블릿 명세의 일부로, 모든 웹 애플리케이션에서 사용할 수 있다.
  - **예시**: 사용자가 로그인한 상태인지 확인하는 Filter.
- **AOP(Aspect-Oriented Programming)**:
  - 관점지향 프로그래밍으로, 특정 로직을 핵심 로직에서 분리하여 관점(Aspect)으로 모듈화하는 것을 의미한다.
  - 로깅, 트랜잭션 관리, 보안 등 여러 모듈에서 공통으로 필요한 기능을 분리/재사용하기 좋다.
  - Spring에서는 `@Aspect`를 사용하여 AOP를 구현한다.
  - **예시**: 모든 메소드 호출 시 실행 시간을 로깅하는 Aspect.
- **Interceptor**:

  - Spring MVC에서 제공하는 기능으로, 요청 처리의 전/후에 특정 작업을 수행할 때 사용된다.
  - Controller로의 요청을 가로채거나, Controller에서의 응답을 가로채는 역할을 한다.
  - 주로 로깅, 인증 및 권한 확인, 프로그램 실행 시간 계산 등에 사용된다.
  - **예시**: 특정 페이지에 대한 접근 권한을 확인하는 Interceptor.

- 세 가지 모두 비즈니스 로직의 전/후에 실행되는 로직을 분리하여 관리하는 것에 중점을 둔다. 하지만 사용하는 범위, 목적, 그리고 활용 방식에 따라 각각 다른 특징을 지니고 있다.
