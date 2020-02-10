## Why
* 기초 레이아웃 이해

## Goal
* 블록 레벨 요소를 원하는 위치에 배열


## Contents

### Position 속성
* What
요소를 배치하는 방법을 지정

* Static
기본값. 위치가 설정된 것이 아님.
```
div {
    position: static;
}
```

[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_position)

* Relative
위치지정 가능(top, bottom, left, right)
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_position&preval=relative)

* fixed
스크롤해도 움직이지 않는 고정 자리 
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_position&preval=fixed)

* absolute
레이아웃에 공간이 배정되지 않고 조상 요소에 상대적으로 배치.
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_position&preval=absolute)

[다른 Position 요소들1](https://developer.mozilla.org/ko/docs/Web/CSS/position)
[다른 Position 요소들2](https://www.w3schools.com/cssref/pr_class_position.asp)


### Display 속성
* What
디스플레이 속성은 요소가 나타나는 틀을 정한다.

* Block
새로운 라인에서 시작하며좌우로 최대한 늘어난다.
다음에 나오는 태그는 줄바꿈된 것처럼 보인다.
적용태그: div, p, li, form, header, footer, section
```
div {
    display:block;
}
```
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_display&preval=block)

* Inline
텍스트에 효과를 주기 위한 태그.
높이나 넓이 속성이 영향을 미치지 못한다.
다음에 나오는 태그가 줄바꿈되지 않고 바로 오른쪽에 나타난다.
적용태그: span, a, i, b
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_display&preval=inline)

* Inline-block
텍스트를 담은 태그에 높이나 넓이를 설정하기 위한 태그.
inline과 같이 줄바꿈이 되지 않지만 block과 같이 크기 지정 가능.
[Example](https://www.w3schools.com/cssref/playit.asp?filename=playcss_display&preval=inline-block)

[다른 Display 요소들1](https://developer.mozilla.org/en-US/docs/Web/CSS/display)
[다른 Display 요소들2](https://www.w3schools.com/cssref/pr_class_display.asp)

### Float
* What
요소의 흐름을 정의(위치지정)
이미지와 텍스트 배치에 사용했지만 최근 레이아웃 용도로 쓰인다고.
```
img {
    float: right;
}
```
직관적으로 right, left, none, inherit 속성 존재
[Example](https://www.w3schools.com/css/css_float.asp)



## Links
* [W3schools](https://www.w3schools.com/cssref/pr_class_display.asp)
* [MDN](https://developer.mozilla.org/ko/docs/Web/CSS/float)
* [ofcourse](https://ofcourse.kr/css-course/display-%EC%86%8D%EC%84%B1)
* [learnlayout](http://ko.learnlayout.com/position.html)
