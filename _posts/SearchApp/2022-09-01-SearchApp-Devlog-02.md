---
title: "[SearchApp Devlog 02] 메인화면이 될 SearchMainView 생성"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog02

---

## Devlog 02

네이버 메인 화면처럼 검색어만 입력받는 검색 화면이 메인이 되면 좋을 것 같아서 SearchMainView를 작업했다. 추후 요 메인 화면에 검색어 기록같은 것도 남길 수 있으면 좋겠는데 우선은 검색을 위한 화면으로 만들어두었다.

### View

<img src="https://user-images.githubusercontent.com/22000470/188043920-9ed110ff-30c8-4911-8c42-9b5524f6ae0f.png" width="400">

앱 이름..은 아니지만 검색창만 있으면 허전하니 Title을 위에 넣어주고 아래 검색어를 입력받을 TextField를 추가해주었다. 


### 하다가 알게된 것들 / 알아보아야 할 것들

#### Stack의 spacing

Stack의 spacing 값을 넣어주면 요소 사이의 간격을 조절할 수 있다. 검색창의 구조가 HStack 안에 TextField와 Button을 넣어준 건데 두 요소 사이에 미세하게 여백이 있는 게 마음에 안들어서 `HStack(spacing:)` 으로 사이 간격을 0으로 없애주었다.

#### padding vs Spacer

padding과 Spacer의 차이를 확실히 알고 정리를 해두면 좋을 것 같다는 생각이 들었다. 우선 내가 원하던 모양은 padding으로 조절하는 것이라 padding을 사용했다.

pdding을 적용할 때도 원하는 edge에만 넣을 수도 있고 EdgeInset으로 한번에 네 방향을 모두 조절할 수 있었다.

#### GeometryReader의 존재

ContentView에 SearchMainView를 노출할 때 정중앙보다는 살짝 위쪽으로 올려주고 싶었다. storyboard라면 centerY에 값을 더하거나 빼는걸로 이동이 가능했는데, SwiftUI는 어떻게 조절하는지를 몰라 검색해보니 GeometryReader로 조절을 할 수 있다는 정보가 있었다.

GeometryReader의 geometryproxy가 컨텐츠를 담을 view의 사이즈 정보를 알려주어서 그 값으로 위치를 이동 시켰다. 요 부분도 추후에 다른 포스트로 정리해보고 여기 링크를 남겨두어야겠다.

### 마무리

TextField를 사용할 때 값을 가져와서 검색 API도 돌려야하고 결과 화면으로 이동도 해야하기 때문에 이제 `@State, @Binding, @Published` 에 관한 내용을 정리하면 좋을 것 같다.

값을 옵저빙하면 자동으로 UI를 업데이트 해준다는 게 좋으면서도 개념을 익숙하게 만드는 게 어렵군..

> 할일
- `@State, @Binding, @Published` 정리
- padding, Spacer 정리
- GeometryReader 정리
