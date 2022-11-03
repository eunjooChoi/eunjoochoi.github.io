---
title: "[Swift] Self와 self"
excerpt: 
  
toc: true
toc_sticky: true

categories:
  - iOS-problems
tags:
  - [iOS, Swift]

permalink: /ios-problems/Self-vs-self

---

### self

참고 페이지
- [Self versus self in Swift 4 – capital “S” and lowercase “s”](http://iosbrain.com/blog/2018/09/26/self-versus-self-in-swift-4-capital-s-and-lowercase-s/)
- [Self vs self](https://ios-development.tistory.com/600)
 
우선 `self` 부터 알아보자.
self는 평소에도 많이 썼다. class나 struct 내부에서 자신의 property에 접근할 때 자주 붙여주었다.
--> 클래스 또는 구조체의 instance를 가리킨다.

![image](https://user-images.githubusercontent.com/22000470/199718815-db3b1f0d-212d-43db-9e4e-f9f3b4758c04.png)

위 이미지처럼 init의 파라메터로 받는 session과 class 내부에 존재하는 session을 명확하게 구분지어줄 때도 self를 사용한다.

- class의 self는 heap 영역(reference)/ struct의 self는 stack 영역(value)
   - class 메서드 내부에서 print(self)를 해보면 주소값이 나오고, struct의 메서드 내에서 self를 print하면 해당 인스턴스의 정보가 나옴.

### Self

- `Self`는 Type.
- protocol이나 class의 method 결과값으로 사용 가능.
   - protocol에서 사용하는 Self는 자기 자신을 채택한 타입을 의미 (아래에서 A라는 protocol을 Test라는 클래스/구조체가 준수한다면 여기서의 Self는 Test가 됨)
   - class에서 사용하는 Self는 해당 인스턴스 타입 자체를 의미

```swift
// protocol에서 사용되는 Self 생략 가능.
protocol A {
   associatedtype T
   init?(value: Self.T)
   var value: Self.T { get }
```

```swift
class TestClass {
   // TestClass 타입 리턴 
   func returnSelf() -> Self {
      return self
   }
```
