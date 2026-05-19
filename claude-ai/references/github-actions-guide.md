# GitHub Actions 자동 배포 설정 가이드

HTML 파일을 main 브랜치에 푸시하면 자동으로 목차를 재생성하고 GitHub Pages에 배포하는 파이프라인입니다.

---

## 전체 흐름

```
git push (main)
    → GitHub Actions 실행
    → python3 scripts/generate_index.py (목차 재생성)
    → GitHub Pages 배포
    → 웹 공개 완료
```

---

## 설정 단계

### 1단계 — 워크플로 파일 추가

레포에 `.github/workflows/deploy.yml` 파일을 아래 내용으로 생성하세요:

```yaml
name: Build & Deploy to GitHub Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - name: Generate index.html
        run: python3 scripts/generate_index.py
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: "."
      - id: deployment
        uses: actions/deploy-pages@v4
```

### 2단계 — 인덱스 생성 스크립트 추가

`scripts/generate_index.py` 파일을 생성하세요.
내용은 https://github.com/imuzikr/open-make-html/blob/main/github-actions/generate_index.py 를 그대로 복사하세요.

이 스크립트가 하는 일:
- 레포의 모든 `.html` 파일을 스캔 (index.html, design-system.html 제외)
- 각 파일의 `<title>`, `<meta name="description">`, 날짜 추출
- 카드 형태의 `index.html` 자동 생성
- 각 아티클에 "목록으로 돌아가기" 버튼 자동 주입

### 3단계 — GitHub Pages 소스 설정

1. 레포 → **Settings** → **Pages**
2. **Source** 를 **GitHub Actions** 로 변경

### 4단계 — 배포 환경 브랜치 허용

1. 레포 → **Settings** → **Environments**
2. **github-pages** 클릭
3. **Deployment branches** 에 **main** 추가

> 이 설정이 없으면 `Branch "main" is not allowed to deploy` 오류가 발생합니다.

---

## 폴더 구조 규칙

인덱스 생성기가 인식하는 폴더:

| 폴더 | 용도 |
|---|---|
| `articles/` | 아티클, 리포트, 설명 문서 |
| `specs/` | 명세서, 구현 계획서 |
| `reviews/` | 코드 리뷰, PR 문서 |
| `explore/` | 옵션 비교 문서 |
| `tools/` | 인터랙티브 도구, 슬라이드 |

파일명 형식: `YYYY-MM-DD-slug.html` (예: `2026-05-19-ontology.html`)

---

## 배포 후 URL

GitHub Pages URL 형식:
```
https://<username>.github.io/<repo-name>/
```

예: `https://imuzikr.github.io/my-html/`

---

## 주의사항

- `index.html`은 Actions가 자동 생성합니다 — 직접 편집하지 마세요.
- 목차 디자인을 바꾸려면 `scripts/generate_index.py`의 `build()` 함수를 수정하세요.
- `workflow_dispatch` 설정으로 Actions 탭에서 수동 실행도 가능합니다.
