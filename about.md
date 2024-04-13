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
