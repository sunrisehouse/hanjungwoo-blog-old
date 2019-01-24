---
layout: post
title:  "Github 블로그 Category Tree Depth 만들기"
date:   2019-01-24 08:00
category: MyBlog
---

# Github Blog Category

## 이전 Category 관리

* <img src="/resource/img/blogcategorydepth(1).PNG" >
* _posts 폴더에 post 들을 그냥 집어 넣었다.
* <img src="/resource/img/blogcategorydepth(3).PNG" >
* 이전에는 카테고리를 YAML 머릿말에서 관리했다. ( ex: categories: android )

## 변경 이유

* <img src="/resource/img/blogcategorydepth.PNG" width="400px" height="500px">
* 이전 category 페이지인데 나는 카테고리가 tree 모양으로 depth 를 갖기를 바랬다. tag 와 다를바가 없다고 생각했다.
* post 가 많아지면서 내가 원하는 post 를 찾기가 힘들어 졌고 이것을 카테고리별 폴더로 관리하고 싶었다.

## 변경 점

### post 폴더 구조

* <img src="/resource/img/blogcategorydepth(2).PNG" width="250px" height="600px">
* post 들이 폴더 구조대로 category 를 갖는다는 것을 찾았다. 그래서 폴더로 카테고리를 나누고 category tree 중 leaf child 에 _post 를 넣었다.  ( 처음에는 _post 폴더 1 개 안에 category 폴더들을 넣었는데 각 post 들이 카테고리를 인식을 못 햇다.)

### YAML 머릿말

* category 부분을 없앴다.

### Depth

* <img src ="/resource/img/blogcategorydepth(4).PNG"> 를 사용해본 결과 post 의 경로가 잘 나왔다. (폴더 구조를 카테고리처럼 안 나누고도 YAML 머릿말 categories 로도 가능한 것을 이제야 알아차렸다. 하지만 폴더 구조 나누는 것은 필요한 일이었으니까)
* <img src ="/resource/img/blogcategorydepth(5).PNG"> 를 이용해 site.categories[0 ~ site.categories.size] 이용해 depth 를 표현하기가 쉽지 않았다. 높은 level 의 category 안에는 그 cateogry 안에 있는 모든 post 들이 다 들어있었기 때문.
* 내가 생각한 방법
    1. post 폴더 구조 이용하기 ( 제일 best 라고 생각. )
    2. 모든 post 들의 category 정보 (page.categories) 이용해 category tree 만들기
    3. category data 를 따로 저장하고 따로 관리하기.

* 1번이 best 인 것 같은데 방법을 모르겠고 2번은 구현 쉬울것 같은데 나중에 post 많아지면 어떻게 되나 모르겠고 3번은 쉬운데 post 들의 폴더관리와 category data 를 따로 관리 해야해서 불편하다고 생각했다.

* 관리하기 귀찮아도 3번이 제일 나을 것 같아서 3번 선택

* category data 를 json 파일로 저장하려다가 _config.yml 에 저장하기로 했다.