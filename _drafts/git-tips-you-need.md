---
title: 깃(Git) 유용한 팁
key: 20181126
categories: [git]
tags: [Git, git guide, git tips]
---

깃은 처음부터 리눅스 오픈 소스를 관리하기 위해 만들어졌습니다. 2005년에 그간 리눅스 소스를 관리하던 툴(BitKeeper)을 공급하던 회사와 관계가 안 좋아 지면서 자체적으로 사용할 수 있는 버전 관리 시스템이 필요하게 되었죠. 리눅스를 처음 만든 리누스 토발즈(Linus Torvalds)도 깃 개발에 적극적으로 참여하게 되고요. 깃 개발의 시작과 과정이 리눅스라고 하는 참으로 복잡한 소프트웨어와 수많은 컨트리뷰터를 위한 것이다 보니 여러 상황에 대처할 수 있는 유용한 기능이 많이 있습니다.

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
이상하게도 꼭 커밋을 하고 나면 너무 쉽게 발견되는 버그가 하나 투이 나옵니다. 수정은 항상 단 한 줄로 해결이 되고요. 다시 커밋을 해야하는데, 그냥 이전 커밋에 포함이 되었더라면 좋았을 것을 하는 생각이 들죠. 이때는  `git commit --amend`라고 명령어를 실행하면 됩니다.
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



## 참고
- 줄립 문서: [Git Guide](https://zulip.readthedocs.io/en/latest/git/fixing-commits.html)
- 깃허브 : [Changing a commit message](https://help.github.com/articles/changing-a-commit-message/)

---
