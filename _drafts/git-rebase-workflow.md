---
title: 깃(Git) 리베이스 사용하기
key: 20181122
categories: [git]
tags: [Git, git guide, git rebase]
---
여러 팀원이 작업한 내용이나 브랜치를 나눠서 작업하던 내용을 합치는 일반적인 깃 명령어는 `git merge`입니다. 가장 많이 사용한다는 표현보다는 거의 이 명령어를 사용해서 작업을 한다고 생각하는 편이 맞겠죠. 한지만 깃에서 제공하는 인티그레이션용 명령어는 `git rebase`라는 것이 하나 더 있습니다. 실제로 팀 채팅 플랫폼인 [줄립(zuplip)](https://zulip.readthedocs.io/en/latest/git/overview.html)이나 오픈 강좌 플랫폼인 [이디엑스(edX)
](https://github.com/edx/edx-platform/wiki/How-to-Rebase-a-Pull-Request)에서는 머지를 통한 인티그레이션이 아닌 리베이스 방식을 사용하고 있습니다.

우선 리베이스가 어떤 것인지를 이야기해보고 머지와 어떤 점이 다른지를 예제를 통해서 확인해 보도록 하겠습니다.

## 리베이스란?
깃에서 각 커밋은 하나의 스냅샷으로 관리가 됩니다. ['깃은 스냅샷으로 관리됩니다'](/git/how-git-is-different.html#깃은-스냅샷으로-관리됩니다)에서 이부분을 간단한 설명을 보실 수 있습니다. 각 커밋은 깃에서 해시값으로 이름을 붙여 놓습니다. 개발이 진행되면서 커밋의 갯수가 쌓이면 아래처럼 해시로 이름 붙여진 노드로 이루어진 트리 구조를 보게 되는데요. 하나의 노드는 하나의 커밋을 대표하게 되겠죠. 예를 들어 설명을 하기 위해서 master 브랜치만 보이는 커밋 히스토리를 하나 가져왔습니다.

![Git rebase master](/assets/images/git_rebase_master.jpg)

그림에 보이는 부분만 보면 총 5개의 커밋이 보입니다. 그중에서 `ddf19c2` 커밋이 현재 master 브랜치의 HEAD입니다. 여기서 오늘 미팅에서 나온 새로운 기능을 추가하기 위해서 브랜치를 하나 생성했습니다. 브랜치 이름을 심플하게 `new_feature`라고 정했고요. 열심히 작업을 해서 3개의 커밋을 했습니다.

![Git rebase new branch](/assets/images/git_rebase_new_branch.jpg)

그 사이 master 브랜치에도 3개의 커밋이 올라왔네요. 여기서 `ddf19c2` 커밋이 new_feature 브랜치의 `merge base`라는 이름으로 불립니다. 간단히 `base`라고 부르기도 하고요. 그냥 그림으로 봐도 왜 베이스라고 하는지는 알겠네요. 새로운 커밋 노드가 시작되는 베이스니까요. 

여기서 new_feature 브랜치에 master에 추가된 3개의 커밋을 가져오려면 어떻게 해야할까요? 앞서 말했듯이 일반적인 방법은 머지를 하는 것입니다. 하지만 여기서는 리베이스를 해 보겠습니다.

![Git rebase rebased](/assets/images/git_rebase_rebased.jpg)

리베이스를 하고 난 후 노드 트리입니다. 원래 new_feature 브랜치의 베이스는 `ddf19c2`였는데요. 리베이스를 하고 난 후 베이스가 `4ac8340`으로 변경이 되었습니다. 왜 `리베이스`라고 하는지 바로 이유가 나오네요. 리베이스는 실제로 커밋된 히스토리를 변경하는 효과가 있습니다. 우선 new_feature 브랜치에서 머지 베이스(ddf19c)로부터 추가된 커밋을 추려 보관합니다. 마치 new_feature 브랜치가 master의 HEAD인 4ac8340에서 시작된 것처럼 베이스를 옮깁니다. 그리고 new_feature 브랜치에서 생성한 커밋을 새로 적용합니다. 그래서 위와 같은 노드 트리가 나오게 됩니다. 그림으로만 봐도 히스토리가 깔끔하게 변한게 확연히 들어납니다. 

## 


참고
- edX [How to Rebase a Pull Request
](https://github.com/edx/edx-platform/wiki/How-to-Rebase-a-Pull-Request)
- zulip [Git Guide](https://zulip.readthedocs.io/en/latest/git/index.html)
- Git scm [Git Branching - Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)

---