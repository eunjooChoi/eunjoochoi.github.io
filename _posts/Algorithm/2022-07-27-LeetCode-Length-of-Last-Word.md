---
title: "[Algorithm] Length of Last Word"
excerpt: "LeetCode - easy"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - Algorithm
  - LeetCode

---

## 문제 설명
메서드의 매개변수로 들어오는 s 문장에서 마지막 단어의 글자수를 리턴하는 문제.

![image](https://user-images.githubusercontent.com/22000470/181143725-3feeda9e-c7e0-445a-9616-4c4d12285608.png)

## 푼 방법

swift에서 split을 자주 사용하는데, 여기 문제에서는 공백이 여러칸일 수도 있어서 과연 될까 싶었지만 단어만 잘 빠져나와준다.. 이럴수가.. 꿀팁이다..

![image](https://user-images.githubusercontent.com/22000470/181143841-18d68433-8ac3-4ec0-a499-3a9faf09b1f4.png)

`"   fly me   to   the moon  "` 처럼 공백이 여러개 들어가 있는 문자열을 넣어도 split을 공백 단위로 쪼개니까 단어만 남는 최고의 방법..

split으로 쪼개고 array의 마지막 단어의 count를 반환하도록 하니 두줄만에 구현이 끝났다.

```swift
    func lengthOfLastWord(_ s: String) -> Int {
        let array = s.split(separator: " ")
        return array.last?.count ?? 0
    }
```

## 결과

![image](https://user-images.githubusercontent.com/22000470/181144057-88f13537-f4b3-42cf-9b93-b9cc75aa8d0d.png)

## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/0960daaa12318db9e88f6497b4105a4ce74f3a6d)
