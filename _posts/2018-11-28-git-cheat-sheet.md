---
title: 깃(Git) 필수 명령어
key: 20181128
categories: [git]
tags: [Git, git guide, git cheat sheet]
---
## 필수 명령어
소프트웨어 개발을 하면서 실전에서 깃을 사용하다보면 익숙하게 사용하는 명령어는 6개 정도 되는 것 같습니다. add, commit, push, pull, status, diff가 개인적으로 제일 많이 사용하는 명령어 입니다. 그나마도 여러가지 옵션을 넣어 사용할 수 있을텐데 기억하고 있는 옵션을 정말 몇개 안되고요.

가장 일반적으로 자주 사용하는 깃 명령어들 모음입니다. 줄립의 [깃 가이드](https://zulip.readthedocs.io/en/latest/git/cheat-sheet.html)에 잘 정리되어 있어서 목록을 그대로 옮겨 놓았습니다. [깃 유용한 팁](git/git-tips-you-need.html)에 있는 내용도 함께 참고해서 보세요. 한번 죽 읽어보고 나중에 필요할 때 참조를 해서 보면 됩니다.

### 명령어 목록
- add
    - `git add foo.py`
- checkout
    - `git checkout -b new-branch-name`
    - `git checkout master`
    - `git checkout old-branch-name`
- commit
    - `git commit -m "topic: Commit message title."`
    - `git commit --amend`: 이전 커밋 변경.
- config
    - `git config --global core.editor nano`
    - `git config --global core.symlinks true`
- diff
    - `git diff`
    - `git diff --cached`
    - `git diff HEAD~2..`
- fetch
    - `git fetch origin`
    - `git fetch upstream`
- grep
    - `git grep update_unread_counts -- '*.js'`
- log
    - `git log`
- pull
    - `git pull`: 리모트로부터 페치를 해서 머지를 해줌.
    - `git pull --rebase`: 리모트로 리베이스를 함.
- push
    - `git push origin branch-name`: 리모트 origin에 커밋된 내용을 푸시.
    - `git push origin +branch-name`: 현재 브랜치의 내용을 강제로 푸시.
- rebase
    - `git rebase -i HEAD~3`
    - `git rebase -i master`
    - `git rebase upstream/master`
- reflog
    - `git reflog | head -10`
- remote
    - `git remote -v`
- reset
    - `git reset HEAD~2`
- rm
    - `git rm oops.txt`
- show
    - `git show HEAD`
    - `git show HEAD~~~`
    - `git show master`
- status
    - `git status`

## 명령어 설명

- add
    - `git add foo.py`: `foo.py`를 스테이징 영역에 추가
    - `git add foo.py bar.py`: `foo.py`와 `bar.py` 를 스테이징 영역에 추가
    - `git add -u`: 깃으로 관리되는 모든 파일을 를 스테이징 영역에 추가
- checkout
    - `git checkout -b new-branch-name`: `new-branch-name`라는 이름으로 브랜치를 생성하고 새로 만들어진 브랜치로 체크아웃
    - `git checkout master`: `master` 브랜치로 변경
    - `git checkout old-branch-name`: 기존에 가지고 있던 `old-branch-name` 브랜치로 변경
- commit
    - `git commit -m "commit message"`: 한줄 코멘트를 넣고 커밋. 
    - `git commit`: 에디터가 자동으로 열리면서 코멘트를 적을 수 있게 해줌. 코멘트를 저장하면 커밋됨.
    - `git commit --amend`: 마지막 커밋에 현재 스테이징 영역에 있는 내용을 포함시킴.
- config
    - `git config --global core.editor nano`: 에디터를 `nano`로 지정함. `vim`이나 다른 것으로도 지정 가능.
    - `git config --global core.symlinks true`: 심볼릭 링크를 허용함.
- diff
    - `git diff`: 현재 작업 영역에 수정한 모든 파일 변경 내역을 보여줌.
    - `git diff --cached`: 스테이징 영역에 있는 내용과 현재 작업 영역에 차이점을 보여줌.
    - `git diff HEAD~2..`: 2개의 가장 최근 커밋의 변경 내용을 보여줌.
- fetch
    - `git fetch origin`: origin 서버에 있는 내용을 가져옴.
- grep
    - `git grep update_unread_counts -- '*.js'`: 이름이 `.js`로 끝나는 파일에서 `update_unread_counts`를 찾음.
- log
    - `git log`: 커밋 로그를 보여줌
    - `git log --oneline | head`: 현재 브랜치의 최근 10의 커밋 로그를 보여줌.
- pull
    - `git pull` (옵션 없음): 리모트로부터 페치를 해서 머지를 해줌.
    - `git pull --rebase`: 리모트로 리베이스를 함.
- push
    - `git push origin branch-name`: 로컬에 커밋된 내용을 리모트 origin 서버에 푸시
    - `git push origin +branch-name`: origin 서버에 로컬의 커밋을 강제로 푸시
- rebase
    - `git rebase -i HEAD~3`: 최근 3개의 커밋을 인터렉티브 리베이스(에디터로 커밋된 로그륿 보여줌)
    - `git rebase -i master`: 현재 브랜치를 마스터로 인터렉티브 리베이스
    - `git rebase origin/master`: 현재 브랜치를 origin 서버의 master로 인터렉티브 리베이스
- reflog
    - `git reflog | head -10`: 최근 10개의 커밋의 레퍼런스 로그를 보여줌.
- remote
    - `git remote -v`: 리모트로 지정된 리포지토리를 보여줌.
- reset
    - `git reset HEAD~2`: 최근 2개의 커밋을 리셋
- rm
    - `git rm oops.txt`: `oops.txt`을 지움.
- show
    - `git show HEAD`: 가장 최근의 커밋을 보여줌.
    - `git show HEAD~~~`: 3번째로 최신인 커밋을 보여줌.
    - `git show master`: `master` 브랜치의 가장 최근의 커밋을 보여줌.
- status
    - `git status`: 현재 작업 트리의 상태, 스테이지 영역에 있는 파일 등을 보여줌.

---