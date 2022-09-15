---
title: "[SwiftUI] TabView 탭하는 타이밍에 해야할 동작 추가하는 법"
excerpt: "onChange 활용하기"
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Swift]

permalink: /ios-problems/tabview-selection

---

### TabView selection 활용

SwiftUI로 검색 앱을 만들어보는 중이라서 TabView를 활용해 블로그, 뉴스, 이미지 세 개의 탭을 만들었다.

탭을 이동할 때 각 카테고리 별로 검색어 API를 조회하도록 수정을 하려고 했는데, 탭을 눌렀을 때의 이벤트를 어떻게 캐치해야 하는지 몰라 찾아보았다.

우선 탭뷰가 추가된 화면을 만드는 방법부터 알아보면

```swift
        TabView {
            BlogListView(keyword: $keyword, blogs: $searchAPI.blogs)
                .tabItem {
                    Label("Blog", systemImage: "book.circle")
                }
            
            NewsListView(keyword: $keyword, news: $searchAPI.news)
                .tabItem {
                    Label("News", systemImage: "newspaper.circle")
                }
        }
```

요런식으로 TabView를 선언하고 그 안에 원하는 View를 추가한 다음 각각의 View마다 .tabItem을 달아주면 된다.

<img src="https://user-images.githubusercontent.com/22000470/190406097-4b71fe4a-9cbe-4172-9124-9a50ef3d6bfc.png" width="400">

하지만 탭을 이동하는 건 어떻게 알 수 있을까? 바로 `tag`와 `.onChange`를 활용하면 된다.

TabView에 사용되는 View들에 각각 tag를 달아줄 수 있다. tag 값으로는 Hashable Value를 할당한다. TabView에서 tag 값을 통해 어떤 것이 선택되었는지를 변경될 때마다 알 수 있도록 `TabView(selection: $tableSelection)`으로 코드를 변경해준다.

> 요 때 selection에 필요한 값은 바인딩되는 값. 코드상으로 tableSelection은 `@State var tabSelection: SearchType = .blog`로 선언되어 있다.

```swift
        TabView(selection: $tabSelection) {
            BlogListView(keyword: $keyword, blogs: $searchAPI.blogs)
                .tabItem {
                    Label("Blog", systemImage: "book.circle")
                }
                .tag(SearchType.blog)
            
            NewsListView(keyword: $keyword, news: $searchAPI.news)
                .tabItem {
                    Label("News", systemImage: "newspaper.circle")
                }
                .tag(SearchType.news)
        }
```

나는 SearchType이라는 String enum을 선언해서 활용중이었기 때문에 각 화면의 tag에도 이 enum을 사용해주었다.

마지막으로 tabview의 선택된 탭이 변경되는 순간에 api 호출을 해야하므로 TabView의 바깥쪽에서 onChange를 구현했다.

```swift
        TabView(selection: $tabSelection) {
            BlogListView(keyword: $keyword, blogs: $searchAPI.blogs)
                .tabItem {
                    Label("Blog", systemImage: "book.circle")
                }
                .tag(SearchType.blog)
            
            NewsListView(keyword: $keyword, news: $searchAPI.news)
                .tabItem {
                    Label("News", systemImage: "newspaper.circle")
                }
                .tag(SearchType.news)
        }
        .onChange(of: tabSelection) { newValue in
            searchAPI.searchType = newValue
            searchAPI.requestSearchResult(keyword: keyword)
        }
```
tabSelection 값이 업데이트 될 때마다 newValue(새롭게 변경된 searchType)을 가지고 API를 조회하도록 수정했다.
