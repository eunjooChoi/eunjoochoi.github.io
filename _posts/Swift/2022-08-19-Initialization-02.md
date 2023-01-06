---
title: "Initialization 02"
excerpt: "iOS 문서 훑어보기 - Initialization 기본 생성자"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift]
  
permalink: /swift/Initialization-02

---
지난 포스팅에 이어서 Swift 초기화에 대해 계속 정리-ing... 링크는 [여기](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)!!

지난 글에서는 Initialization이 어떤 것인지와 기본적인 형태, 사용자가 직접 설정할 수 있는 Custom Initializer에 대해 공부했다.

## Default Initializers

swift에서는 init 메서드를 따로 만들지 않아도 기본적으로 제공하는 Default Initializer(기본 생성자)가 있다. init 메서드를 만들지 않고 인스턴스를 생성해주면 각 프로퍼티의 값은 프로퍼티에 초기화시켜둔 기본 값으로 정해진다. 기본 생성자를 사용하기 위한 조건으로는 두 가지가 있다.

1. 모든 property가 default value를 가져야 한다
2. 부모 클래스가 없어야 한다. (상속을 받지 않은 클래스, a base class with no superclass)

이전 포스팅에서 사용하던 StudentInfo의 init 메서드를 모두 주석처리하고 기본 생성자로 인스턴스를 생성한 뒤 값을 확인해보자.

![image](https://user-images.githubusercontent.com/22000470/185541257-78687617-1b12-4fa0-97ea-18c173b6425f.png)

name과 age는 클래스 내부에서 설정한 기본값으로, 옵셔널로 지정된 address는 nil이 반환되는 것을 확인할 수 있다.

기본 생성자를 사용하기 위해서는 각 프로퍼티에 적절한 값을 초기값으로 설정해주어야 한다. 그렇지 않으면 다음과 같은 에러가 발생한다.

![image](https://user-images.githubusercontent.com/22000470/185541286-ca4a842a-5f08-4c25-bc48-31cdcb4e02e5.png)

기본 생성자를 사용하면서 생성 후 var 프로퍼티의 값을 바꾸는 방법보다는 그냥 let으로 선언하고 init을 명시해주는 게 더 좋은 것 같은데... 무조건 기본 값으로 초기화된 이후에 사용되는 클래스/구조체인 경우에는 쓸모가 있을 것 같다.

### (1) Memberwise Initializers for Structure Types

구조체에는 Memberwise Initializer라는 친구가 존재한다. Custom initializer가 없는 경우 사용할 수 있다. Default Initializer와는 조금 다른 점이 있는데, Memberwise Initializer는 구조체 프로퍼티에 지정된 기본 값이 없더라도 사용이 가능하다는 것이다. 아까 위에서 클래스는 name의 값을 지정하지 않고 Default Initializer를 사용하려고 했을 때 에러가 났는데, 구조체는 어떻게 될지 확인해보자.

![image](https://user-images.githubusercontent.com/22000470/185541330-5c9181c1-68f3-4a16-94a6-ca95f80cd4b6.png)

name, age, address의 기본값을 정해주지 않았지만 Memberwise Initializer를 사용해서 초기화 할 수 있다!! 정말 좋은 기능이다..

만약 저 중에 age에 기본값을 지정하면 아래와 같이 초기화 하는 것도 가능하다.

![image](https://user-images.githubusercontent.com/22000470/185541373-2847e06a-59f9-4430-9eea-2351d42443f2.png)

age에는 기본값이 지정되어있고, address는 옵셔널이기 때문에 name만 초기화하는 Memberwise Initializer를 사용할 수 있다.


## Initializer Delegation for Value Types (Structures, Enumerations)

Initializer Delegation은 init 메서드 안에서 다른 종류의 init 메서드를 호출하는 걸 말한다. Initializer는 인스턴스 초기화의 일부를 수행하기 위해 다른 이니셜라이저를 호출할 수 있다. Initialize Delegation(이니셜 라이저 위임)은 여러 Initializer 사이에 코드가 중복되는 것을 방지한다.

Value types(구조체, enum)과 Class의 Initialize Delegation은 Initializer Delegation을 사용하는 규칙이나 어떤 형태의 Initializer Delegation이 사용될 수 있는지가 조금 다르다. Value Types는 상속이 허용되지 않기 때문에 조금 더 간단하다. 위임을 자신의 다른 init 메서드에게만 할 수 있기 때문이다. Class의 경우는 상속이 가능하기 때문에 Initializer Delegation을 통해 초기화되는 동안 상속되는 모든 프로퍼티에 적절한 값이 할당되어야만 한다. 그래서 조금 더 복잡하다. (Class Type의 내용은 너무 길어서 다음 포스팅에 정리할 예정이다.)

Value Type에서의 Initializer Delegation은 self.init을 다른 custom init 메서드에서 호출하면 된다. 이때 Value Type에서는 custom init을 생성하는 경우 default init이나 memberwise init을 사용할 수 없다는 걸 알고 넘어가자. 보이지도 않는 init 메서드로 delegation 되는 일을 방지하기 위해 쳐둔 방어막 같은 것이라고 생각하고 넘어간다.


아래는 the swift programming language 페이지에 나온 예시를 가져왔다.

```swift
struct Size {
    var width = 0.0, height = 0.0
}

struct Point {
    var x = 0.0, y = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
    
    init() {}
    
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
    
    func printDescription() {
        print("origin is \(self.origin), size if \(self.size) ")
    }
}
```

Rect라는 직사각형의 정보를 가지는 구조체가 있고, 구조체 내부에는 origin과 size를 저장한다. (printDescription은 결과 출력용 함수로 그냥 추가해두었다.)

origin과 size도 각각의 구조체로, 기본 값이 지정되어 있기 때문에 기본 생성자로 초기화를 해둔 상태이다. 
Rect는 세 개의 init 메서드가 존재한다.

- 아무런 값도 받지 않는 기본 생성자(처럼 생긴 custom 생성자)
   - Point와 Size에 지정된 기본 값으로 초기화 된다. 
- origin과 size를 직접 받는 생성자
   - Memberwise Initializer와 동일한 동작을 한다. 출력해보면 아래와 같다.
- center와 size를 받아서 center를 통해 origin을 구한 뒤 초기화하는 생성자
   - center를 받아서 계산을 거친 뒤에 Rect의 init(origin: size:) 메서드를 호출해준다. 이것이 바로 **Initializer Delegation** 이다!

결과 값은 아래와 같이 출력된다.

![image](https://user-images.githubusercontent.com/22000470/185541486-75d269ed-7281-48ec-8a39-9912b8354fde.png)

아까 위에서 struct 내부에서 custom init을 구현한 경우 default init / memberwise init을 사용할 수 없다고 했다. 그렇기 때문에 위에 예제도 직접 default init / memberwise init와 동일한 init 메서드를 생성해서 사용했다. 하지만 custom initializer를 사용하면서 default init / memberwise init을 사용하고 싶은 경우에는 custom init 메서드를 extension에서 구현하고 custom init 내부에서 default init / memberwise init를 호출하는 방식으로도 사용할 수 있다. 참고 링크는 [여기](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)있고 위의 코드는 아래와 같이 변경할 수 있다.)

```swift
struct Rect {
    var origin = Point()
    var size = Size()
    
    func printDescription() {
        print("origin is \(self.origin), size if \(self.size) ")
    }
}

extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

확실히 아까 코드보다 간단해져서 보기가 깔끔해졌다. 결과도 아래와 같이 잘 나온다.

![image](https://user-images.githubusercontent.com/22000470/185541542-0f26030d-f9f7-4d4e-b89c-1f9049d73a56.png)

> 최초작성: tistory / 20210917
