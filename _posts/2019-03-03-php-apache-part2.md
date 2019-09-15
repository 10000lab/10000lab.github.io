---
title:  macOS 10.14 Mojave PHP,Apache 설치 - Part2
key: 20190302p2
categories: [php]
tags: [macOS에서 PHP 설치]
---

macOS 10.14 모자브(Mojave)에서 PHP 웹 개발환경을 만드는 가이드 part 2 입니다. 

[Part 1](/php/php-apache-part1.html)에서는 맥오에스에 아파치를 설치하고 여러버전의 PHP를 쉽게 이동하면서 사용할 수 있는 스크립트를 설치했습니다. 이번 part 2에서는 MySql, Virtual Hosts와 Xdebug를 설치하도록 하겠습니다. 

>이 가이드는 [GRAV 개발 블로그](https://getgrav.org/blog/macos-mojave-apache-mysql-vhost-apc)에 있는 내용을 실제 적용하면서 번역한 내용입니다.

# MySql
현재 브루에서 설치하는 기본 MySql은 버전 8입니다. 본 가이드에서는 최신 버전이 아닌 5.7 버전을 설치하도록 하겠습니다. 브루에서 설치할 수 있는 MySql버전은 `search` 명령어로 확인해 볼 수 있습니다. 설치는 `brew install mysql@5.7`로 합니다.
{% highlight bash %}
$ brew search mysql
$ brew install mysql@5.7
{% endhighlight %}
설치가 완료되면 path에서 mysql 실행파일이 있는 곳을 추가해 줍니다. 
{% highlight bash %}
echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.bash_profile
{% endhighlight %}
새로 추가된 path를 반영하려면 새로운 터미널을 열어야합니다. 새창을 열어 서버를 실행하고 다음 부팅할때 자동으로 실행되도록 합니다.
{% highlight bash %}
$ brew services start mysql@5.7
{% endhighlight %}
그리고 `root` 비밀번호를 설정하기위해 `mysql_secure_installation`를 실행합니다.
{% highlight bash %}
$ mysql_secure_installation
{% endhighlight %}
비밀번호를 새로 입력하고 이어지는 몇가지 질문에 답변을 하면 됩니다.
[SequelPro](http://www.sequelpro.com/)를 다운로드하고 설치합니다. 기능도 잘 구현되어 있는데다가 무료입니다. MySql 서버에 연결해 봅니다.
![Sequel Pro](/assets/images/201903/sequel_pro.png)

서버를 멈추고 싶을때는 아래처럼 간단히 명령어를 입력하면 됩니다.
{% highlight bash %}
$ brew services stop mysql@5.7
{% endhighlight %}
{%- include google-adsense/article-middle.html -%}

# Apache Virtual Hosts
하나의 개발환경에서 여러 프로젝트를 진행하는 간편한 방법은 버추얼 호스트를 이용하는 방법입니다. 예를 들어 shop.mydomain.com은 쇼핑몰 프로젝트로 사용하고 blog.mydomain.com은 블로그 플랫폼을 개발하는 식으로 프로젝트별로 URL을 설정해서 사용할 수 있습니다. 아파치는 기본적으로는 도메인 이름을 기반으로 호스트를 구분하기때문에 하나의 IP에서 여러개의 프로젝트를 구성해서 사용하는게 가능합니다.

이러한 기능은 아파치에 기본으로 탑제되어 있지만 처음에는 비활성화되어 있습니다. 아파치 설정파일인 `/usr/local/etc/httpd/httpd.conf`에서 아래 라인을 찾아서 코멘트를 해제해 줘야합니다. 모듈을 읽어들이는 부분입니다.
{% highlight apache %}
LoadModule vhost_alias_module lib/httpd/modules/mod_vhost_alias.so
{% endhighlight %}
아래 라인도 코멘트를 해제해 줍니다. 버추얼 호스트관련 내용을 추가하는 파일입니다.
{% highlight apache %}
# Virtual hosts
Include /usr/local/etc/httpd/extra/httpd-vhosts.conf
{% endhighlight %}
이제 버추얼 호스트 설정파일(`/usr/local/etc/httpd/extra/httpd-vhosts.conf`)을 편집해서 필요한 내용을 넣을 차례입니다. 파일을 열어보면 이미 예제로 들어있는 내용이 있습니다. 개발하고 있는 프로젝트에따라 필요한 내용을 추가하면 됩니다.

# Xdebug PHP 5.6 버전에 설치
어떤 종류의 개발이던지 디버깅을 하고 코드를 수정할 수 있다는 것은 매우 중요하죠. PHP는 제한적으로 변수를 덤프해서 보거나 파일에 로그를 기록하는 기능을 제공합니다. 하지만 좀 더 복잡한 개발을 하다보면 좀 더 강력한 툴이 필요하게 되죠.

Xdebug는 PHP에 디버깅과 프로파일링 기능을 제공합니다. PHP가 제공하는 `var_dump()` 메써드의 HTML에서 보기 적합한 결과를 주는 방식에는 할 수 없는 확장된 기능을 제공하는 거죠. 덤프를 해주는 메써드와 스택 트레이스를 보여주는 기능도 있지만 무엇보다 리모트 디버깅이 가장 강력한 기능입니다. 브레이크 포인트를 걸고 한 스텝씩 진행을 하면서 PHP 코드의 상태를 확인할 수 있으니까요. PHP 5.6에서 Xdebug를 설치해 보겠습니다.
{% highlight bash %}
$ sphp 5.6
{% endhighlight %}

우선 브루를 사용해서 `autoconf`를 설치합니다.
{% highlight bash %}
$ brew install autoconf
{% endhighlight %}

이제 [PECL로 Xdebug](https://pecl.php.net/package/Xdebug)를 설치하게 됩니다. PECL은 PHP 패키지 매니저인데요. 현재는 PHP 패키지를 설치하는 가장 좋은 방법입니다. 단지 이것을 사용하는 것은 지금까지 브루로 패키지를 설치하는 것보다는 조금 더 수작업을 해야합니다. 

PHP 5.6은 최신 `2.5.x` 버전의 Xdebug를 설치해야합니다. 

pecl로 설치하기 전에 `/usr/local/Cellar/php@5.6/5.6.40` 폴더에 있는 `pecl`를 삭제합니다.
이 파일이 있으면 xdebug를 설치하는 중에 `Warning: mkdir(): File exists in System.php on line 294`라고하는 메시지가 나오면서 설치가 실패합니다.

{% highlight bash %}
$ rm /usr/local/Cellar/php@5.6/5.6.40/pecl
$ pecl install xdebug-2.5.5
....
Build process completed successfully
Installing '/usr/local/Cellar/php@5.6/5.6.40/pecl/20131226/xdebug.so'
install ok: channel://pecl.php.net/xdebug-2.5.5
Extension xdebug enabled in php.ini
{% endhighlight %}
컴파일이 끝나고나면 `xdebug.so`가 설치된 경로가 `Installing '/usr/local/Cellar/php@5.6/5.6.40/pecl/20131226/xdebug.so'`로 표시됩니다. 설정파일을 만들때 필요하니 잘 적어둡니다.

## [필수] Xdebug 설정
PECL로 인스톨을 하고 나면 `php.ini`에 간단한 엔트리를 자동으로 추가합니다. 하지만 좀 더 세밀하게 내용을 설정할 필요가 있습니다. 새로운 설정파일을 하나 만들고 그걸 사용해 보겠습니다.

`php.ini`에 자동으로 추가된 `zend_extension="xdebug.so"`를 삭제합니다. 보통 `/usr/local/etc/php/5.6/php.ini` 제일 윗 줄에 있을겁니다.

자동을 추가된 라인을 삭제했으면 새로운 설정 파일을 `/usr/local/etc/php/5.6/conf.d/ext-xdebug.ini`로 생성합니다.
내용은 아래 내용을 넣으면 됩니다. 중요한 것은 `zend_extension`에 xdebug 라브러리 파일이 설치된 경로를 적습니다.
{% highlight bash %}
[xdebug]
zend_extension="/usr/local/Cellar/php@5.6/5.6.40/pecl/20131226/xdebug.so"
xdebug.remote_enable=1
xdebug.remote_host=localhost
xdebug.remote_handler=dbgp
xdebug.remote_port=9000
{% endhighlight %}
아파치를 `sudo apachectl -k restart` 명령어로 재시작하고 변경 사항이 반영되었는지 확인해 봅니다. 브라우저를 열어서 `http://localhost/info.php`를 열어보면 아래처럼 보이는 것을 알 수 있습니다.
![Xdebug](/assets/images/201903/xdebug01.png)

## 다른 PHP 버전에 설치하기
PHP 7.0 이상에서는 최신 Xdebug `2.6.x`을 설치할 수 있습니다. 설치하는 과정은 앞서 PHP 5.6 버전에 설치하던 것과 거의 동일합니다. 우선 PHP 7.0을 사용하도록 설정하고 Xdebug 라이브러리를 설치해 보겠습니다.
{% highlight bash %}
$ sphp 7.0
$ pecl uninstall -r xdebug
$ pecl install xdebug
{% endhighlight %}
설치과정이 끝나면 `[필수] Xdebug 설정`을 다시 반복해서 실행합니다. `/usr/local/etc/php/7.0/php.ini`에 pecl이 자동으로 추가한 xdebug.so 가 포함된 라인을 삭제합니다. 그리고 `/usr/local/etc/php/7.0/conf.d/ext-xdebug.ini` 파일을 생성하고 내용을 적어 넣습니다.
{% highlight bash %}
zend_extension="xdebug.so"
xdebug.remote_enable=1
xdebug.remote_host=localhost
xdebug.remote_handler=dbgp
xdebug.remote_port=9000
{% endhighlight %}
정확히 이유는 모르겠지만 PHP 5.6에서는 xdebug.so의 전체 경로를 적어야핬었는데, 7.0에서는 그냥 파일명만 적어도 잘 동작합니다.
`sudo apachectl -k restart` 명령어를 아파치를 재 실행합니다. 그리고 앞서와 동일하게 `http://localhost/info.php`를 방문해서 변경사항이 적용이 되었는지 확인합니다.

여기까지 간략하게 맥오에스에서 PHP로 웹서비스를 개발하는 환경을 꾸미는 과정을 설명했습니다.

---
