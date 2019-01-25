---
layout: post
title:  "Github 블로그 Category page"
date:   2019-01-24 08:00
---

# Category page

## 이전 Category page

* 이전엔 카테고리 page 에 들어가게 되면 모든 category 는 자신보다 하위의 category 의 post 들을 다 보여주는 page 였다.
* ex) category 카테고리에 study , project 카테고리가 있다면 category 카테고리에 study 와 post 카테고리의 post list 가 들어있고 study 에도 자기 post list 가 있고 project 에도 자신의 post list 가 있는 식

* 겹치는 게 보기 싫었다.
* 그래서 카테고리를 sidebar 에 놓고 sidebar 에서 카테고리를 클릭했을 때 해당하는 카테고리의 post list 만 보이게 하는 기능을 개발하기로 했다.

## 개발

### javascript

* <img src="/resource/img/blogcategorypage.PNG"> category.html 에 이 코드를 넣었다.

* 두 함수가 있다
    1. 처음 category 페이지가 load ?? 됐을 때 코드
        * 다 안보이게 하고 url 의 끝 부분의 id를 parsing 해서 해당 id 만 보이게 했다. 

    2. category 페이지에서 sidebar 에 있는 category 를 클릭했을 때 이벤트 코드
        * 다 안보이게 하고 누른 category 의 a tag 안 href 속성 값의 마지막 id 를 parsing 해서 해당 id 만 보이게 했다.

### id 변환

* html 태그 안 id 값이 띄어쓰기와 '_' 외 다른 문자가 안돼서 그래서 고생 했다.
* 그래서 다음과 같이 id 값을 변환해 주었다. ('+'왜'#' 은 "c++" category 와 "c#" category 때문에 '_' 개수로 변환했다. 그래서 아마 "+#", "#+" 를 구분 못할 것이다.)
* <img src="/resource/img/blogcategorypage(2).PNG">
* <img src="/resource/img/blogcategorypage(3).PNG">

### 결과
* <img src="/resource/img/blogcategorypage(1).PNG"> 
    이렇게 sidebar 의 Android 를 누르게 되면 Android 에 속한 post 들만 보이게 된다.