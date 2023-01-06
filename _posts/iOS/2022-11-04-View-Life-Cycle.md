---
title: "[iOS] View Life Cycle"
excerpt: 
  
toc: true
toc_sticky: true

categories:
  - iOS
tags:
  - [iOS, Swift]

permalink: /ios/view-life-cycle

---

View life cycle을 정리해보자. (참고사이트: [애플문서](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621454-loadview))

<img src="https://user-images.githubusercontent.com/22000470/199968184-c124de1b-83f4-47bd-b7bd-dfcdf896bf72.png" width="400">


### loadView()
- Controller가 관리하는 View를 생성한다.
   - 직접 호출 금지 (ooo.loadView() 이런식의 호출)
   - view property가 nil인 경우 호출됨 (view를 생성해 view property에 할당하는 역할)
   - view controller가 nib 파일과 연결되어있을 경우 nib 파일로부터 view를 로드. (nib 파일이 연결된 게 없는 경우 plain UIView를 생성)
   - Interface Builder를 사용해서 view를 생성하고 view controller를 초기화 하는 경우에는 **절대 오버라이드 금지**
   - You can override this method in order to create your views manually. If you choose to do so, assign the root view of your view hierarchy to the [view](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621460-view) property. The views you create should be unique instances and should not be shared with any other view controller object. Your custom implementation of this method should not call super.
   - If you want to perform any additional initialization of your views, do so in the [viewDidLoad()](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload) method.

### viewDidLoad()
- controller의 view가 **memory에 로드된 이후** 호출
   - loadView()에서 초기화 되는 것들 이외에 초기화 작업이 필요한 부분을 viewDidLoad를 오버라이드 해서 구현
   - 참고할만한 property / method: [viewIfLoaded](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621360-viewifloaded), [isViewLoaded](https://developer.apple.com/documentation/uikit/uiviewcontroller/2097563-isviewloaded), [loadViewIfNeeded()](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621446-loadviewifneeded)

### viewWillAppear(_:)
- `func viewWillAppear(_animated: Bool)`
- view hierarchy에 view가 추가될 것임을 view controller에게 알려줌
   - view가 최초 생성될 때는 viewDidLoad -> viewWillAppear 순으로 호출되겠지만 다른 view에 들어갔다가 되돌아 나올 때는 viewWillAppear 부터 호출 (popover로 띄워진 view에서 나갈 때는 viewWillAppear 호출되지 않음)
   - animated가 true인 경우 view가 window에 추가될 때 animation 작동
   - override 할 때 super 호출 필요
   - view가 화면에 나타나기 전에 필요한 작업을 추가할 수 있음

### viewDidAppear(_:)
- `func viewDidAppear(_animated: Bool)`
- view가 view hierarchy에 추가되었음을 알림
- viewWillAppear와 마찬가지로 super 호출 필요, popover 상태에서는 호출되지 않음

### viewWillDisappear(_:)
- `func viewWillDisappear(_ animated: Bool)`
- view가 view hierarchy에서 제거될 예정임을 view controller에 알려줌
   - view가 제거되기 전에 호출됨
   - view가 사라지기 전에 다시 원복해야 할 값이 있거나 처리가 필요한 부분을 해당 메서드에서 수행
   - super 호출 필요

### viewDidDisappear(_:)
- `func viewDidDisappear(_ animated: Bool)`
- view가 view hierarchy에서 제거되었음을 알림
   - super 호출 필요
   - view가 제거된 이후 호출됨
