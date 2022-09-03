---
title: "[SwiftUI] State and Binding"
excerpt: "iOS 문서 훑어보기 - SwiftUI에서 UI를 조작하고 화면을 갱신하는 법"
  
toc: true
toc_sticky: true

categories:
  - iOS-docs
tags:
  - [iOS-docs, swift, swiftui]
  
permalink: /ios-docs/swiftui-state-and-binding

---
애플에서 제공하는 SwiftUI 튜토리얼을 따라 만들 때는 그냥 코드를 보면서 대충 이해하고 넘겼지만 이제 직접 앱을 구현해보려고 하니 SwiftUI가 앱 UI를 어떻게 조작하고 값을 업데이트 하는지 제대로 알아둬야 두 번, 세 번씩 찾아보지 않을 것 같아 정리해보는 포스팅.

> 원문 링크는 [여기](https://developer.apple.com/documentation/swiftui/managing-user-interface-state) !!

SwiftUI는 user interface design에 대해 선언적 접근(declarative approach)을 제공한다. 
view들을 구성할 때 view 간의 데이터 종속성도 함께 나타내야 한다. 외부의 이벤트나 사용자의 조작으로 데이터가 변경되었을 때 SwiftUI는 알아서 데이터 변경이 영향을 끼치는 부분을 자동으로 업데이트 해준다. 결과적으로 프레임워크(아마도 SwiftUI겠지..?)는 기존의 view controller가 수행하던 작업의 대부분을 자동으로 수행한다.

<img src="https://user-images.githubusercontent.com/22000470/188273476-eed1caee-1d17-4637-8d1e-0d5512a6ae6a.png" width="400">
*출처: developer.apple.com*


그림으로 보니 훨씬 직관적이다. 외부 이벤트 또는 user interaction이 일어나게 되면 SwiftUI의 Action에서 이벤트를 받아 State를 변화시키고 변화 시킨 값이 View에 업데이트 되며 결과적으로는 사용자가 조작한 결과를 눈으로 볼 수 있게 된다.

SwiftUI에서는 `State`나 `Binding`과 같은 tool을 사용해 앱의 데이터와 user interface를 연결할 수 있다. 여러 화면에서 사용되는 값을 하나만 가지고 있는데 만약 interaction에 따라 그 값이 변경되면 여러 화면의 갱신에서 하나의 데이터만을 사용하므로 single source of truth(단일 진실 공급원 - 값을 한군데에서만 관리하는 것, 여기서는 값을 여러곳에서 다시 정의하는 것이 아닌 하나의 프로퍼티만을 선언해서 여러 뷰에서 활용하는 것)를 유지할 수 있다.

여러 상황에 따라 맞는 tool을 골라 사용하면 된다.

**State**
- 뷰 내부에서 일시적으로 사용되는 UI State에 대한 프로퍼티는 `State` property wrapper. (지역 변수 느낌의 UI State 관리 변수들)
   - ex: `@State private var isvisible = true`

**OservedProperty**
- `ObservableProtocol` 프로토콜을 준수하는 external model data에 연결할 때는 `ObservedProperty` property wrapper.

**EnvironmentObject**
- observable object in environment에 접근할 때는 `EnvironmentObject`.

**StateObject**
- view에서 직접 observable object를 인스턴스화 할 때는 `StateObject`.

**Binding**
- state나 observable object와 같이 single source of truth인 애들을 정의된 곳이 아닌 다른 곳에서 참조할 때는 `Binding`.

**Environment**
- value data를 앱 전체에서 사용할 수 있게 하려면 `Environment`.

**PreferenceKey**
- 하위 뷰에서 상위뷰로 데이터를 전달할 떄는 `PreferenceKey`.

**FetchRequest**
- Core Data에 저장되는 persistent data를 관리할 때는 `FetchRequest`.

정말 많은 property wrapper가 있고 또 아예 처음 보는 것들이기 때문에 익숙하지 않지만 이렇게 정리해두니 나름 명확하게 구분지을 수 있어서 좋다.

## Managing user interface state

그럼 이제 SwiftUI에서 유저의 인터랙션에 따라 변화되는 값의 상태를 어떻게 관리하는지 알아보자.

State로 관리되는 data들은 그 값이 사용되는 view들의 상위 view에 선언된다. (single source of truth를 위해)
요런 값들은 read-only로 만들수도 있고 다른 뷰에서도 값을 갱신할 필요가 있는 경우에는 값을 state와 binding을 사용해 값을 변경할 수 있도록 한다.
SwiftUI에서는 해당 값이 변경되는지 지켜보다가 값이 변경될 때 이 값에 영향을 받는 view들을 갱신해준다.

<img src="https://user-images.githubusercontent.com/22000470/188272431-67fd7808-3546-4678-8c08-a517a8f74157.png" width="400">
*출처: developer.apple.com*


요렇게 하나의 view에 state로 값을 선언하면 다른 뷰에서는 read-only로 값만 가져와서 사용하거나(오른쪽 view, 단방향) binding을 사용해서 값을 변경할 수도 있다(왼쪽 view, 양방향).

## State로 변경 가능한 값 관리하기

간단한 예제를 만들면서 State와 Binding 사용법을 익혀보자.

어떤 버튼을 누르면 Text를 숨겼다 노출시킬 수 있도록 구현한다. 이 때, Button의 Text에 Show / Hide를 나타낼 수 있게 설정한다.

우선 Button의 show/hide 값을 체크할 private 프로퍼티를 하나 만든다. 이때, 이 값은 **버튼에 의해 계속 변화하므로 @State property wrapper를 사용한다.

```swift
@State private var isShowing: Bool = false
```

그 다음 버튼을 추가한다. 이 때 Button을 누르면 toggle 하도록 action 메서드 내부에는 toggle을 추가하고, isShowing에 따라 값이 변경될 text를 설정해준다. Text는 isShowing의 값에 따라 opacity를 조절해 show/hidden 처리를 했다.

```swift
        VStack {
            Button {
                self.isShowing.toggle()
                
            } label: {
                Text(isShowing ? "Show" : "Hide")
            }

            Text("Hello, world!")
                .padding()
                .opacity(isShowing ? 1 : 0)
        }
```

실행해보면??

<img src="Sep-04-2022 00-35-40](https://user-images.githubusercontent.com/22000470/188277774-d6ba96e4-0705-4caa-bef1-b7629d96214d.gif" width="400">

Button을 누를 때마다 toggle 메서드가 호출되어 isShowing의 값이 함께 변화하고, 그 값의 결과가 Text와 버튼의 Text에 나타난다.

> View에서 수정 할 수 있는 데이터를 저장하는 경우에 State property wrapper를 사용한다. 이 때 property는 **private**으로 선언하는데, 이는 view의 내부에서만 값을 사용하거나 변경할 수 있도록 하기 위함이다.


## immutable value를 선언하고 사용하기 

그럼 이번엔 Text의 내용을 좀 바꿔보자. user profile 처럼 이름과 나이를 show/hide하도록 해본다. 유저 정보를 담을 UserProfile이라는 구조체를 선언해주었다.

```swift
struct UserProfile {
    let name: String
    let age: Int
}
```

그리고 ContentView의 내용을 다음과 같이 수정해주었다.

```swift
    let userProfile = UserProfile(name: "eunjoo", age: 28)
    @State private var shouldShowing: Bool = false
    
    var body: some View {
        VStack {
            Button {
                self.shouldShowing.toggle()
                
            } label: {
                Text(shouldShowing ? "Hide Profile" : "Show Profile")
            }
            
            HStack {
                Text("name: \(userProfile.name)")
                Text("name: \(userProfile.age)")
            }
            .padding()
            .opacity(shouldShowing ? 1 : 0)
            
        }
    }
```

- 유저 정보인 userProfile을 생성했다.
- 화면에 프로필을 노출할지 말지를 결정한다는 뜻에서 isShowing을 shouldShowing으로 변경했다.
   - Button의 Text도 Hide Profile / Show Profile로 변경했다.
- 유저 정보를 HStack에 넣고 userProfile의 name과 age를 표기하도록 했다.
- Text 각각에 padding과 opacity를 주지 않고 HStack 전체에 적용하도록 했다.

preview를 실행히보면 아래와 같이 나온다.

<img src="Sep-04-2022 00-50-01](https://user-images.githubusercontent.com/22000470/188278309-908bd0f7-7080-45d8-ba43-a18a33b312e0.gif" width="400">

> immutable value는 기존과 동일하게 let propertyName = 과 같이 선언할 수 있다.

## Binding을 사용해 State로 선언된 property 값 함께 쓰기

Button을 별도의 View로 분리해보자. 분리하면서 Button에 이미지도 달아주어본다.

<img src="https://user-images.githubusercontent.com/22000470/188278623-d0259cde-2554-439f-9b5f-ef980a9e1155.png" width="400">

ButtonView를 생성해 기존의 Button 코드를 그대로 옮겨오면 shouldShowing을 찾을 수 없다는 에러가 나온다. 당연하다, shouldShowing은 ContentView에 선언되어 단일로 관리 되어야 하는 값이기 때문이다.

그럼 이럴 때 shouldShowing을 어떻게 사용하느냐? 바로 **Binding**을 사용한다.
ButtonView 내부에 `@Binding var shouldShowing: Bool`을 선언해준다. 그러면 깔끔하게 에러를 다 없앨 수 있다.

> Binding 프로퍼티에는 값을 지정하지 않고 var로 선언한다. 값을 저장하지 않는 대신 State 프로퍼티와 two-way connection을 제공한다.

ButtonView를 분리했으니, 다시 CotentView로 돌아가서 ButtonView를 호출한다.

![image](https://user-images.githubusercontent.com/22000470/188278978-3978a147-c7c8-4f70-8efd-a62129b512f1.png)

> State 값을 하위 뷰에 넘겨줄 때는 프로퍼티 앞에 **$** 를 붙여준다. (바인딩에서 바인딩을 전달할 수 있다는 말이 적혀있는데, 이 부분은 공부하면서 좀 더 알아봐야겠다.)

만약 UserProfile에 Bool 값이 있다면 해당 값을 바인딩할 수 있다. 정보 제공 동의 여부를 user profile에 추가해 toggle로 보여준다면

1. 우선 UserProfile 구조체에 `var agreeProvideInformation: Bool`를 추가한다. (값 변경이 될 프로퍼티이므로 var 선언)
2. ContentView에 선언된 userProfile을 @State private var로 변경한다. (역시 값 변경이 될 프로퍼티이므로 @State property wrapper 사용)
3. 유저 정보를 노출할 때 Toggle을 추가한다 (`Toggle("provide user information", isOn: $userProfile.agreeProvideInformation)`)

<img src="https://user-images.githubusercontent.com/22000470/188279320-a96cdc25-7891-484e-a7ce-15dd7a6718c7.png" width="300">

Toggle을 선언할 때 보니 isOn의 값이 어차피 바인딩으로 들어간다. @State로 감싸진 userProfile을 사용해 구현해본 결과는 아래와 같다.

<img src="Sep-04-2022 01-23-38](https://user-images.githubusercontent.com/22000470/188279561-aade28a9-7817-4255-a11c-d428c40fc876.gif" width="400">

button에 이미지도, toggle 상태도 아주 잘 나온다. agreeProvideInformation 값이 잘 변경되어 show/hide 했을 때도 변경된 값이 잘 보여진다.

굿...👍 
