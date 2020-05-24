---
layout  : wiki
title   : 
summary : 
date    : 2020-05-01 11:26:16 +0900
updated : 2020-05-24 18:14:59 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 설치

- [지킬로 깃허브에 무료 블로그 만들기](https://nolboo.kim/blog/2013/10/15/free-blog-with-github-jekyll/)

## 환경설정

### PermissionError 해결

```python
$ [[ -d ~/.rbenv  ]] && \
  export PATH=${HOME}/.rbenv/bin:${PATH} && \
  eval "$(rbenv init -)"
```

```python
$ source ~/.zshrc
$ gem install bundler
```

```python
jekyll serve --watch
bundle exec jekyll serve

```

### 빌드오류 해결

```python
{% raw %} 

{% csrf %}
{{ csrf }}

{% endraw %}
```

## Link

- [Mac에서 Gem::FilePermissionError 에러 발생시 해결 방법](https://jojoldu.tistory.com/288)
- [지킬로 깃허브에 무료 블로그 만들기](https://nolboo.kim/blog/2013/10/15/free-blog-with-github-jekyll/)
