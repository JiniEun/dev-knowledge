# dev-knowledge

백엔드 개발 지식을 정리하는 저장소
학습한 개념, 트러블슈팅 경험, 참고 자료 등을 마크다운으로 기록.

📖 위키로 보기: [jinieun.github.io/dev-knowledge](https://jinieun.github.io/dev-knowledge/)

## 목적

- 학습한 내용을 나만의 언어로 정리하여 복습에 활용
- 실무/프로젝트에서 겪은 문제와 해결 과정 기록
- 필요할 때 빠르게 찾아볼 수 있는 개인 레퍼런스 구축

## 구조

```text
dev-knowledge/
├── README.md
└── docs/                        # GitHub Pages 소스 (Settings → Pages → /docs)
    ├── index.md                 # 위키 홈페이지
    ├── java/index.md            # Java 언어, JVM
    ├── spring/index.md          # Spring / Spring Boot
    ├── database/index.md        # DB, 쿼리 최적화, 트랜잭션
    ├── network/index.md         # HTTP, TCP/IP, OS 등 CS 기초
    ├── infra/index.md           # Docker, CI/CD, 클라우드
    └── troubleshooting/index.md # 문제 해결 기록
```

## 카테고리

- [Java](docs/java/index.md) — Java 언어, JVM
- [Spring](docs/spring/index.md) — Spring / Spring Boot
- [Database](docs/database/index.md) — DB, 쿼리 최적화, 트랜잭션
- [Network](docs/network/index.md) — HTTP, TCP/IP, OS 등 CS 기초
- [Infra](docs/infra/index.md) — Docker, CI/CD, 클라우드
- [Troubleshooting](docs/troubleshooting/index.md) — 문제 해결 기록

각 카테고리의 index.md에서 해당 주제의 개별 문서 목록을 관리.
새 문서를 추가하면 해당 카테고리 index.md에만 링크 추가.

## 작성 규칙

- 새 문서는 `docs/<카테고리>/` 아래에 만들고, 파일 상단에 Jekyll 프런트매터를 넣어야 위키 페이지로 렌더링됩니다.

  ```yaml
  ---
  title: 문서 제목
  ---
  ```

- 파일명은 주제를 알아볼 수 있도록 영문 소문자와 하이픈(`-`)으로 작성합니다.
- 문서 상단에 작성일 또는 최종 수정일을 남깁니다.
- 출처가 있는 경우 하단에 참고 링크를 남깁니다.
