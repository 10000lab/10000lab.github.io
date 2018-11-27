---
title: 깃(Git) 유용한 팁
key: 20181127
categories: [git]
tags: [Git, git guide, git tips]
---

깃은 처음부터 리눅스 오픈 소스를 관리하기 위해 만들어졌습니다. 2005년에 그간 리눅스 소스를 관리하던 툴(BitKeeper)을 공급하던 회사와 관계가 안 좋아 지면서 자체적으로 사용할 수 있는 버전 관리 시스템이 필요하게 되었죠. 리눅스를 처음 만든 리누스 토발즈(Linus Torvalds)도 깃 개발에 적극적으로 참여하게 되고요. 깃 개발의 시작과 과정이 리눅스라는 복잡한 소프트웨어와 수많은 컨트리뷰터를 위한 것이다 보니 여러 상황에 대처할 수 있는 유용한 기능이 많이 있습니다.

![Git Tips](/assets/images/git_tips.png)

깃을 사용하면서 알아두면 큰 자신감을 얻게되는 유용한 팁들을 알아보겠습니다. 관련된 내용은 오픈 소스 팀 채팅 소프트웨어인 [줄립의 문서](https://zulip.readthedocs.io/en/latest/git/index.html)에서 많은 부분 참고하였습니다.


## 커밋 내용 수정
### 마지막 커밋 수정
#### 메시지 수정
커밋하고 나서 커밋 메시지를 수정해야할 때는 커밋 명령어에 `--amend` 옵션을 사용합니다. `git commit --amend -m "New Message"` 명령어를 사용해서 마지막 커밋 메시지를 변경해 보겠습니다. git log로 지금 로그를 확인해 보고 수정 명령어를 실행한 뒤 결과를 확인해 본 예입니다.
{% highlight bash %}
$ git log
commit 5e3da875aca86f5f36f543a22998e0df517328eb (HEAD -> new_feature)
Author: Ken You <ken@10000lab.xyz>
Date:   Sun Nov 25 22:04:05 2018 +1100

    order: Add update function.

$ git commit --amend -m "New Message"
[new_feature bf37424] New Message
 Date: Sun Nov 25 22:04:05 2018 +1100
 1 file changed, 3 insertions(+)
$ git log
commit bf37424a4662a34e8ddfc63c993bcf0008e6cc2b (HEAD -> new_feature)
Author: Ken You <ken@10000lab.xyz>
Date:   Sun Nov 25 22:04:05 2018 +1100

    New Message
{% endhighlight %}

#### 마지막 커밋 수정
이상하게도 꼭 커밋을 하고 나면 바로 버그가 하나 튀어 나옵니다. 수정은 항상 단 한 줄로 해결이 되고요. 다시 커밋을 해야하는데, 그냥 이전 커밋에 포함이 되었더라면 좋았을 것을 하는 생각이 들죠. 이때는  `git commit --amend`라고 명령어를 실행하면 됩니다.
  1. 수정이 필요한 파일을 수정합니다.
  2. `git add` 명령어로 파일을 추가합니다.
  3.  `git commit --amend`를 실행합니다.

### 지나간 커밋 수정
인터렉티브 깃 리베이스 명령어를 사용해서 수정을 할 수 있습니다. ['깃 리베이스 사용하기'](/git/git-rebase-workflow.html)에서도 일부 다루었던 기능입니다. `git rebase -i HEAD~n` 명령어를 사용하게 됩니다. 여기서 n은 수정할 커밋을 포함하는 최근 커밋 갯수입니다. 상황에 맞도록 어떻게 사용하는지 보겠습니다.
#### 커밋 메시지 수정
1. 최근 5개의 커밋을 수정하고자 한다면 `git rebase -i HEAD~5`라고 실행합니다.
2. 에디터가 열리면서 5개의 커밋이 각각 줄로 표시되면서 `pick`이라고 첫 단어로 나옵니다. 수정하고자 하는 커밋을 `reword`라고 변경하고 저장합니다. 그리고 에디터를 종료합니다.
3. 새로 에디터가 열리면서 메시지를 수정하라고 합니다. 메시지를 수정하고 저장합니다.

#### 커밋을 삭제
1. 최근 5개의 커밋에 삭제할 커밋이 포함되어 있다면 `git rebase -i HEAD~5`라고 실행합니다. 커밋의 갯수에따라 숫자를 변경하면 됩니다.
2. 에디터가 열리면 삭제하고자 하는 커밋의 `pick`이라고 되어있는 것을 `drop`이라고 변경하고 저장합니다.

#### 커밋을 합치기(squashing)
깃으로 개발하는 과정에서는 가능하면 자주 커밋을 해서 변경사항을 실수로라도 잃어버리는 일이 없도록 습관을 들이는게 좋습니다. 단지 이러고 나면 커밋 갯수가 너무 많아져서 좀 합쳐서 간단하게 보이고 싶은 경우가 있습니다. 어떤때는 그냥 통째로 하나의 커밋으로 만들고 싶을 때도 있고요.
1. `git rebase -i HEAD~n`라고 실행합니다. 여기서 n은 합치고자 하는 커밋의 갯수에따라 숫자로 넣으면 됩니다.
2. 에디터가 열리면 보존하고 싶은 커밋은 그대로 두고 합치고 싶은 커밋을 `pick`대신 `squash`라고 써 줍니다. 저장합니다.

#### 커밋 순서 변경
1. `git rebase -i HEAD~n`라고 실행합니다. 여기서 n은 합치고자 하는 커밋의 갯수에따라 숫자로 넣으면 됩니다.
2. 에디터가 열리면 원하는 대로 커밋 순서를 변경합니다. 저장합니다.

### 변경 후 푸시
이미 푸시가 된 커밋 내용을 이렇게 변경하고 나서 다시 푸시를 하려면 에러가 납니다. 이때는 강제로 푸시를 해 줘야하는데요. 푸시하는 브랜치의 이름 앞에 `+`를 붙여 주면 됩니다. 예를 들어 `new_feature` 브랜치를 푸시하려고 한다면 `git push origin +new_feature`라고 실행하면 됩니다.

## 변경 내용 확인
### 로컬 변경 사항
파일을 수정하고 아직 스테이징 영역에 넣지 않은 상태에서 변경사항을 보는 경우입니다. 즉 아직 `git add` 명령어 실행전입니다.
{% highlight bash %}
$ git diff
{% endhighlight %}
`git add`를 해서 스테이징 영역에 있는 파일과 커밋되어 있는 내용을 비교하려면 `--cached` 옵션을 사용합니다.
{% highlight bash %}
$ git diff --cached
{% endhighlight %}
아직 스테이징 영역에 있지 않은 수정사항과 `git add`로 스테이징 영역에 들어있는 파일을 마지막 커밋된 내용과 비교할 수 있습니다.
{% highlight bash %}
$ git diff HEAD
{% endhighlight %}

### 같은 브랜치안에서
현재 브랜치 내에서 두 커밋을 비교할 수 있습니다. 각 커밋의 해쉬(git-ref)를 이름으로 사용하면 됩니다.

마지막 커밋과 그 바로 앞 커밋을 비교하는 경우입니다.
{% highlight bash %}
$ git diff HEAD^ HEAD
{% endhighlight %}
커밋 해쉬를 이용해서 임의의 두 커밋을 비교할 수 있습니다.
{% highlight bash %}
$ git diff 4ac8340 66655af
{% endhighlight %}

### 다른 브랜치
두개의 서로 다른 브랜치간의 비교를 하려면 브랜치 이름을 이용합니다.
{% highlight bash %}
$ git diff new_feature master
{% endhighlight %}
새 브랜치가 만들어지고 난 후부터 master 브랜치에 변경된 내역을 보려면 아래와 같이 하면 됩니다.
{% highlight bash %}
$ git diff new_feature...master
{% endhighlight %}
리모트에 있는 브랜치와도 비교를 할 수 있습니다. origin에 있는 master와 현재 로컬 브랜치를 비교하는 경우입니다.
{% highlight bash %}
$ git diff origin/master...master
{% endhighlight %}

## 깃 리셋
상당히 위험한 명령어이지만, 개발을 하다보면 한번쯤은 꼭 쓰게되는 명령어입니다. `git reset` 명령어와 커밋 레퍼런스 해쉬로 원하는 커밋 상태로 리셋을 해 줍니다. 몇가지 옵션을 줄 수 있는데 가장 많이 사용하게 되는 `--hard`와 `--merge` 옵션을 사용해 보겠습니다.

### 하드 모드
말 그대로 하드한 리셋입니다. 만일 수정한 소스가 있는데 아직 커밋을 하기 전이라면 해당 수정 사항은 영원히 잃어버리게 되죠. 여기서 커밋 전이라는 것은 스테이지 영역에 있는 것을 포함하고 있으니 각별히 주의를 해야합니다.
예를 들어 보겠습니다. 리뷰 관련 기능을 추가 중입니다. 일부 기능을 수정하면서 2번에 거쳐 커밋을 했습니다. 그리고 또 다른 기능을 수정하고 있었는데 갑자기 새로 구현하고 있는 기능이 필요가 없어졌습니다. 현재 상황을 볼까요.
{% highlight bash %}
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   review.py

no changes added to commit (use "git add" and/or "git commit -a")
$ git reflog
e16690d (HEAD -> master) HEAD@{0}: commit: review: Add delete function
4e4af76 HEAD@{1}: commit: review: Add update function
5dc0734 (origin/master, origin/HEAD) HEAD@{2}: rebase finished: returning to refs/heads/master
{% endhighlight %}
`git status`로 보니 review.py를 수정중이었습니다. `git reflog`로 보니 최근 2개의 commit을 한게 보입니다. 기능이 필요가 없어졌으니 `5dc0734` 커밋 상태로 변경을 하면 됩니다. `git reset --hard 5dc0734` 명령어를 실행해 보겠습니다.
{% highlight bash %}
$ git reset --hard 5dc0734
HEAD is now at 5dc0734 Merge branch 'master' of https://github.com/saykent/gitwork
macmini:gitwork ken$ git reflog
5dc0734 (HEAD -> master, origin/master, origin/HEAD) HEAD@{0}: reset: moving to 5dc0734
e16690d HEAD@{1}: commit: review: Add delete function
4e4af76 HEAD@{2}: commit: review: Add update function
5dc0734 (HEAD -> master, origin/master, origin/HEAD) HEAD@{3}: rebase finished: returning to refs/heads/master

$ git log
commit 5dc0734137b617428f5a8ee25ceb826b9c5cd2b4 (HEAD -> master, origin/master, origin/HEAD)
Merge: 6dbc75c c0180e7
Author: Ken You <ken@10000lab.xyz>
Date:   Tue Nov 27 21:58:57 2018 +1100

    Merge branch 'master' of https://github.com/saykent/gitwork

commit 6dbc75c8e6cb11997dc3b277c594b2c8871240ed
Author: Ken You <ken@10000lab.xyz>
Date:   Sun Nov 25 22:04:45 2018 +1100

    order: Add delete function.
{% endhighlight %}
리멧 명령어를 실행하니 HEAD를 옮겼다는 메시지가 나옵니다. `git reflog` 명령어로 보니까 현재 HEAD가 옮겨졌다는 내용과 이전에 원래 있었던 2개의 커밋이 보입니다. 하지만 실제로 `git log`를 해보면 2개의 커밋을 사라졌다는 것을 알 수 있습니다. 주의할 점은 앞서 수정 중이던 review.py는 수정했던 내용이 없어지고 리셋이 되었다는 것입니다.

### 머지 모드
하드리셋과 머지 리셋의 다른 점은 커밋하지 않은 수정 내역을 다루는 방식입니다. 앞서 하드 리셋은 커밋하지 않은 작업 내용을 과감하게 리겟해 버리는 효과가 있었습니다. 하지만 머지 리셋은 작업을 했던 파일을 그대로 보존합니다. 그려면 앞서 예에서 보여줬던  review.py 파일의 경우는 어떻게 될까요? 앞의 두 커밋이 모두 review.py 파일을 수정한 것이었기때문에 리셋을 하면 현재 review.py의 수정 내역을 보존 할 수 없습니다. 그래서 에러를 내고 리셋을 하지 않습니다. 혹시 모를 실수를 방지할 수 있겠네요.
{% highlight bash %}
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   review.py

no changes added to commit (use "git add" and/or "git commit -a")
$ git reset --merge 5dc0734
error: Entry 'review.py' not uptodate. Cannot merge.
fatal: Could not reset index file to revision '5dc0734'.
{% endhighlight %}

### 커밋 다시 살리기
만일 리셋을 한 상황에서 커밋을 다시 살리고 싶다면 어떻게 할 수 있을까요? 이때는 `git cherry-pick` 명령어를 사용하면 됩니다. cherry-pick 명령어 뒤에 다시 살리고 싶은 커밋의 해쉬를 적어주면 됩니다. git log를 해 보면 해당 커밋이 다시 살아난 것을 볼 수 있습니다. 
{% highlight bash %}
$ git cherry-pick 4e4af76
[master d3c00c9] review: Add update function
 Date: Tue Nov 27 22:02:40 2018 +1100
 1 file changed, 3 insertions(+)

$ git log
commit d3c00c946a0b20128068b14c1a041117c9bdae3f (HEAD -> master)
Author: Ken You <ken@10000lab.xyz>
Date:   Tue Nov 27 22:02:40 2018 +1100

    review: Add update function
{% endhighlight %}

몇개 안되는 명령어이지만 깃을 사용하다보면 이 명령어를 사용할 일이 꼭 생깁니다. 한번씩 연습 삼아서 해 보는 것도 좋습니다. 물론 연습용 깃 리포짓토리를 새로 만들고 말이죠.


## 참고
- 줄립 문서: [Git Guide](https://zulip.readthedocs.io/en/latest/git/fixing-commits.html)
- 깃허브: [Changing a commit message](https://help.github.com/articles/changing-a-commit-message/)
- 프로 킷: [A Short History of Git](https://git-scm.com/book/en/v2/Getting-Started-A-Short-History-of-Git)

---
