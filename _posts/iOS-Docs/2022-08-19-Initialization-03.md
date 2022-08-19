---
title: "Initialization 03"
excerpt: "iOS 문서 훑어보기 - Designated Init, Convenience Init"
  
toc: true
toc_sticky: true

categories:
  - iOS-docs
tags:
  - [iOS-docs, swift]
  
permalink: /ios-docs/Initialization-03

---
지난 포스팅에서 값 타입(Structure)의 initializer까지 공부했다.

이번 포스팅에서는 Class의 initializer에 대해 공부해 보자. (Class 부분이 너무 길어서 Init 정리 글은 총 네 편 정도 될 것 같다..)

## Class Inheritance and Initialization

모든 클래스들의 property들은 초기화 과정에서 **반드시** 초기 값이 할당되어야 한다. (상속 받은 클래스가 있는 경우는 상위 클래스들의 property도 모두 초기 값이 할당되어야 한다.)

swift에서는 모든 클래스의 init이 보장될 수 있도록 두 가지 초기화 메서드를 제공하는데, 그것이 바로 **'Designated Initializer'** 와  **'Convenience Initializer'** 이다.

### (1) Designated Initializers and Convenience Initializers
Designated Initializers (지정 초기화)
   - 클래스에 지정된 모든 property를 초기화 하고 상속받은 클래스가 있는 경우 슈퍼 클래스의 init을 호출한다.
   - 클래스는 최소 하나 이상의 Designated Initializer를 갖는다.

Convenience Initializer
   - 클래스에 지원되는 보조적인 initializer이다.
   - 의무로 꼭 생성되어야 하는 이니셜라이저는 아니다.
   - 필요한 경우 생성할 수 있다. (ex: 나이를 저장하는 클래스인데 연도를 받아서 초기화하는 경우)

두 개의 이니셜라이저는 다음과 같이 표기된다.

```swift
/* Designated Initializer */
init(paramaters) {
    // statements
}

/* Convenience Initializer */
convenience init(paramaters) {
    // statements
}
```

### (2) Initializer Delegation for Class Types
지난 포스팅에서 설명했던 Value Type에 대한 Delegation에 이어 이번에는 Class Type에 대한 Delegation을 알아본다.

Swift에서는 이니셜라이저 간의 Delegation에 대해 세 가지 규칙을 정해주었다.

1. Designated Initializer에서 직접 슈퍼클래스의 Designated Initializer를 호출해야 한다.
2. Convenience Initializer는 클래스의 다른 이니셜라이저를 호출해야 한다.
3. Convenience Initializer는 2번 룰을 지켜서 다른 이니셜라이저를 호출하더라도 결국엔 Designated Initializer를 호출해야만 한다.

세 가지 규칙을 그림으로 그려보면 아래와 같다.

![image](https://user-images.githubusercontent.com/22000470/185541945-1ac059fb-f6b0-4733-aa1f-ddc0866c41a7.png)

### (3) Two-Phase Initialization (2단계 초기화)
초기화는 두 단계로 나뉘어 진다.

1. **1단계** 에서는 클래스 내부에 있는 stored property에 대한 초기 값이 할당
상속을 받았다면 모든 슈퍼클래스의 stored property가 초기화되어야 1단계가 끝난다.
2. **2단계** 에서는 클래스가 인스턴스로 사용되기 전에 추가로 사용자 정의

2단계 초기화는 property가 초기화 되기 전에 액세스 되는 것을 방지하고, 다른 이니셜라이저에 의해 다른 값으로 설정되는 일을 방지한다.

Swift의 컴파일러는 2단계 초기화가 에러 없이 완료되는지 확인하기 위해 4가지 **safety check** 를 진행한다.

1. Designated Initializer에서 해당 클래스의 stored property를 모두 초기화 해야 슈퍼클래스의 Designated Initializer를 호출할 수 있다.
2. 자식 클래스에서 상속받은 property에 값을 할당하려면 이전에 슈퍼클래스의 Designated Initializer 호출 이후에 할당해야 한다.
   - 자식 클래스에서 먼저 할당하고 슈퍼클래스의 Designated Initializer가 호출되면 자식 클래스에서 할당한 값이 사라진다.
3. Convenience Initializer에서 해당 클래스의 stored property에 값을 할당하려면 Designated Initializer를 호출해야 한다.
   - 2번과 마찬가지로 이후에 호출되는 Designated Initializer의 값으로 저장된다.
4. 인스턴스 메서드나 프로퍼티는 초기화 1단계가 끝나기 전까지 호출할 수 없다.


예제를 한 번 만들어본다..!

```swift
class Family {
    var lastName: String
    var address: String
    
    init() {
        self.lastName = "Choi"
        self.address = "Seoul"
    }
    
    init(lastName: String, address: String) {
        self.lastName = lastName
        self.address = address
    }
}
```

Family라는 클래스를 하나 만들어두었다. convenience이 붙지 않았으니 두 init 메서드 모두 designated initializer라고 볼 수 있다.

그러면 이제 Family를 상속 받은 Me 클래스를 만들어보자. 아래와 같이 만들어보았다.

```swift
class Me: Family {
    let firstName: String
    
    init(firstName: String) {
        self.firstName = firstName
        super.init()
    }
}
```

#### Safety Check 1
여기서 super.init()을 호출하기 전에 firstName을 미리 초기화하고 있다. 만약 super.init()을 firstName 초기화 전에 먼저 호출하려고 한다면 다음과 같은 에러가 발생한다.

> Property 'self.firstName' not initialized at super.init call

이는 위에서 언급한 safety check 1번을 만족하지 않아서 발생한다. 슈퍼클래스의 이니셜라이저를 호출하기 위해서는 먼저 자신의 프로퍼티부터 초기화해야 한다.

![image](https://user-images.githubusercontent.com/22000470/185542217-7730dd60-bbae-4a41-8e0e-e4cb6fd060a0.png)

다시 원래대로 돌아와서 Me 인스턴스를 만들어 print로 결과를 출력해보자.

![image](https://user-images.githubusercontent.com/22000470/185542241-81313e98-d12a-41b7-86eb-41e31c00418d.png)

#### Safety Check 2

만약 내가 서울에서 살지 않고 가족과 따로 떨어져 살게 되었다고 가정해보자. 그러면 슈퍼클래스의 address 값이 아닌 새로 살게 될 도시 이름이 출력되어야 한다. super.init() 전에 address를 변경하려고 한다면 다음과 같은 에러를 만나게 된다.

> 'self' used in property access 'address' before 'super.init' call

![image](https://user-images.githubusercontent.com/22000470/185542285-2a8dc458-fb8b-450d-a9dd-e542c6c92508.png)

safety check 2번을 만족하지 못했다. 슈퍼 클래스의 Designated Initializer가 호출된 이후에 속성을 변경해야 하는데 그전에 값을 변경하려고 해서 발생하는 에러다. 아래와 같이 super.init() 이후에 호출하면 정상적으로 출력이 된다.

![image](https://user-images.githubusercontent.com/22000470/185542337-d627937b-c897-4267-bf7a-de6fc1e1ed44.png)

#### Safety Check 3

Me 클래스의 프로퍼티에 age를 추가하고 convenience init으로 birthYear를 받아 초기화하도록 만들었다. 이때 self.init()을 호출하기 전에 self.age에 접근하려고 하면 다음과 같은 에러가 출력된다.

> 'self' used before 'self.init' call or assignment to 'self'

![image](https://user-images.githubusercontent.com/22000470/185542377-6ce63a6e-6415-4365-8f2c-0c99564c8f12.png)

Me 클래스의 Designated init이 호출되기 전에 클래스의 프로퍼티에 접근하려고 해서 에러가 났다. self.init 이후에 호출하면 정상적으로 실행이 된다.

![image](https://user-images.githubusercontent.com/22000470/185542407-6ad251b7-f49d-4623-b403-cce09712f44c.png)

init 이후에 100이라는 숫자를 넣어서 변경하니까 아래 age가 100으로 출력이 되는 것을 확인할 수 있다.

#### Safety Check 4

Me 클래스의 프로퍼티를 출력하는 메서드를 만들었다. 해당 메서드를 super.init() 전에 호출하면 어떻게 될까? 아마 다음과 같은 에러를 만나게 될 것이다.

> 'self' used in method call 'printInformation' before 'super.init' call

![image](https://user-images.githubusercontent.com/22000470/185542439-4cdcd2b1-7f6c-498c-aa64-a7fe016c7bce.png)

safety check 4번을 충족하지 못했다. super.init()까지 호출되어서 모든 클래스의 프로퍼티가 초기화되어야지만 1단계가 완료되기 때문에 인스턴스 메서드인 printInformation은 슈퍼클래스 이니셜라이저 호출 이후에 사용할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185542460-2b8f7ac2-d347-4b19-95e5-555d97e8f0b3.png)

슈퍼클래스 이니셜라이저 호출 이후에 printInformation을 호출하면 정상적으로 출력을 할 수 있는 것을 확인할 수 있다.



### (4) Initializer Inheritance and Overriding
슈퍼 클래스의 designated init을 오버라이드 할 수 있다. 

```swift
class Me: Family {
    let firstName: String
    var age: Int
    
    override init() {
        self.firstName = ""
        self.age = 0
        super.init()
        self.address = "Busan"
    }
    
    init(firstName: String, age: Int) {
        self.firstName = firstName
        self.age = age
        super.init()
        self.address = "Jeju"
    }
    
    convenience init(birthYear: Int) {
        self.init(firstName: "Emily", age: 2021 - birthYear)
    }
    
    func printInformation() {
        print("My name is \(self.firstName) \(self.lastName) and \(self.age) years old. Address is \(self.address).")
    }
}

let me = Me()
me.printInformation()
```

Family에서 만들었던 init() 메서드를 Me 클래스에서 재정의하도록 생성했다. 이때 init 앞에는 override가 붙게 된다. 여기서도 다른 경우와 마찬가지로 슈퍼클래스의 이니셜라이저를 호출하기 전에 가지고 있는 프로퍼티를 초기화 해야한다. 출력하면 아래와 같은 결과를 확인할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185542505-056a6578-b275-40e3-9be2-f44a97614fec.png)

만약 override 해야 하는 슈퍼클래스의 생성자가 argument를 받지 않는다면 하위 클래스에서는 override 이니셜라이저 내부에서 하위 클래스의 프로퍼티만 초기화해주면 super.init() 호출을 생략할 수 있다. 예시로 들었던 Family 클래스에서 init(lastName:address:)을 주석처리하고 나면 아래 이미지처럼 init(firstName:age:)에서 super.init()을 생략해도 동작하는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185542537-0984b06b-1644-441b-9e7c-3d240fe9f548.png)

## Automatic Initializer Inheritance

하위 클래스는 기본적으로 슈퍼 클래스 이니셜라이저를 상속하지 않는다. 대신 특정 조건이 충족되면 슈퍼클래스 이니셜라이저는 자동으로 상속된다.

1. 하위 클래스가 Designated Initializer를 정의하지 않는 경우, 슈퍼클래스의 모든 Designated Initializer를 자동으로 상속한다.
   - 하위 클래스의 모든 프로퍼티에 기본값이 지정된 경우는 designated initializer를 지정하지 않아도.. 되려나
2. 슈퍼 클래스의 Designated Initializer를 모두 구현하거나 override 하면 슈퍼클래스의 convenience initializer는 자동으로 상속된다.

### (1) Designated and Convenience Initializers in Action
2번에 대한 예제가 swift docs에 있어서 가져와 보았다.

![image](https://user-images.githubusercontent.com/22000470/185542584-d6996783-3ff1-42c8-8dc4-c584e5abeda7.png)

Food를 상속하는 recipeIngredient 클래스에서 슈퍼클래스의 init(name:)을 convenience init으로 오버라이드 해서 구현했다. RecipeIngredient는 Food의 Designated Initializer를 모두 구현했으므로 Food의 convenience init도 호출할 수 있게 된다..!

![image](https://user-images.githubusercontent.com/22000470/185542606-e8359ddd-cf4e-49f4-947c-ec693cbfdc69.png)

ShoppingListItem은 기본값이 제공되고 init 메서드가 없기 때문에 1번 조건을 만족하므로 슈퍼클래스에 있는 모든 Designated Initializer와 Convenience Initializer를 자동으로 상속받을 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185542632-07f257ff-7615-4e8e-95bd-d8e0ce75bbeb.png)

위 이미지와 같이 breakfastList에서 ShoppingListItem에는 없는 init 메서드들로 값을 초기화할 수 있는 것을 확인할 수 있다.

----

Class에 대한 이니셜라이저가 공부하기 꽤 까다로웠는데 이번 기회에 이렇게 정리할 수 있어서 참.. 다행이구나 싶다. 복잡하긴 하지만 좋은 기능들도 있고, 내가 평소에 개발하면서 사용할만한 개념들도 많았다. init 메서드를 감으로 사용하고 있었는데 이제는 정확히 알고 쓸 수 있으니 코드에 직접 적용해볼 일만 남았다 :D 다음 포스팅에서는 Initializer의 마지막 부분인 Failable Initializers와 Required Initializers에 대해 정리할 예정이다.

참고 링크: https://docs.swift.org/swift-book/LanguageGuide/Initialization.html﻿

> 최초작성: tistory / 20210917
