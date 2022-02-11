---
layout: page
title:
permalink: /about/
comment: false
---

<div class="contact">
{% if site.github_username %}
        <a href="https://github.com/{{ site.github_username }}">GitHub</a>
{% endif %}
{% if site.twitter_username %}
        <a href="https://twitter.com/{{ site.twitter_username }}">Twitter</a>
{% endif %}
{% if site.email %}
        <a href="mailto:{{ site.email }}">Email</a>
{% endif %}
        <a href="{{ "/feed.xml" | prepend: site.baseurl }}">RSS</a>
</div>

## Notice

이 블로그의 형식은 [기계인간](https://github.com/johngrib/johngrib-jekyll-skeleton)님이 만드셨습니다. 저는 개인적 친분이 없지만 허락을 구한 후 사용하고 있습니다. 블로그 소스가 궁금하신 분은 [기계인간](https://github.com/johngrib/johngrib-jekyll-skeleton)님의 블로그를 방문해주시길 부탁드립니다.

또한 기계인간님은 단순 블로그 소스 공유자가 아니라 매일 공부하며 배움과 나눔을 실천하는 분입니다. 우연히 들어간 블로그에서 학습의 흔적을 보고 혀를 내두른 기억이 납니다. 삶의 큰 축복 중 하나가 만남의 복이라고 하던데 개발을 꿈꾸는 분이라면 큰 복을 만나실지도 모르겠습니다.
