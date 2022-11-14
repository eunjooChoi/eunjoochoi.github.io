---
title: "[WWDC19] Data Flow Through SwiftUI"
excerpt: "SwiftUI에서는 데이터를 어떻게 사용할까?"
  
toc: true
toc_sticky: true

categories:
  - WWDC
tags:
  - [iOS, WWDC]

permalink: /wwdc/WWDC19-Data-Flow-Through-SwiftUI

---

영상: [Data Flow Through SwiftUI](https://developer.apple.com/videos/play/wwdc2019/226)
관련 정리 포스트
- [State and Binding](https://eunjoochoi.github.io/ios-docs/swiftui-state-and-binding)
- [ObservableObject, @ObservedObject, @EnvironmentObject, @StateObject 이해하기](https://eunjoochoi.github.io/ios-docs/managing-model-data-in-your-app)

> 이미지 출처: https://developer.apple.com/videos/play/wwdc2019/226

## What is data?
- UI를 움직이는 모든 정도
   - 데이터는 모든 종류의 모양과 형태로 제공 (ex: 토글)
- 모델 데이터(ex: 메시지 목록 정보...)

## Source of Truth
![image](https://user-images.githubusercontent.com/22000470/201662222-88805256-5e60-4370-aa69-f4ec11302837.png)

- 형제 뷰에서 isPlaying을 각각 관리하면 하나의 값이 업데이트 될 때 노티나 KV Observing을 사용해서 다른 값까지 업데이트 해주어야 함. (Source of Truth x)

![image](https://user-images.githubusercontent.com/22000470/201662239-9b9aa344-326b-49bc-b676-3d796004b50b.png)

- 값이 필요한 view들의 공통 조상에 isPlaying을 선언 --> 값을 하나로 관리할 수 있다. (Source of Truth o)

## State
![image](https://user-images.githubusercontent.com/22000470/201663710-9fa9301f-9857-43b8-91df-32cfb91c9b44.png)

- persistent storage에 State 할당. (isPlaying을 system이 storage에 생성할 때 명확하게 constant value를 지정해주어야 한다.)
   - View는 system에 의해 자주 재생성될 수 있는데 state를 사용하면 같은 view를 업데이트 하더라도 storage에 저장된 변수를 유지해야 한다는 것을 프레임워크가 알 수 있다.
- State로 선언된 프로퍼티는 해당 변수가 선언된 view에서만 동작하도록 **private** 으로 설정

![image](https://user-images.githubusercontent.com/22000470/201665614-ef204731-8364-46e2-8419-06ee72401091.png)

- State 변수를 SwiftUI가 알고있고, 재생 버튼을 누르게 되면 isPlaying이 true가 되는 것을 PlayerView에 알림
   - 변수를 가지고 있는 PlayerView의 유효성을 검사(너 이 프로퍼티 가지고 있는데 업데이트?? --> Button에서 쓰고있는데 업데이트?? 이런식으로 하위에 있는 view들을 비교하고 **변경된 부분만 다시 렌더링** 하기 때문에 뷰 갱신이 효율적으로 이뤄진다.)

> Every `@State` is a source of truth.

> Views are a function of state, not of a sequence of events.

![image](https://user-images.githubusercontent.com/22000470/201667584-071faf07-01d8-46cc-b319-d1d608f59e4f.png)

- User Interaction -> State 변수를 mutation -> View는 그걸 받아서 업데이트 + Render 하도록 단방향으로 흐른다.

## Binding

![image](https://user-images.githubusercontent.com/22000470/201668950-5fb143bf-ba50-425a-8341-c4fe0f90c9f8.png)

- PlayerView의 Button을 PlayButton이라는 또 다른 View로 분리. PlayerButton에서도 isPlaying을 써야하는데.... 이것도 State로 쓸까??
   - PlayButton에서도 isPlaying을 State로 선언하면 Source of Truth로 관리하는 isPlaying 변수가 두 개가 된 것이지 같은 값을 공유하는 것이 아니다.

그래서 나온 것이 바로 **Binding**

Binding은 ownership 없이 읽기와 쓰기가 가능하다. State가 붙은 변수들의 값을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/201669694-2e9a622c-7316-4964-bf05-ab46ec777420.png)

![image](https://user-images.githubusercontent.com/22000470/201669474-c0af59a7-014d-44df-9aca-d0d4060296ae.png)

Binding은 위와 같이 사용한다. PlayerView에서 Binding에 필요한 State 변수를 `$State 변수`로 넘겨주게 된다.

> Binding을 통해 참조하는 것이라 View 간에 동기화 하는 로직(ex: 노티피케이션, 델리게이트 같은)이 필요없다. 

## Working With External Data
![image](https://user-images.githubusercontent.com/22000470/201671738-312aa364-0190-45a1-af16-52536328232f.png)

SwiftUI에서는 External events에 대해서도 동일하게 동작한다. SwiftUI에서는 이런 external events를 표현하기 위한 single abstraction을 가지고 있다. 그것은 바로 **Publisher**.

Combine Framework에 있는 publisher, Combine은 시간 경과에 따른 값 처리(processing values over time)를 위한 unified declarative API이다.

> Publishers를 SwiftUI에서 사용할 때 메인 스레드에서 제외해야 한다. (these publishers with SwiftUI, they should omit on the main thread.) Main thread에서는 .receive(on:)을 사용한다. (별도로 공부해서 정리해보기)

## BindableObject Protocol (지금은 ObservableObject로 사용)
- 상단에 정리해둔 것으로 참고

## EnvironmentObject
![image](https://user-images.githubusercontent.com/22000470/201677920-76b765ad-358e-49e6-8159-48551aafe489.png)

Model을 EnvironmentObject로 선언하면 여러 곳에서 사용이 가능하다. 하나를 가지고 여러곳에서 사용하는 것이기 때문에 data 변경이 일어나면 해당 model을 사용하는 모든 view 계층에서 자동으로 update 작업이 일어난다.

View 계층을 타고 들어가는 것이 아니기 때문에 사용을 원하는 View에서만 사용할 수 있다는 장점!

## 정리
![image](https://user-images.githubusercontent.com/22000470/201678760-c9d68ad4-7d66-4cf3-b74a-b3c35bac2fac.png)
![image](https://user-images.githubusercontent.com/22000470/201678831-1c425b37-867f-4b73-8ba2-96e833c1b32b.png)
![image](https://user-images.githubusercontent.com/22000470/201678880-9f53456f-21e9-4c9e-9e4f-e10403b83ac6.png)

- State는 view local, value type, managed, allocated and created by the framework 인 데이터들에게 사용
   - Button을 눌렀는지 안눌렀는지와 같은 케이스에서 State는 good.
- BindableObject는 내가 컨트롤 할 데이터들에게 사용
- view에서 데이터를 변경할 필요가 없을 때는 read-only 프로퍼티로 선언
- Binding은 데이터에 대한 first class reference. 값을 소유하지 않은 상태로 읽고 쓰기 가능
