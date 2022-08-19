---
title: "Initialization 05"
excerpt: "iOS 문서 훑어보기 - Deinitialization"
  
toc: true
toc_sticky: true

categories:
  - iOS-docs
tags:
  - [iOS-docs, swift]
  
permalink: /ios-docs/Initialization-05

---
앞에서 Initialization을 알아봤으니 이번엔 Deinitialization을 공부해야겠다.

Deinitialization. 앞에 De가 붙은 것에서 유추할 수 있듯이 Initialization의 반대 개념이다. 인스턴스가 메모리에서 해제될 때 호출되는 친구인데, 키워드는 **deinit** 을 사용한다. 하지만 Init과 다르게 deinit은 클래스에서만 사용할 수 있다.



## How Deinitialization Works
Swift에서는 인스턴스 메모리 관리를 ARC(Automatic Reference Counting)를 체크해서 하기 때문에 인스턴스가 더이상 필요하지 않다고 판단될 때 자동으로 deallocate 된다. 자동으로 해결되는데 도대체 왜 Deinit이 필요한가? 요거는 내가 인스턴스 소멸 전에 처리해야 할 것들이 남아있을 때 사용하면 된다. (파일 open을 한 상태였다면 deinit에서 파일을 반드시 close 한다던지..)

deinit이 init과 다른 점이 또 있다면 딱 하나만 만들 수 있다는 것. init은 여러 개지만 deinit은 최대 하나만 만들 수 있다. deinit은 파라미터도 안 받고 괄호도 없이 아래처럼 사용한다.

```swift
deinit {
    // 처리할 놈들을 처리하자
}
```

Deinit은 인스턴스가 메모리에서 해제되기 전에 자동으로 호출되는 거지 내가 직접 호출하는 게 아니다. 슈퍼클래스의 deinit도 자식 클래스의 deinit이 끝나고 난 뒤에 자연스럽게 호출되므로 직접 호출할 필요가 없다. init과 다르게 인스턴스가 소멸되기 전 상태이므로 아직 프로퍼티에 접근할 수 있다. (UIViewController와 같은 친구들도 클래스니까 당연히 deinit 사용할 수 있다.)
