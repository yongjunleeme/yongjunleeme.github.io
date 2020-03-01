---
layout  : wiki
title   : github-basic-1
summary : 
date    : 2020-02-12 20:25:32 +0900
updated : 2020-02-12 20:42:17 +0900
tags    : 
toc     : true
public  : true
parent  : tool
latex   : false
---
* TOC
{:toc}

## What

github 기본 명령어들 정리

## 초기 설정

```shell
"사용자 정보"
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com

"편집기 선택"
$ git config --global core.editor vim

"설정 확인"
$ git config --list

"도움말 보기"
$ git help <verb>
$ man git-<verb>
$ git add -h
```

```shell
$ git init

"깃헙 홈페이지에서 프로젝트 생성"

$ git remote add origin "깃헙주소"

$ git add 파일이름

$ git commit -m "커밋 메시지"

$ git push origin master

"상태확인"
$ git status -s 
```

## 브랜치

```shell
"브랜치"
git branch issue1  <생성>

git checkout issue1  <전환, 로그인>
git checkout -b issue1 <작성, 전환 한꺼번에>

git add myfile.txt
git commit -m "설명 추가"
git push origin issue1  "master가 아닌 브랜치 이름"

"마스터로 머지까지 마무리"
git checkout master
git merge issue1

"브랜치 삭제"
git branch -d issue1 <삭제>

"브랜치 확인"
$ git branch
```

- 충돌 문제 

```shell
"병합할 때 발생하는 충돌 해결하기"
$ git checkout master
$ git merge issue2
$ git merge issue3

"충돌 부분 모두 수정"
$ git add myfile.txt
$ git commit -m "issue3 브랜치 병합"

"충돌 해결"
$ git checkout master
$ git pull origin master
$ git checkout 브랜치명
$ git merge master
"충돌 부분 모두 수정"

"rebase 로 병합하기"
$ git checkout issue3
$ git rebase master

"충돌 부분 모두 수정"
$ git add myfile.txt
$ git rebase --continue
$ git checkout master
$ git merge issue3
```

## 깃헙배포

```shell
"github-page 배포"
1. gh-pages 반드시 이 이름으로 branch 생성
2. 브랜치에서 커밋
3. [username.github.io/a](http://username.github.io/a) name of the project(gh-pages)
```