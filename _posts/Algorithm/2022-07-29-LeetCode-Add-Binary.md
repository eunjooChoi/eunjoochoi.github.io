---
title: "[Algorithm] Add Binary"
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

String으로 들어오는 이진수 a와 b를 더한 결과를 String으로 반환하는 문제.

![image](https://user-images.githubusercontent.com/22000470/181773917-43237c32-120c-4e0d-8ac9-5846bcefadf8.png)

문제의 설명도, 예시도 아주 심플하다.

## 푼 방법

설명과 예시는 심플하지만 내 코드는 심플하지가 않았다...

```swift
func addBinary(_ a: String, _ b: String) -> String {
        let longerBinaryArr: [String.Element]
        let shortBinaryArr: [String.Element]
        
        if a.count <= b.count {
            longerBinaryArr = Array(b)
            shortBinaryArr = Array(a)
        } else {
            longerBinaryArr = Array(a)
            shortBinaryArr = Array(b)
        }
        
        var result: [Character] = []
        var add = 0
        
        for (idx, longerBinaryChar) in longerBinaryArr.reversed().enumerated() {
            if shortBinaryArr.count - idx - 1 < 0 {
                let longerBinaryInt = Int(longerBinaryChar.description) ?? 0
                let addResult = addResult(longerBinaryInt + add)
                
                result.insert(contentsOf: addResult.0, at: 0)
                add = addResult.1
                
            } else {
                let shortBinaryChar = shortBinaryArr[shortBinaryArr.count - idx - 1]
                let addResult = addResult((Int(longerBinaryChar.description) ?? 0) + (Int(shortBinaryChar.description) ?? 0) + add)
                
                result.insert(contentsOf: addResult.0, at: 0)
                add = addResult.1
            }
        }
        
        if add == 1 {
            result.insert("1", at: 0)
        }
        
        return String(result)
    }
    
    func addResult(_ result: Int) -> (String, Int) {
        let string: String
        let int: Int
        switch result {
        case 2:
            string = "0"
            int = 1
        case 3:
            string = "1"
            int = 1
        default:
            string = String(result)
            int = 0
        }
        
        return (string, int)
    }
```

- 우선 Swift에서는 subscript로 String의 글자에 접근할 수 없어서 먼저 Array로 String을 변환해주었다.
- 더 길게 들어오는 string을 기준으로 for문을 돌기 위해 a와 b의 길이를 비교했다.
- 반복문을 돌면서 두 이진수를 더해주었다.
   - addResult라는 메서드를 따로 빼서 2인경우, 3인 경우, 둘 다 아닌 경우를 반환해서 받아올림이 있는지와 해당 자리에 어떤 숫자가 들어가야 하는지 판단하도록 했다.
- 마지막 문자까지 체크한 뒤 for문을 빠져나왔을 때 add가 1로 설정되어 있다면 받아 올림이 되어 새로운 자리수가 생겨야 하는 것을 체크하도록 했다.
- 최후에 Character 배열을 String으로 변환하여 리턴했다.

알고리즘을 풀고 싶지 않을 때는 이상하게 코드가 더러워지는 것 같다. (항상 풀기 귀찮은 것 같기도 함..^^)

## 결과

![image](https://user-images.githubusercontent.com/22000470/181774668-650b87eb-d492-4eb5-a54b-3d1f9667d2c8.png)


## 레포 커밋
- [Algorithm commit](https://github.com/eunjooChoi/algorithm/commit/b27e7c99ce79ac330b43e593d9201a82713eb259)


