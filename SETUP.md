# oikbas-blog 세팅 가이드

## 1. 블로그 repo 생성

```powershell
# blog-skeleton 폴더를 별도 위치로 복사
Copy-Item -Recurse C:\Users\HAN\Documents\Drive\OIKBAS\blog-skeleton C:\Users\HAN\Documents\oikbas-blog

cd C:\Users\HAN\Documents\oikbas-blog

# Hugo PaperMod 테마 추가
git init && git branch -m main
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod

# 초기 커밋 + repo 생성
git add -A
git commit -m "init: oikbas-blog Hugo site"
gh repo create oikbas-blog --public --source=. --push
```

## 2. GitHub Pages 활성화

1. GitHub > oikbas-blog > Settings > Pages
2. Source: "GitHub Actions" 선택
3. 첫 배포가 자동 실행됨

## 3. Vault → Blog 자동 발행 연결

oikbas-vault repo에서 블로그 repo에 push할 수 있도록 토큰 설정:

1. GitHub > Settings > Developer settings > Personal access tokens > Fine-grained tokens
2. 새 토큰 생성:
   - Name: `oikbas-blog-publish`
   - Repository access: `oikbas-blog` only
   - Permissions: Contents (Read and write)
3. oikbas-vault repo > Settings > Secrets > Actions
   - Name: `BLOG_TOKEN`
   - Value: 위에서 생성한 토큰

## 4. 노트 발행 방법

볼트에서 노트의 frontmatter에 아래 필드를 추가:

```yaml
---
tags: [Research, diffusion]
publish: true
title: "Diffusion 모델 기반 VFX 렌더링 최적화"
summary: "최신 diffusion 기법을 VFX 파이프라인에 적용한 결과"
date: 2026-03-24
blog_category: research
---
```

git push하면 자동으로 블로그에 배포됨.

## 5. 로컬 미리보기

```powershell
cd C:\Users\HAN\Documents\oikbas-blog
hugo server -D
# http://localhost:1313 에서 확인
```
