---
title: "[WWDC19] Introducing SwiftUI: Building Your First App"
excerpt: "몰랐던 기능들 정리해두기"
  
toc: true
toc_sticky: true

categories:
  - WWDC
tags:
  - [iOS, WWDC]

permalink: /wwdc/WWDC19-Introducing-SwiftUI-Building-Your-First-App

---

영상: [Introducing SwiftUI: Building Your First App](https://developer.apple.com/videos/play/wwdc2019/204/)

## Xcode.. 너 좀 새롭다...

1. Xcode 우측 상단 + 눌러서 원하는 요소들 preview에 넣으면 코드 자동 삽입
   - 심지어 corner radius까지 있음..!!
![image](https://user-images.githubusercontent.com/22000470/201105609-bef0d6ba-91eb-42b8-aecc-f8d0ebf9ad49.png)

2. 코드 새로운 struct로 분리하기
   - 이것도 조금 신기했다. 코드가 길어지면 Extract SubView로 추출할 수 있다니... 물론 내부에서 사용되는 변수 같은 걸 정리하긴 해야겠지만 요렇게 자동으로 분리해주는 건 신기해서 넣어봤다.
![image](https://user-images.githubusercontent.com/22000470/201106042-9e87c0a6-e4e7-4040-8f86-dea04261c36f.png)


## SwiftUI에 대한 기본 설명들

- SwiftUI의 View들은 View를 채택한 **구조체** 들
- extracting subview를 주저하지 말자, SwiftUI에서는 오버헤드가 없다.
- @State를 사용하면 해당 프로퍼티의 값이 바뀌었을때 프레임워크가 body에게 물어본다. '너 요 값 쓰고 있던데, 이거 새로운 값으로 바뀌었어~ 너도 갱신할꺼야?' --> refresh rendering

![image](https://user-images.githubusercontent.com/22000470/201110646-1c9c9450-26a8-4c34-b3be-749fa1cba340.png)

- 기존의 View는 데이터가 갱신이 잘못 됐을 때 뷰 갱신이 제대로 이뤄지지 않아 bug가 발생할 수 있었는데 SwiftUI는 자동으로 값 갱신을 관리하기 때문에 이런 버그가 일어나지 않는다.

- UIKit을 사용해 구현 할 때는 completion과 같은 예상 시간을 알 수 없는 요인들을 포함해 여러가지 요인(navigation push/pop ...)들이 정상 동작을 방해할 가능성이 있지만 SwiftUI에서는 View를 준수하기 위해서 **단 하나의 property인 body** 만을 구현하면 된다. 진입점(아마도 body를 말하는.. 것이겠지?)도 단 하나!


초심을 다잡자는 마음으로 틀어보는 19년 첫 SwiftUI 소개 WWDC 영상. Tutorial 하다가 갑자기 3년전으로 되돌아가니 조금은 달라진 문법도 있지만(예를들면 BindableObject 같은거..?) 어차피 SwiftUI는 처음 배우는 거라 요렇게 아예 첫 발걸음부터 보는 재미도 쏠쏠하다..!

다음 영상은 Data Flow Through SwiftUI가 될 것 같다.
