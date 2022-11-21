---
title: "[WWDC19] Building Custom Views with SwiftUI(진행중...)"
excerpt: "SwiftUI에서 View를 만들고 사용하는 방법"
  
toc: true
toc_sticky: true

categories:
  - WWDC
tags:
  - [iOS, WWDC]

permalink: /wwdc/WWDC19-Building-Custom-Views-with-SwiftUI

---

> 영상: [Building Custom Views with SwiftUI](https://developer.apple.com/videos/play/wwdc2019/237)
> 이미지 출처: https://developer.apple.com/videos/play/wwdc2019/237

## Layout Basics
- Root View: Safe Area를 제외한 화면 전체
- Content View: body로 표현되는 화면 (Root View 영역과 같아서 동일한 뷰로 취급할 수 있다.)
- Text(다른 SwiftUI Components): Content View 내에 추가하는 요소들 

![image](https://user-images.githubusercontent.com/22000470/201856061-7ae9324e-7bb0-4aa2-974f-ff0400f12acf.png)

**Child chooses its own size** : parent view는 child view 크기를 자기가 원하는 사이즈로 있게 둔다. (frame을 지정해주면 frame 크기대로, aspectRatio를 지정하면 그 비율 그대로)

**SwiftUI rounds coordinates to nearest pixel**: SwiftUI에서 각 코너의 round들을 view의 가장 가까운 pixel로 보정. anti-aliasing처럼 보이지 않고 명확하게 보여지게 된다.

|Before|After|
|-|-|
|![image](https://user-images.githubusercontent.com/22000470/201856695-0ddb4128-7ce0-4b0e-b439-ecfe9828249b.png)|![image](https://user-images.githubusercontent.com/22000470/201856745-b36b78f1-b995-45f4-ab93-f5e8fb68ea76.png)|

코드로 View의 구성이 어떻게 이뤄지는지 살펴보자.

```swift
struct Toast: View {
   var body: some View {
      Text("Avocado Toast")
         .padding(10)
         .background(Color.green)
   }
}
````

layer로 보자면 아래와 같다.
![image](https://user-images.githubusercontent.com/22000470/201859557-0194d25b-7415-442b-814e-b327ff6e5126.png)

그렇다면 layout process는 어떤식으로 이뤄지느냐?
1. root view가 background view에 사이즈를 제안한다. '너,,, 아직 어떤애인지 모르니까 일단 view 전체 사이즈로 한 번 제안해볼게'
2. 하지만 background는 아직 사이즈를 확답할 수 없는 상태(layout neutral). 그래서 padding view에게 다시 물어본다. 
3. padding은 그래도 10이라는 숫자를 갖고 있어서 '아~ 일단 view에서 10만큼 떨어져야 하는 건 알고 있어~' 라고 답한다. 
![image](https://user-images.githubusercontent.com/22000470/201860687-a99daa04-1ffa-44d4-9e8e-7c8738a78b18.png)
   - 그래서 요렇게 background의 크기가 줄어든다. 요 영역만큼을 Text에게 너가 쓸 수 있는 공간이야~ 하고 알려줌.
4. 그 이후 텍스트를 coordinate space에 적절하게 배치!
5. 그럼 다시 Text의 위치에 맞게 padding이 조절이 되고, padding은 그 사이즈를 background에 전달한다.
6. 요때, 바로 Root View에 자신의 영역을 알리지 않고 Color에게 background의 크기를 알려준다. 그래야 background만큼 green으로 칠해지니까!
7. 마지막으로 background는 자기의 크기를 root view에 알리고 root view는 이 Text를 중앙에 배치한다.

추가로 아보카도 이미지로도 설명을 해줬는데, frame은 제약조건 같은 것이 아니라 액자 같은거라고 생각하는 게 중요하다는 말을 덧붙였다.

## HStack and VStack
