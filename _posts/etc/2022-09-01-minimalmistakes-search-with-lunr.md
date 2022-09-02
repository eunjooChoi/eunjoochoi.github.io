---
title: "minimal-mistakes 테마가 적용된 github.io에 포스팅 검색 기능 추가하기"
excerpt: "오늘의 교훈: 어떤 것이든 공식문서를 잘 참조하자.."
  
toc: true
toc_sticky: true

categories:
  - etc
tags:
  - [github-blog]
  
permalink: etc/minimalmistakes-search-with-lunr

---

## minimal-mistakes 테마가 적용된 github.io에 검색 기능 추가하기

계속 깃헙페이지를 사용할거라면 점점 포스트가 많아질텐데 나중에 필요한 정보가 있을 때 이걸 하나하나 찾을 수도 없는 노릇인지라 검색 기능을 활성화해보기로 했다.

### 정말 간단했던 검색 기능추가작업..

우선 해당 테마를 제공하는 github 저장소에 lunr를 사용해 검색기능을 추가하는 방법이 아주 간단하지만 정확하게 나와있다.

링크: https://mmistakes.github.io/minimal-mistakes/docs/configuration/#site-search

정말로 간단하다..

<img src="https://user-images.githubusercontent.com/22000470/188045934-32b35f60-d146-4734-b0ad-0f395364259b.png" width="400">

`_config.yml` 파일에 search를 true로 바꿔준다. 그리고 `lunr: search_within_pages`를 추가하고 true로 적용한다.

만약 게시글의 전체 내용에서도 검색되게 하고 싶다면 search_full_content도 true로 바꿔주면 된다. (안그러면 게시글의 50단어까지만 검색됨)

이렇게만 적용하고 push해보면 우측 상단에 돋보기 아이콘이 나타난다.

![image](https://user-images.githubusercontent.com/22000470/188046210-5c9eec54-e405-419e-88c8-be85f217f8ea.png)

들어가서 검색해보면 아주 깔끔하게 결과가 나온다.

![image](https://user-images.githubusercontent.com/22000470/188046272-cce05148-3f87-4ce1-b798-534f27f720c5.png)

사실 처음에는 검색하면 teaser 이미지가 대문짝만하게 나왔었다. 보기에 아주 별로라서 teaser 이미지를 제거해버렸다...^^ 딱히 쓰는 곳이 없어서 지워도 괜찮았다.


### 마무리

당연히 구글링해서 적용해야 할 거라고 생각했던 건데 당황스럽게도 minimal-mistakes docs에 잘 나와있었던 사이트 내 포스팅 검색 기능 활성화..

오늘의 교훈은 바로 공식문서를 잘 활용하자는 것.. 흑흑

이것때문에 4시간을 썼다는 게 어이가 없을 따름이다.
