---
title: "REST와 RESTful"
description: REST와 RESTful의 개념
---

## REST란

REST(REpresentational State Transfer)는 Roy Fielding이 2000년 박사 학위 논문에서 제안한 **아키텍처 스타일**이다. 특정 기술이나 프로토콜이 아니라, "자원을 어떻게 식별하고 어떻게 다룰지"에 대한 설계 원칙이다.

- **자원(Resource)**: 서버가 관리하는 데이터. URI로 식별한다. (예: `/users/1`)
- **행위(Verb)**: 자원에 무엇을 할지. HTTP 메서드로 표현한다. (GET, POST, PUT, DELETE 등)
- **표현(Representation)**: 자원을 주고받을 때의 형식. JSON, XML 등.

즉 "URI는 자원(명사)만 나타내고, 그 자원에 대한 행위는 HTTP 메서드(동사)로 표현한다"는 게 핵심이다. HTTP 프로토콜 인프라를 그대로 활용하기 때문에 특정 기술이나 언어에 종속되지 않고, 개발자들 사이에 널리 쓰이는 형식이다.

## REST의 특징

- **Server-Client (클라이언트-서버 구조)**: 클라이언트는 자원을 요청하고, 서버는 자원을 가지고 응답한다. 역할이 명확히 분리돼 있어 각자 독립적으로 구현/배포할 수 있다.
- **Stateless (무상태)**: 서버가 클라이언트의 상태(context)를 저장하지 않는다. 매 요청에 필요한 정보가 다 담겨 있어야 한다.
- **Cacheable (캐시 가능)**: HTTP 표준을 그대로 쓰기 때문에 `Last-Modified`, `E-Tag` 같은 HTTP의 캐싱 기능을 그대로 활용할 수 있다.
- **Layered System (계층화)**: 클라이언트는 REST API 서버만 호출하면 되고, 그 뒤에 보안/로드밸런싱/인증 등을 담당하는 계층을 자유롭게 추가할 수 있다. 클라이언트 입장에서는 서버가 여러 계층으로 구성돼 있는지 알 필요가 없다.
- **Uniform Interface (인터페이스 일관성)**: URI로 지정한 자원에 대한 조작을, 통일되고 한정된 인터페이스(HTTP 메서드)로 수행한다. 특정 언어나 기술에 종속되지 않는다.

## REST의 장단점

### 장점

- HTTP 인프라를 그대로 사용하므로 별도 인프라 구축이 필요 없음
- 클라이언트-서버 역할이 명확히 분리되어 독립적으로 구현 가능
- HTTP 표준을 따르는 모든 플랫폼에서 사용 가능

### 단점

- 사용할 수 있는 메서드(GET/POST/PUT/PATCH/DELETE 등)가 한정적
- 명확한 표준이 존재하지 않아, 프로젝트/개발자마다 해석이 조금씩 다를 수 있음

## REST vs RESTful

- **REST**: 위에서 설명한 아키텍처 스타일 그 자체(원칙/이론)
- **RESTful**: REST 원칙을 잘 따르고 있는 API/시스템을 가리키는 형용사

실무에서 "REST API"와 "RESTful API"는 거의 같은 의미로 섞어 쓰지만, 엄밀히는 REST 원칙을 얼마나 잘 지켰는지에 따라 "RESTful하다/아니다"를 구분할 수 있다. REST의 비공식적인 구현 가이드를 RESTful이라고 부르기도 하는데, 표준이 없다 보니 개발자마다 "RESTful하다"고 생각하는 기준이 조금씩 다를 수 있다.

RESTful하게 설계하는 근본적인 목적은 성능 향상이 아니라, **일관된 컨벤션을 통해 API의 이해도와 호환성을 높이는 것**이다. 그래서 성능이 훨씬 중요한 상황이라면 굳이 RESTful한 API를 고집할 필요는 없다.

### RESTful하지 못한 예시

- CRUD 기능을 전부 `POST` 하나로만 처리하는 API
- URI 경로에 자원/ID 외의 동작이 들어가는 경우 (예: `/students/updateName`)

## Richardson 성숙도 모델 (REST를 얼마나 지켰는가)

| 단계 | 내용 |
| --- | --- |
| Level 0 | HTTP를 단순 전송 수단으로만 사용. 하나의 URI, 대부분 POST 하나로 모든 요청 처리 (RPC 스타일) |
| Level 1 | 자원별로 URI를 분리 (`/users`, `/orders`) |
| Level 2 | HTTP 메서드를 의미에 맞게 사용 (GET=조회, POST=생성, PUT/PATCH=수정, DELETE=삭제) |
| Level 3 | HATEOAS 적용 — 응답에 다음 행동 가능한 링크를 포함 |

실무에서 만드는 API 대부분은 **Level 2**에서 멈춘다. HATEOAS(Level 3)까지 지키는 API는 드물고, "Level 2 정도면 RESTful하다"고 실용적으로 부르는 경우가 많다.

## URI 설계 규칙

- URI에는 **명사**만 사용, 동사는 HTTP 메서드로 표현
  - ❌ `GET /getUsers`
  - ✅ `GET /users`
- 슬래시(`/`)는 계층 관계를 나타내는 데 사용: `GET /users/{id}/orders` (특정 유저의 주문 목록)
- URI 마지막 문자로 슬래시를 포함하지 않음 (`/users/` ❌ → `/users` ✅)
- 하이픈(`-`)은 가독성을 위해 사용하고, 밑줄(`_`)은 사용하지 않음
- URI 경로에는 소문자를 사용 (대문자 지양)
- 파일 확장자(`.json` 등)를 URI에 넣지 않고, `Accept` 헤더로 응답 형식 협상
- 리소스 간 연관관계가 있는 경우 `/리소스명/리소스ID/연관리소스명` 형태로 표현 (예: `/users/1/orders`)

## HTTP 메서드 ↔ CRUD 매핑

| HTTP 메서드 | 의미 | 멱등성(Idempotent) |
| --- | --- | --- |
| GET | 조회 | O |
| POST | 생성 | X |
| PUT | 전체 수정(치환) | O |
| PATCH | 부분 수정 | X (구현에 따라 다름) |
| DELETE | 삭제 | O |

멱등성은 "같은 요청을 여러 번 보내도 결과가 같은가"를 뜻한다. `PUT`으로 같은 데이터를 두 번 보내면 결과는 동일하지만, `POST`로 생성 요청을 두 번 보내면 리소스가 두 개 생길 수 있다.

## 헷갈리기 쉬운 포인트

- **PUT vs PATCH**: `PUT`은 리소스 전체를 새 값으로 치환(누락된 필드는 null/기본값 처리), `PATCH`는 명시한 필드만 부분적으로 수정.
- **상태 코드를 의미 있게 쓰기**: 무조건 200 대신 생성은 `201 Created`, 삭제 성공 후 반환할 데이터가 없으면 `204 No Content`, 잘못된 요청은 `400`, 존재하지 않는 자원은 `404`, 충돌(중복 등)은 `409`처럼 상황에 맞게 사용.
- **HATEOAS는 실무에서 드묾**: 응답에 `_links` 같은 하이퍼미디어를 넣는 설계는 크고 복잡해서, 대부분의 API는 Level 2에서 실용적으로 멈춘다.

## Spring에서의 구현

Spring MVC에서 RESTful API를 만들 때 쓰는 어노테이션이 `@RestController`다. 클래스 전체가 뷰가 아니라 데이터(자원의 표현)를 응답하도록 강제하는 것이 REST의 "표현(Representation)" 개념과 맞닿아 있다. 자세한 내용은 [@Controller vs @RestController](../spring/controller-vs-restcontroller.md) 참고.
