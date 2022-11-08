---
title: "[Algorithm] 푸드 파이트 대회"
excerpt: "프로그래머스 연습문제 - lv.1"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, Programmers]

permalink: /algorithm/programmers-food-fight
---

## 문제 설명

푸드 파이트 대회가 열렸다. 두 사람이 동시에 같은 칼로리의 음식을 먹고 최종적으로 물을 먼저 마시는 사람이 승리.

주어진 Food 배열이 [1, 3, 4, 6] 라면

- [0]은 물
- [1]은 1번음식이 3개이므로 둘이서 하나씩 먹고 하나는 남음
- [2]는 2번 음식이 4개이므로 둘이서 두 개씩 먹음
- [3]은 3번 음식이 6개이므로 둘이서 세 개씩 먹음

따라서 이 음식을 줄지어 늘여뜨려보면 **1223330333221** 로 답을 내릴 수 있다. (가운데는 물, 두 선수가 양 끝에서부터 먹는다고 본다.)

## 푼 방법

```swift
class FoodFight {
    func solution(_ food:[Int]) -> String {
        var result = ""
        let devidedFood: [Int] = food.map { $0 / 2 }
        
        for (idx, food) in devidedFood.enumerated() {
            result.append(String(repeating: String(idx), count: food))
        }
        
        let reversedResult = result.reversed()
        
        return result + "0" + reversedResult
    }
}
```

- food 배열을 모두 2로 나눈다.
- result 문자열에 음식 번호인 idx를 String(repeating:, count:)로 2로 나눈 값만큼 더한다.
- return은 결과값 + 물 + 결과값뒤집은 값이 되어야 양쪽을 표현할 수 있으므로 result + "0" + reversedResult로 반환한다.

## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/649505c014068a7384166fb56c31218467abbe4a)


