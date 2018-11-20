---
title: 깃(Git) 작업 플로우
key: 20181118
categories: [git]
tags: [Git, git guide]
---
실제 팀으로 일하면서 깃을 사용하다보면 코딩보다 깃으로 소스 관리하는게 더 어려울 때가 있습니다. 자주 사용하는 명령어는 다섯 손가락 안에 들 정도로 몇개 안되는데요. 한창 개발 중인 코드를 실수로 `master`에 머지를 한다던지, 라이브 서버에 접속해서 개발용 브랜치를 풀 받는다던지 하는 예상치 못 했던 실수가 사람을 식은 땀 흘리게 만들죠. 분명히 고객 끄덕이는 분 계실 겁니다.

개인적으로는 제일 많이 사용하는 깃 명령어는 `git status`와 `git diff`입니다. 커밋, 푸시, 머지 하기전에 수시로 확인하기때문입니다. 돌다리도 두드려보고 건너간다는 말 꼭 기억해야합니다.

개발을 할 때 어떤 깃 명령어를 사용하는지 보도록 하겠습니다. 적절한 시점에 꼭 필요한 명령어 사용해서 한번씩 더 확인하는 습관을 꼭 기억해 주세요.
![Using Git As you work](/assets/images/usinggitasworking.png)
## 현재 브랜치 확인은 기본
깃을 사용할 때 꼭 확인하고 기억해야할 것은 현재 어떤 브랜치에서 작업하고 있는지 입니다. 대부분의 깃 명령어는 현재 채크아웃 된 브랜치를 대상으로 동작합니다. 중요하고 자주 사용하는 명령어인 `add, commit`도 특별히 브랜치 이름을 옵션으로 주지 않습니다. 개발 브랜치에서 작업하다 동료가 코드 확인 요청을 해서 마스터 브랜치로 옮겨서 확인을 해 주고 아무 생각없이 마스터에서 작업을 계속 하는 경우는 참 흔하게 벌어집니다. 깃 애드 명령어 전에 이 사실을 인식하지 못 했다면 결과는 커밋하고 나서 식은 땀을 한번 죽 흘리는 것이죠.

현재 작업중인 브랜치를 확인하는 방법은 몇가지가 있는데요. 그중 하나가 `git status`입니다.
{% highlight bash %}
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
{% endhighlight %}

`git branch` 명령어로 현재 로컬에 있는 브랜치를 모두 확인해 볼 수도 있는데요. 이때 `*`가 앞에 붙어 있는 브랜치가 현재 체크아웃 브랜치 입니다.
{% highlight bash %}
$ git branch
  develop
  feature-order
* master
{% endhighlight %}

`git branch -vva` 명령어로는 로컬 브랜치의 좀 더 자세한 내용과 리모트 브랜치까지 확인해 볼 수 있습니다.
{% highlight bash %}
$ git branch -vva
  develop               d71c4c6 products : add products list
  feature-order         d7aa891 order : add a new file
* master                6c84a15 [origin/master: ahead 2] products: add functions
  remotes/origin/HEAD   -> origin/master
  remotes/origin/master 59dfe84 Initial commit
{% endhighlight %}

## 소스 업데이트
혼자 프로젝트를 만들어 늦은 밤까지 개발을 즐기는 혼코팅족에게도 깃은 매력적입니다. 하지만 팀으로 개발을 할 때 깃의 진가가 발휘되죠. 각자 맡은 부분을 개발하고 일부 개발이 되는대로 서버에 푸시를 하면 각 개발자는 풀을 받아서 소스를 최신 상태로 유지하게 됩니다. 혹시나 있을 컨플릭트는 중간에 빨리 발견해서 해결을 하는게 좋습니다. 멋지게 작업을 다 했는데 라이브에 적용하려고 머지를 하는 순간 빨갛게 표시된 컨플릭트 파일이 있다고 생각해 보세요. 불은 조금이라도 빨리 끄는게 좋죠. 

소스를 업데이트할때 가장 많이 사용하는 명령어는 `git pull`입니다.
{% highlight bash %}
$ git pull origin master
{% endhighlight %}
여기서 `origin`은 리모트에 있는 깃서버 이름입니다. 깃에서 기본으로 설정하는 이름이죠. 그다음 `master`는 origin 서버에 있는 브랜치 이름이고요. origin 서버에 있는 master 브랜치를 가져와서 현재 로컬 리포지토리의 master 브랜치에 머지를 해 달라는 명령어 입니다. 실제로 `git pull`은 `git fetch && git merge FETCH_HEAD` 를 한번에 실행해 주는 명령어인데요. `git fetch`는 리모트 서버로부터 변경된 내역을 받아와서 FETCH_HEAD에 저장을 해 줍니다. 현재 작업하고 있는 브랜치의 HEAD에 바로 머지를 해 주지 않고 별도로 보관을 하고 있는 거죠. 그 다음에 `git merge FETCH_HEAD` 명령어로 현재 작업 브랜치에 머지를 해 주는 것입니다.

특별히 내가 수정한 내역을 로모트 서버에 푸시를 할 때는 먼저 풀을 해 줘야합니다. 만일 내가 마지막으로 풀을 받은 이후에 리모트 서버에 새로 푸시된 내역이 있다면 풀 없이 푸시를 하게 되면 에러가 납니다.
{% highlight bash %}
$ git push origin master
To https://github.com/saykent/gitwork.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/saykent/gitwork.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
{% endhighlight %}
`reject`라는 단어가 눈에 띠네요. 아래 리젝트가 된 이유가 나오는데요. 'because the remote contains work that you do not have locally' 서버에 로컬에 없는 추가된 내용이 있다는 겁니다.

![Git Push Rejected](/assets/images/gitpull01.jpg)
1. 로컬 master 브랜치입니다. 그래프에서는 파란색이네요.
2. 리모트 브랜치가 현재 로컬 브랜치에 머지가 안되어 있는 것이 보입니다.

풀을 받아 보겠습니다.

{% highlight bash %}
$ git pull origin master
From https://github.com/saykent/gitwork
 * branch            master     -> FETCH_HEAD
Merge made by the 'recursive' strategy.
 review.py | 4 ++++
 1 file changed, 4 insertions(+)
 create mode 100644 review.py
{% endhighlight %}

![Git after pull](/assets/images/gitpull02.jpg)

풀을 받았더니 새로운 파일이 하나 추가 되었습니다. 그리고 그래프도 붉은 선이 파라색 선에 머지가 되어 있는 것이 보입니다. 이제 푸시를 하면 기대하던 대로 잘 푸시가 됩니다.
{% highlight bash %}
$ git push origin master
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 519 bytes | 519.00 KiB/s, done.
Total 5 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To https://github.com/gitwork.git
   f0bbd1f..5a469b0  master -> master
{% endhighlight %}

## 피처 브랜치
개발 속도가 빠르게 진행되고 팀이 애자일 하게 움직일수록 여러 이슈나 기능 개발이 동시에 진행될 수 있습니다. 특히 이미 상용화 되어 고객이 사용하고 있는 소프트웨어가 있다면 버그를 수정하고 고객의 요구에 맞는 수정을 하기위해 브랜치를 수시로 생성하게 됩니다. '[깃(Git)은 뭐가 다른가?](/git/how-git-is-different.html#깃은-브랜치-생성과-머지가-자유롭습니다)'에서도 이야기했듯이 깃은 브랜치를 자유롭게 만들고 필요없으면 지우는 것을 큰 어려움 없이 할 수 있도록 설계되어 있습니다. 원하는 만큼 브랜치를 생성해도 되는 것은 물론 브랜치를 많이 생성하도록 권장하고 있다고 생각하면 됩니다.

master 브랜치를 베이스로 하는 피처 브랜치를 하나 만들어 보겠습니다. 브랜치 이름은 간단하게라도 피처나 이슈를 구분할 수 있는 것이 좋습니다.
{% highlight bash %}
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

$ git checkout -b jira-1002
Switched to a new branch 'jira-1002'
{% endhighlight %}
지라 티켓 번호로 브랜치를 만든 예입니다. 새로운 기능을 개발하고 있다면 기능 이름으로 브랜치를 만드는 것도 좋습니다. 이제 새로 만들어진 브랜치에서 작업을 원하는대로 하면 됩니다.

## 스테이지 영역
깃에서 관리하는 파일은 `커밋된 파일(committed)`, `수정된 파일(modified)`, `스테이지된 파일(staged)`로 상태를 구분할 수 있습니다. 

커밋을 하려면 우선 준비 과정이 필요한데요. 수정한 파일 중에서 커밋을 하려고 하는 파일을 스테이지 영역에 추가를 해야합니다. 수정된 파일을 넣을 수도 있고 새로 생성된 파일도 넣을 수 있습니다. 필요한 경우에는 파일을 삭제할 수도 있죠.

### 작업중인 디렉토리의 상태 확인
`git status` 명령어로 현재 작업중인 디렉토리의 파일 상태를 확인할 수 있습니다. 만일 수정된 내역이 없다면 아래와 같은 메시지를 보게 됩니다.
{% highlight bash %}
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
{% endhighlight %}

아직 스테이지 영역에 넣지 않은 수정 내역이나 새로운 파일이 있다면 아래와 같은 메시지를 보게됩니다.
{% highlight bash %}
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   review.py

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	order.py

no changes added to commit (use "git add" and/or "git commit -a")
{% endhighlight %}
review.py는 수정된(modified) 파일이고 order.py는 새로 생성된 파일로 아직 깃에 의해서 관리되는 파일이 아닙니다.(Untracked files)

수정된 내역을 확인해 볼 수도 있습니다. `git diff` 명령어를 입력해 볼까요.
{% highlight bash %}
$ git diff
diff --git a/review.py b/review.py
index 98f8d87..9b84010 100644
--- a/review.py
+++ b/review.py
@@ -1,3 +1,4 @@
+import json
 
 def review_list():
     pass
{% endhighlight %}
git diff 명령어를 사용하면 수정된 파일의 변경사항을 이미 커밋된 내용과 비교해 줍니다. 파일을 커밋하기 전에 변경된 내용을 확인해서 혹시나 테스트 코드나 로그를 찍었던 코드가 남아 있지 않는지 한번씩 확인해 볼 수 있습니다.

### 스테이지 영역에 추가
변경된 내역을 스테이지 영역에 추가를 하기 위해서는 `git add` 명령어를 사용합니다. 커밋을 하기위해서 스테이지 영역에 파일을 추가할 때는 수정된 파일을 넣을 수도 있지만 새로 생성된 파일을 넣을 수도 있죠. 사용하는 명령어는 똑같이 `git add`입니다. 

새로 생성된 파일 order.py를 `git add order.py`로 추가해 보겠습니다. 그리고 `git status`를 해 보면 뭐가 달라졌는지 볼 수 있겠죠.
{% highlight bash %}
$ git add order.py 
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   order.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   review.py
{% endhighlight %}
order.py가 커밋될 변경 사항(Changes to be committed)에 포함이 된 것을 볼 수 있습니다. `git diff`로 수정된 내용을 비교해 보는 것은 git add를 하기 전의 수정된 파일에만 가능합니다. 일단 스테이지 영역으로 들어온 파일은 `git diff --cached`로 확인해 볼 수 있습니다.

작업 디렉토리에 있는 변경 내역을 한번에 추가하고 싶다면 '-A' 옵션을 사용헤사 `git add -A`라고 합니다. 

스테이지 영역에 있는 파일을 다시 빼올 수도 있습니다. `fit reset HEAD <filename>` 명령어를 사용하면 됩니다. 명령어 실행 후에 git status를 해 보면 변화를 확인할 수 있습니다.
{% highlight bash %}
$ git reset HEAD order.py
Unstaged changes after reset:
M	review.py

$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   review.py

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	order.py

no changes added to commit (use "git add" and/or "git commit -a")
{% endhighlight %}


참고 : Zulip 문서 [Using Git as you work](https://zulip.readthedocs.io/en/latest/git/using.html)

---