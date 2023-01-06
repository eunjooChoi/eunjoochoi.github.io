---
title: "[SwiftUI] ObservableObject, @ObservedObject, @EnvironmentObject, @StateObject 이해하기"
excerpt: "iOS 문서 훑어보기 - Managing Model Data In Your App"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift, SwiftUI]
  
permalink: /swift/managing-model-data-in-your-app

---

요번 포스팅에서는 Model Data를 만들고 그 값을 View에 보여주도록 하는 내용을 정리해본다.

원문 페이지를 살펴보니 `ObservableObject`, `@ObservedObject`, `@EnvironmentObject`, `@StateObject`를 활용하는 예제를 구현하고 있어서 각각의 쓰임에 대해 이번 포스팅에서 좀 더 자세히 알아보는 시간이 될 것 같다.

> 원문 링크는 [여기](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app) !!

앱에 사용하는 데이터를 관리할 때 보통의 경우 별도의 파일로 만들어 데이터를 어떻게 저장할 지 저장 방식을 모델링 한 이후에 class 또는 struct로 구현하게 된다. 이렇게 로직과 데이터를 분리하게 되면 모듈화/ 테스트가 용이한 코드 환경을 조성할 수 있다. 

기존에는 뷰 컨트롤러가 user interface와 모델 사이의 데이터를 관리했지만 SwiftUI에서는 동기화를 통해 데이터가 관리된다. 데이터가 변경된 것을 화면에 반영하고 싶을 때는 Model Data Class를 **Observable**하게 생성하고 클래스의 프로퍼티를 **Publish** 하며 **special attributes**로 Model Data Instance를 생성할 수 있다. (이렇게 되면 Source of Truth도 유지할 수 있다! 값을 바인딩해서 계속 사용하기 때문에...! 요 내용은 아래에서 설명한다.)


## Make model data observable

모델의 데이터 변경을 SwiftUI로 표현할 때는 모델 클래스에서 `ObservableObject`를 채택하면 된다. 
[이전 게시글](https://eunjoochoi.github.io/ios-docs/swiftui-state-and-binding) 에서는 UserProfile의 정보를 Show/Hide만 했지만 위에 Edit 창을 하나 만들어 이름과 나이를 변경할 수 있도록 해보자.

#### State로 선언되었던 UserProfile의 변경

우선 UserProfile의 값들이 변경되는 것을 감지하기 위해서 UserProfiile을 class로 변경하고 `ObservableObject` 프로토콜을 채택한다.

> UserProfile을 수정하기 위해서는 우선 name과 age가 var로 선언되어야 한다.

<img src="https://user-images.githubusercontent.com/22000470/188476662-bf14963a-3364-4c49-854f-579602e6c8fc.png" width="400">

#### @Published 사용

Model Data의 프로퍼티 값이 변경되었을 때 UserProfile을 사용하는 다른 곳에서도 변경된 것을 알 수 있도록 해주어야 한다.
'나 값 바뀌었어요~~~' 하고 알려줘야 하는 프로퍼티임을 표시하기 위해 `@Published`를 프로퍼티 앞에 붙여준다. (Published의 Definition을 타고 들어가보니 요 놈은 Published의 typealias라고 나온다. 이름이 같은데 굳이 typealias로 한 것은... 이유가 뭘까?)

> 시스템이 ObservableObject를 채택한 클래스에 대해 자동으로 `ObjectWillChangePublisher` 타입을 추론하고 @Published로 선언된 프로퍼티의 값이 변경된 것을 알려주는 `ObjectWillChange` 메서드를 통합한다. Publisher에 관한 설명은 [여기](https://developer.apple.com/documentation/combine/publisher)!

> 만약 값을 변경해야 하지 않는 프로퍼티가 있다면... (ex. let으로 선언되었다거나?) 쓸데없이 @Published propertywrapper를 사용하지 말자. 오버헤드를 줄이는 게 좋으므로! 하지만 저는 모든 값을 변경해볼 것이기 때문에 모든 프로퍼티에 사용했습니다.

<img src="https://user-images.githubusercontent.com/22000470/188481408-ede5cfb2-aa57-45e3-b254-950e4327fb8e.png" width="400">


## ObservableObject 변화 감지

ObservableObject를 채택한 Model Data를 사용하고 싶다면, 그리고 그 값이 변경되는 것도 확인해야겠다면!!!

사용하는 곳에서 model data를 선언할 때 앞에 **@ObservedObject** 속성을 추가해주면 된다.

이전 게시글에서 만든 userProfile은 @State 속성이 추가되어있지만, 변경해준다.

```swift
@ObservedObject var userProfile = UserProfile()
// UserProfile을 클래스로 바꾸면서 모두 default 값을 설정해주어서 UserProfile()로 수정했다.
```

ObservableObject와 ObservedObject가 참 헷갈리는데... 전자는 관찰 가능한 객체다!!니까 Model Data를 선언할 때 쓰고 후자는 이미 너는 관찰중인 객체다!!라서 view에서 쓴다고 생각하기로 했다.

> Check 01. ObservedObject로 선언된 userProfile의 프로퍼티를 각각 다른 하위 뷰로 보내기 가능합니다. (ex. Text(userDefault.name)

> Check 02. 당연히 userProfile을 통째로 하위뷰로 넘겨줄 수도 있습니다.
   
> Check 03. 만약 데이터가 변경된다면 SwiftUI는 @ObservedObject로 선언된 모든 곳을 업데이트 해줍니다.
   
> Check 04. ObservedObject는 SwiftUI에 속해있고 ObservableObject는 Combine에 선언되어있습니다. ObservableObject를 사용할 때 Combine을 import하지 않더라도 빌드는 되지만 그래도 명시적으로 import를 했습니다.


## @StateObject의 등장

SwiftUI는 필요에 따라 언제든지 View를 다시 만들 수 있다. 그럼 View를 다시 만들 때 우리가 만든 @ObjservedObject도 새롭게 생성이 된다.

왜냐..? 바로 아래와 같이 선언했기 때문이다.

```swift
@ObservedObject var userProfile = UserProfile()
```

우리는 UserPfofile을 생성하는 코드를 넣었기 때문에 View가 새로 생성될 때 userProfile도 같이 초기화가 되는 불상사가 생기게 된다. 아니 나는 이 데이터 값을 계속 옵저빙해야하는 상황인데 뷰 잠깐 없어졌다가 생겼다고 값이 초기화 되면 추적하는 의미가 없지 않나요?? 라고 생각할 수 있는데,

SwiftUI에서는 이렇기 때문에 **@StateObject**를 사용하라고 한다.

StateObject는 ObservedObject와 같은 동작을 하는 친구인데 다른 점이 있다면 바로 뷰가 몇번 다시 생성되던지 **view instance 내에서 single instance로 관리된다**는 점이다. (A state object behaves like an observed object, except that SwiftUI knows to create and manage a single object instance for a given view instance, regardless of how many times it recreates the view.)

StateObject가 들어있는 View를 여러개 생성하면 View 별로 각각의 StateObject가 생성이 된다. 동일한 뷰이더라도 모델 데이터는 각각 개별적으로 관리되는 것 같다.(While SwiftUI doesn’t recreate the state object within a view, it does create a distinct object instance for each view instance.)

또한 StateObject로 선언된 모델 데이터는 :App 이 있는 최상위 인스턴스에서도 생성할 수 있다. 이렇게 되면 앱이 종료될 때까지 동일한 인스턴스를 하나만 가지고 계속 사용할 수 있게된다.


## environmentObject(_:)

하지만 @StateObject를 사용한다면 View가 여러 계층인 화면의 경우 계속 하위 View로 프로퍼티를 주입해주어야 한다. 만약 전역적으로 사용될 Model Data라면 `environmentObject(_:)` 를 사용해보자. 

`environmentObject(_:)`는 '나 이런 observableObject가 있는데 하위 계층에서도 이 데이터를 사용할 거예요~~'라고 알려준다.

```swift
@main
struct SampleAppApp: App {
    @StateObject private var userProfile = UserProfile()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(userProfile)
        }
    }
}
```

메인이 되는 SampleAppApp(이름이 이상하..다..하핫)에 userProfile을 선언하고 ContentView에 environmentObject()를 추가했다. 

ContentView에서 사용할 때는 `@EnvironmentObject var userProfile: UserProfile` 로 사용할 수 있다.

`@EnvironmentObject`는 '나 상위뷰에서 만든 ObservableObject 사용할거예요~~' 라고 알려준다.

> @EnvironmentObject를 선언하게 되면 Preview에서도 똑같이 environmentObject를 써서 값을 넣어주어야 한다. 위에 메인에서도 ContentView를 호출할 때 넣어준 것과 동일하다.


## Create a two-way connection using bindings

모델 데이터의 값이 변경되는지를 확인해보자. 지난번에 만든 화면에 TextField를 두 개 추가했다. (이름용, 나이용)

나이는 Int형이기 때문에 TextField(titleKey:value:formatter)를 사용했다.

```swift
struct ContentView: View {
    @EnvironmentObject var userProfile: UserProfile
    @State private var shouldShowing: Bool = false
    
    var body: some View {
        VStack() {
            TextField("Change Name", text: $userProfile.name)
                .padding()
                .border(.green, width: 2)
            
            TextField("Change Age", value: $userProfile.age, formatter: NumberFormatter())
                .padding()
                .border(.blue, width: 2)
            
            ButtonView(shouldShowing: $shouldShowing)
            
            VStack {
                Text("name: \(userProfile.name)")
                Text("name: \(userProfile.age)")
                Toggle("provide user information", isOn: $userProfile.agreeProvideInformation)
            }
            .padding()
            .opacity(shouldShowing ? 1 : 0)
        }
    }
}
```

텍스트 필드에서 노출되는 값은 userProfile의 나이와 이름이다. $사용하면 해당 프로퍼티들의 속성에 대해 바인딩이 가능하다. 내가 TextField에서 변경한 값이 아래 정보 칸에서 변경되는지 확인해보자.

<img src="https://user-images.githubusercontent.com/22000470/188491847-674116a0-383d-48d5-a155-56ca60f5ae26.gif" width="400">

잘된다 잘된다 너무 잘된다!!!!!
Landmarks 앱을 따라 만들때는 정리하면서도 제대로 안 알아보고 따라하니까 의미나 용도를 정확히 파악하지 않고 넘어가는 경우가 많았는데 이렇게 하나씩 개념 정리하니까 조금 더 이해가 확확되어서 좋다.. 🍎 좋아..

