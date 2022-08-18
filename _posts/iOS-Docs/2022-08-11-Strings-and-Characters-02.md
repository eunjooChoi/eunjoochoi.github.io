---
title: "Strings and Characters 02"
excerpt: "iOS 문서 훑어보기 - String, Character"
  
toc: true
toc_sticky: true

categories:
  - iOS-docs
tags:
  - [iOS-docs, swift]
  
permalink: /ios-docs/Strings-and-Characters-02

---
지난 시간에 이어 String, Character 알아보기 두 번째.

## Accessing and Modifying a String
String 내장 method나 property, subscript 구문을 사용해서 String에 접근하거나 수정할 수 있다.

### String Indices

String의 n번째 문자열을 가져와야 하는 알고리즘 문제가 종종 있다. 그래서 문자열[0]로 찾으려고 하면 subscript에 String.Index를 넣어야지 Int를 넣으면 안 된다는 에러가 난다.
![image](https://user-images.githubusercontent.com/22000470/184156628-7031b420-59aa-4cb4-8552-556c6ae08bea.png)


String에서 n번째 문자열을 가저오려면 String.Index가 필요하다. String의 각 위치(index)에 접근하면 Character를 얻을 수 있다.
- startIndex
   - 처음 index의 Character를 반환
- endIndex
   - 마지막 index의 Character를 반환
- index(before:)
   - 특정 index 이전 Character를 반환
- index(after:)
   - 특정 Index 다음 Character를 반환
- index(_:offsetBy:)
   - 주어진 문자열에서 특정 위치로부터 offset만큼 떨어진 위치에 있는 Character를 반환

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

각 index별로 for-in을 돌릴수도 있다. String.indices를 사용한다.
<img src="https://user-images.githubusercontent.com/22000470/184156993-9f90fec5-3794-4880-b792-a5a5dda061bc.png" width="300">

###Inserting and Removing

String 사이에 Character나 String을 끼워 넣을 수도 있다.

```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"

welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"
```

insert(_:at:)으로는 특정 위치에 Character를, insert(contentsOf:at:)으로는 특정 위치에 String을 삽입할 수 있다.
제거는 remove(at:)이나 removeSubrange(_:)로 한다.

```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

특정 범위를 지울 때 removeSubrange를 사용하는데 rnage를 지정하는 작업이 필요하다.

## Substrings
String은 [] 사이에 범위를 넣어 substring을 구할 수 있다. (오 이건 처음 알았다)

```swift
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"

// Convert the result to a String for long-term storage.
let newString = String(beginning)
```

위 코드를 보면 ,가 처음 나오는 index를 찾아서 greeting[.. <index]로 범위만큼 Substring을 구한다.
이때 beginning은 String이 아니라 String.SubSequence라는 타입이다.

![image](https://user-images.githubusercontent.com/22000470/184157405-85d96edb-4114-4a4a-8a01-d3e0d90d9ae7.png)
> 출처: docs.swift.org

docs.swift.org에서 가져온 이미지를 보면 beginning은 기존 greeting의 메모리 중 Hello의 위치까지만 공유하는 substring이어서 새롭게 메모리를 참조하는 변수로 만들려면 String(beginning)으로 선언해주어야 한다.

## Compare Strings
### String and Character Equality

String이나 Character 둘 다 값 비교를 할 때 간단하게 == 으로 비교한다. 아주 간단.
![image](https://user-images.githubusercontent.com/22000470/184157507-11394e00-783b-49a0-85b3-e7312bb70656.png)

hasPrefix와 hasSuffix로 특정 문자열이 있는지도 확인 가능하다.

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]

var act1SceneCount = 0
for scene in romeoAndJuliet {
    if scene.hasPrefix("Act 1 ") {
        act1SceneCount += 1
    }
}
print("There are \(act1SceneCount) scenes in Act 1")
// Prints "There are 5 scenes in Act 1"

var mansionCount = 0
var cellCount = 0
for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}
print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")
// Prints "6 mansion scenes; 2 cell scenes"
```


[참고 링크](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html)

> 최초작성: tistory / 20220224
