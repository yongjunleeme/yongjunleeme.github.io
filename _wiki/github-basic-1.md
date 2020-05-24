---
layout  : wiki
title   : github-basic-1
summary : 
date    : 2020-02-12 20:25:32 +0900
updated : 2020-05-24 18:14:57 +0900
tags    : 
toc     : true
public  : true
parent  : tool
latex   : false
---
* TOC
{:toc}

## 쓸 것 같은 명령어들

```shell
$ git diff --staged         "커밋하려는 추가 코드를 보여준다"
$ git commit -am "코멘트"   "add, commit 한 번에"
$ git log --oneline         "커밋 리스트 확인
$ git status -s
```

### gitignore

```python
$ echo "db.sqlite3" >> .gitignore
```

```python
# 슬래시(/)로 시작하면 하위 디렉터리에 적용되지(recursivity) 않는다.
# 느낌표(!)로 시작하는 패턴의 파일은 무시하지 않는다.

# 윗 라인에서 확장자가 .a인 파일은 무시하게 했지만 lib.a는 무시하지 않음
!lib.a

# 현재 디렉토리에 있는 TODO 팡리은 무시하고 subdir/TODO처럼 하위 디렉토리에 있는 파일은 무시하지 않음
/TODO

# build/ 디렉터리에 있는 모든 파일은 무시
build/

# doc/notes.txt 파일은 무시하고 doc/server/arch.txt 파일은 무시하지 않음
doc/*.txt

# doc 디렉터리 아래의 모든 .pdf 파일을 무시
doc/**/*.pdf
```

### `git diff`

```python
$ git diff               - unstaged 상태인 것들만 보여준다
$ git diff --staged      - Stagind Area에 넣은 파일의 변경 부분 확인
$ git diff --cached      
```

### `git commit`

```python
$ git moddit -a -m 'added new benchmarks'
# -a 옵션 추가(git add 안 해도 됨)하면 Tracked 상태의 파일을 자동으로 Staging Area에 넣는다.
```

### `git rm`

```python
$ git rm gret.gemsepc
- 그냥 지우면 삭제 파일 Unstaged상태, git rm으로 지우면 staged
- 이미 파일을 수정했거나 수정한 파일을 staging area에 추가했다면  -f 옵션으로 강제 삭제해야
- 이 점은 실수로 데이터 삭제 못하도록 하는 안전장치

$ git rm --cached README
- Staging Area에서만 제거하고 워킹 디렉터리에 있는 파일은 지우지 않고 남겨둔다.
- 다시 말해, 하드디스크에 있는 파일은 그래도 두고 Git만 추적하지 않게 한다.
```



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

```shell
"git add 취소"
$ git reset HEAD <파일명> "파일명 쓰지 않으면 전체 취소"

"git commit 취소"
$ git reset HEAD^

"commit 메시지 변경"
$ git commit —amend
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

## 충돌 문제 

### 실제 충돌 시 

1. Master를 최신으로 변경
    - `git log``로 번호 확인
    - 레파지토리 커밋 상에서 최신 번호 확인
    - 내 로컬에서 최신과 다른 변경사항이 있으면 `git stash`로 저장
    - 이후에 `git fetch`, `git pull origin master`로 최신화

2.  충돌난 브랜치로 이동

    - `git merge master`
    - 충돌 부분 수정
    - `git add, commit, push`
    - 브랜치마스터가 머지

```shell
$ git checkout master
$ git fetch
$ git pull origin master
$ git status
$ git checkout 브랜치명
$ git log -1 
"해시번호 최신확인"
$ git merge master
"merge conflicting로 명시된 파일 수정"
$ git push, commit

"현재 변경코드를 백업
"마스터로 돌아가서 git pull origin master
"새로운 브랜치 생성 후 변경코드를 복붙
```

```shell
$ git add -i  <interactive로 확인?>
"2: update 
"삭제된 파일 업데이트

"4: add untranced
"추가된 파일 업데이트
$ git add, commit
```

#### rebase

```python
$ git checkout master
$ git pull origin master

$ git rebase -i master 브랜치이름
p <- pick으로 나머지를 녹인다.
s
s

충돌파일 변경 - 헤드
$ git add
$ git rebase --continue

충돌파일 변경 - 헤드

combination이 뜨면 최종 커밋써야

$ git add
$ git rebase --continue

성공하면 푸시 origin 

석세스풀리 리베이스되었는데 푸시 안되는 경우
$ git push origin 피처/브랜치 -f
```


### 교과서적 충돌 시

```shell
병합할 때 발생하는 충돌 해결하기"
$ git checkout master
$ git merge issue2
$ git merge issue3

"충돌 부분 모두 수정"
$ git add myfile.txt
$ git commit -m "issue3 브랜치 병합"

"충돌 중 수정한 파일이 있을 때?"
$ git stash "하던 작업을 임시로 저장"

"충돌 해결"
$ git checkout master
$ git pull origin master
$ git checkout 브랜치명
$ git merge master
"충돌 부분 모두 수정" - 둘중 하나만 남기면 된다

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

## git flow

why 
어떻게 깃을 써야 가장 효율적일까?

- 기존 브랜치
    - master, feature 

- flow 브랜치
    - hotfix(상용 서비스 디버깅), release(배포후보, 마지막 테스트 전), master(배포코드), develop(마스터 역할), feature
    - Why? 배포중에도 개발을 멈추지 않기 위해
    - release 브랜치에서 버그생기면 release에서 디버깅
    - 마스터로 가고 이후에 develop가서 머지까지
    - hotfix는 master에서 브랜치 생성, 디버깅후 master로 올려서 배포, develop가서 머지까지

## Tip


