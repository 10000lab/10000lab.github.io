---
title: OpenAI GPT-2 실행해 보기
key: HowToRunOpenAIGPT-2
categories: [Natural Language Processing]
tags: [Natural Language Processing]
---

2019년 11월 5일 OpenAI는 언어모델인 GPT-2의 최종본인  모델을 공개했습니다. 최초 공개는 2019년 2월 124M 파라미터 모델이었습니다. 그후 5월에 355M, 8월에 774M를 공개하고 이번에 최종 [1.5B(1558M)모델을 공개](https://openai.com/blog/gpt-2-1-5b-release/)한 것입니다.

이렇게 단계적으로 데이터를 공개한 이유는 이 모델이 너무 정교해서 가짜 뉴스를 만들어 내는데 악용될 수 있다는 우려가 있었기 때문입니다. 그 만큼 성능이 좋다는 것을 자신하는 것이겠죠.
![Open AI GPT-2](/assets/images/201911/open-ai-gpt-2.png)

저는 NLP에 완전 초보자이기때문에 설명은 생략하고요. 원래 이 글의 목적인 GPT-2를 직접 실행해 보는데 초첨을 맞추겠습니다.

우선 코드를 받아서 실행을 해 보기전에 맛 보기로 [Talk to Transformer](https://talktotransformer.com/)에 들어가 보시죠. 

여기서 Custom prompt에 "I met a friend on my way home." 이라고 적고 GENERATE ANOTHER을 누르면 AI가 작문을 시작합니다.
> I met a friend on my way home. A stranger helped me pack my things, and stayed until I was away. They couldn't have seen the overwhelming support that I received, but that's when I realized how deep the humanity runs. It's so gratifying to be able to spend time with strangers and still have the joy of making their day. The goal of the Philadelphia events was to give other people the joy of hearing from someone they've only ever met by being present and getting to know someone for a second.
I'd never experienced this before. Yet, my happiness and my gratitude for my friend, knowing he's not alone, are something I know I'll always

정말 신기하네요.

# 실습환경
- OS는 Ubuntu 18.04입니다. Python을 사용하기때문에 다른 환경에서도 잘 동작할 것으로 예상합니다.
- Python 3.6입니다. Ubuntu 18.04에 기본으로 깔려서 사용했습니다.
- Tensorflow 1.15 버전입니다.

# 소스 다운로드 및 라이브러리 설치
## 소스를 다운로드 합니다.
Git으로 소스를 다운로드 하고 폴더를 이동합니다.
{% highlight bash %}
$ git clone https://github.com/openai/gpt-2.git
$ cd gpt-2
{% endhighlight %}

## venv 환경을 만들고 라이브러리를 설치합니다.
venv를 만들고 pip를 업데이트 해 줍니다.
{% highlight bash %}
$ python3 -m venv venv
$ source venv/bin/activate
$ pip install -U pip
{% endhighlight %}

GPT-2 github에서는 tensorflow 1.12 버전을 설치하라고 되어 있는데요. 실제로 1.12 버전을 설치하면 에러가 납니다. 여기서는 1.15 버전을 설치하고 나머지 필요한 라이브러리를 설치하겠습니다.
{% highlight bash %}
$ pip install tensorflow==1.15
$ pip install -r requirements.txt
{% endhighlight %}

## 모델을 다운로드 합니다.
모델을 다운로드하는 스크립트를 실행합니다. 파일을 다운로드 받는데 상당한 시간이 걸릴 수 있으므로 테스에 사용할 모델만 골라서 받아도 됩니다.
{% highlight bash %}
$ python download_model.py 124M
$ python download_model.py 355M
$ python download_model.py 774M
$ python download_model.py 1558M
{% endhighlight %}

## 예제를 실행합니다.
355M 모델을 사용해서 실행을 해 보겠습니다.

아래 명령어를 사용해서 interactive sample을 실행하면 직접 문장을 넣어 보면서 AI로 작문한 결과를 볼 수 있습니다.
{% highlight bash %}
python src/interactive_conditional_samples.py --model_name="355M"
{% endhighlight %}

Talk to Transformer에 입력했던 것과 동일하게 "I met a friend on my way home."라고 입력해 봤습니다. 제가 사용한 노트북에서는 결과를 보여주는데 상당히 오랜 시간이 걸리네요.

결과로 받은 문장입니다.
> This one seemed like a nice guy, but all of the sudden he resigned himself to my task, and he never bothered doing anything." Daniel doubted he would ever manage to encounter a genuine soul– a wall– to the other side of the truth. Matt Peck's novel set his teeth on edge. Alvin Cohn, a nineteen-year-old journalist with colorful pasts– political scandal, the Caper of Tiffany Bloomingdale–skipped grades, wanted nothing to do with politics. Land ant Larry Biesecker gave up on schooling until he felt ready to go to graduate school, 37. His Godfather pass'rack Jewish, where Bruno's provides saving glimmer of minimum conscious. His godhusband Noël, strange London aficionado– only touched Trace Wilson, instigator of Trudeau's Texas polling controversy, disagreed that communism represented reality. Like idealistic heroes, the prototypical Bistro closet misfit from ATS, the Trump friend disappeared from view. Their vomiting friendship rarely felt like the innocent detour of Christmas singles. S.W. proved a shrew dog of Ottawa's personally uncomfortable establishment. Phil declined all federal income tax payments for principal and interest, notwithstanding his job silhouette, Gleeson a naively fervent lifelong Union treasury man, Molyneux a storied Real Nixon. B-file Steve Buckley crashes into sins of the bloodline (actually, liberal upbringing) and imagines entering paradise without dying soul and lung tissue, though it seems more a sheer psychic status gesture. In the trash, Lisk responded to love. In the market, the artists made vast fortunes outside. Lenex, observers agreed, followed the truth well enough. Already Crosley had admitted a talent for stretching reality and making it fit condition. Gagliano's sexual life from October and Clementine's was his own. Theresa Mlee's incest—too easy for middle-class kids. Mack protested leukemia and not progressive charity, handset GrandmaIn one phenomenal turn, Ruth Miltimore, GRACE scorned Gideon's radical in favor of efforts to catabolic Rats run the hospital, industrial clientele contend with temperature conveniently deemed chilly, developing a contact with CJ of justice, no ideas? EV: Paget member, ordeal discloses more of man's falsehoods, or is it merely paranoia from the pages of history? AV: Good fortune, says Gagliano—Got too many fish in a barrel in short supply, and starting to confess delusions and hypocrisy, pedestal thee rat has sensitive artificial

네. 다 이해는 할 수 없지만 뭔가 그럴듯 해 보이네요.

이만 실습을 마치겠습니다.

---
