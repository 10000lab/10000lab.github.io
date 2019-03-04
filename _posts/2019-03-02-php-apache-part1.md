---
title:  macOS 10.14 Mojave PHP,Apache 설치 - Part1
key: 20190302p1
categories: [php]
tags: [php, apache, macos]
---

macOS 10.14 모자브(Mojave)에서 PHP 웹 개발환경을 만드는 가이드입니다.

맥에서 웹앱을 개발하는 것은 자체만으로도 폼이나고 기분이 좋기는 합니다. 특히 윈도우즈 환경에서 개발을 하던 분이라면 더 절실히 느껴지는 바가 있죠. 맥에서 개발환경을 설정하는 방식은 여러가지가 있겠지만 좀 더 직관적이고 이해하기 쉬운 방법을 기술하고자 합니다.  

>이 가이드는 [GRAV 개발 블로그](https://getgrav.org/blog/macos-mojave-apache-multiple-php-versions)에 있는 내용을 실제 적용하면서 번역한 내용입니다.

이번 가이드에서는 `Apache 2.4`와 `여러개의 PHP 버전`을 설치하고 설정하는 과정을 차례로 설명합니다. [두번째 파트](/php/php-apache-part2.html)에서는 `MYSql, Apache virtual hosts, Xdebug`를 설치해 보도록 하겠습니다.

>이미 Homebrew/php 탭을 사용하여 PHP를 설치한 적이 있는데 Homebrew/core 탭을 사용한 방식으로 변경을 하고자 하신다면 [홈브루 업데이트](/php/upgrading-homebrew.html)를 먼저 보신 후 다시 이 가이드를 보시기 바랍니다. 우선 이전 설치된 내용을 깨끗이 지우는 과정이 설명되어 있습니다.

# Xcode 커멘드라인 툴 설치
아직 Xcode를 설치한 적이 없다면 꼭 설치를 해야합니다. 홈브루가 Xcode 커멘드라인 툴을 사용하기때문인데요. Xcode를 설치한 후 아래 명령어를 꼭 실행하시기 바랍니다.

{% highlight bash %}
$ xcode-select --install
{% endhighlight %}

# 홈브루 설치
여기서 설명드릴 설치과정은 macOS 패키지 매니저인 홈브루(Homebrew)가 사용됩니다. `brew` 커멘드를 사용해서 여러가지 기능을 맥에 설치할 수 있는데요. 우선 수동으로 `brew`를 설치해 줘야합니다. 설치는 아주 간단합니다. 터미널을 열어서 아래 명령어만 실행하면 됩니다.
{% highlight bash %}
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}
터미널에 나타나는 내용을 그대로 따라가면 되는데요. 가끔씩 비밀번호를 치라고 하면 넣어주세요. 시간이 좀 걸리는 작업이니 시간을 갖고 기다리시면 됩니다. 설치가 끝났다고 하면 아래 명령어를 입력해 보면 설치가 제대로 되었는지 금방 확인할 수 있습니다. 출력되는 내용은 좀 다를 수 있겠지만, 비슷하게 보이면 일단을 안심하면 됩니다.
{% highlight bash %}
$ brew --version
Homebrew 2.0.2
Homebrew/homebrew-core (git revision 1e709; last commit 2019-03-02)
{% endhighlight %}

아래 명령어를 쳐 보면 설치도 잘 되었고 설정도 문제 없는지 확인해 볼 수 있습니다.
{% highlight bash %}
$ brew doctor
{% endhighlight %}
만일 어딘가 문제가 있다면 이 명령어 끝에 어떤 조치를 취해야하는지도 같이 설명해 줍니다. 대부분 그대로 따라하면 해결이 되고요. 참고로 대부분은 경고라고 되어 있어서 실제 어떤 문제가 발생하고 있지 않다면 참고 사항 정도로만 생각해도 괜찮습니다.

# 모자브에 필요한 라이브러리
새로 설치된 모자브에서 이후 진행되는 과정 중에 라이브러리가 없다고 에러를 내는 경우가 있습니다. 문제가 나기 전에 우선 아래 라이브러리를 설치해 줍니다.
{% highlight bash %}
brew install openldap libiconv
{% endhighlight %}

{%- include google-adsense/article-middle.html -%}

# 아파치 설치
맥오에스 모자브에는 아파치 2.4버전이 이미 설치되어 있습니다. 그런데 애플에서 몇몇 스크립트를 제거해서 홈브루와 함께 이 아파치를 사용하기는 어렵게 되었습니다. 방법은 아파치도 홈브루를 사용해서 추가로 다시 설치하고 기본 포트(80, 443)를 사용할 수 있도록 설정을 해 주는 것입니다.

이미 기본 설치된 아파치를 사용하고 있다면 우선 실행을 중단하고 자동 실행을 하는 스크립트를 삭제합니다. 아래 명령어를 차례로 실행합니다. 맥에서 한번도 아파치를 사용해 본 적이 없더라도 괜찮으니까 그냥 실행하면 됩니다.
{% highlight bash %}
sudo apachectl stop
sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
{% endhighlight %}

다음은 홈브루에서 제공하는 아파치를 설치합니다.
{% highlight bash %}
brew install httpd
{% endhighlight %}

일부러 옵션을 추가하지 않았다면 새로 컴파일을 하지 않기때문에 비교적 빠른 시간에 설치가 끝날 겁니다. 설치가 끝나면 아래와 같은 메시지를 보여줍니다.
{% highlight bash %}
==> Summary
🍺  /usr/local/Cellar/httpd/2.4.38: 1,648 files, 26.9MB
{% endhighlight %}

새로 설치된 아파치가 자동으로 실행될 수 있도록 아래 명령어를 실행해 줍니다. 아파치가 설치되면서 자동으로 실행이 되었을 수도 있습니다. 그럴때는 명령어를 실행하면 이미 실행중이라고 나옵니다.
{% highlight bash %}
sudo brew services start httpd
{% endhighlight %}

여기까지 홈브루 아파치를 설치하고 자동으로 실행될 수 있도록 했습니다. 아파치가 정상적으로 실행이 되고 있을테니 브라우저로 [http://localhost:8080](http://localhost:8080)을 열면 "It works!"라고 하는 아주 단순한 화면을 볼 수 있을겁니다. 만일 문제가 있다면 어떻게 확인해 봐야하는지 바로 설명해 드리겠습니다.

![brew apache works](/assets/images/201903/brew_apache01.png)

# 아파치가 동작하지 않는 경우
브라우저에서 웹서버에 연결할 수 없다는 메시지를 보여준다면 우선 아파치가 실행중인지를 먼저 확인해 봅니다.
{% highlight bash %}
ps -aef | grep httpd
{% endhighlight %}

아파치가 실행중이라면 httpd 프로세스가 몇개 동시에 돌고 있는 것이 보여야합니다.

아파치를 다시 실행을 해 봅니다.
{% highlight bash %}
sudo apachectl -k restart
{% endhighlight %}
이때 만일 설정파일 같은데 문제가 있다면 에러를 보여줍니다. 에러 메시지를 보고 설정파일을 수정합니다. 기존에 아파치를 설치했던 적이 있는 경우 이런 에러가 날 수 있습니다. 이때는 설정파일을 다른데 백업을 해 두고 설정파일을 모두 지우고 아파치를 다시 설치합니다.
{% highlight bash %}
brew uninstall --force httpd
rm -rf /usr/local/etc/httpd/*
brew install httpd
{% endhighlight %}

맥의 호스트명을 변경한 경우에 아파치에서 에러를 내는 경우도 있습니다.
{% highlight bash %}
$ sudo apachectl -k restart
AH00557: httpd: apr_sockaddr_info_get() failed for macmini
{% endhighlight %}
이 경우에는 `/etc/hosts` 파일에 터미널 프롬프트에 보이는 맥의 호스트명을 추가해 줍니다.

아파치의 에러 로그를 터미털에서 볼 수도 있습니다. 새로운 터미널을 하나 열어서 아래 명령어를 실행하고 어떤 에러 메시지가 나오는지 확인해 봅니다.
{% highlight bash %}
$ tail -f /usr/local/var/log/httpd/error_log
{% endhighlight %}

아파치는 `apachectl` 명령어로 실행을 하게됩니다. 자주 사용하는 유용한 명령어를 미리 익혀두면 좋습니다.
{% highlight bash %}
$ sudo apachectl start
$ sudo apachectl stop
$ sudo apachectl -k restart
{% endhighlight %}
>아파치 명령어에 사용되는 `-k` 옵션은 강제로 바로 재 시작하라는 의미입니다.

# 아파치 설정
아파치 설치가 끝나고 잘 동작하는 것을 확인했습니다. 이제는 개발용 서버로 적합하도록 좀 더 설정을 변경할 차례립니다.
## 포트
브루로 설치한 아파치는 기본으로 8080 포트를 사용하고 있습니다. 이를 80 포트로 변경을 하려면 아파치의 설정 파일을 수정해 줘야합니다. 에디터로 아파치 설정 파일을 열어주세요. 본 가이드에서는 비주얼 스튜디오 코드(Visual Studio Code)를 사용했습니다. vi나 주로 사용하는 텍스트 에디터로 설정파일을 열어주세요.
{% highlight bash %}
$ code /usr/local/etc/httpd/httpd.conf
{% endhighlight %}

아래와 같은 라인을 찾아서 
{% highlight apache %}
Listen 8080
{% endhighlight %}
80 으로 변경해 주세요.
{% highlight apache %}
Listen 80
{% endhighlight %}
## 도큐먼트 루트
다음은 아파치 도큐먼트 루트(Document root)를 변경합니다. 이 폴더는 아파치가 파일을 찾는 곳입니다. 기본으로는 `/usr/local/var/www`로 되어 있습니다. 이 가이드는 개발환경을 만들고 있으므로 사용자의 홈 디렉토리 안에 도큐먼트 루트를 만든다고 가정하겠습니다.

설정 파일에서 `DocumentRoot` 라는 단어를 찾습니다. 아래처럼 보일 겁니다.
{% highlight apache %}
DocumentRoot "/usr/local/var/www"
{% endhighlight %}
이를 아래와 같이 수정합니다. `your_user`는 사용하고 있는 어카운트 이름으로 변경해야합니다.
{% highlight apache %}
DocumentRoot /Users/your_user/Sites
{% endhighlight %}
또한 도큐먼트 루트 바로 밑에 있는 `<Directory>` 태그도 변경해야합니다. 여기도 도큐먼트 루트와 같은 경로로 변경합니다.
{% highlight apache %}
<Directory /Users/your_user/Sites>
{% endhighlight %}
같은 `<Directory>` 블럭 안에서 `AllowOverride`를 찾습니다. 그리고 아래와 같이 수정합니다.
{% highlight apache %}
AllowOverride All
{% endhighlight %}
다음은 `mod_rewrite`를 활성화해야합니다. 기본적으로는 코멘트로 막혀있기때문에 `mod_rewrite.so`를 찾아서 `#`을 제거합니다.
{% highlight apache %}
LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so
{% endhighlight %}

## User & Group
아파치 설정을 변경해서 홈 디렉토리에 있는 Sites 폴더를 사용하도록 변경했습니다. 아직 남아 있는 문제가 하나 더 있습니다. 아파치는 기본적으로는 `_www` 사용자, `_www` 그룹으로 설행이 됩니다. 이 설정은 홈 디렉토리에 있는 파일을 접근할 때 문제가 됩니다.  `httpd.conf` 파일 3분의 1 쯤 내려가면 아파치가 실행할때 사용될 `User`와 `Group`을 찾을 수 있습니다. 이 값을 사용자 계정 아이디(`your_user`를 실제 아이디로 변경하세요)와 그룹(`staff`)으로 변경을 합니다.
{% highlight apache %}
User your_user
Group staff
{% endhighlight %}

## Severname
아파치는 실행할 때마다 Severname을 설정에서 찾습니다. 기본적으로는 코멘트로 막혀있는데요. 찾아서 코멘트를 풀어주고 값을 변경합니다.
{% highlight apache %}
#ServerName www.example.com:8080
{% endhighlight %}
위의 내용을 아래처럼 바꿔 줍니다.
{% highlight apache %}
ServerName localhost
{% endhighlight %}

## Sites 폴더
이제 홈 디렉토리에 `Sites` 폴더를 만들 차례입니다. 폴더를 만들고 `index.html`을 생성합니다.
{% highlight bash %}
$ mkdir ~/Sites
$ echo "<h1>My User Web Root</h1>" > ~/Sites/index.html
{% endhighlight %}

이제 아파치를 재 실행해서 지금까지 변경한 내용이 잘 반영되는지 확인해 봅니다.
{% highlight bash %}
$ sudo apachectl -k restart
{% endhighlight %}
브라우저를 열어서 [http://localhost](http://localhost)로 이동을 해 봅니다. index.html에 새로 넣은 내용이 나온다면 잘 동작하는 겁니다. 

![brew apache works](/assets/images/201903/brew_apache02.png)

# PHP 설치
>이미 홈브루를 사용하여 PHP를 설치한 적이 있면 [홈브루 업데이트](/php/upgrading-homebrew.html)를 먼저 보신 후 다시 이 가이드를 보시기 바랍니다. 우선 이전 설치된 내용을 깼끗이 지우는 과정이 설명되어 있습니다.

2018년 3월 이전에는 PHP관련된 브루 패키지는 `Homebrew/php`  탭으로 관리가 되었습니다. 하지만 이후로는 지원이 중단되고 `Homebrew/core`로 지원이 변경되었죠. 앞으로는 좀 더 지원이 잘 될게 예상되지만 현재는 패키지가 좀 부족한 것들이 있습니다.

PHP 5.6과 PHP 7.0 둘 다 브루에서 지원이 중단되었습니다. 이유는 두 버전 모두 [PHP에서도 공식적으로 업데이트가 중단](http://php.net/supported-versions.php)되었기 때문이죠. 같은 이유로 이 버전으로 프러덕션에 적용하는 것은 좋은 생각이 아닙니다. 하지만 개발중에는 어쩔 수 없이 이 버전들을 사용해야하는 경우가 생기게 되죠.

브루에서는 공식적으로는 PHP 7.1에서 7.3까지만 지원하고 있습니다. 그래서 PHP 5.6과 7.0을 설치하려면 아래 탭을 추가해 줘야합니다.
{% highlight bash %}
$ brew tap exolnet/homebrew-deprecated
{% endhighlight %}

먼저 여러가지 버전의 PHP를 설치하겠습니다. 그리고 사용할 버전을 변경할 수 있는 간단한 스크립트를 작성할 겁니다. 아래 설치 명령어 중에서 설치를 원하지 않는 버전은 빼고 설치를 하시면 됩니다.
{% highlight bash %}
$ brew install php@5.6
$ brew install php@7.0
$ brew install php@7.1
$ brew install php@7.2
$ brew install php@7.3
{% endhighlight %}
첫번째 있는 버전은 설치하는데 시간이 좀 어래 걸릴겁니다. 디펜던시가 있는 다른 패키지를 좀 설치를 해야하기때문인데요. 그 다음 부터는 좀 더 빠르게 설치가 됩니다.
> 새로운 버전을 설치할 때마다 `unlink`를 할 필요는 없습니다. 자동으로 링크가 되지 않기때문입니다.

PHP를 사용하다보면 필요에따라 설정을 변경할 일이 생기는데요. 메모리 설정이나 `date.timezone` 설정 같은 것을 주로 변경하게 됩니다. 이때 각 PHP 버전별 `pho.ini` 파일이 위치한 경로는 아래와 같습니다.
{% highlight bash %}
/usr/local/etc/php/5.6/php.ini
/usr/local/etc/php/7.0/php.ini
/usr/local/etc/php/7.1/php.ini
/usr/local/etc/php/7.2/php.ini
/usr/local/etc/php/7.3/php.ini
{% endhighlight %}

설치가 끝나면 마지막 설치된 버전으로 PHP가 되어 있을텐데요. 첫번째 설치한 버전으로 변경을 해 보겠습니다.
{% highlight bash %}
$ brew unlink php@7.3 && brew link --force --overwrite php@5.6
{% endhighlight %}

>이 시점에서 모든 열려있는 터미널을 우선 닫기를 추천합니다. 이 후로 진행하는 과정은 새로운 터미널을 열고 해야한다는 의미인데요. 그렇지 않으면 정말 이상한 실행 경로 관련된 이슈가 생깁니다. 꼭 모든 터미널을 닫고 터미널을 종료한 후 새로 시작해 주세요.

간단히 우리가 원하는 버전으로 PHP가 설정되어 있는지 살펴보겠습니다.
{% highlight bash %}
$ php -v
PHP 5.6.40 (cli) (built: Feb 11 2019 14:27:20) 
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
{% endhighlight %}

# Apache PHP 설정 - Part 1
지금까지 PHP 여러버전을 성공적으로 설치했습니다. 하지만 아파치가 설치된 PHP를 사용하도록 설정을 변경해 줘야합니다. 앞서 한번 수정을 했던 적이 있는 `/usr/local/etc/httpd/httpd.conf` 파일을 열어서 `LoadModule` 목록 중에서 제일 끝으로 이동합니다.

앞에서 이 글의 내용대로 따라왔다면 마지막 내용은 `mod_rewrite` 모듈이 될 겁니다.
{% highlight apache %}
LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so
{% endhighlight %}
위 내용 밑으로 아래에 있는 `libphp` 모듈을 추가해주세요.
{% highlight apache %}
LoadModule php5_module /usr/local/opt/php@5.6/lib/httpd/modules/libphp5.so
#LoadModule php7_module /usr/local/opt/php@7.0/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.1/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.2/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.3/lib/httpd/modules/libphp7.so
{% endhighlight %}

PHP 모듈은 한번에 하나씩만 사용할 수 있습니다. 지금 설정한대로라면 `php@5.6`만 활성화 되어 있는 상태입니다. 아파치로 하여금 PHP 리퀘스트가 들어오면 PHP 5.6 버전을 사용하라고 알려주는 겁니다. (PHP 버전간 변경을 하는 방법은 조금 후에 설명합니다.)

추가로 PHP를 위한 디렉토리 인덱스를 설정해야합니다. 아래 내용이 있는 블럭을 아파치 설정 파일에서 찾습니다.
{% highlight apache %}
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
{% endhighlight %}
그리고 아래 내용으로 변경을 합니다.
{% highlight apache %}
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
{% endhighlight %}

변경 내용을 저장하고 아파치를 새로 시작해 보겠습니다. 이제 새로 설치된 PHP를 사용할 수 있게되었네요.
{% highlight bash %}
$ sudo apachectl -k stop
$ sudo apachectl start
{% endhighlight %}

## 설치된 PHP 확인
PHP가 잘 설치되고 동작하는지 확인하는 가장 좋은 방법은 `phpinfo()`를 사용하는 것입니다. 당연히 프러덕션 환경에서 이것을 사용하도록 두면 안되겠죠. 그래도 개발하는 과정에서는 유용한 방법입니다.

가단하게 `info.php`파일을 홈 디렉토리에 있는 `Sites/` 폴더 아래 만듭니다.
{% highlight bash %}
$ echo "<?php phpinfo();" > ~/Sites/info.php
{% endhighlight %}
그리고 브라우저를 열여서 `http://localhost/info.php`로 이동을 합니다. PHP 정보를 보여주는 페이지가 나오면 잘 동작하고 있는 겁니다.

![brew apache phpinfo](/assets/images/201903/brew_phpinfo01.png)

위와 비슷한 화면을 보신다면 성공입니다. 아파치하고 PHP가 성공적으로 설치된 거라고 보면되고요. 다른 버전도 시험해 보고 싶다면 아파치 설정 파일에서 `LoadModule ... php@5.6 ...`처럼 되어 있는 라인은 코멘트처리하고 원하는 버전이 있는 라인을 코멘트를 풀어주면 됩니다. 아파치를 재 시작하고 페이지를 새로 고침하면 변경된 내용을 보실 수 있을겁니다.

## PHP 버전 변경 스크립트
지금까지는 아파치에 PHP 5.6을 사용하도록 하드코딩을 했습니다. 하지만 버전을 좀 쉽게 변경하면 좋겠죠. 다행히도 아주 유용한 [PHP 버전 변경 스크립트](https://gist.github.com/rhukster/f4c04f1bf59e0b74e335ee5d186a98e2)를 작성해서 공유하신 분이 있습니다.

이 스크립트(`sphp`)를 브루의 표준 디렉토리인 `/usr/local/bin`에 설치하도록 하겠습니다.
{% highlight bash %}
curl -L https://gist.githubusercontent.com/rhukster/f4c04f1bf59e0b74e335ee5d186a98e2/raw > /usr/local/bin/sphp
chmod +x /usr/local/bin/sphp
{% endhighlight %}
## Path 확인
홈브루가 설치되는 과정에서 `/usr/local/bin`와 `/usr/local/sbin`가 자동으로 실행경로에 포함이 되어 있어야합니다. 한번 확인해 보겠습니다.
{% highlight bash %}
$ echo $PATH
{% endhighlight %}
위에서 말한 경로가 보이지 않는다면 수동으로 추가를 해 줍니다. 사용하고 있는 쉘에따라 `~/.profile`, `~/.bash_profile`나 `~/.zshrc`에 수정할 파일이 달라지는데요. 기본 배시쉘을 사용하고 있다면 `~/.bash_profile`에 아래 내용을 추가해 줍니다.
{% highlight bash %}
export PATH=/usr/local/bin:/usr/local/sbin:$PATH
{% endhighlight %}

## PHP 버전 변경 시험
지금까지 과정을 잘 진행을 했으면 `sphp` 명령으로 PHP 버전을 쉽게 변경할 수 있습니다.
{% highlight bash %}
$ sphp 7.1
{% endhighlight %}
명령어를 실행하면서 비밀번호를 물어보면 입력을 합니다. 아래와 같은 내용이 출력될 겁니다.
{% highlight bash %}
$ sphp 7.0
Switching to php@7.0
Switching your shell
Unlinking /usr/local/Cellar/php@5.6/5.6.39... 25 symlinks removed
Unlinking /usr/local/Cellar/php@7.0/7.0.33... 0 symlinks removed
Unlinking /usr/local/Cellar/php@7.1/7.1.25... 0 symlinks removed
Unlinking /usr/local/Cellar/php/7.3.1... 0 symlinks removed
Linking /usr/local/Cellar/php@7.0/7.0.33... 25 symlinks created

If you need to have this software first in your PATH instead consider running:
  echo 'export PATH="/usr/local/opt/php@7.0/bin:$PATH"' >> ~/.bash_profile
  echo 'export PATH="/usr/local/opt/php@7.0/sbin:$PATH"' >> ~/.bash_profile
You will need sudo power from now on
Switching your apache conf
Restarting apache

PHP 7.0.33 (cli) (built: Dec 14 2018 16:20:36) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
    with Zend OPcache v7.0.33, Copyright (c) 1999-2017, by Zend Technologies

All done!
{% endhighlight %}

브라우저에서도 `http://localhost/info.php` 페이지를 열어서 버전이 PHP 7.0으로 변경되었는지 확인해 보시길 바랍니다. 
![brew apache phpinfo](/assets/images/201903/brew_phpinfo02.png)

# PHP 업데이트
브루를 사용하면 PHP 뿐 아니라 설치된 다른 페키지를 업그레이드 하는게 아주 간편합니다. 우선 `update` 명령어로 업데이트할 목록을 가져옵니다. 업그레이드를 하려면 단순히 `upgrade` 명령어를 사용하면 됩니다.
{% highlight bash %}
$ brew update
$ brew upgrade
{% endhighlight %}

PHP 링크가 설정되는 방식때문에 한번에 하나의 버전만 엑티브 상태가 됩니다. 그리고 액티브된 버전만 업데이트가 됩니다. 그래서 다른 버전을 업데이트 하고 싶으면 우선 활성화된 버전을 변경하고 업데이트를 해 줘야합니다.

현재 활성화된 버전을 확인하는 명령어는 아래와 같습니다.
{% highlight bash %}
$ php -v
{% endhighlight %}
그리고 특정 버전에 관한 정보를 보고 싶을 때는 아래와 같이 명령어를 실행하면 됩니다.
{% highlight bash %}
$ brew info php@7.1
php@7.1: stable 7.1.26 (bottled) [keg-only]
General-purpose scripting language
....
{% endhighlight %}

자 이제 전체 3개의 파트중에서 첫번째가 끝났습니다. 아파치 2.4를 설치하고 PHP 버전을 간단히 변경할 수 있는 스크립트도 추가했습니다. [2번째 파트](/php/php-apache-part2.html)에서는 MySql, Virtual Hosts, Xdebug 환경을 설정합니다.

---
