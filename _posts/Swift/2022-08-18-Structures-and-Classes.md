---
title: "Structures and Classes"
excerpt: "iOS 문서 훑어보기 - Structure, Class"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift]
  
permalink: /swift/Structures-and-Classes

---
오늘은 구조체와 클래스에 대해 알아본다.

원문 Documents는 [여기](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)!

클래스와 구조체 모두 interface와 implementation의 위치를 다르게 두어도 된다. 개발할 때도 클래스나 구조체를 별도의 파일로 분리해두고 사용했던 기억이 난다. 그 편이 클래스/구조체가 어떤 생김새인지 알아보기도 편하고 관리하기도 편했었다.

----

## Comparing Structures and Clases
### 구조체 - 클래스 공통점

- Define properties to store values
- Define methods to provide functionality
- Define subscripts to provide access to their values using subscript syntax
- Define initializers to set up their initial state
- Be extended to expand their functionality beyound a default implementation
- Conform to protocols to provide standard functionality of a certain kind

클래스와 구조체 둘 다 value, function 정의가 가능하고 subscript도 정의할 수 있고 init도 있다. 프로토콜을 채택할 수도 있다. Be extended 어쩌고는 extension 같은걸로 기능을 확장할 수 있다는 뜻일까...? 그런걸로....



### 클래스에서만 가능한 기능들

- 상속
- Deinitializer
- Type casting enables you to check and interpret the type of a class instance at runtime. (타입 캐스팅을 사용하면 런타임에 클래스의 인스턴스 유형을 확인/해석할 수 있다.)
- Reference counting allows more than one reference to a class instance. (Reference count는 클래스 인스턴스에 대한 둘 이상의 참조를 허용한다. - ARC를 조금 공부할 필요가 있겠다.)

클래스가 이것저것 조금 더 지원해 주는 기능들이 많다. 하지만 general guideline으로 구조체가 더 추론하기 쉽기 때문에 구조체 사용을 선호하라는 것 같다. 꼭 필요할 때만 클래스를 사용하자! 라는 주의인듯. ([Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing-between-structures-and-classes))

## Definition Syntax

```swift
// structure
struct TestStructure {
    var a = 0
    var b = 1
}

// class
class TestClass {
    var testStructure = TestStructure()
    var c = 1
    var boolean = false
}

// 구조체와 클래스의 이름은 대문자로 시작하기
```

init에 관한 이야기들은 Initialization을 확인하면 된다. 정리해둔 포스트가 있어서 다행이다..ㅎ

[initialization1](https://choi-log-life.tistory.com/entry/iOS-Swift-initialization-1?category=1047722)

[initialization2](https://choi-log-life.tistory.com/entry/iOS-Swift-initialization-2?category=1047722)

[initialization3](https://choi-log-life.tistory.com/entry/iOS-Swift-initialization-3?category=1047722)

[initialization4](https://choi-log-life.tistory.com/entry/iOS-Swift-initialization-4?category=1047722)



## ★ Structures and Enumerations Are Value Types

구조체와 enum은 value type(값 타입) 이다.

그럼 값 타입은 뭘까? 바로 어떤 값이 변수/상수/함수에 전달될 때 복사되는 것.



함수에 구조체 인스턴스인 A를 넘긴다고 치면 함수로 넘어갈 때 이 A의 값이 복사되어 넘어간다.(A')

함수 내에서 A'의 변수 값을 바꾼다고 한들 함수가 끝나는 순간 이 복사된 A'는 사라지고 외부에서 넘겨준 A는 변함 없이 이전 상태로 있는것.

![image](https://user-images.githubusercontent.com/22000470/185429705-d522f9d1-a744-4b8c-a7fe-ebe91a387fbd.png)

요 그림으로 본다면 hd가 있었고 cinema = hd로 cinema의 값을 할당해준 상태다.

코드로 봤을 땐 같은 곳을 참조하고 있을 것 같지만 struct는 값 타입이므로 cinema = hd라는 코드가 들어올 때 새로운 복사본 하나가 생겨나게 된다.

> array나 dictionary같은 컬렉션들은 최적화를 사용해 복사 성능 비용을 절감한다. 컬렉션은 복사본을 바로 만드는 대신 원본 인스턴스와 복사본이 저장되는 메모리를 공유한다. 그러다가 어떤 값이 수정되면 수정 직전에 그 요소가 복사된다. (계속 같은 곳을 참조하다가 수정할 때 복사된 다는 뜻이겠지?) 코드에서 볼때는 바로 복사가 이뤄진 것처럼 보여진다.

## ★ Classes Are Reference Types
클래스는 구조체나 enum과 다르게 값타입이 아니라 reference type(참조 타입)이다.

값 타입은 변수/상수/함수에 전달될 때 복사되지만 클래스는 **복사되지 않는다.**

복사되지 않고 그것 그대로를 사용하게 된다.

그림으로 보면 아래와 같다.

![image](https://user-images.githubusercontent.com/22000470/185429899-381c1ba0-65f6-4f9f-a900-e4f7ba470441.png)

VideoMode가 클래스일 때 tenEighty = alsoTenEight 코드를 만나게 되면 struct와 달리 같은 인스턴스를 가리키고 있는 것을 볼 수 있다.

그럼 둘 중 누군가 VideoMode 인스턴스의 값을 바꾸면 다른 한쪽도 영향을 받게 될 것이다.

구조체는 값이 바뀌지 않으니까 막 썼던 것 같은데 클래스도 구조체처럼 막 쓰다가는 어디 하나 구멍날 수도 있겠다..(Concurrency 같은거 섞이면 조심해야할듯)

## Identity Operators

- Identical to (===)
- Not Identical to (!==)

위에서 본 tenEighty와 alsoTenEigty를 ===로 비교하면 참이 나온다.
==와 달리 정확히 같은 클래스 인스턴스임을 비교하는 친구들.
==은 두 인스턴스 사이에 같은 값을 가졌는지 체크.

## Pointers
어떤 참조 유형의 인스턴스를 참조하는 스위프트 상수 또는 변수는 C의 포인터와 유사하지만 메모리에 있는 주소에 대한 직접 포인터가 아니며 참조를 만들고 있음을 나타내기 위해 *를 쓸 필요가 없다.

대신 이러한 참조는 Swift의 다른 상수 또는 변수처럼 정의된다. 표준 라이브러리는 포인터와 직접 상호 작용해야 하는 경우 사용할 수 있는 포인터 및 버퍼 유형을 제공한다.

> 최초작성: tistory / 20220629
