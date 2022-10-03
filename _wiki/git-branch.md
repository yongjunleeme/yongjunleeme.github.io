---
layout  : wiki
title   : 
summary : 
date    : 2022-10-03 14:30:15 +0900
updated : 2022-10-03 14:30:44 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 브랜치란 무엇인가 
git은 데이터를 변경사항(diff)이 아닌 스냅샷으로 기록한다. 무엇을 저장할까? 
- Staging Area에 있는 데이터의 스냅샷에 대한 포인터
- 저자나 커밋 메시지 같은 메타데이터
- 이전 커밋에 대한 포인터 등을 포함하는 커밋 개체(커밋 Object)를 저장

Git의 브랜치는 커밋 사이를 가볍게 이동할 수 있는 포인터 같은 것이다. 실제로는 어떤 한 커밋을 가리키는 40글자의 SHA-1 체크섬 파일이다. 브랜치는 자동으로 가장 마지막 커밋을 가리킨다.

HEAD라는 특수한 포인터는 현재 작업하는 로컬 브랜치를 가리킨다. 아래 명령어로 브랜치가 어떤 커밋을 가리키는지 확인할 수 있다.

```shell
$ git log --oneline --decorate
```

브랜치가 가리키고 있는 히스토리를 확인하려면?

```shell
$ git log --oneline --decorate --graph --all
```

## 브랜치와 Merge의 기초
프로젝트에서 커밋하는 과정을 통해서 `fast-forward`와 `3-way Merge`의 차이를 먼저 살펴보자. 이슈관리 시스템에 등록된 53번 이슈를 처리한다고 가정한다.

```shell
$ git checkout -b iss53
새로 만든 'iss53' 브랜치로 전환합니다

$ vim test.rb  # 파일 수정

$ git commit -a -m "made a change"
[iss53 8c93e6c] made a change
 1 file changed, 1 insertion(+)
 create mode 100644 test.rb 
```

그러고 나서 프로젝트에 문제가 생겨 즉시 고칠 문제가 생긴다면 main 브랜치로 돌아간 다음 hotfix 브랜치를 만든다. 하지만 아직 커밋하지 않은 파일이 checkout할 브랜치와 충돌이 나면 브랜치를 변경할 수 없으므로 워킹디렉터리를 정리하는 것이 좋다.

```shell
$ git checkout main
'main' 브랜치로 전환합니다
브랜치가 'origin/main'에 맞게 업데이트된 상태입니다.

$ git checkout -b hotfix
새로 만든 'hotfix' 브랜치로 전환합니다

$ vim test2.rb # 파일 수정

$ git commit -m "fixed test2"
[hotfix 55fcc4a] fixed test2
 1 file changed, 1 insertion(+)
 create mode 100644 test2.rb
```

main 브랜치에 합치기 위해 git merge 명령을 사용한다.

```shell
$ git checkout main'main' 브랜치로 전환합니다
브랜치가 'origin/main'에 맞게 업데이트된 상태입니다.

$ git merge hotfix
업데이트 중 8748062..55fcc4a
Fast-forward
 test2.rb | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test2.rb
```

그러면 Fast-forward로 메시지가 나오는데 이는 Merge 과정 없이 그저 최신 커밋으로 이동한다는 의미다. Merge할 브랜치가 가리키는 커밋이 현 브랜치 커밋의 Upstream 브랜치이기 때문이다.

더이상 필요 없는 hotfix 브랜치는 삭제하고 다시 일하던 브랜치로 돌아간다. hotfix는 iss53 브랜치에 영향을 미치지 않는다.

```shell
$ git branch -D hotfix
hotfix 브랜치 삭제 (과거 55fcc4a).

$ git checkout iss53
'iss53' 브랜치로 전환합니다
브랜치가 'origin/main'보다 1개 커밋만큼 앞에 있습니다.
  (로컬에 있는 커밋을 제출하려면 "git push"를 사용하십시오)

$ git merge iss53
Merge made by the 'ort' strategy.
 test.rb | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 test.rb

# Pro Git에서는 Merge made by the 'recursive' strategy로 메시지가 나옴
```
현재 브랜치가 가리키는 커밋이 Merge할 브랜치의 조상이 아니므로 Fast-forward가 아니라 3-way Merge를 한다. 이는 공통 조상 하나와 각 브랜치가 가리키는 두 개의 커밋을 사용한다. 


<사진>

Merge하는 두 브랜치에서 같은 파일을 수정하면 충돌이 난다.

```shell
$ git merge iss54
자동 병합: test.rb
충돌 (내용): test.rb에 병합 충돌
자동 병합이 실패했습니다. 충돌을 바로잡고 결과물을 커밋하십시오.
```

이때 어떤 파일을 Merge할 수 없었는지 보려면 `git status` 명령을 사용한다.

```shell
$ git status
현재 브랜치 main
브랜치가 'origin/main'보다 4개 커밋만큼 앞에 있습니다.
  (로컬에 있는 커밋을 제출하려면 "git push"를 사용하십시오)

병합하지 않은 경로가 있습니다.
  (충돌을 바로잡고 "git commit"을 실행하십시오)
  (병합을 중단하려면 "git merge --abort"를 사용하십시오)

병합하지 않은 경로:
  (해결했다고 표시하려면 "git add <파일>..."을 사용하십시오)
	양쪽에서 수정:  test.rb

커밋할 변경 사항을 추가하지 않았습니다 ("git add" 및/또는 "git commit -a"를
사용하십시오)
```

> Merge 시 발생한 충돌은 7.8고급 Merge를 참고

브랜치 리스트를 확인하고 merge된 브랜치인지 아닌지 확인하려면 아래 명령어를 사용한다.

```shell
$ git branch -v

$ git branch --merged

$ git branch --no-merged
```

## Rebase하기

Git에서 브랜치를 합치는 방법은 Merge와 Rebase가 있다. 공통 조상과 두 브랜치의 마지막 커밋 두 개를 사용하는 3-way merge와 비슷한 결과를 나타내는 Reabse는 변경된 사항을 Patch로 만들어서 합칠 브랜치에 적용하는 방법이다.

공통 커밋으로 이동하고 그 커밋부터 지금 checkout한 브랜치가 가리키는 커밋까지 diff를 차례롤 만들어 임시로 저장한다. Rebase할 브랜치가 합칠 브랜치가 가리키는 커밋을 가리카게 하고 임시 저장해놓은 변경사항을 차례대로 적용한다.

그러고 나서 main 브랜치를 Fast-forward한다.
```shell
$ git checkout iss54
'iss54' 브랜치로 전환합니다

$ git rebase main
Successfully rebased and updated refs/heads/iss54.

$ git checkout main
'main' 브랜치로 전환합니다

$ git merge iss54
```

메인 프로젝트에 Patch를 보낼 준비가 되면 origin/main으로 Rebase한다. 이렇게 Rebase하고 나면 프로젝트 관리자는 통합작업을 할 필요 없이 그냥 main 브랜치를 Fast-forward하면 된다.

Merge는 최종결과만 가지고 합친다면 Rebase는 브랜치 변경사항을 순서대로 다른 브랜치에 적용하면서 합친다.

## Rebase 활용

테스트가 덜 된 server 브랜치는 그대로 두고 client 브랜치만 main으로 합치려는 상황을 가정해보자. server와 관련이 없는 client의 커밋을 main 브랜치에 적용하라면 아래의 명령어를 사용한다. 

```shell
$ git rebase --onto main server client
```

이는 client 브랜치를 checkout하고 server와 client의 공통조상 이후의 Patch를 만들어 main에 적용하라는 의미다. 그러면 main으로 돌아가서 Fast-forward할 수 있다.

```shell
$ git checkout main
$ git merge client
```

## Rebase의 위험성

이미 공개 저장소에 Push한 커밋을 Rebase하지 말라. 이미 공개해 사람들이 사용하는 커밋을 Rebase하면 틀림없이 문제가 생긴다.

git pull에 `--rebase` 옵션을 적용해 rebase 옵션을 추가할 수 있고 `git config --global pull.rebase true`명령으로 rebase 설정을 추가할 수 있다.

```shell
$ git pull --rebase
```

로컬 브랜치에서 작업할 때는 히스토리를 정리하기 위해 Rebase할 수도 있지만, 리모트 등 밖으로 Push한 커밋은 절대 Rebase하지 말아야 한다.


## Source

- [Pro git](https://github.com/yongjunleeme/yongjunleeme.github.io)

