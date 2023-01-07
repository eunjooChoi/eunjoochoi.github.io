---
title: "Error Handling"
excerpt: "iOS 문서 훑어보기"
  
toc: true
toc_sticky: true

categories:
  - Swift
tags:
  - [swift-docs, swift]
  
permalink: /swift/error-handling

---
Swift 문서는 [여기](https://docs.swift.org/swift-book/LanguageGuide/ErrorHandling.html)!

> Swift Error handling은 다른 언어의 예외 처리와 비슷하다. try, catch, throw 키워드를 사용하고 Objective-C를 포함한 많은 언어에서의 Error Handling과는 다르게 계산 비용이 많이 들 수 있는 프로세스인 unwinding the call stack을 포함하지 않는다. throw statement는 return statement의 성능과 유사하다.

## Error 정의, error를 throw 하기

- Error 정의
   - enum에 Error 채택
   - associated value 사용가능

````swift
enum VendingMachineError: Error {
    case invalidSelection
    case insufficientFunds(coinsNeeded: Int)
    case outOfStock
}
````

- 위 Error로 throw 하면

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
// associated value로 추가적인 정보를 넘겨줄 수 있음.
```

## Handling Errors

- 메서드에서 Error를 던져주기
   - 메서드의 return arrow(->) 앞에 throws를 붙인다.

````swift
func canThrowErrors() throws -> String
````

### 예시

```swift
struct Item {
    var price: Int
    var count: Int
}

class VendingMachine {
    var inventory = [
        "Candy Bar": Item(price: 12, count: 7),
        "Chips": Item(price: 10, count: 4),
        "Pretzels": Item(price: 7, count: 11)
    ]
    var coinsDeposited = 0

    func vend(itemNamed name: String) throws {
       // 각 guard 문에서 error를 던진다.
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }

        guard item.count > 0 else {
            throw VendingMachineError.outOfStock
        }

        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }

        coinsDeposited -= item.price

        var newItem = item
        newItem.count -= 1
        inventory[name] = newItem

        print("Dispensing \(name)")
    }
}
```


### 에러 받기

1. do-catch

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// Prints "Insufficient funds. Please insert an additional 2 coins."
```
- do 내부에 try로 호출해야하는 메서드 가져옴. 만약 리턴값이 있는 경우에는 try 메서드 아래에 다음 작업 코드 추가
- Error는 catch로 들어옴. 각 에러별로 catch를 작성해도 되고, catch로 하면 모든 에러가 다 거기로 들어옴
- 오류 처리 안돼서 top-level scope까지 전달되면 runtime error 발생

2. try?

```swift
func fetchData() -> Data? {
    if let data = try? fetchDataFromDisk() { return data }
    if let data = try? fetchDataFromServer() { return data }
    return nil
}
```

- 모든 오류를 동일한 방식으로 처리하려는 경우에 에러 처리 코드를 간단하게 작성.
   - 에러가 발생하면 nil 처리된다.

3. try!

- 오류가 발생하지 않을 것이라는 확신이 있는 경우 사용
- runtime assertion (try!를 사용한 부분에서 오류 발생 시 runtime error 남)

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

## Specifying Cleanup Actions

- error handling으로 또는 기타 다른 상황에 의해 메서드가 종료되는 경우 defer 구문에서 정리가 필요한 작업들을 수행할 수 있다.

>  Deferred actions are executed in the reverse of the order that they’re written in your source code. That is, the code in the first defer statement executes last, the code in the second defer statement executes second to last, and so on. The last defer statement in source code order executes first.

### 예시

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

- try 코드 동작하다가 갑자기 코드가 종료되어도 defer 코드가 있기 때문에 file을 닫을 수 있다.
