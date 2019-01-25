---
layout: post
title:  "Github 블로그 Sidebar 꾸미기"
date:   2019-01-25 08:00
---

# Github 블로그 Sidebar

## 내가 원하는 기능

* 나는 sidebar 에 내 post 의 map 역할을 하는 list 가 ( post 의 구조를 보여주는 ) 스크롤 내려도 따라다녔으면 좋겠다고 생각했다.
* 또한 sidebar 에 같은 카테고리 내의 다른 관련된 post 들을 보여줬으면 좋겠다고 생각햇다.
* 나는 전체적인 구조가 쉽게 보이는 것을 선호하는데 이 두 기능이 내가 공부한 것을 정리하는데 좋을 것이라고 생각했다.

## 기능 개발

### 1. TOC

* 이 기능은 이미 개발 돼 있는 jekyll theme 를 이용했다
* http://jekyllthemes.org/themes/easybook/
* 쓰다보니 안 예쁜것 같지만 내가 원하는 이 기능이 구현되어 있어 그냥 사용하기로 했다.

### 2. 같은 카테고리 내 Another Post

* 이 기능도 스크롤 내려도 따라다니면 좋겠다고 생각했는데 위의 TOC list 들을 연달아 놓게 되면 너무 길 것이라고 생각했다
* 그래서 Tab 으로 너무 길어지는 것을 방지 했다.
* https://imivory.tistory.com/8 이 blog 를 참조해 아주 간단한 tap 을 구현 후에 넣었다.
* <img src="/resource/img/blogsidebar.PNG">
* 그리고 tap-content 에 이 list 를 넣었다. 현재 포스트의 카테고리를 구해 그 카테고리의 포스트들을 list 로 표현한 것.

* <img src="/resource/img/blogsidebar(1).PNG">
* 이 list 도 scroll 을 내리는 동안 따라다녔으면 좋겠다고 생각했기 때문에 easybook Theme 에 이미 구현되어 있던 TOC 가 따라다니는 기능을 살짝 손봤다.
* 손 데려다 보니 대충 봤는데 따라다니게 하는게 fixed 로 했을 것이라 생각했는데 사이에 div 를 넣고 height 를 엄청 키우는 코드여서 당황했다.

* <img src="/resource/img/blogsidebar(2).PNG"> <img src="/resource/img/blogsidebar(3).PNG"> 
* tab 부분 나중에 좀 더 꾸미고 구분 잘 가게하고 Another Post 이름 좀 바꾸면 될 것 같다.