---
title: 헥소 설정기
date: 2017-01-21 21:42:14
tags:
  - hexo
  - github
  - pages
  - 헥소
  - 깃헙
  - 페이지
---

[2017년을 맞으며](/at-beginning-of-2017/)를 쓰며 정리했던 목표 중 하나인 글쓰기를 하기 위해 분위기를 전환할 겸 블로그를 세팅하기로 했다. 마치 시험기간에 안 하던 집 청소를 하는 느낌이지만, 뭐 어때? :)

Github Pages는 지킬([Jekyll](https://jekyllrb.com/))이 대표적으로 사용된다. 작년에 한 번 회사 블로그를 세팅할 때 설정해봤다. 또한, 루비를 좋아하기 때문에 좋은 선택이었을 수 있다. 그런데 지킬은 이미 레거시라는 이야기를 들었고(귀가 얇진 않은데...), 헥소([Hexo](https://hexo.io/))가 그 다음 후보였다. 헥소는 Node.js로 구동되는데, 최근에는 루비보다 자바스크립트를 많이 쓰게 돼서, 오히려 루비보다 자바스크립트가 편하게 느껴진다. 따라서 헥소로 결정!

헥소를 설정하는 게 정말 어렵지 않은데, 제대로 설정하려고 하다보니 시행착오를 많이 겪었다. 글로 남겨두면 헥소로 블로그를 세팅하실 분들에게 조금이나마 도움이 되지 않을까하여 글을 쓴다. 설정을 해두니 헥소의 장점들이 많이 보인다. 추천!

## 헥소 설정하기

*Node.js 패키지 매니저([npm](https://www.npmjs.com/)/[yarn](https://yarnpkg.com/))는 설치되어 있다고 가정하고 진행합니다.*

1. 헥소 커맨드 라인 인터페이스 설치
```bash
npm i hexo-cli -g # for npm
yarn global add hexo-cli # for yarn
```

1. 블로그 생성 및 패키지 설치
```bash
hexo init <blog>
cd <blog>
npm i # for npm
yarn # for yarn
```
  - `<blog>` 위치에 블로그 이름으로 변경해도 된다.
  - `hexo init`까지만 입력 시 현재 디렉토리에 블로그 파일들이 생성된다.

1. 헥소 실행해보기
```bash
hexo server
```
  - 헥소는 기본적으로 4000번 포트로 실행된다. 따라서 http://localhost:4000 로 접근할 수 있다. 커맨드 실행 시 `-o` 옵션을 사용하면, 서버를 실행하고 자동으로 브라우저로 연결시켜준다.
  - Hello World라는 글이 자동으로 생성되는데, `./source/_posts` 디렉토리 내에 있다. 삭제하면 끝!

1. 헥소 설정
  - 헥소 설정은 `./_config.yml` 파일에서 할 수 있다. 내용이 꽤 직관적이어서 기초는 쉽게 설정할 수 있다.
  - 더 자세한 내용은 [여기](https://hexo.io/ko/docs/configuration.html)를 참고

1. 글 작성
```bash
hexo new <title>
```
  - `<title>` 위치에 원하는 제목을 입력한다.
  - 제목이 공백을 포함하면 따옴표로 묶는다.

1. 테마 설정
  - 다른 곳에도 있겠지만, 공식 사이트에서 [리스트](https://hexo.io/themes/)를 제공한다.
  - `./themes/<theme name>` 디렉토리에 저장하면 된다.
    - 테마 파일 자체를 넣어도 되고, git submodule 등으로 관리해도 된다.
  - 테마를 적용하기 위해서는 `./_config.yml`에서 `theme: <theme name>`을 설정하면 된다.

1. 배포하기 (Github Pages)
  1. Github에 `계정명.github.io`로 repository를 생성한다.
  1. Git에 배포하기 위해 패키지를 설치한다.
```bash
npm i --save hexo-deployer-git
```
  1. `./_config.yml`의 `deploy`를 설정한다.
```yml
deploy:
  type: git
  repo: <repository url>
  branch: [branch]
  message: [message]
```
    - `<repository url>`에 1번에서 생성한 Github repository 주소를 입력한다.
    - `[branch]` 및 `[message]`를 설정하지 않으면 기본적으로 알아서 판단해서 설정이 된다. 혹은 원하는 설정을 입력하면 된다.
  1. 배포하자!
```bash
hexo deploy -g
```
    - 정적 파일을 생성 후 Github repository에 배포를 한다. `-g` 옵션이 정적 파일 생성하는 옵션이다.
    - 위 옵션을 붙이지 않으면 현재 생성되어 있는 정적 파일을 올리게 된다.

이로써 설치 및 글 작성, 배포를 할 수 있다. 쉽다! 보다 자세한 설명은 [공식 사이트](https://hexo.io/ko/)를 참고하면 좋다. 심지어 한글 문서도 잘 되어 있으니, 누구든 쉽게 접할 수 있을 것 같다.

하지만, 아직 시행착오를 겪으며 생긴 노하우를 전하지 않았다.

## (반드시 알 필요는 없는) 노하우

위의 과정을 다 따라했으면, 어떻게 배포가 이루어지는지 알 것이다. 정적 파일을 생성해 Github Pages에 배포를 하게 된다. 그러나, 정적 파일을 제외한 부분은 관리를 하기 어렵다. 이에 대한 (거창한 표현인 것 같지만)노하우가 있다.

1. Git 브랜치를 master 외의 다른 브랜치로 옮긴다.
1. Github repository로 push한다.
1. Github repository에 접근한다.
1. Settings - Branches로 이동한다.
1. 새로 올린 브랜치를 Default branch로 설정한다.

이제, 해당 Github repository에 접근하면 설정 파일 및 마크다운으로 작성된 글들이 기본적으로 보이게 된다. master는 오로지 배포용으로만 사용할 수 있고 설정 파일과 정적 파일을 분리함으로써 관리를 효율적으로 할 수 있다! Awesome!
