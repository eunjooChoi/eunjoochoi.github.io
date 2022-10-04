---
title: "[SearchApp Devlog 06] pagenation과 StateObject"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog06

---

## Devlog 06

### pagenation

기존에는 검색 결과를 10개까지만 받아오도록 해놨기 때문에 아래로 스크롤하면 다음 검색 결과가 나타나도록 보여주고 싶었다.

페이징 처리를 해야해서 리스트를 뿌려줄 때 나타나는 아이템이 가장 마지막 요소인지 체크하고 검색 api의 startIndex 값을 증가시켜 새로운 검색 결과를 가져오도록 했다.

- ListView
```swift
var body: some View {
        List {
            ForEach(blogs, id: \.self) { result in
                NavigationLink {
                    // TODO: WebView 연결
                } label: {
                    BlogRow(keyword: $keyword, blog: result)
                }
                .onAppear {
                    guard let index = blogs.firstIndex(where: {$0.title == result.title}) else { return }
                    
                    if index == blogs.count - 1 {
                        searchAPI.loadMoreContents()
                    }
                }
            }
        }
    }
```

ForEach로 아이템을 하나하나 넣어줄 때 .onAppear 속성을 추가해 마지막 아이템인지 확인을 하도록 했다.

마지막이면 loadMoreContents를 호출한다.

- loadMoreContents
```swift
func loadMoreContents() {
        self.page += 1
        self.requestSearchResult(startIndex: self.page, keyword: self.keyword)

```

생각보다 간단하다. page를 SearchAPI 클래스 내부에서 관리하도록 프로퍼티로 선언하고 page 값을 메서드가 호출 될 때 1씩 증가시킨다.
그리고 기존에 만들어두었던 requestSearchResult를 호출한다.

- requestSearchResult
```swift
func requestSearchResult(startIndex: Int = 1, keyword: String) {
// ...
      if startIndex == 1 {
           self.blogs.removeAll()
      }
      self.blogs.append(contentsOf: result.items)
}
```

메서드에 들어가는 파라메터가 하나 추가됐다. startIndex로 page 숫자를 받는다. 다만 기존에 탭뷰를 눌렀을 때마다 조회하는 부분이 있기 때문에 1로 디폴트 값을 정해주었다.

기존에는 검색 결과로 각 결과 배열을 교체하도록 했지만 paging이 추가되면서 page 값이 1인 경우는 결과를 지우고 다시 append한다.

### StateObject 클래스는 바인딩을 하는 것이 아니다

API 클래스에 저장된 각 결과값들을 사용하기 위해 StateObject로 활용하고 있었다.

pagenation을 위해 ListView에서 아이템을 생성할 때도 api를 조회해야 하기 때문에 @Binding으로 주면 되는 줄 알았는데 웬걸, 다음과 같은 에러가 났다.

`Cannot convert value of type 'ObservedObject<SearchAPI>.Wrapper' to expected argument type 'Binding<SearchAPI>'`

왜 바인딩을 안시켜줘!! 라고 승질이 날 뻔 했는데 찾아보니 StateObject나 ObservedObject는 클래스다. 클래스는 뭐다? 레퍼런스 타입!!
레퍼런스 타입은 값을 넘겨주기만 하면 주소값으로 접근하기 때문에 굳이 바인딩을 해줄 필요가 없었다. 멍청쓰...


### 마무리
어쩌다보니 웹뷰 연결 전에 페이징 처리를 하게 되었다. 하면서 간과하고 있던 점도 알게 되었으니 다음에는 진짜 웹뷰 해야지.

![Oct-04-2022 23-33-43](https://user-images.githubusercontent.com/22000470/193847689-56316ea4-3f45-4efb-af99-ffbeaf6d161a.gif)

