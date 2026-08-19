---
title: "@Controller vs @RestController"
description: Spring MVC의 @Controller와 @RestController 차이 정리
---

## 핵심 차이

`@RestController`는 `@Controller` + `@ResponseBody`를 합쳐놓은 어노테이션이다. 반환값을 뷰(View)로 해석할지, HTTP 응답 바디에 그대로 직렬화할지가 핵심 차이다.

| 구분 | `@Controller` | `@RestController` |
| --- | --- | --- |
| 메서드 반환값 | 뷰 이름(String)으로 해석 → ViewResolver가 템플릿 렌더링 | HttpMessageConverter가 JSON/XML로 직렬화해 응답 바디에 씀 |
| 데이터 응답 | 메서드마다 `@ResponseBody` 명시 필요 | 클래스 전체에 암묵적으로 `@ResponseBody` 적용 |
| 주 용도 | 서버 사이드 렌더링(SSR), Thymeleaf/JSP 뷰 반환 | REST API, JSON 응답 |

`@RestController`는 단순히 "JSON을 응답하는 어노테이션"이 아니라, **RESTful 웹 서비스 API를 정의할 때 쓰는 어노테이션**이다. URI는 자원을 나타내고 HTTP 메서드로 행위를 표현하는 REST 원칙을, 컨트롤러가 뷰 대신 자원의 표현(JSON 등)을 직접 응답하도록 강제함으로써 뒷받침한다. REST 개념 자체는 [REST와 RESTful](../network/rest-restful.md) 문서에 따로 정리했다.

## 코드로 비교

`@Controller`만 쓰면 기본적으로 반환값을 뷰 이름으로 취급한다.

```java
@Controller
public class UserPageController {

    @GetMapping("/users/{id}")
    public String userPage(@PathVariable Long id, Model model) {
        model.addAttribute("user", userService.findById(id));
        return "users/detail"; // templates/users/detail.html 렌더링
    }

    @GetMapping("/users/{id}/json")
    @ResponseBody // 이 메서드만 JSON으로 응답하고 싶을 때
    public UserResponse userJson(@PathVariable Long id) {
        return userService.findById(id);
    }
}
```

`@RestController`는 클래스 전체가 데이터 응답 전용이라고 선언하는 것과 같다.

```java
@RestController
@RequestMapping("/api/users")
public class UserApiController {

    @GetMapping("/{id}")
    public UserResponse getUser(@PathVariable Long id) {
        return userService.findById(id); // 자동으로 JSON 직렬화
    }
}
```

## 내부 동작

- `@Controller`: `DispatcherServlet`이 반환된 문자열을 `ViewResolver`에 넘겨 실제 뷰(HTML 등)를 찾아 렌더링한다.
- `@ResponseBody`가 붙으면 `ViewResolver`를 거치지 않고, 등록된 `HttpMessageConverter`(기본적으로 Jackson의 `MappingJackson2HttpMessageConverter`)가 객체를 JSON으로 변환해 응답 바디에 직접 쓴다.
- `@RestController = @Controller + @ResponseBody`이므로, 클래스 안의 모든 핸들러 메서드가 이 변환 과정을 거친다.

## 헷갈리기 쉬운 포인트

### 1. `@RestController`에서 뷰 이름을 리턴하는 실수

`@RestController`가 붙은 클래스에서 뷰를 반환할 생각으로 문자열을 리턴하면, ViewResolver를 거치지 않고 **그 문자열 자체가 응답 바디**로 나간다.

```java
@RestController
public class MyController {

    @GetMapping("/home")
    public String home() {
        return "index"; // "index"라는 순수 텍스트가 그대로 응답됨 (뷰 렌더링 아님!)
    }
}
```

`@Controller`였다면 `templates/index.html`을 찾아 렌더링했겠지만, `@RestController`는 항상 `@ResponseBody`가 적용된 상태라 문자열을 응답 바디로 바로 써버린다. 뷰와 데이터 응답을 한 클래스에 섞으면 이런 실수가 나기 쉬우므로, 애초에 컨트롤러를 역할별로 분리하는 게 안전하다.

### 2. Content Negotiation (JSON이냐 XML이냐)

`HttpMessageConverter`는 요청의 `Accept` 헤더를 보고 응답 형식을 결정한다. Jackson(JSON)과 JAXB(XML) 관련 라이브러리가 클래스패스에 있으면, 클라이언트가 `Accept: application/xml`을 보냈을 때 같은 컨트롤러 메서드가 XML로도 응답할 수 있다 — 컨트롤러 코드 자체는 그대로고, 변환기 선택만 달라지는 것이다.

### 3. 상태 코드/헤더까지 제어하려면 `ResponseEntity`

`@RestController` 메서드가 객체를 그냥 리턴하면 상태 코드는 기본 200으로 고정된다. 상태 코드나 응답 헤더를 직접 다루고 싶으면 `ResponseEntity<T>`로 감싸서 리턴한다.

```java
@GetMapping("/{id}")
public ResponseEntity<UserResponse> getUser(@PathVariable Long id) {
    UserResponse user = userService.findById(id);
    return ResponseEntity.ok(user); // 201, 404 등 상황별 상태 코드도 지정 가능
}
```

### 4. 예외 처리: `@ExceptionHandler`의 뷰/데이터 분기도 똑같이 적용

`@ControllerAdvice`/`@ExceptionHandler`로 예외를 처리할 때도 같은 규칙이 적용된다. 뷰를 반환하는 전역 예외 처리기는 `@ControllerAdvice` + 메서드에 `@ResponseBody` 없이 뷰 이름을 반환하고, REST API 전역 예외 처리는 `@RestControllerAdvice`(= `@ControllerAdvice` + `@ResponseBody`)를 쓴다. `@Controller`/`@RestController`의 관계와 정확히 대응되는 구조다.

### 5. 트랜잭션·AOP와는 무관

`@RestController`는 응답 처리 방식(뷰 vs 바디 직렬화)만 결정할 뿐, 트랜잭션 경계나 다른 AOP 동작에는 전혀 영향을 주지 않는다. `@Transactional`은 서비스 계층에 붙이는 것이 원칙이고, 컨트롤러 어노테이션 선택과는 별개의 관심사다.

### 6. 도입 배경

`@RestController`는 Spring 4.0(2013)에서 추가됐다. 그전에는 REST API를 만들려면 클래스의 모든 메서드에 `@ResponseBody`를 일일이 붙여야 했는데, 그 반복을 없애기 위한 합성(composed) 어노테이션으로 나온 것이다.

## 언제 뭘 쓰나

- 화면(HTML)을 그대로 서버에서 렌더링해서 내려줘야 하면 → `@Controller`
- 프런트엔드(React/Vue 등)나 외부 클라이언트에 JSON API만 제공하면 → `@RestController`
- 한 프로젝트 안에서도 두 방식을 섞어 쓸 수 있다 (뷰 반환용 컨트롤러 + API 전용 컨트롤러 분리).
