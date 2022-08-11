---
title: "Strings and Characters 01"
excerpt: "iOS 문서 훑어보기 - String, Character"
  
toc: true
toc_sticky: true

categories:
  - iOS-docs
tags:
  - [iOS-docs, swift]
  
permalink: /ios-docs/Strings-and-Characters

---
## String
- "hello, world"처럼 character들이 모여 이루는 문자열. String으로 선언된다. 
- NSString 클래스와 연결되어있다. (bridge) - import Foundation을 넣으면 String이더라도 NSString으로 캐스팅 없이 NSString 메서드도 사용 가능한 듯?
- String은 값 타입이다. (새 String을 만들 경우 해당 String 값은 함수 또는 메서드에 전달되거나 상수 또는 변수에 할당될 때 복사된다.) 그래서 내가 직접 그 변수의 값을 수정하지 않는 이상, 어디 메서드에 전달된다고 하더라도 변수에 할당된 값이 변하지 않는다.


## 선언

```swift
let str: String = String()
let emptyString: String = ""
```

빈 문자열로 선언할 때는 String 객체를 선언하거나 빈 문자열로 선언한다. (추후에 값이 바뀐다면 var로 선언)


```swift
let str: String = "hello, world!"
```

문자열로 초기화할 때는 큰따옴표 안에 문자열을 넣어 변수를 초기화. 만약 여러 줄을 할당하고 싶다면 아래와 같이 선언하면 된다.


```swift
let softWrappedQuotation = """
The White Rabbit put on his spectacles.  "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

여러 줄을 넣을 때 코드 상으로는 가독성을 위해 다음 줄에 넣었지만 출력할 때는 붙여서 나오게 하고 싶을 때는 \를 적어주면 된다.


## Special Characters in String Literals
specaial Charaters는 다음과 같이 쓴다. 한 번 출력해보자.

- The escaped special characters \0 (null character), \\ (backslash), \t (horizontal tab), \n (line feed), \r (carriage return), \" (double quotation mark) and \' (single quotation mark)

![image](https://user-images.githubusercontent.com/22000470/184153258-73905ef8-c772-4507-8611-4a73c5fa0dcc.png)

그리고 유니코드 스칼라도 사용할 수 있다. (플레이그라운드에서 sparkilngHeart라니.. 왠지 귀엽네)

```swift
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
let blackHeart = "\u{2665}"      // ♥,  Unicode scalar U+2665
let sparklingHeart = "\u{1F496}" // 💖, Unicode scalar U+1F496
```
![image](https://user-images.githubusercontent.com/22000470/184153326-3532800f-fdb8-4037-86b7-107d77b48f57.png)

## 문자열 더하기 (String Mutability)
- String끼리 더하는 건 숫자 더하듯이 +=으로 간단하게 더할 수 있다. 

<img src="https://user-images.githubusercontent.com/22000470/184153495-dcb32173-054e-486a-bcb4-4c2556035a76.png" width="300">

- 대신 string끼리만 더해야지 int를 더하면 당연히 컴파일 에러가 난다.

![image](https://user-images.githubusercontent.com/22000470/184153568-f1fb6384-9e7c-4e5d-9091-c75acac75a7a.png)


- character도 추가할 수 있다. (append 사용)

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome now equals "hello there"

let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome now equals "hello there!"
```

## Working with Characters
String의 각 문자들은 Character로 뽑아낼 수 있다. for-in 구문을 사용해서 뽑아보자.
<img src="https://user-images.githubusercontent.com/22000470/184153797-a1078316-87ca-463f-bfe1-7f774d58fac9.png" width="300">

며칠 전에 알고리즘 문제 풀 때 String을 character로 분해해야 하는 상황이 있었는데, 이 포스팅을 먼저 썼더라면 enumerated()를 쓰겠다고 난리 치다 헤매진 않았을 텐데... 그냥 for문을 돌리면 character로 하나씩 분리가 된다.

Characer의 선언은 아래와 같이 한다. 작은따옴표로 묶는 줄 알았는데 큰따옴표로 묶고 한 글자를 넣어주면 된다.

```swift
let exclamationMark: Character = "!"
```

변수를 Character로 선언해놓고 문자열을 넣으면 String 넣었다고 에러를 뿜어낸다.
![image](https://user-images.githubusercontent.com/22000470/184153990-df68e685-d312-4fd6-8037-ea1983877c21.png)

또한 Character 배열을 통해 String을 생성할 수도 있다. (character로도, 배열로도 String 초기화가 가능하다.)

```swift
let exclamationMark: Character = "!"
let newString = String(exclamationMark)

let characters: [Character] = ["h", "e", "l", "l", "o"]
let stringFromArray = String(characters)
```

문자열의 글자가 몇 글자인지 확인할 때는 count를 확인하면 된다.

```swift
var word = "cafe"
print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in cafe is 4"
```

## String Interpolation
한국어로 해석하면 문자열 보간이라는데... 보간이 대체 뭐지........ 보간은 몰라도 문서는 읽을 수 있으니 읽어보자

문자열 내부에서 int 같은 다른 타입들을 문자열로 출력할 수 있게 해 준다는 것 같다. 코드로 확인해보는 게 빠르다.


이런 식으로 \() 내부에 수식이나 값을 넣으면 문자열로 출력을 해준다. String이 생각보다 유연한 것 같다. 개발할 때도 많이 쓰이는 타입인데 interpolation 기능은 진짜 요긴하게 쓰고 있다.

![image](https://user-images.githubusercontent.com/22000470/184154128-bf8e510a-3669-41a6-87ce-7791e3584e60.png)


String 관련 메서드들은 다음 포스팅에서 계속..!

> [참고 링크](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html)
> 최초 작성: tistory / 20220223
