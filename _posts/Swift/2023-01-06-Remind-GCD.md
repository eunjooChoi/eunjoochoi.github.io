---
title: "[Async/Await] GCD 대충 정리하기"
excerpt: "Async/Await 알아보기 01"
  
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

Async/Await 그리고 Actor를 알아보기 전에 간단하게 돌아보는 GCD(Grand Central Dispatch)인 DispatchQueue

### Concurrency
- Multiple things at the same time

### If... 

Main Thread에서 UIEvent와 이미지 다운로드를 같이 작업한다면? --> Main Thread는 Serial Queue를 사용하기 때문에 이미지 다운로드를 하는 순간 UI 동작 처리가 Queue 뒤에 쌓이면서 UI 조작이 되지 않음. 이미지 다운로드가 모두 끝나야 UI 동작 가능.

> 이미지 다운로드는 Background Thread에서 돌아야 Main Thread에서 UI Event만 처리하기 때문에 프리징이 안됨.

### How to Use

- Serial Queue

```swift
let queue = DispatchQueue(label: "SerialQueue")

queue.async {
   // this task is executed first
}

queue async {
   // this task is executed second
}
```

- Concurrent Queue
   - QoS(Quality of Service) 종류: userInteractive, userInitiated, default, utility, background
 
```swift
let queue = DispatchQueue(label: "ConcurrentQueue", attributes: .concurrent)

queue.async {

}

queue async {

}

// Tasks will start in the other they ar added but they can finish in any order
```

- Background Queue
   - 메인 스레드에서 돌리면 UI가 블락되는 것들을 작업
```swift
DispatchQueue.global().async {
   // download images
   DispatchQueue.main.async {
      // refresh the UI (UI 갱신은 무조건 main에서)
   }
}
```
