---
layout: post
title:  "Github 블로그 Category Tree Depth 만들기"
date:   2019-01-24 08:00
category: MyBlog
---

# Github Blog Category

## 이전 Category 관리

* <img src="/resource/img/blogcategorydepth(1).PNG" width="250px" height="500px">
* _posts 폴더에 post 들을 그냥 집어 넣었다.
* <img src="/resource/img/blogcategorydepth(3).PNG" >
* 이전에는 카테고리를 YAML 머릿말에서 관리했다. ( ex: categories: android )

## 변경 이유

* <img src="/resource/img/blogcategorydepth.PNG" width="400px" height="500px">
* 이전 category 페이지인데 나는 카테고리가 depth 를 갖기를 바랬다. 
* post 가 많아지면서 내가 원하는 post 를 찾기가 힘들어 졌고 이것을 카테고리별 폴더로 관리하고 싶었다.

## 변경 점

### post 폴더 구조

* post 들이 폴더 구조대로 category 를 갖는다는 것을 찾았다. 그래서 폴더로 카테고리를 나누고 category tree 중 leaf child 에 _post 를 넣었다.  ( 처음에는 _post 폴더 1 개 안에 category 폴더들을 넣었는데 각 post 들이 카테고리를 인식을 못 햇다.)
* <img src="/resource/img/blogcategorydepth(2).PNG" width="250px" height="600px">

### YAML 머릿말

* category 부분을 없앴다.

### Depth

* 폴더 구조를 만들었지만 depth 가 생기는 것은 아니었다.
* <img src="/resource/img/blogcategorydepth(4).PNG">
* 이 사진은 다음 코드를 찍어본 결과인데 각 category 계층에 포함 되는 모든 post 들을 불러오게 됐다.
    ``` html
        <p>{{site.categories}}</p>
    ```
* 그래서 depth 는 어떻게 해결할까??