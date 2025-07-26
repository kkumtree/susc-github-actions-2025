# 튜토리얼

## 원격 저장소 추가

- SOURCE CONTROL > Remote > Add Remote

```bash
git remote
git fetch blog
git checkout deployment
```

## 사전 빌드

### 부트스트래퍼

```bash
pwd
# `/workspaces/susc-github-actions-2025`에 있는지 확인
mkdir srcs && cd srcs
hugo new site .
```

### 서브모듈로 테마만 가져오기

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
git submodule add --depth 1 https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
git rm --cached themes/ananke
rm ../.gitmodules
echo "theme = 'ananke'" >> hugo.toml
```

### 의존성만 복사

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
cp -r . ..
```

### srcs 삭제

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
cd ..
rm -r srcs
```

### hugo.toml 업데이트

```bash
baseURL = 'https://`<USERNAME>`.github.io/'
languageCode = 'ko-kr'
title = '`<원하는 제목 입력>`'
theme = 'ananke'
```

### archetypes 업데이트

- archetypes/default.md 열기
  - 초안 모드 비활성화 설정
  - 일부 가이드

```yaml
---
date: '{{ .Date }}'
draft: false
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
---

## 여기에 제목 대체

여기에 텍스트 입력
```

### `<USERNAME>`.github.io 저장소에 업로드

```bash
pwd
# `/workspaces/susc-github-actions-2025`에 있는지 확인
git status
git checkout -b ephemeral blog/main
git add .
git status
git commit -m "update: pre-build resources"
git push blog ephemeral: main
```

## GitHub Actions

### 폴더와 파일 생성

```bash
mkdir -p .github/workflows && cd .github/workflows
touch deploy.yml
```

- 들여쓰기를 SPACE 2로 설정
  - Tab 4가 아님

### 사전 요구사항

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
  # workflow_dispatch:

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.139.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget --progress=bar:force:noscroll -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb  
          DEBIAN_FRONTEND=noninteractive sudo dpkg -i ${{ runner.temp }}/hugo.deb
```

### Job 1

```yaml
      - uses: actions/checkout@v4
        name: Checkout main branch
        with:
          ref: main
          fetch-depth: 0
          submodules: recursive
      
      - name: Build Website with Hugo
        run: hugo
```

### Job 2

```yaml
      - uses: actions/checkout@v4
        name: Checkout gh-pages repo
        with:
          ref: gh-pages
          fetch-depth: 0
          path: emerald

      - name: Copy main/public To gh-pages/emerald
        working-directory: emerald
        run: |
          rm -rf *
          cp -rf ../public/* .
```

### Job 3

```yaml
      - name: Commit Website Updates
        working-directory: emerald
        run: |
          git config --global user.name github-actions
          git config --global user.email github-actions@github.com
          git add .
          git diff-index --quiet HEAD || git commit -m "Deploy website updates with GitHub Actions: ${GITHUB_SHA}"
          git push origin gh-pages
```

### 전체 Actions

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
  # workflow_dispatch:

concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.139.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget --progress=bar:force:noscroll -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb  
          DEBIAN_FRONTEND=noninteractive sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - uses: actions/checkout@v4
        name: Checkout main branch
        with:
          ref: main
          fetch-depth: 0
          submodules: recursive
      
      - name: Build Website with Hugo
        run: hugo

      - uses: actions/checkout@v4
        name: Checkout gh-pages repo
        with:
          ref: gh-pages
          fetch-depth: 0
          path: emerald

      - name: Copy main/public To gh-pages/emerald
        working-directory: emerald
        run: |
          rm -rf *
          cp -rf ../public/* .
      
      - name: Commit Website Updates
        working-directory: emerald
        run: |
          git config --global user.name github-actions
          git config --global user.email github-actions@github.com
          git add .
          git diff-index --quiet HEAD || git commit -m "Deploy website updates with GitHub Actions: ${GITHUB_SHA}"
          git push origin gh-pages
```

## 기타 작업

### 배지

```markdown
![workflow status](https://github.com/`<USERNAME>`/`<USERNAME>`.github.io/actions/workflows/deploy.yml/badge.svg)
```

### 경계

```yaml
name: Deploy Website
on:
  push:
    branches: [ main ]
    paths: [ contents ]
```

### 파비콘

- themes/ananke/layouts/partials/site-favicon.html
- hugo.toml
- ~~themes/ananke/config/_default/params.toml~~

```html
<link rel="shortcut icon" href="{{ relURL ($.Site.Params.favicon) }}" type="image/x-icon" />
```

```toml
baseURL = 'https://`<USERNAME>`.github.io/'
languageCode = 'ko-kr'
title = '`<원하는 제목 입력>`'
theme = 'ananke'

[Params]
  favicon = "/img/favicon/<image-name>.png"

```

```bash
git add .
git commit -m "update: favicon"
git push blog ephemeral:main
```

### 기타

- giscus: <https://giscus.app/ko>
- uxwing: <https://uxwing.com/>
