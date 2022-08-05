---
title: "[Algorithm] 문자열압축"
excerpt: "프로그래머스 lv.02"
  
toc: true
toc_sticky: true

categories:
  - Algorithm
tags:
  - [Algorithm, Programmers]

permalink: /algorithm/Programmers-String-Zip

---

왠지 레벨 2도 풀어봐야 할 것 같아서 손대본 문제.
예전에 문제를 봤던 것 처럼 익숙한데 왜일까...?

## 문제 설명
어피치가 문자열 압축을 공부하고 있다고 한다.
문자열에서 같은 값이 연속해서 나타나는 것을 문자의 개수와 반복되는 값으로 표현하고 싶다고 한다.

일단 예시를 보자.

> 간단한 예로 "aabbaccc"의 경우 "2a2ba3c"(문자가 반복되지 않아 한번만 나타난 경우 1은 생략함)와 같이 표현할 수 있는데, 이러한 방식은 반복되는 문자가 적은 경우 압축률이 낮다는 단점이 있습니다.  예를 들면, "abcabcdede"와 같은 문자열은 전혀 압축되지 않습니다. "어피치"는 이러한 단점을 해결하기 위해 문자열을 1개 이상의 단위로 잘라서 압축하여 더 짧은 문자열로 표현할 수 있는지 방법을 찾아보려고 합니다.
 
문자열이 들어오면 1글자 단위, 2글자 단위 ... n글자 단위로 잘라보면서 가장 짧게 압축이 될 때의 문자열 카운트를 반환하면 된다.

<img src="https://user-images.githubusercontent.com/22000470/179454625-c8e3fa35-8467-4a60-a397-9e67904c6aea.png" width="400">

메서드에는 소문자 알파벳으로만 이루어진 문자열 s가 들어온다.

abcabcdede는 3글자 단위로 잘랐을 때 3abcdede로 가장 짧아지므로 8을 리턴하게 된다.


## 푼 방법

끝없는 반복문들과의 싸움이었다.. 풀면서 현타가 왔다.. 이렇게 반복문을 여러번 써야 하는건지.. 더 좋은 방법이 있을 것도 같고...

1. 문자열을 쪼갤 단위가 1부터 문자열 글자수까지 가능하므로 `1...s.count`만큼 반복문을 돌린다.
2. 쪼개는 단위로 문자열을 쪼개 배열 splitedArr에 넣는다.
3. splitedArr을 돌면서 동일한 문자열은 제거하고 그 수만큼 숫자를 넣은(1이면 숫자 생략) result 문자열을 만들어준다.
4. 문자열 result를 반환한다.

```swift
    func solution(_ s:String) -> Int {
        var result = Int.max
        for splitCount in 1...s.count {
            var subStr = ""
            var splitedArr: [String] = []
            
            for (idx, char) in s.enumerated() {
                let remainder = (idx + 1) % splitCount
                if idx > 0, remainder == 0 {
                    
                    if splitCount == 1 {
                        splitedArr.append(subStr)
                        subStr.removeAll()
                        subStr.append(char)
                    } else {
                        subStr.append(char)
                        splitedArr.append(subStr)
                        subStr.removeAll()
                    }
                    
                    continue
                }
                
                if remainder < splitCount {
                    subStr.append(char)
                }
            }
            
            splitedArr.append(subStr)
            
            var resultStr = ""
            var repeatStr = splitedArr[0]
            var repeatCount = 1
            
            for (idx, string) in splitedArr.enumerated() {
                if idx == 0 { continue }
                if string != repeatStr {
                    let reapetCountStr = repeatCount == 1 ? "" : "\(repeatCount)"
                    resultStr.append(reapetCountStr + repeatStr)
                    repeatStr = string
                    repeatCount = 1
                } else {
                    repeatCount += 1
                }
            }
            
            let reapetCountStr = repeatCount == 1 ? "" : "\(repeatCount)"
            resultStr.append(reapetCountStr + repeatStr)
            
            
            if result > resultStr.count {
                result = resultStr.count
            }
        }
        
        return result
    }
```

풀어놓고도 찝찝한 이 마음.. Swift를 Swift답게 쓰지 못하는 것 같은 이 마음...

## 결과

<img src="https://user-images.githubusercontent.com/22000470/179455039-6d93faad-8bbe-4d73-80b4-ae4af4ce096a.png" width="300">


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/adab65e78bf02b58be77cff61f230d72d7256575)
