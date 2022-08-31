---
title: "[SearchApp Devlog 01] 블로그/뉴스 리스트에 들어갈 ArticleView 생성"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog01

---

## Devlog 01

드디어 프로젝트를 생성하고 첫 UI 작업을 시작했다. 사실 네트워크 작업부터 할까 하다가 UI 작업 하면서 API 응답값을 가져다가 붙여넣는 게 더 괜찮을 것 같아서 우선은 블로그와 뉴스 결과 화면에 필요한 `ArticleView` 를 먼저 만들었다.

### View
우선 View의 모습은 아래와 같다.

<img src="https://user-images.githubusercontent.com/22000470/187696658-ff009d9e-bde6-498e-8f71-efdbafaf6c45.png" width="400">

네이버 검색 화면처럼 좌측에는 이미지가 있고 우측에는 제목과 내용, 날짜와 블로그명이 들어가도록 구현했다.
사실 뉴스에는 블로그 이름이 필요 없기 때문에 추후에 뉴스 화면인 경우에는 블로그 명을 가려주어야 한다.


### 하다가 알게된 것들

#### HStack의 양 끝에 간격을 주고 싶었다.

Swift Tutorial을 진행하면서 List에 들어갈 View를 만들어본 덕분에 쉽게 할 수 있었다. 다만 중간중간 뭔가 애매한 것들이 있었는데, 우선 content를 모두 감싸는 HStack이 View의 양 끝에 너무 딱 붙어있었던 점이다. Spacer()를 까먹고 계속 padding으로 뭘 하려고 시도하다가 Spacer가 있다는 걸 깨닫고 부랴부랴 HStack의 뒤에 추가해주었다. (이미지가 생각보다 떨어져있는 것 같아서 HStack 앞에는 안붙였는데 붙여주어야 할 것 같다.)

#### Image에 넣을 기본 아이콘 이름을 몰랐다.

요거는 SF Symbol beta를 받아서 아이콘 목록을 확인한 후에 넣을 수 있었다. 관련 포스트는 [여기](https://eunjoochoi.github.io/ios-problems/sf-symbols)

#### 각 폰트 사이즈를 조절하고 싶었다.

기본적으로 .headline과 같은 값을 지원해주지만 내가 원하는 폰트 사이즈를 주고 싶었는데 이 부분은

```swift
.font(.system(size:))
```
로 원하는 폰트 사이즈를 주어서 해결이 가능했다.

#### 텍스트가 모두 왼쪽 정렬이 되어야 한다.

요거는 텍스트를 묶어둔 VStack의 alignment를 leading으로 설정하니 깔끔하게 해결!

```swift
VStack(alignment: .leading) {
   ...
}
```

### 마무리

잘 만들어졌는지 확인하기 위해 ContentView의 body에 List를 추가하고 그 안에 ArticleView를 넣어주니 아래와 같이 완성이 됐다.

<img src="https://user-images.githubusercontent.com/22000470/187699580-c3f96a55-ccb7-4802-8bcb-7a85d48be910.png" width="400">

다음에는 툴바와 검색 창을 추가해보는 작업을 진행해야겠다. (이거 코드 리뷰를 해주는 사람이 없으니 산으로 가도 모른다는 약간의 두려움이...)
