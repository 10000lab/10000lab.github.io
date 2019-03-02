---
title:  macOS 10.14 Mojave PHP,Apache 설치 - 홈브루 업그레이드
key: 20190302
categories: [php]
tags: [php, apache, macos]
---

이 글은 맥오에스 모자브에서 PHP 개발환경을 만들기 위한 사전 준비 작업에 해당하는 내용입니다. 특히  Homebrew/php 탭이 디프리케이션(deprecation)이 되면서 새로운 Homebrew/core 탭으로 개발환경을 설정하기 위해서 미리 해 두어야하는 작업에 해당합니다. 이전의 브루 포뮬러(brew formulae)에서 새로운 페클 페키지(pecl package) 방식으로 전환하는 가장 깔끔한 방법은 PHP 관련된 것을 모두 제거 하고 새로운 방식으로 설치를 하는 거겠죠.

>이 가이드는 [GRAV 개발 블로그](https://getgrav.org/blog/macos-mojave-apache-upgrade-homebrew)에 있는 내용을 실제 적용하면서 번역한 내용입니다.

먼저 해야할 것은 설치된 브루 패키지를 최신으로 업데이트를 하고 업그레이드까지 하는 것인데요. 실제로 이렇게 하면 코어 PHP 패키지를 자동으로 변환해 줍니다. 하지만 관련된 많은 심링크(symlink)들이 이후에 문제가 발생할 가능성이 있죠. 

그래서 업그레이드가 끝나면 PHP 패키지를 모두 삭제해서 새로운 설치를 할 수 있도록 하겠습니다.

{% highlight bash %}
$ brew update
$ brew upgrade
$ brew cleanup
{% endhighlight %}
실제 위 명령어를 순서대로 실행해 보면 시간이 꽤 걸립니다.

현재 설치뢰어 있는 PHP 패키지는 아래 명령어로 확인해 볼 수 있습니다.
{% highlight bash %}
$ brew list | grep php
{% endhighlight %}

이제 설치되어 있는 PHP관련된 모든 것을 지울 차례입니다. 아래 명령어 외에 위 list 명령어로 더 나온 것이 있으면 추가로 삭제를 합니다.

{% highlight bash %}
$ brew uninstall --force php56 php56-apcu php56-opcache php56-xdebug php56-yaml
$ brew uninstall --force php70 php70-apcu php70-opcache php70-xdebug php70-yaml
$ brew uninstall --force php71 php71-apcu php71-opcache php71-xdebug php71-yaml
$ brew uninstall --force php72 php72-apcu php72-opcache php72-xdebug php72-yaml
$ brew cleanup
{% endhighlight %}

위 목록 중에서 실제 설치가 안되었던 것이 있을 수도 있고, 빠진 것도 있을 겁니다. 이건 기존 어떤 패키지를 사용하느냐에따라 달라지는 거니까요. 알고 있는 패키지를 빼먹지 않고 우선 지우는게 중요합니다.

아래 명령어로 한 번 더 남아 있는게 있는지 확인해 봅니다.
{% highlight bash %}
$ brew list | grep php
{% endhighlight %}

아무것도 안 나오면 잘 된 거고요. 나오는게 더 있으면 `brew uninstall --force` 명령어를 사용해서 삭제를 해 줍니다.

마지막으로 이전에 저장된 설정파일도 모두 지우도록 하겠습니다.

{% highlight bash %}
$ rm -Rf /usr/local/etc/php/*
{% endhighlight %}

자 이제 이전에 설치했던 모든 내용을 다 삭제를 했습니다. 이제는 새로 설치를 해야겠죠.

---
