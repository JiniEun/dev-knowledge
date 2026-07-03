# 작성 가이드

`docs/` 아래에 새 카테고리·문서를 추가할 때 참고하는 상세 규칙입니다. 저장소 전체 구조는 [README.md](README.md) 참고.

## 새 카테고리 추가

1. `docs/<category>/` 폴더 생성
2. `docs/<category>/index.md` 생성 (아래 "index.md 작성 규칙" 참고)
3. `docs/index.md`의 카테고리 목록에 링크 추가
4. 루트 `README.md`의 구조 다이어그램·카테고리 목록에도 반영

## 새 문서 추가

1. `docs/<category>/<파일명>.md` 생성 — 파일명은 영문 소문자와 하이픈(`-`)
2. 파일 상단에 프런트매터 작성 (아래 참고)
3. 해당 카테고리 `index.md`의 `## 문서 목록`에 링크 한 줄 추가

## Front matter (`title`)

파일 최상단에 아래 형식으로 작성합니다.

```yaml
---
title: 제네릭
---
```

- GitHub Pages는 기본적으로 `jekyll-titles-from-headings` 플러그인이 켜져 있어서, 프런트매터가 없으면 본문 첫 `#` 제목을 자동으로 페이지 타이틀로 씁니다. 다만 이 위키는 본문에 `# 제목`을 따로 쓰지 않고 프런트매터의 `title`만 쓰는 것으로 통일합니다 (본문에 `#`을 또 쓰면 테마가 타이틀을 두 번 보여줌).
- `description`도 선택적으로 추가할 수 있습니다. `jekyll-seo-tag`가 기본 활성화되어 있어 검색엔진 메타 태그로 쓰입니다.

  ```yaml
  ---
  title: 제네릭
  description: Java 제네릭의 타입 소거와 와일드카드 정리
  ---
  ```

## index.md 작성 규칙

- 카테고리 폴더의 `index.md`는 그 카테고리의 홈페이지입니다 (Jekyll이 `/database/index.md`를 `/database/` 경로로 매핑).
- 상단에 카테고리 소개를 1~2줄로 씁니다.
- 하단 `## 문서 목록`에 그 폴더 안 문서 링크를 사람이 직접 추가/관리합니다 (자동 생성 아님 — 새 문서 만들 때 여기 한 줄 추가하는 걸 잊지 않기).

## 링크 작성

- 같은 카테고리 안의 문서: 파일명만 상대경로로 — `[제네릭](generics.md)`
- 다른 카테고리로: 상위 폴더를 거쳐서 — `[Spring](../spring/index.md)`
- `.md` 확장자를 그대로 써도 됩니다. GitHub Pages의 `jekyll-relative-links` 플러그인이 빌드 시점에 실제 렌더링된 페이지로 자동 연결해줍니다.
- 루트 `README.md`는 Jekyll 빌드 대상이 아닌 순수 저장소 파일이라, 여기서 위키 문서를 링크할 땐 `docs/` 접두사를 붙여야 합니다 (예: `docs/java/index.md`).

## 코드블록 (신택스 하이라이팅)

언어를 명시하면 GitHub Pages 기본 하이라이터인 Rouge가 자동으로 강조해줍니다. 별도 설정 불필요.

````
```java
public class Foo {}
```
````

## 표 · 취소선 · 체크리스트 (GFM)

GitHub Pages는 `kramdown-parser-gfm`을 기본 사용해서 GitHub에서 쓰던 마크다운 문법을 그대로 쓸 수 있습니다.

```markdown
| 항목 | 설명 |
| --- | --- |
| A | 설명 A |

~~취소선~~

- [x] 완료된 항목
- [ ] 미완료 항목
```

## 로컬 미리보기 (선택)

Ruby/Bundler가 설치되어 있다면:

```bash
bundle exec jekyll serve --source docs
```

설치가 번거로우면 생략하고 push 후 GitHub Pages 빌드 결과로 확인해도 무방합니다.

## 디자인 — 커스텀 터미널 테마

GitHub 공식 테마(`theme:`)는 쓰지 않고, 직접 만든 다크 터미널 스타일을 씁니다. 관련 파일:

- `docs/_layouts/page.html` — 전체 레이아웃 (터미널 타이틀바 + 왼쪽 사이드바 + 본문). `jekyll-default-layout` 플러그인이 GitHub Pages에서 기본 활성화돼 있어서, 문서 파일에 `layout:` 프런트매터를 따로 안 써도 이 레이아웃이 자동 적용됩니다. 만약 어떤 페이지가 레이아웃 없이 렌더링되면 그 파일 프런트매터에 `layout: page`를 명시하세요.
- `docs/assets/css/style.css` — 색상/타이포그래피/코드 하이라이팅(Rouge) 전부 이 파일에 있습니다. `:root`의 CSS 변수(`--bg`, `--accent` 등)만 바꿔도 톤이 크게 바뀝니다.
- `docs/_data/nav.yml` — 사이드바에 표시되는 카테고리 순서/이름. 카테고리를 추가하면 여기도 한 줄 추가해야 사이드바에 뜹니다 (카테고리 안의 개별 문서 목록은 `site.pages`에서 자동으로 뽑아오므로 문서 추가 시 여기는 안 건드려도 됨).
- `docs/_config.yml`의 `baseurl: "/dev-knowledge"` — 프로젝트 페이지(`jinieun.github.io/dev-knowledge/`)라서 반드시 필요한 값입니다. 지우면 CSS/사이드바 링크가 전부 깨집니다.
