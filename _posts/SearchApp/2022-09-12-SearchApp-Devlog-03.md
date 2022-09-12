---
title: "[SearchApp Devlog 03] json data 파싱하기"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog03

---

## Devlog 03

드디어 검색 api를 호출해 데이터를 가져오는 데 성공했다.

### Codable과 파싱

swift를 사용해 API 결과값으로 내려오는 data를 파싱할 때 참 좋은 점은 파싱을 원하는 모델이 Codable을 채택하면 된다는 점이다.

1. blog 검색 결과를 가져와야 할 때 Blog 구조체가 Codable을 채택하도록 만든다.

```swift
struct Blog: Codable, Hashable {
    var title: String
    var link: String
    var description: String
    var bloggername: String
    var bloggerlink: String
}
```

> List에서는 요소를 나타낼 때 각 요소들의 유일값이 필요하므로 Hashable을 채택

2. decode를 실행한다.

```swift
try? JSONDecoder().decode(Blog.self, from: data)
``` 

그런데 검색 결과 API는 Blog 데이터는 items로 내려오고 lastBuildDate, total, start, display가 함께 내려온다. 그렇기 때문에 decode에 Blog.self를 넣을수도 없었다.

Blog 뿐만 아니라 Image나 News도 결과값 구조는 동일했기 때문에 어떻게 하면 좋을까 고민하다가 items를 Generic으로 선언해 사용할 수 있도록 변경했다.

```swift
struct APIResult<T: Codable>: Codable {
    let lastBuildDate: String
    let total: Int
    let start: Int
    let display: Int
    let items: [T]
}
```

> 그냥 APIResult<T>로 하면 T가 Codable하지 않다는 에러가 나오기 때문에 Codable한 T를 사용한다는 명시를 해주어야 한다.

이렇게 items를 제네릭으로 선언 후 API 호출 부분에서는 searchType에 맞춰 Blog, Image(Photo), News로 파싱되도록 구현했다.

```swift
try? JSONDecoder().decode(APIResult<Blog>.self.self, from: data)
```

### API Class의 ObservableObject 채택

우선 API에서 가져오는 로직은 있는데 이 데이터를 저장하고 View에서 사용하려면 어떻게 해야할까 하다가 API Class 자체를 ObservableObject로 선언했다.

클래스 내부 프로퍼티로 blogs, photos, news를 관리하며 @Published를 사용해 다른 뷰에서도 변경된 값을 체크할 수 있도록 수정했다. (역시 익숙치 않은 방법.. 틀린 것을 아닐까..?)

### 마무리

API는 붙였고 이제 테스트 코드를 한 번 추가해보려고 하는데 과연 잘 될지 모르겠다. Published 값을 테스트 할 수 있는지부터 찾아봐야 하나?.?
