---
title: 깃(Git) 리베이스 사용하기
key: 20181126
categories: [git]
tags: [Git, git guide, git rebase]
---
여러 팀원이 작업한 내용이나 브랜치를 나눠서 작업하던 내용을 합치는 일반적인 깃 명령어는 `git merge`입니다. '가장 많이' 라는 말보다는 '거의' 라고 말하는 편이 맞겠죠. 그런데 깃에서 제공하는 인티그레이션용 명령어는 `git rebase`라는 것이 하나 더 있습니다. 실제로 팀 채팅 플랫폼인 [줄립(zuplip)](https://zulip.readthedocs.io/en/latest/git/overview.html)이나 오픈 강좌 플랫폼인 [이디엑스(edX)
](https://github.com/edx/edx-platform/wiki/How-to-Rebase-a-Pull-Request)에서는 머지를 통한 인티그레이션이 아닌 리베이스 방식을 사용하고 있습니다. 좀 더 정확히는 오픈 소스 컨트리뷰터가 풀 리퀘스트를 할때 리베이스를 사용하도록 하고 있는데요. 일반적인 팀 환경에서 기능 개발 후 master 머지 전에 이 기능을 사용하면 히스토리 관리가 깔끔하고 좋습니다.

![Git Rebase](/assets/images/git_rebase.png)

우선 리베이스가 어떤 것인지를 이야기해보고 어떻게 사용하는지를 보도록 하겠습니다.

## 리베이스란?
깃에서 각 커밋은 하나의 스냅샷으로 관리가 됩니다. ['깃은 스냅샷으로 관리됩니다'](/git/how-git-is-different.html#깃은-스냅샷으로-관리됩니다)에서 이부분을 간략한 설명을 보실 수 있습니다. 각 커밋은 깃에서 해시값으로 이름을 붙여 놓습니다. 개발이 진행되면서 커밋의 갯수가 쌓이면 아래처럼 해시로 이름 붙여진 노드로 이루어진 트리 구조를 보게 되는데요. 하나의 노드는 하나의 커밋을 대표하게 되겠죠. 예를 들어 설명을 하기 위해서 master 브랜치만 보이는 커밋 히스토리를 하나 가져왔습니다.

![Git rebase master](/assets/images/git_rebase_master.jpg)

그림에 보이는 부분만 보면 총 5개의 커밋이 보입니다. 그중에서 `ddf19c2` 커밋이 현재 master 브랜치의 HEAD입니다. 
간단한 예를 들기위해 오늘 오전에 개발 미팅이 있었다고 해보겠습니다. 미팅을 마치고 이 새로운 기능을 추가하기 위해서 브랜치를 하나 생성했습니다. 브랜치 이름을 심플하게 `new_feature`라고 정했고요. master의 HEAD인 `ddf19c2`를 베이스로 브랜치가 생성됩니다. 그리고 바로 일을 열심히 해서 3개의 커밋을 했습니다.

![Git rebase new branch](/assets/images/git_rebase_new_branch.jpg)

그 사이 master 브랜치에도 다른 개발자가 열심히 작업을 해서인지 3개의 커밋이 올라왔네요. 여기서 `ddf19c2` 커밋이 new_feature 브랜치의 `merge base`라는 이름으로 불립니다. 간단히 `base`라고 부르기도 하고요. 그냥 그림으로 봐도 왜 베이스라고 하는지는 알겠네요. 새로운 커밋 노드가 시작되는 베이스니까요. 

여기서 new_feature 브랜치에 master에 추가된 3개의 커밋을 가져오려면 어떻게 해야할까요? 앞서 말했듯이 일반적인 방법은 머지를 하는 것입니다. 하지만 여기서는 리베이스를 해 보겠습니다.

![Git rebase rebased](/assets/images/git_rebase_rebased.jpg)

리베이스를 하고 난 후 노드 트리입니다. 원래 new_feature 브랜치의 베이스는 `ddf19c2`였는데요. 리베이스를 하고 난 후 베이스가 `4ac8340`으로 변경이 되었습니다. 왜 `리베이스`라고 하는지 바로 이유가 나오네요. 리베이스는 실제로 커밋된 히스토리를 변경하는 효과가 있습니다. 리베이스가 어떤 일을 하는지 순서대로 정리해 볼까요.
1. 우선 new_feature 브랜치에서 머지 베이스(ddf19c)로부터 추가된 커밋을 추려 보관합니다. 
2. 마치 new_feature 브랜치가 master의 HEAD인 4ac8340에서 시작된 것처럼 베이스를 옮깁니다.
3. 그리고 new_feature 브랜치에서 생성한 커밋을 새로 적용합니다. 

그래서 위와 같은 노드 트리가 나오게 됩니다. 그림으로만 봐도 히스토리가 깔끔하게 변한게 확연히 들어납니다. 새로운 베이스 위에 추가된 6a8126c, 927d6d4, bb6bd8a는 리베이스가 되기 전에는 각각 c845ee4, 6ecbac9, 4214826으로 불리던 커밋이었습니다. 이처럼 깔끔하게 수정된 내역이 정돈되어 보이는 효과가 있기는 하지만 히스토리가 좀 변경되는 면이 있습니다. 아래는 new_feature 브랜치를 rebase한 후 master 브랜치에 머지를 한 후 커밋 트리 모습입니다. 깔끔하고 보기 좋네요.

![Git rebase and merged](/assets/images/git_rabase_merged.jpg)

## 리베이스관련 명령어
깃 리베이스 명령어는 `git rebase`입니다. 상황을 좀 간단하게 만들기 위해서 master 브랜치가 최종 인티그레이션이 이루어지는 브랜치라고 하겠습니다. 여기서 새 기능을 개발하기 위한 피처 브랜치(feature branch)를 new_feature라는 이름으로 생성했다는 상황에서 작업을 해 보려고 합니다.

### 새로운 브랜치
새로운 기능을 개발하기 위해서 제일 먼저 해야할 일은 깃 브랜치를 생성하는 것입니다. 이거는 너무 당연한 습관이면서 원칙이 되어야겠죠. 현재 브랜치인 master에서 new_feature라는 이름으로 브랜치를 생성합니다.
{% highlight bash %}
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
$ git checkout -b new_feature
Switched to branch 'new_feature'
{% endhighlight %}

새로 생성된 브랜치에서 기능을 구현하여 완성이 되었습니다. 그간 총 3번의 커밋을 하게 되었습니다. 깃에서는 커밋이 참 중요한 행동중의 하나인데요. 꼭 ['깃 커밋 가이드'](/git/git-commit-discipline.html)를 참고해서 커밋을 하면 좋겠습니다. 아래는 현재 상황을 나타내는 트리입니다.

![Git rebase example 01](/assets/images/git_rebase_example_01.jpg)

파란색 트리가 master를 나타내고 붉은색 트리가 new_feature 트리를 보여주고 있습니다. 머지 베이스는 `bb6bd8a`로 되어 있습니다. 위 트리는 현재 로컬 머신에 있는 내용이기때문에 master에 새로운 내용이 없는 것처럼 보입니다. 하지만 분명히 그동안 중앙 서버인 리모트 master에는 다른 개발자가 올린 내용이 들어 있겠죠.

### master 풀 받기
결과적으로는 이 작업된 내용이 master에 머지가 되어야할 텐데요. 머지를 하기 전에 master를 체크아웃하고 리모트로부터 풀을 받아 보겠습니다.
{% highlight bash %}
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
$ git pull origin master
From https://github.com/saykent/gitwork
 * branch            master     -> FETCH_HEAD
   21dc8c8..b8ab574  master     -> origin/master
Updating bb6bd8a..b8ab574
Fast-forward
 order.py | 7 +++++++
 1 file changed, 7 insertions(+)
{% endhighlight %}
여기까지 하고 커밋 트리를 한번 볼까요. 특별할 것 없이 예상했던 대로 master에 다른 개발자가 푸시한 내용이 들어 있습니다.

![Git rebase example 02](/assets/images/git_rebase_example_02.jpg)

### 스쿼시, 리워드
이 과정은 커밋 히스토리를 조정하는 기능입니다. 기능을 개발하는 중에 사소한 부분을 여러면 커밋한 적이 있다면 이 커밋을 큰 하나의 커밋으로 변경을 할 수 있습니다. 때로는 간단한 문구 수정이나, 줄 간격 맞추기 등도 수정을 한 후 커밋을 하게 되니까요. 이 수정도 리베이스 명령어를 사용하게 되는데요. `git rebase -i HEAD~n` 처럼 명령어를 사용하게 되고요. 여기서 n은 변경을 하고자하는 커밋 갯수입니다. 여기서는 n을 3으로 넣어서 명령어를 실행해 보겠습니다.
{% highlight bash %}
$ git rebase -i HEAD~3
{% endhighlight %}

명령어를 실행하면 바로 에디터가 실행이 됩니다.
{% highlight vim %}
pick 44d0f0c new feature: Add add feature function.
pick 85dd2a2 order: Fix add function error.
pick 6411bf5 order: Add update function.
 
# Rebase bb6bd8a..6411bf5 onto bb6bd8a (3 commands)
#
{% endhighlight %}
첫 3 줄을 보면 그간 커밋한 내용이 보입니다. 여기서 남기고 싶은 커밋을 pick으로 합치고 싶은 커밋은 `squash`라고 적어주면 됩니다. 추가로 내용을 수정하고 싶은 커밋은 `reword`라고 하면 됩니다. 맨 위의 것만 남기고 두번째는 squash, 세번째는 reword라고 하고 내용을 변경하겠습니다. 이렇게 하면 squash된 커밋은 위쪽에 있는 pick에 합쳐지게 됩니다. reword는 커밋은 남아 있지만 커밋 메시지는 변경됩니다. 변경한 내용은 저장하고 에디터를 종료하면 됩니다. 종료함과 동시에 새로운 에디터가 다시 열리는데요. 마찬가지로 그냥 저장 종료를 하면 됩니다. 결과물은 2개의 커밋이 됩니다.
{% highlight bash %}
pick 44d0f0c new feature: Add add feature function.
squash 85dd2a2 order: Fix add function error.
reword 6411bf5 new feature: Add update function.
{% endhighlight %}

### 리베이스
master를 리모트에 있는 최신으로 풀을 받았으니 new_feature 브랜치를 합니다. 현재 체크아웃 브랜치가 master라면 `git rebase master new_feature` 명령어를 실행합니다. 만일 현재 브랜치가 new_feature라면 마지막 new_feature를 생략하고 `git rebase master` 하면 됩니다.
{% highlight bash %}
$ git rebase master new_feature
First, rewinding head to replay your work on top of it...
Applying: new feature: Add add feature function.
Applying: order: Fix add function error.
Applying: order: Add update function.
$ git status
On branch new_feature
nothing to commit, working tree clean
{% endhighlight %}

명령어를 실행하니 우선 new_feature의 HEAD를 옮기고 new_feature에 있었던 3개의 커밋을 차례로 다시 적용하는 것을 볼 수 있습니다. 커밋 트리를 보면 머지와는 다르게 깔끔하게 히스토리가 변한 것을 볼 수 있습니다. 붉은 색으로 표시된 커밋이 new_feature 브랜치입니다. 아직 머지 전이라서 master는 b8ab574를 HEAD로 하고 있습니다. 보기 좋네요. 주의할 점은 브랜치가 자동으로 `new_feature`로 변경되었다는 점입니다. 리베이스와 커밋을 적용하는 작업이 new_feature 브랜치에서 되어야하니까 당연히 브랜치가 변경하는 것이 맞겠죠.

![Git rebase example 04](/assets/images/git_rebase_example_04.jpg)

### 컨플릭트
리베이스를 할때 master에서 수정한 위치와 new_feature 브랜치에서 수정한 소스의 위치가 같으면 컨플릭트가 생깁니다. 이 상황에서 깃은 리베이스를 멈추고 수동으로 컨플릭트를 해결할 것을 요청합니다. 이뿐 아니라 다른 옵션도 함께 알려주는데요. 리베이스를 포기하고 리베이스 이전 상태로 돌릴 수 있는 방벙을 알려줍니다. `git rebase --abort` 명령어입니다. 

컨플릭트를 해결하는 것은 머지를 하면서 발생한 컨플릭트를 해결하는 것과 동일합니다. 우선 `git status` 명령어로 어떤 파일이 컨플릭트가 났는지 확인합니다. 다음 에디터를 열어서 컨플릭트를 해결합니다. 다시 `git add` 명령어를 사용해서 컨플릭트 난 파일을 추가합니다. 마지막으로 git commit 명령어 대신에 `git rebase --continue` 명령어를 사용합니다. 간혹 git add 후에 git rebase --continue 를 헸는데 여전히 컨플릭트를 수정하라는 메시지가 나올 때가 있습니다. 수정된 파일이 이미 커밋된 내용과 동일한 경우인데요. 바로 아래와 같은 경우입니다. 이때는 `git rebase --skip`을 해 주면 됩니다.
{% highlight bash %}
$ vim order.py 
$ git add order.py 
 git rebase --continue
Applying: order: Change order params.
No changes - did you forget to use 'git add'?
If there is nothing left to stage, chances are that something else
already introduced the same changes; you might want to skip this patch.

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".

$ git rebase --skip
{% endhighlight %}

### 다시 master로
리베이스 작업이 끝났습니다. 설명은 여러가지 경우를 나열하고 있어 복잡해 보이지만 실제로 해 보면 간단합니다. 리베이스라는 단어가 갖는 의미만 이해를 하고 있어도 사용하는데 문제가 없을 정도니까요. 이 상태에서 다시 master로 브랜치를 변경하고 `git merge new_feature`를 해서 master에 머지를 합니다.
{% highlight bash %}
$ git status
On branch new_feature
nothing to commit, working tree clean
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
$ git merge new_feature
Updating b8ab574..c0180e7
Fast-forward
 feature.py | 6 ++++++
 1 file changed, 6 insertions(+)
{% endhighlight %}

![Git rebase example 05](/assets/images/git_rebase_example_05.jpg)

머지 후에도 여전히 커밋트리가 깔끔하게 보이네요. 앞서 해 보았던 머지와는 다르게 머지 커밋도 따로 없고요. 마치 master에서 계속 작업을 해 온 것 같은 트리를 보여줍니다.

## 주의할 점
### 강제로 푸시하기
리베이로 작업을 하다보면 중앙 서버로 푸시를 할 때 문제가 생기는 경우가 있습니다. 예를 들어 중앙 서버에 푸시된 new_feature 브랜치가 오전에 master를 리베이스한 것이라고 해 보겠습니다. 퇴근 전까지 작업한 내요을 푸시하기 전에 현재 master를 풀 받아서 리베이스를 다시 했습니다. 그리고 `git push origin new_feature`라고 명령어를 실행합니다. 이때 깃은 푸시를 하지 못 하고 에러를 냅니다. origin 서버에 있는 내용과 현재 로컬에 있는 내용의 머지 베이스가 다르기 때문인데요. 이때는 브랜치 이름 앞에 `+`를 붙여서 푸시를 하면 됩니다. `git push origin +new_feature` 처럼 하면 됩니다.

### 공용 브랜치
위와 비슷한 이유로 브랜치가 공용 브랜치인 경우 리베이스를 사용하려면 주의가 필요합니다. 머지 베이스가 서로 다른 상태에서 origin에 있는 내용을 풀 받거나 푸시를 할 때 복잡한 이슈가 생기기때문인데요. 만일 현재 작업하는 브랜치가 혼자 작업하는 브랜치가 아니라면 master와 리베이스 없이 사용을 하다가 꼭 필요한 경우만 리베이스를 하기를 권장 합니다. 단 작업이 완료가 된 후 마지막 master에 머지하기 전에는 꼭 리베이스를 해야합니다.

### 그냥 머지
여기서 잠깐, 비교를 해 보기위해서 리베이스 대신에 머지를 해 볼까요. 실제 리베이스로 작업을 할때는 머지를 하면 안됩니다. 여기서는 머지 트리를 비교해 보고 싶어서 소스를 다른 폴더에 복사를 한 후 해 봤습니다.
{% highlight bash %}
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

$ git merge new_feature
Merge made by the 'recursive' strategy.
 feature.py | 6 ++++++
 1 file changed, 6 insertions(+)
{% endhighlight %}

커밋 트리는 아래와 같이 보입니다. 특별히 `1fd8f16` 커밋은 `git merge` 명령어를 사용할 때 자동으로 생성되는 커밋으로 `머지 커밋`이라고 합니다. 트리를 보면 new_feature 브랜치가 머지 베이스로부터 수정이 되어 머지 커밋인 '1fd8f16'에서 합쳐지는 것을 볼 수 있습니다. 트리 모양이 복잡해 보이는 것은 물론이고 머지 커밋이라고 하는 자동 생성되는 커밋이 하나 더 추가된 것을 볼 수 있습니다.

![Git rebase example 03](/assets/images/git_rebase_example_03.jpg)

## 깃 실전 가이드
1. [깃은 뭐가 다른가](/git/how-git-is-different.html)
2. [깃과 함께 개발하기](/git/using-git-as-you-work.html)
3. [깃 커밋 가이드](/git/git-commit-discipline.html)
4. [깃 리베이스 사용하기](/git/git-rebase-workflow.html)
5. [깃 유용한 팁](/git/git-tips-you-need.html)
6. [깃 용어 정리](/git/important-git-terms.html)
7. [깃 필수 명령어](/git/git-cheat-sheet.html)

## 참고
- edX [How to Rebase a Pull Request
](https://github.com/edx/edx-platform/wiki/How-to-Rebase-a-Pull-Request)
- zulip [Git Guide](https://zulip.readthedocs.io/en/latest/git/index.html)
- Git scm [Git Branching - Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

---