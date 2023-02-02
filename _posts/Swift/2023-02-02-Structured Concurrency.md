---
title: "[Async/Await] Structured Concurrency"
excerpt: "Async/Await 알아보기 02"
  
toc: true
toc_sticky: true

categories:
  - Async
  - Await
  - Swift
tags:
  - [iOS, async, await, swift]

permalink: swift/remind-gcd

---

### async-let

- As Is

```swift
let (data1, response) = try await URLSession.shared.data(from:)

let (data2, responese) = try await URLSession.shared.data(from:)
```

- To Be

```swift
async let (data1, response) = URLSession.shared.data(from:)

async let (data2, response) = URLSession.shared.data(from:)

let score = try? JSONDecoder().decode(CreditScore.self, from try await data1)

let score = try? JSONDecoder().decode(CreditScore.self, from try await data2)
```
> As Is는 데이터가 나올 때 까지 해당 코드에서 suspend 되지만 To Be 코드처럼 사용하면 decode 하는 부분에서 suspend. (URLSession 코드 두 개는 Concurrency 하게 동작함. data1의 결과를 기다리지 않고 data2를 받을 수 있음.)

### async-let loop

```swift
func someAsyncFunc(id: Int) async throws -> Double { … }

let ids = [1, 2, 3, 4, 5]

Task {
   for id in ids {
      let result = try await someAsyncFunc(id: id)
   }
}

```

> id가 여러 개 인 경우 for loop을 사용해 async 메서드를 수행할 수 있다. 이 경우 하나의 id에 대한 async func 작업이 완료될 때 까지 다음 id에 대한 작업은 suspend 된다.

### cancelling task

```swift
Task {
   for id in ids {
      do {
         try Task.checkCancellation()
         let result = try await someAsyncFunc(id: id)
      } catch {
         print(error)
      }
   }
}

```

`Task.checkCancellation()` : task가 cancel되는 경우 error를 throw

### Group Tasks

`withThrowingTaskGroup(of:returning:body:)`

실행하고 싶은 task를 TaskGroup에 넣음. group에서 작업한 결과 값의 타입은 of에 표기한다.

withTaskGroup에서 외부의 변수(self.xx)에 접근할 때는 groupTask에서 값이 return된 이후 반복문에서 group을 사용하는 부분에서 접근 가능하다.

- [참고사이트](https://velog.io/@wansook0316/Task-TaskGroup)

### WWDC
- 나중에 봐보기
   - [WWDC 2021 Explore structured concurrency in Swift](https://developer.apple.com/videos/play/wwdc2021/10134/)
