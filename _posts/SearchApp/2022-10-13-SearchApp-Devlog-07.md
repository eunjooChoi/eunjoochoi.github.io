---
title: "[SearchApp Devlog 07] WebView와 UIViewControllerRepresentable"
excerpt: ""
  
toc: true
toc_sticky: true

categories:
  - SwiftUI-SearchApp
tags:
  - [SearchApp]
  
permalink: swiftui-searchapp/devlog07

---

## Devlog 07

### WKWebView를 SwiftUI에서 사용

결과 목록에서 link를 열기 위해서는 WKWebView를 사용해야 했다. SwiftUI 컴포넌트에 뭔가 웹뷰 관련된 것이 있지 않을까 했는데, WebKit을 사용하는 방법뿐이었다.

하지만 WebKit에서 제공하는 WKWebView는 UIView를 상속받는 친구라서 SwiftUI에서 사용할 수 있도록 처리가 필요하다.

요때 **UIViewRepresentable** 의 존재를 알게 됐다.

UIViewRepresentable는 SwiftUI에서 UIKit의 컴포넌트들을 사용할 수 있도록 만들 수 있는 프로토콜인데 다음과 같이 사용할 수 있다.

```swift
import SwiftUI
import WebKit

struct WebView: UIViewRepresentable {
    var url: URL

    func makeUIView(context: Context) -> WKWebView {
        return WKWebView()
    }

    func updateUIView(_ webView: WKWebView, context: Context) {
        let request = URLRequest(url: url)
        webView.load(request)
    }
}
```

WebView라는 struct를 하나 선언하고 UIViewRepresntable을 채택한다. 그러면 두 가지 메서드를 작성해야 한다는 에러가 뜬다.
그것이 바로 `makeUIView`와 `updateUIView`이다.

- makeUIView: 뷰 객체를 만들고 뷰 객체의 초기 상태를 구성
- updateUIView: SwiftUI로부터 새로운 정보(뷰의 새로운 상태)를 받아 화면 갱신

그래서 makeUIView에서는 내가 필요한 WKWebView를 생성해주고, updateUIView에서는 webView에 원하는 Url을 로드하면 되는 간단한 구성이다.

### CustomViewController와 UIViewControllerRepresentable

하지만 제가 원했던 건 WKWebView를 사용하면서 뒤로, 앞으로, 새로고침이 있는 화면이었습니다만...? 위 방법으로 하면 그냥 웹뷰만 덜렁 나온다.

그래서 아예 WKWebView와 툴바가 들어간 VC를 만들어서 그걸 SwiftUI에서 사용하게 하면 되겠다! 라고 생각해 코드를 수정했다.

일단 xib로 VC를 만들어주었다.

<img src="https://user-images.githubusercontent.com/22000470/195614832-73eade2b-9c62-4459-bd9d-5bfbae470ae1.png" width="400">

그리고 아주 간단하게 back, forward, refresh 버튼의 action 코드를 작성했다.

<img src="https://user-images.githubusercontent.com/22000470/195615035-b432edaf-d395-4e0a-a65a-53576566dac6.png" width="400">

마지막으로 원래 UIViewRepresentable을 사용하던 부분을 모두 UIViewControllerRepresentable로 변경해주면 사용할 준비 완료! (UIView와 다른 점은 UIViewController를 SwiftUI에서 사용할 수 있다는 점 뿐이었다.)

<img src="https://user-images.githubusercontent.com/22000470/195615337-ca3532e3-3899-416c-9bdc-c992edbafa37.png" width="400">

블로그와 뉴스 코드에서는 필요한 부분에 아래와 같이 호출해주면 된다.
`SwiftUIWebView(url: result.link)`

SwiftUIWebView를 호출해주면 결과적으로는 WebViewController가 호출이 되어 SwiftUI에서 UIKit의 요소들을 사용할 수 있게되다니..! 이런 브릿징을 잘 해주어서 현업에서도 SwiftUI와 UIKit 사이에 전환을 조금 더 수월하게 할 수 있지 않을까?

### 마무리
이제 이미지 화면만 노출하면 완성되는 샘플 앱. 다 하고 나면 SwiftUI 코드들의 테스트를 고민해봐야겠다.
