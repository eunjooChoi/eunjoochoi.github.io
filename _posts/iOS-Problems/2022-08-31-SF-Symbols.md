---
title: "SF Symbols - xcode에서 애플 기본 아이콘 사용하기"
excerpt: "SwiftUI에서 애플 기본 아이콘 사용하기"
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Xcode]

permalink: /ios-problems/sf-symbols

---

검색앱 개발을 시작하고 나서 최대한 애플에서 제공하는 기본 아이콘을 사용하고 싶었다. 
storyboard에서는 기본 이미지를 확인하기가 괜찮았던 것 같은데 SwiftUI에서는 기본 이미지를 어떻게 확인해야할지 도통 모르겠는 것..!

그래서 구글에 애플 기본 이미지를 검색해보니 **SF Symbols** 라는 것이 나왔다.
맥북에 기본으로 설치되어있나 싶어서 검색해보니 안나오고 애플의 SF Symbols 페이지로 연결이 되어서 다운로드를 받았다.


> SF Symbols 링크는 [여기](https://developer.apple.com/sf-symbols/)

페이지에 들어가보니 굉장히 많은 symbol들을 제공한다고 자랑을 하고 있어서 냉큼 다운로드를 받았다. 지금은 베타버전인데 다음에는 정식 버전이 나오겠지..?

아무튼 SF Symbols 베타를 다운로드 받으면 다음과 같은 화면이 나온다.


<img src="https://user-images.githubusercontent.com/22000470/187691912-e75b1d53-4f5f-4ab9-bc24-dd9f22eb5ab3.png" width="400">

여기서 기본적으로 제공하는 아이콘들을 확인할 수 있다. 무엇보다 카테고리 별로 정리도 되어있고 검색도 가능해서 나중에 필요한 아이콘이 있을 때 찾기도 쉬울 것 같다.

나는 photo에 관련된 아이콘이 필요해서 검색했고, 필요한 아이콘 명을 찾아 Image(systemName:)으로 원하는 아이콘을 추가할 수 있었다.
