---
title: "[WWDC19] Building Custom Views with SwiftUI"
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
- Stack을 사용하면 애플에서 제공하는 기본적인 spacing을 사용하게 된다. (알아서 해주니까 굉장히 깔끔.) 하지만 간격을 직접 조정하고 싶을 때는 **spacing** 값을 조절해주면 된다.
   - `VStack(spacing: 4)` 처럼 사용한다.
- 읽는 방향이 다른 언어들에 대해서도 레이아웃 자동으로 설정돼서 개발자가 딱히 건드려줄 것이 없다

## How Stacks Work
![image](https://user-images.githubusercontent.com/22000470/206829399-dc9f286c-20f1-49d9-b857-0c7e5eb11025.png)

1. Stack 내에서 요소 간 간격을 제외
2. 남은 길이를 HStack에 들어갈 요소 세개가 동일하게 배분
3. 세 개 중 가장 가변성이 적은 image의 길이를 고정 후 총 길이에서 image의 길이 제외
4. Text 두 개가 나머지 길이를 동일하게 나눠가짐
   - 그 중 조금 더 길이가 짧은 Delicious의 길이를 고정하고 그 후 남은 길이를 Avocado Toast가 갖게 됨

하지만 만약 화면이 좁아서 두 text가 잘릴 위기에 처한다면??
--> SwiftUI에서 Truncate 처리를 한다. 만약 두 Text 중 더 중요한 Text가 있다면 **layoutPriority(0~1사이의값)** 을 적용한다. (priority 값이 높은 순으로 길이가 적용된다.)

![image](https://user-images.githubusercontent.com/22000470/206829496-7e92a1e5-3da6-4d28-83b2-1f2e98a69823.png)
 
## Alignments

![image](https://user-images.githubusercontent.com/22000470/206829579-767e6af9-15c6-4fdf-b3d9-d106d24db302.png)

WWDC에서 예시 들어주는 거 왜이렇게 웃기지 ㅋㅋㅋㅋㅋㅋㅋ 디자이너가 와서 Delicious와 이미지와 Avocado Toast의 라인이 안맞잖아여!! 라고 확대해서 가져오면 어떡하냐~ 이런 예시를 들어줬는데 뭔가 현업에서 있을 법한 얘기라 웃겨 죽겠네 ㅋㅋㅋㅋㅋ.... 하지만 슬프다...

이런 경우에도 alignment 값만 조정해주면 된다고 한다. 바로 **lastTextBaseline**

![image](https://user-images.githubusercontent.com/22000470/206829625-770300fc-e74e-4c8d-b3cd-90e73fe877fe.png)

값을 적용해주면 요렇게 새 가지 요소의 baseline이 동일해진다고..! 

그럼 이미지...의 baseline만 조절하고 싶을 때는??

![image](https://user-images.githubusercontent.com/22000470/206829682-0fb2ae51-c025-4e2f-8221-ec4f825a0e79.png)
