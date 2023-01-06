---
title: "Initialization 04"
excerpt: "iOS 문서 훑어보기 - Failable Init"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift]
  
permalink: /swift/Initialization-04

---
드디어 initialization 문서의 마지막을 정리하는 포스팅이다. 이번 포스팅에서는 Faliable Initializer, Required Initializer를 정리한다!

## Failable Initializers
클래스나 구조체, enum은 유효하지 않은 파라미터가 들어오는 경우 초기화에 실패할 수도 있다. 

실패할만한 조건이 들어올 때 초기화를 하지 않도록 하기 위해서는 `init?` 키워드를 사용해서 초기화 메서드를 작성한다.

대신 같은 파라메터와 이름을 가진 failable / nonfailable 초기화 메서드는 함께할 수 없다고 한다. (아래와 같은 에러가 난다)

![image](https://user-images.githubusercontent.com/22000470/185542959-af950ad2-4df4-40aa-9cb5-068dd0148fd1.png)

위에 작성한 failable init 메서드를 보면 알 수 있듯이 초기화에 필요한 조건을 만족하지 않는 경우는 nil을 리턴 시키면 된다. 이니셜라이저는 초기화가 정확하게 초기화되도록 하는 역할이기 때문에 성공할 때는 값을 반환하지 않는다. (실패일 때만 nil을 반환하게 된다.)

위에서 동일한 이름을 가진 init을 제거한 코드를 통해 조건에 맞지 않는 경우 nil이 반환되는지 확인해보자.

```swift
class StudentInfo {
    var name: String
    var age: Int = 20
    var address: String?

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    init?(name: String, birthYear: Int) {
        guard birthYear >= 0 else { return nil }
        self.name = name
        self.age = 2021 - birthYear
    }
}

```

![image](https://user-images.githubusercontent.com/22000470/185543030-cc6f0136-1fd6-4744-acd5-57246524ac83.png)

음수인 경우는 nil을 리턴하도록 failable init을 작성했기 때문에 -1을 넣는 경우에는 nil이 반환되는 것을 확인할 수 있다.

구조체도 동일하게 Failable Initializer를 작성할 수 있다.

![image](https://user-images.githubusercontent.com/22000470/185543061-8e2bbe2a-9498-484d-9bbb-bfec89034edf.png)

init 메서드를 통해 초기화 된 클래스 또는 구조체는 옵셔널일 수 없다. 만약에 init?을 통해 정상적으로 초기화가 된 클래스나 구조체는 어떨까? 


![image](https://user-images.githubusercontent.com/22000470/185543104-9177c911-c382-4199-a2ca-711d61eeddda.png)

failable initializer를 통해 정상적으로 생성된 구조체이지만 옵셔널로 생성된다. 그래서 이 failable initializer는 아래 넣어둔 코드처럼 초기화가 제대로 되었는지를 확인할 때 사용할 수 있다고 한다.

![image](https://user-images.githubusercontent.com/22000470/185543139-770435c7-3672-4e25-bdfb-066bd0f26702.png)

enum의 failable initializer도 비슷하다. init 메서드를 사용할 수 있는데, 초기화 조건이 맞지 않는 경우 nil을 반환하게 된다. enum이라서 클래스, 구조체와 다르게 self =으로 시작한다는 점이 조금 다르다.

![image](https://user-images.githubusercontent.com/22000470/185543165-d35f04a4-79b5-4ac2-a225-8d2d30cf7650.png)

하지만 위에 적어둔 예는 각 작물의 firstCharacter를 rawValue로 지정한 뒤에 사용하면 init?을 직접 구현하지 않아도 된다.

![image](https://user-images.githubusercontent.com/22000470/185543190-089955a8-5848-4bfc-97cd-cd8780a8c64b.png)

rawValue로 초기화 하는 것 자체가 failable init으로 구성되어 있는 것 같다. 반환형이 Crops?로 나온다.

![image](https://user-images.githubusercontent.com/22000470/185543211-bf6909d7-f0e8-4d70-b167-60592a5ad7bc.png)

### (1) Propagation of Initialization Failure
- 클래스나 구조체, enum의 failable init에서 같은 클래스나 구조체, enum 내부의 다른 failable init에 위임할 수 있다. (가장 마지막 문장을 보니 위임한다는 뜻이 failable init 내부에서 다른 failable init을 호출할 수 있다는 말인 듯하다.)

- 하위 클래스의 failable init에서는 슈퍼 클래스의 failable init를 호출할 수 있다.

두 경우 모두 failable init에서 다른 초기화 메서드를 호출했을 때 거기서 초기화에 실패한다면 초기화 프로세스가 더 이상 실행되지 않는다.

### (2) Overriding a Failable Initializer
- 슈퍼 클래스의 Failable initializer는 하위 클래스에서 오버라이딩이 가능하다. (failable로 오버라이딩 해도 되지만 nonfailable로 오버라이딩 할 수도 있다.)

- 하지만 nonfailable인 슈퍼클래스의 초기화 메서드를 하위 클래스에서 failable로 오버라이딩 할 수는 없다.



그런데 뭔가 이상하다. 슈퍼 클래스의 초기화 메서드가 failable인데 하위 클래스는 nonfailable이라니. swift 문서에서 예시로 들어준 코드를 보자.


```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

Document의 failable init 메서드를 AutomaticallyNamedDocument 클래스에서는 nonfailable로 오버라이드 했다. 슈퍼클래스에서는 name이 비어있는 경우 초기화 실패로 간주하고 nil이 반환되는데 하위 클래스에서는 name이 비어있는 경우 [Untitled]로 초기화하도록 한다. 하위 클래스에서는 실패로 간주하지 않고 초기화를 하겠다는 의지로 보인다. 만약에 init(name:)에서 super의 failable initializer를 호출하면 어떻게 될까?

![image](https://user-images.githubusercontent.com/22000470/185543297-b1befed9-624b-455f-b3b6-53ba71e7da08.png)

init은 무조건 초기화가 성공해야 하는데, super의 failable initializer를 사용하려고 하니 당연히 에러가 난다.(나는 초기화를 무조건 성공시켜야 하는데 failable init을 호출하면 nil로 초기화가 실패할 수도 있으니까!) 이때 컴파일러는 두 가지 선택권을 준다. 하나는 하위 클래스의 init(name)을 failable로 바꾸는 것, 그리고 남은 하나는 다음 절에서 확인할 수 있는 강제 언래핑 초기화 메서드를 사용하는 법이다.

### (3) The init! Failable Initializer
위에서 에러가 난 부분을 `super.init(name: name)!` 로 변경해서 슈퍼 클래스의 init 결과를 강제로 언래핑하면 사용할 수는 있다. 

![image](https://user-images.githubusercontent.com/22000470/185543345-a2f9c7e9-24bf-4726-8408-ffca5829cad9.png)

이 경우는 name이 비어있지 않은 경우이고, 비어있지 않기 때문에 슈퍼클래스도 nil을 리턴하지 않고 초기화가 정상적으로 진행된다. 하지만 name에 ""를 넣으면 어떻게 될까?

> error: Execution was interrupted, reason: EXC_BREAKPOINT (code=1, subcode=0x18ec081b8).
The process has been left at the point where it was interrupted, use "thread return -x" to return to the state before expression evaluation.

![image](https://user-images.githubusercontent.com/22000470/185543377-557dc2ed-1e22-4acf-a4a1-b1a00cb2aad7.png)

wow.. 슈퍼 클래스에서 nil이 리턴되기 때문에 바로 에러를 뿜어낸다. 역시 강제 언래핑은 지양해야겠다. 아니면 확실히 nil이 반환되지 않을 거라는 보장이 있는 경우(있을까..?)에만 사용해야겠다.


## Required Initializers
클래스의 모든 하위 클래스에서 내 이니셜라이저를 꼭 구현해야 된다는 것을 알려주기 위해서는 init 앞에 required를 붙인다. 하위 클래스에서 슈퍼 클래스의 required init을 오버라이드 할 때는 override를 붙이지 않고 required init으로 구현한다. (그래야 하위 클래스의 하위 클래스에서도 required init을 구현할 수 있기 때문이 아닐까..?)



## Setting a Default Property Value with a Closure or Function

```swift
struct Chessboard {
    let boardColors: [Bool] = {
        var temporaryBoard: [Bool] = []
        var isBlack = false
        for i in 1...8 {
            for j in 1...8 {
                temporaryBoard.append(isBlack)
                isBlack = !isBlack
            }
            isBlack = !isBlack
        }
        return temporaryBoard
    }()
    func squareIsBlackAt(row: Int, column: Int) -> Bool {
        return boardColors[(row * 8) + column]
    }
}

let board = Chessboard()
print(board.squareIsBlackAt(row: 0, column: 1))
// Prints "true"
print(board.squareIsBlackAt(row: 7, column: 7))
// Prints "false"
```

만약 초기화할 때 기본 값을 closure로 초기화 한다면 어떨까? 초기화 할 때마다 또는 접근할 때마다 계속 사용자가 원하는 값으로 세팅을 할 수 있을 것이다. (대신 클로저 내부에서 프로퍼티가 초기화되는 동안에는 아직 인스턴스의 나머지 프로퍼티에 값이 할당되지 않은 상태이므로, 인스턴스 메서드나 프로퍼티에 접근할 수는 없다.)

----

드디어 길고 길었던 init 문서의 정리가 끝났다!! 앞으로 헷갈리는 부분이 있거나 기억이 안 나면 틈나는 대로 와서 확인하고 공부할 수 있겠다. Deinit까지 공부해야 init에 대한 공부를 다 했다고 할 것 같아서 다음 포스팅은 Deinit으로..

참고 링크: https://docs.swift.org/swift-book/LanguageGuide/Initialization.html

> 최초작성: tistory / 20210924
