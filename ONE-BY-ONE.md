# 튜토리얼

## devcontainer.json 편집

- 브랜치: hands-on
- 경로: /.devcontainer/devcontainer.json
- L25: `<USERNAME>` 편집
  - 예시. "kkumtree/kkumtree.github.io"  

## 적절한 실습 환경 대기  

```bash
/workspaces/susc-github-actions-2025 (playground) $ hugo version
hugo v0.139.3-2f6864387cd31b975914e8373d4bf38bddbd47bc+extended linux/amd64 BuildDate=2024-11-29T15:36:56Z VendorInfo=gohugoio
```

## 린터 설치

- markdownlint

![image](https://github.com/user-attachments/assets/a3147e27-e21d-47c9-a711-aafe50cbbc87)

## 원격 저장소 추가  

```bash
git remote
# origin
# upstream
git checkout playground
# Already on 'playground'
# Your branch is up to date with 'origin/playground'.
```

- SOURCE CONTROL > Remote > Add Remote
  - GitHub에서 원격 추가 > 허용 (확장 프로그램 'GitHub'가 GitHub를 사용하여 로그인하려고 합니다.)
- SOURCE CONTROL > Remote > Add Remote > Select `<USERNAME>`
  - `<USERNAME>`.github.io
  - Type `blog`

![image](https://github.com/user-attachments/assets/7bfb42c8-5376-4735-9a9a-3ae2fa3682be)  

```bash
git remote
# blog  
# origin  
# upstream  
```

## 부트스트래퍼  

```bash
pwd
# `/workspaces/susc-github-actions-2025`에 있는지 확인
mkdir srcs && cd srcs
hugo new site . 
```

## 테마를 서브모듈로 가져오기

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인  
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

### 플레이그라운드

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
git submodule deinit -f themes/ananke
rm -rf .git/modules/themes/ananke
git rm -f themes/ananke
git submodule add --depth 1 --force https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

## 폴더 트리 확인

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
tree -L 2
.
├── archetypes
│   └── default.md
├── assets
├── content
├── data
├── hugo.toml
├── i18n
├── layouts
├── README.md
├── static
└── themes
    └── ananke
```  

## `hugo.toml` 편집  

```bash  
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
echo "theme = 'ananke'" >> hugo.toml
```

```yaml
baseURL = 'https://`<USERNAME>`.github.io/'
languageCode = 'ko-kr'
title = '`<원하는 제목 입력>`'
theme = 'ananke'
```

## HELLO WORLD  

```bash
hugo server
# Ctrl+C로 중지
hugo new content content/posts/my-first-post.md
hugo server
```

## 게시를 위해 중지  

```bash
# Ctrl+C로 중지
```

## 게시  

- Public 폴더의 파일만 업로드

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
git status
# On branch playground
# Your branch is up to date with 'origin/playground'.
# 
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         new file:   ../.gitmodules
#         new file:   themes/ananke
# 
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         .hugo_build.lock
#         archetypes/
#         hugo.toml
#         public/
#         resources/
cd public
cp -r . ../../
cd ../../
```

- public 파일 추출 후 SRCS 삭제

```bash
pwd
# `/workspaces/susc-github-actions-2025`에 있는지 확인
git submodule deinit -f srcs/themes/ananke
# Cleared directory 'srcs/themes/ananke'
# Submodule 'srcs/themes/ananke' (https://github.com/theNewDynamic/gohugo-theme-ananke.git) unregistered for path 'srcs/themes/ananke'
git rm -rf srcs
# rm 'srcs/themes/ananke'
rm -rf srcs
git status
# On branch playground
# Your branch is up to date with 'origin/playground'.
# 
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         new file:   .gitmodules
# 
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#         404.html
#         ananke/
#         categories/
#         images/
#         index.html
#         index.xml
#         sitemap.xml
#         tags/
```

- GitHub Pages가 있는 다른 저장소에 게시

```bash
pwd
# `/workspaces/susc-github-actions-2025/srcs`에 있는지 확인
git fetch blog
git checkout -b ephemeral blog/gh-pages
# @kkumtree ➜ /workspaces/susc-github-actions-2025 (ephemeral) $ # 브랜치 변경됨
git add .
git commit -m "Post: My First Blog"
# [ephemeral 0a16386] test
#  12 files changed, 490 insertions(+)
#  create mode 100644 .gitmodules
#  create mode 100644 404.html
#  create mode 100644 ananke/css/main.css.map
#  create mode 100644 ananke/css/main.min.css
#  create mode 100644 categories/index.html
#  create mode 100644 categories/index.xml
#  create mode 100644 images/gohugo-default-sample-hero-image.jpg
#  create mode 100644 index.html
#  create mode 100644 index.xml
#  create mode 100644 sitemap.xml
#  create mode 100644 tags/index.html
#  create mode 100644 tags/index.xml
git push blog ephemeral:gh-pages
#  Enumerating objects: 20, done.
#  Counting objects: 100% (20/20), done.
#  Delta compression using up to 2 threads
#  Compressing objects: 100% (17/17), done.
#  Writing objects: 100% (19/19), 312.34 KiB | 14.20 MiB/s, done.
#  Total 19 (delta 5), reused 0 (delta 0), pack-reused 0 (from 0)
#  remote: Resolving deltas: 100% (5/5), done.
#  To https://github.com/kkumtree/kkumtree.github.io.git
#     1d71560..0a16386  ephemeral -> gh-pages
```

## 기타 작업  

### Stackoverflow  

- <https://stackoverflow.com/a/26752628>  

- 원본  

```bash  
mv subfolder subfolder_tmp  
git submodule deinit subfolder  
git rm --cached subfolder  
mv subfolder_tmp subfolder  
git add subfolder  
```  

- 파생  

```bash  
mv themes/ananke/ themes/bianchi
git submodule deinit themes/ananke
git rm --cached themes/ananke
rm -rf themes/ananke  
mv themes/bianchi/ themes/ananke/
```  

### 차이점

```bash
tree resources
# resources
# └── _gen
#     └── assets
#         └── ananke
#             └── css
#                 ├── main.css_9d7c906d8fe82fddbbd923faebb24419.content
#                 └── main.css_9d7c906d8fe82fddbbd923faebb24419.json
```

```html
<link rel="stylesheet" href="/ananke/css/main.min.d05fb5f317fcf33b3a52936399bdf6f47dc776516e1692e412ec7d76f4a5faa2.css" >
```
